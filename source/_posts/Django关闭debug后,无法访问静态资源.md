---
title: Django关闭debug后,无法访问静态资源
date: 2021-11-09 03:18:28.327
updated: 2021-11-09 07:57:40.688
url: /?p=25
categories: 
- 后端
tags: 
- django
---

## 问题出现原因: 

当我们在开发django应用时如果设置了 DEBUG = True，那么django便会自动帮我们对静态文件进行路由；但是当我们设置DEBUG = False后，这一功能便没有了，此时静态文件就会出现加载失败的情况，想要让静态文件正常显示，我们就需要配置静态文件服务了。

## 解决方式:

+ 修改配置文件 `settings.py`

  ```python
  STATIC_URL = '/static/'
  STATIC_ROOT = 'static'
  STATICFILES_DIRS = [
    os.path.join(BASE_DIR, '/static/'), # 这里如果设置完了之后还是无法访问的话,可以尝试去掉"/"
  ]
  ```

+ 修改路由文件 `urls.py`

  ```python 
  from django.views import static
  from django.conf import settings
  from django.conf.urls import url
  
  urlpatterns = [
    ...
  　##　以下是新增
    path('static/<path:path>', static.serve,{'document_root': settings.STATIC_ROOT}, name='static'),
  ]
  ```