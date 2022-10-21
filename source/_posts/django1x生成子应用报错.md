---
title: django1.x生成子应用报错
date: 2021-08-14 02:18:25.0
updated: 2021-11-09 07:54:35.292
url: /?p=23
categories: 
- 后端
tags: 
- Python
- django
---

## django生成子应用是报错 '%s=%s' % (k, v) for k, v in params.items(), ^ SyntaxError: Generator expression must be parenthesized

报错内容如下:

```bash
(venv) D:\python_learn\restfullearn\RESTAPI>python manage.py startapp book
Traceback (most recent call last):
  File "manage.py", line 22, in <module>
    execute_from_command_line(sys.argv)
  File "D:\Python\Python\venv\lib\site-packages\django\core\management\__init__.py", line 364, in execute_from_command_line
    utility.execute()
  File "D:\Python\Python\venv\lib\site-packages\django\core\management\__init__.py", line 338, in execute
    django.setup()
  File "D:\Python\Python\venv\lib\site-packages\django\__init__.py", line 27, in setup
    apps.populate(settings.INSTALLED_APPS)
  File "D:\Python\Python\venv\lib\site-packages\django\apps\registry.py", line 85, in populate
    app_config = AppConfig.create(entry)
  File "D:\Python\Python\venv\lib\site-packages\django\apps\config.py", line 94, in create
    module = import_module(entry)
  File "D:\Python\Python38\lib\importlib\__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
  File "<frozen importlib._bootstrap>", line 991, in _find_and_load
  File "<frozen importlib._bootstrap>", line 975, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 671, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 783, in exec_module
  File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
  File "D:\Python\Python\venv\lib\site-packages\django\contrib\admin\__init__.py", line 4, in <module>
    from django.contrib.admin.filters import (
  File "D:\Python\Python\venv\lib\site-packages\django\contrib\admin\filters.py", line 10, in <module>
    from django.contrib.admin.options import IncorrectLookupParameters
  File "D:\Python\Python\venv\lib\site-packages\django\contrib\admin\options.py", line 12, in <module>
    from django.contrib.admin import helpers, widgets
  File "D:\Python\Python\venv\lib\site-packages\django\contrib\admin\widgets.py", line 152
    '%s=%s' % (k, v) for k, v in params.items(),
    ^
SyntaxError: Generator expression must be parenthesized
```

我们直接删除`"D:\Python\Python\venv\lib\site-packages\django\contrib\admin\widgets.py"`, line 152后面的逗号解决

![image-20210914201108691](https://i.loli.net/2021/09/15/ctn8Mu1CZRWDvo4.png)

![image-20210914201034468](https://i.loli.net/2021/09/15/D4jQPkI97lXyt8z.png)