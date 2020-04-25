# 后台定制和修改模型

## 1.定制 admin 后台

* 设置模型 `__str__`
* 定制 `admin`

`./Django_demo/article/models.py`：

```python
from django.db import models

# Create your models here.
class Article(models.Model):
    title = models.CharField(max_length = 30)
    content = models.TextField()

    def __str__(self):
        return "<Article: %s>" % self.title
```

`./Django_demo/article/admin.py`：

```python
from django.contrib import admin
from .models import Article

# Register your models here.
@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ("id", "title", "content")
    ordering = ("id",)
    # ordering = ("-id",)

# admin.site.register(Article, ArticleAdmin)
```

## 2.修改模型

修改模型:

`./Django_demo/article/models.py`：

```python
# method 1
from django.db import models
from django.utils import timezone

# Create your models here.
class Article(models.Model):
    title = models.CharField(max_length = 30)
    content = models.TextField()
    created_time = models.DateTimeField(default = timezone.now)

    def __str__(self):
        return "<Article: %s>" % self.title


# method 2
from django.db import models
from django.utils import timezone

# Create your models here.
class Article(models.Model):
    title = models.CharField(max_length = 30)
    content = models.TextField()
    created_time = models.DateTimeField(auto_now_add = True)

    def __str__(self):
        return "<Article: %s>" % self.title
```

`./Django_demo/article/admin.py`：

```python
from django.contrib import admin
from .models import Article

# Register your models here.
@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ("id", "title", "content", "created_time")
    ordering = ("id",)
    # ordering = ("-id",)

# admin.site.register(Article, ArticleAdmin)
```

更新数据库：

```shell
$ python3 manage.py makemigrations
$ python3 manage.py migrate
```


