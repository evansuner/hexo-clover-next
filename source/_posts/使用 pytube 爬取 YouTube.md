---
title: 使用 pytube 爬取 YouTube 视频
date: 2021-11-14 03:16:01.549
updated: 2021-11-14 03:16:01.549
url: /?p=39
categories: 
- 爬虫
tags: 
- Python
- 爬虫
- pytube
---

看完《同居人是猫》后，发现片头曲《アンノウンワールド》（Unknown world）真的很好听，但是在网抑云上却没找到歌曲。
> 这里不得不说，网抑云的版权是真的少，动漫已经出了快两年了，歌曲都还没有争取到。

下面进入正题：

## 准备
```bash
# 直接使用 pytube，上手简单
pip3 install pytube
```
## 代码
```python
from pytube import YouTube
yt = YouTube("https://www.youtube.com/watch?v=U_MH7q3hSL0")
yt.streams.get_highest_resolution().download()
```

## pytube 使用手册
pytube 我认为最主要的两个类：
+ YouTube
下载视频
+ Playlist
下载列表视频
下面我们先介绍一下 YouTube 这个类
### Youtube
```python
from pytube import YouTube
yt = YouTube("https://www.youtube.com/watch?v=U_MH7q3hSL0")

print(yt.title) # 返回视频名称

print(yt.thmbnail_url) # 返回视频开始前的缩略图url

print(yt.streams) # 返回当前视频的音频/视频流 这是一个 queryset
[
<Stream: itag="17" mime_type="video/3gpp" res="144p" fps="8fps" vcodec="mp4v.20.3" acodec="mp4a.40.2" progressive="True" type="video">, 
<Stream: itag="18" mime_type="video/mp4" res="360p" fps="30fps" vcodec="avc1.42001E" acodec="mp4a.40.2" progressive="True" type="video">, 
<Stream: itag="22" mime_type="video/mp4" res="720p" fps="30fps" vcodec="avc1.64001F" acodec="mp4a.40.2" progressive="True" type="video">, 
<Stream: itag="299" mime_type="video/mp4" res="1080p" fps="60fps" vcodec="avc1.64002a" progressive="False" type="video">, 
<Stream: itag="298" mime_type="video/mp4" res="720p" fps="60fps" vcodec="avc1.4d4016" progressive="False" type="video">, 
<Stream: itag="135" mime_type="video/mp4" res="480p" fps="30fps" vcodec="avc1.4d4014" progressive="False" type="video">, 
<Stream: itag="134" mime_type="video/mp4" res="360p" fps="30fps" vcodec="avc1.4d401e" progressive="False" type="video">, 
<Stream: itag="133" mime_type="video/mp4" res="240p" fps="30fps" vcodec="avc1.4d400c" progressive="False" type="video">, 
<Stream: itag="160" mime_type="video/mp4" res="144p" fps="30fps" vcodec="avc1.4d400b" progressive="False" type="video">, 
<Stream: itag="139" mime_type="audio/mp4" abr="48kbps" acodec="mp4a.40.5" progressive="False" type="audio">, 
<Stream: itag="140" mime_type="audio/mp4" abr="128kbps" acodec="mp4a.40.2" progressive="False" type="audio">, 
<Stream: itag="251" mime_type="audio/webm" abr="160kbps" acodec="opus" progressive="False" type="audio">]
"""

# streams 的每一项下的 field 我们都可以作为一个 streams 的过滤属性
# 例如
print(yt.streams.filter(progressive=True)) # 过滤可供下载的流
print(yt.streams.filter(file_extension='mp4')) # 过滤格式为 mp4的流

# 选择一个想要的流我们可以使用一下方式进行下载
yt.streams.get_by_itag(22).download() # 根据 yt.streams的返回结果我们可以得到想要的 itag 值
# 或者
yt.streams.get_highest_resolution().download()
yt.streams.get_lowest_resolution.download()
yt.streams.get_audio_only().download()
```
### Playlist

```python
from pytube import Playlist
p = Playlist(https://www.youtube.com/watch?v=hGkMbBzU-ic&list=PLZbj1ccJWFKWqYPrZMxkYhIrNdQNHNSPL)

print(p.title) # 返回视频列表的名称

# 获取列表下的每一个视频 url
for url in p.video_urls[:3]:
    print(url)
"""
['https://www.youtube.com/watch?v=hGkMbBzU-ic', 'https://www.youtube.com/watch?v=XJXRhiH8h94', 'https://www.youtube.com/watch?v=TfpeS-y6h1E']
"""

# 下载列表中的所有视频
for video in p.videos:
    video.streams.get_highest_resolution().download()

```

