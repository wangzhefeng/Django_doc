
# Django 博客分页和 shell 命令行模式

## 1.shell 命令行模式

```shell
cd mysite
python3 manage.py shell
```

### 快速查看博客

```python
from blog.models import Blog, BlogType
from django.contrib.auth.models import User

# 查看当前博客数量、内容
dir()
Blog.objects.count()
Blog.objects.all().count()
BlogType.objects.all()
User.objects.all()
```

### 模型新增对象

添加示例：

```python
from blog.models import Blog, BlogType
from django.contrib.auth.models import User

blog = Blog()
blog.title = "shell下第一篇"
blog.content = "xxxxxxxxxxxxxx"
blog_type = BlogType.objects.all()[0]
blog.blog_type = blog_type
user = User.objects.all()[0]
blog.author = user
blog.save()

Blog.objects.all()
```

批量添加：

```python
from blog.models import Blog, BlogType
from django.contrib.auth.models import User

for i in range(1, 6):
    for j in range(BlogType.objects.all().count()):
        blog = Blog()
        blog.title = "Blog %s %s" % (BlogType.objects.all()[j], i)
        blog.content = "XXXXXXXXXXXXXXXX"
        blog_type = BlogType.objects.all()[j]
        blog.blog_type = blog_type
        user = User.objects.all()[0]
        blog.author = user
        blog.save()

```


## 2.Django 内容分页

内容排序：


```python
from django.db import models
from django.contrib.auth.models import User
from ckeditor_uploader.fields import RichTextUploadingField

# Create your models here.
class BlogType(models.Model):
    type_name = models.CharField(max_length = 15)

    def __str__(self):
        return self.type_name

class Blog(models.Model):
    title = models.CharField(max_length = 50)
    blog_type = models.ForeignKey(BlogType, on_delete = models.DO_NOTHING)
    # content = models.TextField()
    content = RichTextUploadingField()
    author = models.ForeignKey(User, on_delete = models.DO_NOTHING)
    created_time = models.DateTimeField(auto_now_add = True)
    last_update_time = models.DateTimeField(auto_now = True)

    def __str__(self):
        return "<Blog: %s>" % self.title

    class Meta:
        """博客分类"""
        ordering = ["-created_time"]
```

博客分页：

* 前端：发送请求，请求打开具体分页内容
* 后端：处理请求，返回具体分页内容相应请求

```python
del Blog

# 分页器
from django.core.paginator import Paginator
from blog.models import Blog, BlogType

# 博客列表
blogs = Blog.objects.all()
blogs.count()

# 实例化分页器,具体如何分页
paginator = Paginator(blogs, 10)

# 具体页面
for i in paginator.page_range()
    page = paginator.page(paginator.page(i))
```



```python
# Create your views here.
def blog_list(request):
    blogs_all_list = Blog.objects.all()
    paginator = Paginator(blogs_all_list, 10) # 每10页进行分页
    page_num = request.GET.get("page", 1) # 获取页码参数(GET请求)
    page_of_blogs = paginator.get_page(page_num)

    context = {}
    # context["blogs"] = Blog.objects.all()
    context["blogs"] = page_of_blogs.object_list
    context["blog_types"] = BlogType.objects.all()
    context["page_of_blogs"] = page_of_blogs
    context["blogs_count"] = Blog.objects.all().count()
    return render_to_response("blog/blog_list.html", context)
```


## 3.优化分页展示

友好的用户体验：

* 当前页高亮
* 不要过多页码选择，影响页面布局



