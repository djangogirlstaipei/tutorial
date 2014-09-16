# Template tags

因為瀏覽器只看得懂 HTML，CSS 和 JavaScript ，其實我們不能在 HTML 檔案使用 Python 程式語法。

[Django template tags](https://docs.djangoproject.com/en/dev/ref/templates/builtins/) 讓你可以在 HTML 檔案裡使用類似 Python 的語法，動態存取 View 傳過來的變數，或是在顯示到瀏覽器之前幫你做簡單的資料判斷、轉換、計算等等。

---

我們在前面 Templates 的章節已經使用過顯示變數的語法，也在 Django ORM 學習到如何跟資料庫溝通。

這一章，我們會結合兩者，教你如何從資料庫取出資料，並透過基本的 template tags 語法，在網頁上顯示動態資訊。

---

現在讓我們來建立一個新的 View function - `home`：
```
# trips\views.py

from django.shortcuts import render
from trips.models import Post


def home(request):
    # get all the posts
    post_list = Post.objects.all()

    return render(request,
                  'home.html',
                  {'post_list': post_list})
```
記得先 import 你需要使用到的 Model，我們透過`Post.objects.all()`從資料庫取得全部的 post 並回傳至新的 template - `home.html`：

```
{% load static %}
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>My Trips</title>

        <link rel=stylesheet href="{% static 'css/style.css' %}">
    </head>
    <body>
        <div class="container">
            <h1 class="title site-title text-center">
                <a href="{% url 'index' %}">我的旅行日記</a>
            </h1>
        </div>
        <div class="container">
            {% for post in post_list %}
                <div class="post-wrapper">
                    <div class="post">
                        <div class="post-heading">
                            <h2 class="title"><a href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
                            <div class="date">{{ post.created_at|date:"Y / m / d h:i:s" }}</div>
                        </div>
                        <div class="photo">
                            <img src="{{ post.photo }}" alt="">
                        </div>
                        <div class="post-content read-more-block">
                            {{ post.content }}
                        </div>
                        <div class="post-footer">
                            <a class="read-more" href="{% url 'post-detail' post.id %}">Read More</a>
                        </div>
                    </div>
                </div>
            {% endfor %}
        </div>
    </body>
</html>
```

`{{ posts }}`

posts screenshot

```
HTML 用 for
```

## Template tags
### for loop
```
{% for post in posts %}
    {{ post }}
{% endif %}
```
### if...else

```
{% if %}
{% else %}
{% endif %}
```

想範例!!!


## Template filter
###date
