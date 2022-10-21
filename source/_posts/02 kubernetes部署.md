---
title: 02 kubernetes部署
date: 2021-11-13 20:27:17.53
updated: 2021-11-13 20:27:17.53
url: /?p=38
categories: 
- Kubernetes 学习笔记
tags: 
- k8s
---

## 一、kubeadm 部署篇

kubernetes 有两种方式：

+ 二进制方式，可定制但是部署复杂容易出错
+ kubeadm 工具安装，部署简单，不可定制化（讲解）

### 1、部署系统版本

| 软件            | 版本                                            |
| :-------------- | :---------------------------------------------- |
| CentOS          | CentOS Linux release 8.4.2105                   |
| Docker          | Docker version 20.10.10, build b485636          |
| Kubernetes      | V1.19.1                                         |
| Flannel         | V0.13.0                                         |
| Kernel-lm       | kernel-lt-4.4.245-1.el7.elrepo.x86_64.rpm       |
| Kernel-lm-devel | kernel-lt-devel-4.4.245-1.el7.elrepo.x86_64.rpm |

### 2、节点规划

| Hostname      | Ip            | 内核版本                    |
| :------------ | :------------ | :-------------------------- |
| k8s-master-01 | 192.168.12.11 | 4.4.245-1.el7.elrepo.x86_64 |
| k8s-node-01   | 192.168.12.12 | 4.4.245-1.el7.elrepo.x86_64 |
| k8s-node-02   | 192.168.12.13 | 4.4.245-1.el7.elrepo.x86_64 |

### 3、关闭 selinux

```bash
# 永久关闭
sed -i 's#enforcing#disabled#g' /etc/selinux/config
# 临时关闭
setenforce 0
```

### 4、关闭 swap 分区

一旦触发 swap，会导致系统性能急剧下降，所以一般情况下，k8s 要求关闭 swap 分区

```bash
swapoff -a
sed -i.bak '-s/^.*centos-swap/#&/g' /etc/fstab
echo 'KUBELET_EXTRA_ARGS="--fail-swap-on=false"' > /etc/sysconfig/kubelet
```

### 5、配置国内 yum 源

默认情况下，CentOS 是用的是官方 yum 源，所以一般情况下在国内使用非常的慢，所以我们可以替换国内一些比较成熟的 yum 源，例如清华大学镜像源，网易镜像源等。

```bash
# 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak

curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
curl -o /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo

# 刷新缓存
yum makecache
# 更新系统
yum update -y --exclud=kernel*
```

### 6、升级内核版本

由于 Docker 运行较新的系统内核功能，例如 ipvs 等等，所以一般情况下，我们需要使用4.0+以上版本的系统内核。（这里我不做更新，因为我的是 centos 8）

```bash
wget https://elrepo.org/linux/kernel/el7/x86_64/RPMS/kernel-lt-4.4.245-1.el7.elrepo.x86_64.rpm
wget https://elrepo.org/linux/kernel/el7/x86_64/RPMS/kernel-lt-devel-4.4.245-1.el7.elrepo.x86_64.rpm

yum localinstall -y kernel-lt*

grub2-set-default  0 && grub2-mkconfig -o /etc/grub2.cfg

grubby --default-kernel

# 重启
reboot
```

### 7、安装 ipvs

ipvs 是系统内核中的一个模块，其网络转发性能很高。一般情况下，我们首选 ipvs。

```bash
yum install -y conntrack-tools ipvsadm ipset conntrack libseccomp

# 加载 ipvs 模块
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
ipvs_modules="ip_vs ip_vs_lc ip_vs_wlc ip_vs_rr ip_vs_wrr ip_vs_lblc ip_vs_lblcr ip_vs_dh ip_vs_sh ip_vs_fo ip_vs_nq ip_vs_sed ip_vs_ftp nf_conntrack"
for kernel_module in \${ipvs_modules}; do
  /sbin/modinfo -F filename \${kernel_module} > /dev/null 2>&1
  if [ $? -eq 0 ]; then
    /sbin/modprobe \${kernel_module}
  fi
done
EOF

chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep ip_vs
```

### 8、内核参数优化

内核参数优化的主要目的是使其更适合 kubernets 的正常运行。

