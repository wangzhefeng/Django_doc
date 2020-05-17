
## Django filer 筛选条件


## filter 筛选条件

* 等于
    - 直接筛选
* 大于
    - `__gt`
* 大于等于：
    - `__gte`
* 小于
    - `__lt`
* 小于等于
    - `__lte`
* 包含
    - `__contains`
    - `__icontains` (加 `i` 忽略大小写)
* 开头是
    - `__startswith`
* 结尾是
    - `__endswith`
* 其中之一
    - `__in`
* 范围
    - `__range`

示例:

```python
def blog_detail(request, blog_pk):
    context = {}
    blog = get_object_or_404(Blog, pk = blog_pk)
    context["previous_blog"] = Blog.objects.filter(created_time__gt = blog.created_time).last()
    context["next_blog"] = Blog.objects.filter(created_time__lt = blog.created_time).first()
    context["blog"] = blog
    
    return render_to_response("blog/blog_detail.html", context)
```


## exclude 排除条件

`Blog.objects/exclude(created_time__gt = blog.created_time)`

## 条件中的双下划线

* 字段查询类型
* 外键拓展（以博客分类为例）
* 日期拓展（以月份分类为例）
* 支持链式查询：可以一直链接下去




