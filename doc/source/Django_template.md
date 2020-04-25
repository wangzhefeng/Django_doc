
# Django 使用模板显示内容

### 1.查看文章页面

> 如何通过一个处理方法获取？文章唯一标识！

创建 APP 处理方法(`./Django_demo/article/views.py`)：

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def article_detail(request, article_id):
    return HttpResponse("文章 id: %s", article_id)
```

配置 APP 响应请求(`./Django_demo/Django_demo/urls.py`)：

```python
from django.contrib import admin
from django.urls import path, re_path
from . import views
from article.views import article_detail

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", views.index),
    path("article/<int:article_id>", article_detail, name = "article_detail"),
    # re_path("^$", views.index),
]
```

### 2.objects

模型的 `objects` 是获取或操作模型的对象：

* Article.objects.get(条件)
* Article.objects.all()
* Article.objects.filter(条件)

配置 APP 引用模型(`./Django_demo/article/views.py`)：

```python
from django.shortcuts import render
from django.http import HttpResponse, Http404
from .models import Article

# Create your views here.
def article_detail(request, article_id):
    try:
        article = Article.objects.get(id = article_id)
    except Article.DoesNotExist:
        raise Http404("Not Exist")
    return HttpResponse("<h2>文章标题: %s</h2><br>文章内容: %s" % (article.title, article.content))
```


### 3.使用模板

> 前端页面和后端代码分离，减低耦合性

创建模板文件夹(模板文件的创建已经在 `./Django_demo/Django_demo/setting.py` 中有了配置)：

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

创建模板文件 `./Djang_demo/article/templates/article_detail.html`


```html
<html>
	<head>
	</head>
	<body>
        <h2>{{article_obj.title}}</h2>
        <hr>
		<p>{{article_obj.content}}</p>
	</body>
</html>
```


```python
from django.shortcuts import render, render_to_response, get_object_or_404
from django.http import HttpResponse, Http404
from .models import Article

# Create your views here.
def article_detail(request, article_id):
    try:
        article = Article.objects.get(id = article_id)
        context = {}
        context["article_obj"] = article
        # return render(request, "article_detail.html", context)
        return render_to_response("article_detail.html", context)
    except Article.DoesNotExist:
        raise Http404("Not Exist")
```


```python
from django.shortcuts import render, render_to_response, get_object_or_404
from django.http import HttpResponse, Http404
from .models import Article

# Create your views here.
def article_detail(request, article_id):
    article = get_object_or_404(Article, pk = article_id)
    context = {}
    context["article_obj"] = article
    return render_to_response("article_detail.html", context)
```


### 4.获取文章列表


创建 APP 文章列表模板(`./Djang_demo/article/templates/article_list.html`)：

```html
<html>
    <head>
    </head>
    <body>
        {% for article in articles %}
            <a href="{% url 'article_detail' article.pk%}">{{article.title}}</a>
        {% endfor %}
    </body>
</html>
```

`./Django_demo/Django_demo/urls.py`

```python
from django.contrib import admin
from django.urls import path, re_path
from . import views
from article.views import article_detail, article_list

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", views.index),
    path("article/<int:article_id>", article_detail, name = "article_detail"),
    path("article", article_list, name = "article_list"),
    # re_path("^$", views.index),
]
```

`./Djang_demo/article/views.py`

```python
from django.shortcuts import render, render_to_response, get_object_or_404
from django.http import HttpResponse, Http404
from .models import Article

# Create your views here.
def article_detail(request, article_id):
    article = get_object_or_404(Article, pk = article_id)
    context = {}
    context["article_obj"] = article
    return render_to_response("article_detail.html", context)

def article_list(request):
    articles = Article.objects.all()
    context = {}
    context["articles"] = articles
    return render_to_response("article_list.html", context)
```

### 5.总 urls 包含 APP 的 urls

创建、编辑 `./Django_demo/article/urls.py`

```python
from django.urls import path, re_path
from . import views

urlpatterns = [
    path("<int:article_id>", views.article_detail, name = "article_detail"),
    path("", views.article_list, name = "article_list"),
    # re_path("^$", views.index),
]
```

编辑 `./Django_demo/Django_demo/urls.py`:

```python
from django.contrib import admin
from django.urls import path, re_path, include
from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", views.index),
    path("article/", include("article.urls"))
]
```

