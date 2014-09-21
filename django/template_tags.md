# Template tags

因為瀏覽器只看得懂 HTML，CSS 和 JavaScript ，其實我們不能在 HTML 檔案使用 Python 程式語法。

因為我們[Django template tags](https://docs.djangoproject.com/en/dev/ref/templates/builtins/) 讓你可以在 HTML 檔案裡使用類似 Python 的語法，動態存取 View 傳過來的變數，或是在顯示到瀏覽器之前幫你做簡單的資料判斷、轉換、計算等等。

---

我們在前面 Templates 的章節已經使用過顯示變數的語法，也在 Django ORM 學習到如何跟資料庫溝通。

這一章，我們會結合兩者，教你如何從資料庫取出資料，並透過基本內建的 template tags 語法，在網頁上顯示動態資訊。

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
記得先 import 你需要使用到的 Model，我們透過`Post.objects.all()`從資料庫取得全部的 post 並回傳至新的 template - `home.html`。

接下來，我們修改 **urls.py** ，將首頁( 正規表達式`^$` ) 指向 home View function：

```
# mysite/urls.py

urlpatterns = patterns('',
    ...
    url(r'^$', 'trips.views.home'),
)
```

## Template tags

為了瞭解如何應用 template tags，我們先不加入任何 HTML/CSS，直接在 templates 資料夾底下新增 `home.html` 輸入：
```
{{ post_list }}
```

打開瀏覽器進入你的首頁 [http://localhost:8000/](http://localhost:8000/)，你會發現變數是以 List 的方式顯示：

post_list only screenshot


### for loop

當寫 Python 時，想把 List 裡的每一個元素都取出，我們會使用 `for` 迴圈，Django template tags 也同樣提供了 [for](https://docs.djangoproject.com/en/dev/ref/templates/builtins/#for) 迴圈，修改`home.html`如下：

```
{% for post in post_list %}
    {{ post.created_at }}
{% endfor %}
```
- Django template tags 由`{%  %}` 區塊標籤組成
- 迴圈中，使用變數標籤`{{ }}`顯示每一個元素的值和其屬性
- 在迴圈結尾必須使用`{% endfor %}`

重新整理瀏覽器，你會看到網頁上顯示每一筆 post 的 created_at ( 建立時間 ) ：

many post screenshot


### if...else

另一個常用的 template tags 是 [if](https://docs.djangoproject.com/en/dev/ref/templates/builtins/#if) 判斷式，用法如下：

```
{% if %}
{% else %}
{% endif %}
```

需要範例

## Template filter

除了 template tags ，Django 在內建也提供許多好用的 [template filter](https://docs.djangoproject.com/en/dev/ref/templates/builtins/#built-in-filter-reference)，它會在變數顯示之前幫你做計算、設定預設值，置中等等，使用方法如下:

`{{`*`<variable_name>`*`|`*`<filter_name>`*`:`*`<filter_arguments>`*`}}`

- **< variable_name > ** -- 變數名稱
- **< filter_name > ** -- filter 名稱，例如`add`, `cut`等等
- **< filter_arguments > ** -- 要傳入 filter 的參數

在這裡，我們只練習其中一種很常用的 filter - [date](https://docs.djangoproject.com/en/dev/ref/templates/builtins/#date)，它可以將`datetime`型別的物件轉成我們指定的 Date Format ( 例如：`Y / m / d h:i:s` )，並輸出成字串 ( String ) ，如下：

```
{% for post in post_list %}
    {{ post.created_at|date:"Y / m / d h:i:s" }}
{% endfor %}
```
重新載入後，你會發現日期及時間的顯示方式改變了：

many post screenshot - format date

---
最後，結合我們這一章教過的 template tags 和 template filter，並加上 HTML/CSS，完整的`home.html`如下：
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
! 移除 read more 和 連到 post-detail 的url

! 加上 if no photo else...

! 移除 static

! 替換 url 'index'

最後的裝飾完成了，重新整理你的瀏覽器吧！

完整版 screenshot