```bash
cat > /etc/sysctl.d/k8s.conf << EOF
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
fs.may_detach_mounts = 1
vm.overcommit_memory=1
vm.panic_on_oom=0
fs.inotify.max_user_watches=89100
fs.file-max=52706963
fs.nr_open=52706963
net.ipv4.tcp_keepalive_time = 600
net.ipv4.tcp.keepaliv.probes = 3
net.ipv4.tcp_keepalive_intvl = 15
net.ipv4.tcp.max_tw_buckets = 36000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp.max_orphans = 327680
net.ipv4.tcp_orphan_retries = 3
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.ip_conntrack_max = 65536
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.top_timestamps = 0
net.core.somaxconn = 16384
EOF

# 立即生效
sysctl --system
```

### 9、安装基础软件

安装一些一出软件，是为了方便我们的日常使用。

```bash
yum install wget expect vim net-tools ntp bash-completioin ipvsadm ipset jq iptables conntrack sysstat libseccomp -y
```

### 10、关闭防火墙

关闭防火墙是为了方便日常使用，不会给我们造成困扰，在生产环境中建议打开。

```bash
systemctl disable --now firewalld
```

### 11、安装 docker

Docker 主要是作为 k8s 管理的常用容器工具之一。

#### 11.1、centos7.x

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum install docker-ce -y
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://8mh75mhz.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload ; systemctl restart docker;systemctl enable --now docker.service
```

#### 11.2、centos8.x

```bash
wget https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.13-3.2.el7.x86_64.rpm
yum install containerd.io-1.2.13-3.2.el7.x86_64.rpm -y
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum install docker-ce -y
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://8mh75mhz.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload ; systemctl restart docker;systemctl enable --now docker.service
```

### 12、同步集群时间

在集群当中，时间是一个很重哦个要的概念，一旦集群当中某台机器跟集群的时间不一致，可能会导致集群面临很多问题。所以在不熟集群之前需要同步集群当中的所有机器的时间

#### 12.1、centos7.x

```bash
yum install ntp -y
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo 'Asia/Shanghai' > /etc/timezone

ntpdate time2.aliyun.com

# 写入定时任务
*/1 * * * * ntpdate time2.aliyun.com > /dev/null 2>&1
```

#### 12.2、centos8.x

```bash
rpm -ivh http://mirrors.wlnmp.com/centos/wlnmp-release-centos.noarch.rpm
yum install wntp -y

lln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo 'Asia/Shanghai' > /etc/timezone

# 写入定时任务
*/1 * * * * ntpdate time2.aliyun.com > /dev/null 2>&1
```

### 13、配置 kubernetes 源

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

setenforce 0

yum install -y kubelet kubeadm kubectl

systemctl enable kubelet && systemctl start kubelet
```

### 14、节点初始化

```bash
kubeadm init \
--image-repository=registry.cn-hangzhou.aliyuncs.com/k8sos \
--kubernetes-version=v1.18.8 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```

![image-20211112173821575](https://i.loli.net/2021/11/12/hCwGFY9T7Rs8DzL.png)

#### 14.1、配置 kubernetes 用户信息

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### 14.2、增加命令提示

```bash
yum install -y bash-completion
source /usr/share/bash-completion/bash_completion
source < (kubectl completion bash)
echo "source < (kubectl completion bash)" >> ~/.bashrc 
#这里如果服务器使用了 zsh，需要变更为~/.zshrc
```

### 15、安装集群网络插件

kubernetes 需要使用第三方的网络插件来实现 kubernetes 的网络功能，这样一来，安装网络插件成为必要前提；第三方网络插件有很多，常用的有 flanneld、calico和 cannel（flanneld+calico），不同的网络组件，都提供基本的网络功能，为各个 Node 节点提供 IP 网络等。

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/k8sos/flannel:v0.12.0-amd64;\
docker tag registry.cn-hangzhou.aliyuncs.com/k8sos/flannel:v0.12.0-amd64 \
quay.io/coreos/flannel:v0.12.0-amd64

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

![图片1](https://i.loli.net/2021/11/13/uBIvd9LUMgVxsNb.png)

### 16、Node 节点加入集群

```bash
# 创建 TOKEN
kubeadm token create --print-join-command

# node 节点加入集群
kubeadm join 10.0.0.50:6443 -token 038qwm.hpoxkc1f2fkgti3r \
--discovery-token-ca-cert-hash sha256:edcd2c212be408f741e439abe304711ffb0adbb3bedbb1b93354bfdc3dd13b04
```

### 17、查看部署结果

```bash
kubectl get node -o wide
```