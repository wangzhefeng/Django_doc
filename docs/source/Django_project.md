
# 构建个人博客网站

## 1.简单构建

* 网站功能模块(Django App)
    - 博客
        + 博文
        + 博客分类
        + 博客标签
    - 评论
    - 点赞
    - 阅读
    - 用户
        + 第三方登录(QQ/微博)

## 2.开启本地虚拟环境

> 隔开 Python 项目的运行环境

1. 避免多个项目之间 Python 库的冲突
2. 完整便捷导出 Python 库的列表

```shell
$ mkvirtualenv django_mysite_env
$ workon django_mysite_env
$ pip3 install Django==2.1.5
$ cd ~/project/
```

## 3.初步创建 blog 应用

* 博文
* 博客分类
    - 一篇博客一种分类
    - 一篇博客多种分类

```shell
$ django-admin startproject mysite
$ cd mysite
$ python3 manage.py startapp blog
```


创建 `blog` 类：

```python
# ./mysite/blog/apps.py

from django.apps import AppConfig


class BlogConfig(AppConfig):
    name = 'blog'
```


添加 `blog` APP：

```python
# ./mysite/mysite/setting.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```

创建 `blog` APP 模型:

```python
# ./mysite/blog/models.py

from django.db import models
from django.contrib.auth.models import User

# Create your models here.
class BlogType(models.Model):
    type_name = models.CharField(max_length = 15)

    def __str__(self):
        return self.type_name

class Blog(models.Model):
    title = models.CharField(max_length = 50)
    blog_type = models.ForeignKey(BlogType, on_delete = models.DO_NOTHING)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete = models.DO_NOTHING)
    created_time = models.DateTimeField(auto_now_add = True)
    last_update_time = models.DateTimeField(auto_now = True)

    def __str__(self):
        return "<Blog: %s>" % self.title
```

配置后台显示内容:

```python
# ./mysite/blog/admin

from django.contrib import admin
from .models import BlogType, Blog

# Register your models here.
@admin.register(BlogType)
class BlogTypeAdmin(admin.ModelAdmin):
    list_display = ("id", "type_name")

@admin.register(Blog)
class BlogAdmin(admin.ModelAdmin):
    list_display = ("title", "blog_type", "author", "created_time", "last_update_time")
```


初始化数据库：

```shell
$ python3 manage.py migrate
```

```
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying sessions.0001_initial... OK
```

创建超级管理员：

```shell
$ python3 manage.py createsuperuser
```

```
Username (leave blank to use 'zfwang'): wangzf
Email address: wangzhefengr@163.com
Password:
Password (again):
Superuser created successfully.
```

创建数据库迁移文件：

```shell
$ python3 manage.py makemigrations
```

```
Migrations for 'blog':
  blog/migrations/0001_initial.py
    - Create model Blog
    - Create model BlogType
    - Add field blog_type to blog
```

启动项目：

```shell
$ python3 manage.py runserver
```

## 4. 创建 `requirements.txt`，一键导出和安装

```shell
pip3 freeze > requirements.txt
pip3 install -r requirements.txt
```