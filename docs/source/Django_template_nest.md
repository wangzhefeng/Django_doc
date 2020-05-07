
# 模板嵌套

## 1.常用的模板标签

* 元素
    - `{% element %}`
* 循环
    - `for`
* 条件
    - `if`、`ifequal`、`ifnotequal`
* 链接
    - `url`
* 模板嵌套
    - `block`、`extends`、`include`
* 注释
    - `{# #}`


## 2.公共模板文件

base.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title%}{% endblock %}</title>
</head>
<body>
    <div>
        <a href="{% url 'home' %}">
            <h3>个人博客网站</h3>
        </a>
    </div>
    <hr>
    {% block content %}{% endblock %}
</body>
</html>
```

blog_detail.html

```html
{% extends 'base.html' %}

{# 页面标题 #}
{% block title %}
    {{ blog.title }}
{% endblock %}

{# 页面内容 #}
{% block content %}
    <h3>{{ blog.title }}</h3>
    <p>作者：{{ blog.author }}</p>
    <p>发表日期：{{ blog.created_time|date:"Y-m-d H:n:s" }}</p>
    <p>分类：
        <a href="{% url 'blogs_with_type' blog.blog_type.pk %}">
            {{ blog.blog_type }}
        </a>
    </p>
    <p>{{ blog.content }}</p>
{% endblock %}


<!-- <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ blog.title }}</title>
</head>
<body>
    <div>
        <a href="{% url 'home' %}">
            <h3>个人博客网站</h3>
        </a>
    </div>
    <hr>
    <h3>{{ blog.title }}</h3>
    <p>作者：{{ blog.author }}</p>
    <p>发表日期：{{ blog.created_time|date:"Y-m-d H:n:s" }}</p>
    <p>分类：
        <a href="{% url 'blogs_with_type' blog.blog_type.pk %}">
            {{ blog.blog_type }}
        </a>
    </p>
    <p>{{ blog.content }}</p>
</body>
</html> -->
```

base_list.html

```html
{% extends 'base.html' %}

{# 页面标题 #}
{% block title %}
    wangzf
{% endblock %}

{# 页面内容 #}
{% block content %}
    {% for blog in blogs %}
        <a href="{% url 'blog_detail' blog.pk %}">
            <h3>{{ blog.title }}</h3>
        </a>
        <p>{{ blog.content|truncatechars:30 }}</p>
    {% empty %}
        <p>-- 暂无博客，敬请期待 --</p>
    {% endfor %}
    <p>一共有{{ blogs|length }}篇博客</p>
{% endblock %}


<!-- 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>wangzf</title>
</head>
<body>
    <div>
        <a href="{% url 'home' %}">
            <h3>个人博客网站</h3>
        </a>
    </div>
    <hr>
    {% for blog in blogs %}
        <a href="{% url 'blog_detail' blog.pk %}">
            <h3>{{ blog.title }}</h3>
        </a>
        <p>{{ blog.content|truncatechars:30 }}</p>
    {% empty %}
        <p>-- 暂无博客，敬请期待 --</p>
    {% endfor %}
    <p>一共有{{ blogs|length }}篇博客</p>
</body>
</html> -->
```

base_with_type.html

```html
{% extends 'base.html' %}

{# 页面标题 #}
{% block title %}
    {{ blog_type.type_name }}
{% endblock %}

{# 页面内容 #}
{% block content %}
    <h3>{{ blog_type.type_name }}</h3>
    {% for blog in blogs %}
        <a href="{% url 'blog_detail' blog.pk %}">
            <h3>{{ blog.title }}</h3>
        </a>
        <p>{{ blog.content|truncatechars:30 }}</p>
    {% empty %}
        <p>-- 暂无博客，敬请期待 --</p>
    {% endfor %}
    <p>一共有{{ blogs|length }}篇博客</p>
{% endblock %}


<!-- <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ blog_type.type_name }}</title>
</head>
<body>
    <div>
        <a href="{% url 'home' %}">
            <h3>个人博客网站</h3>
        </a>
    </div>
    <hr>
    <h3>{{ blog_type.type_name }}</h3>
    {% for blog in blogs %}
        <a href="{% url 'blog_detail' blog.pk %}">
            <h3>{{ blog.title }}</h3>
        </a>
        <p>{{ blog.content|truncatechars:30 }}</p>
    {% empty %}
        <p>-- 暂无博客，敬请期待 --</p>
    {% endfor %}
    <p>一共有{{ blogs|length }}篇博客</p>
</body>
</html> -->
```

## 3.全局模板文件夹

settings -> TEMPLATES -> DIRS

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR, "templates"),
        ],
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

## 4.模板文件设置建议

* app 模板文件 -> app
* project 模板文件 -> project

```
.
├── blog
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-37.pyc
│   │   ├── admin.cpython-37.pyc
│   │   ├── models.cpython-37.pyc
│   │   ├── urls.cpython-37.pyc
│   │   └── views.cpython-37.pyc
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   ├── __init__.py
│   │   └── __pycache__
│   │       ├── 0001_initial.cpython-37.pyc
│   │       └── __init__.cpython-37.pyc
│   ├── models.py
│   ├── templates
│   ├── tests.py
│   ├── urls.py
│   └── views.py
├── data.json
├── db.sqlite3
├── manage.py
├── mysite
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-37.pyc
│   │   ├── settings.cpython-37.pyc
│   │   ├── urls.cpython-37.pyc
│   │   └── wsgi.cpython-37.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── requirements.txt
└── templates
    ├── base.html
    └── blog
        ├── blog_detail.html
        ├── blog_list.html
        └── blogs_with_type.html
```

