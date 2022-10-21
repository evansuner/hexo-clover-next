---
title: Redis的安装与使用
date: 2021-07-03 02:18:33.0
updated: 2021-11-09 03:24:38.411
url: /?p=31
categories: 
- 服务器
tags: 
- centos
- redis
---

<hr id="hr-toc">
<h2 id="1.%E7%99%BB%E5%BD%95%E5%AE%98%E7%BD%91%E4%B8%8B%E8%BD%BD%E7%A8%B3%E5%AE%9A%E7%89%88Redis%E6%BA%90%E7%A0%81"><a name="t0"></a>1.登录官网下载稳定版Redis源码</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;官网下载地址：<a href="https://redis.io/download">https://redis.io/download</a></p> 
<p><img alt="" height="473" src="https://img-blog.csdnimg.cn/20210322111055195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="695"></p> 
<h2 id="2.%E5%B0%86%E4%B8%8B%E8%BD%BD%E5%A5%BD%E7%9A%84%E6%BA%90%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8%EF%BC%8C%E4%BD%BF%E7%94%A8tar%E5%91%BD%E4%BB%A4%E8%A7%A3%E5%8E%8B"><a name="t1"></a>2.将下载好的源文件上传到服务器，使用tar命令解压</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;笔者上传到user/local目录，执行指令：</p> 
<pre><code class="hljs css"><span class="hljs-selector-tag">tar</span> <span class="hljs-selector-tag">-zxvf</span> <span class="hljs-selector-tag">redis-6</span><span class="hljs-selector-class">.2</span><span class="hljs-selector-class">.1</span><span class="hljs-selector-class">.tar</span><span class="hljs-selector-class">.gz</span></code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="74" src="https://img-blog.csdnimg.cn/20210322113340751.png" width="1169"></p> 
<h2 id="3.%E5%AE%89%E8%A3%85gcc%E7%8E%AF%E5%A2%83"><a name="t2"></a>3.安装gcc环境</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;Redis源码编译需要c的环境，执行命令安装：</p> 
<pre><code class="hljs sql">yum <span class="hljs-keyword">install</span> gcc-c++</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="291" src="https://img-blog.csdnimg.cn/20210322120810190.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="1102"></p> 
<h2 id="4.%E7%BC%96%E8%AF%91%26%E5%AE%89%E8%A3%85"><a name="t3"></a>4.编译&amp;安装</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;进入redis-6.2.1文件夹进行编译：</p> 
<pre><code class="hljs go"><span class="hljs-built_in">make</span>
</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="88" src="https://img-blog.csdnimg.cn/20210322121450729.png" width="486"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;进入src目录进行安装：</p> 
<pre><code class="hljs sql">make <span class="hljs-keyword">install</span></code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="237" src="https://img-blog.csdnimg.cn/20210322121707126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="458"></p> 
<h2 id="5.%E6%95%B4%E7%90%86Redis%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84%E5%B9%B6%E5%90%AF%E5%8A%A8%E6%9C%8D%E5%8A%A1"><a name="t4"></a>5.整理Redis目录结构并启动服务</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;在usr/local目录下新建redis目录，在redis目录下创建bin、etc目录：</p> 
<pre><code class="hljs properties"><ol class="hljs-ln" style="width:930px"><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="1"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-attr">mkdir</span> <span class="hljs-string">redis</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="2"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-attr">cd</span> <span class="hljs-string">redis</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="3"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-attr">mkdir</span> <span class="hljs-string">bin</span></div></div></li><li><div class="hljs-ln-numbers"><div class="hljs-ln-line hljs-ln-n" data-line-number="4"></div></div><div class="hljs-ln-code"><div class="hljs-ln-line"><span class="hljs-attr">mkdir</span> <span class="hljs-string">etc</span></div></div></li></ol></code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;将原编译目录src路径下的常用命令移动到bin目录下：</p> 
<pre><code class="hljs sql">mv mkreleasehdr.sh redis-benchmark redis-<span class="hljs-keyword">check</span>-aof redis-cli redis-<span class="hljs-keyword">server</span> /usr/<span class="hljs-keyword">local</span>/redis/<span class="hljs-keyword">bin</span>/</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="93" src="https://img-blog.csdnimg.cn/20210322122608140.png" width="1185"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;将原编译目录redis.conf文件移动到etc路径下：</p> 
<pre><code class="hljs groovy">mv redis.conf <span class="hljs-regexp">/usr/</span>local<span class="hljs-regexp">/redis/</span>etc</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="75" src="https://img-blog.csdnimg.cn/20210322122948903.png" width="714"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;启动redis服务：</p> 
<pre><code class="hljs vbscript">redis-<span class="hljs-built_in">server</span> ../etc/redis.conf</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="550" src="https://img-blog.csdnimg.cn/2021032212320831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="843"></p> 
<h2 id="6.%E9%85%8D%E7%BD%AE%E5%A4%96%E7%BD%91%E8%AE%BF%E9%97%AE"><a name="t5"></a>6.配置外网访问</h2> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;修改配置文件，配置后台启动</p> 
<pre><code class="hljs">daemonize</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="327" src="https://img-blog.csdnimg.cn/20210322161227631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="800"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;注释掉这一行，此行作用为指定redis接收哪一ip地址的请求</p> 
<pre><code class="hljs yaml"><span class="hljs-string">bind</span> <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span></code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="308" src="https://img-blog.csdnimg.cn/20210322161326604.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="826"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;开启requirepass并配置密码</p> 
<pre><code class="hljs">requirepass</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="268" src="https://img-blog.csdnimg.cn/20210322161957599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="829"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;最后一个，禁用redis安全模式</p> 
<pre><code class="hljs crystal"><span class="hljs-keyword">protected</span>-mode</code><div class="hljs-button signin" data-title="登录后复制" data-report-click="{&quot;spm&quot;:&quot;1001.2101.3001.4259&quot;}" onclick="hljs.signin(event)"></div></pre> 
<p><img alt="" height="135" src="https://img-blog.csdnimg.cn/20210322162044264.png" width="764"></p> 
<p>&nbsp; &nbsp; &nbsp; &nbsp;用RedisDesktopManager测试远程连接</p> 
<p><img alt="" height="323" src="https://img-blog.csdnimg.cn/20210322163032356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU2Njgy,size_16,color_FFFFFF,t_70" width="557"></p> 
<p>&nbsp;</p>
                </div>