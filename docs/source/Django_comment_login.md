
# Django 评论以用户登录

## 1.实现评论功能的方式

1. 第三方社会化评论插件
    - 友言
    - 多说
    - 畅言
    - Disqus
    - 网易云跟帖
2. Django 评论库 `django-commnet`
3. 自己写代码实现

## 2.创建评论模型

* 评论对象
* 评论内容
* 评论时间
* 评论者


### 创建评论模型 APP

```shell
$ python3 manage.py startapp comment
```


`./mysite/mysite/comment/models.py`

```python
from django.db import models
from django.contrib.contenttypes.fields import GenericForeignKey
from django.contrib.contenttypes.models import ContentType
from django.contrib.auth.models import User


# Create your models here.
class Comment(models.Model):
    content_type = models.ForeignKey(ContentType, on_delete = models.DO_NOTHING)
    object_id = models.PositiveIntegerField()
    content_object = GenericForeignKey("content_type", "object_od")

    text = models.TextField()
    comment_time = models.DateTimeField(auto_now_add = True)
    user = models.ForeignKey(User, on_delete = models.DO_NOTHING)

```


## 3.评论需要登录用户

* 确保较低程度减少垃圾评论
* 提高了评论门槛（第三方登录解决）
* 通知用户