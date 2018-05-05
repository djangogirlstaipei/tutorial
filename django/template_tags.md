# Template tags

在先前的 Templates 章節中，我們已經學會基礎的 Django Template 用法 (在 Template 裡呈現變數內容)。但為了產生完整的網頁，我們會需要能在 Template 裡執行一些簡單的 Python 語法，例如：

- **邏輯判斷** (if-else)  -- 若使用者己經登入，則顯示使用者的暱稱；若未登入，則顯示登入按鈕
- **重覆 HTML 片段** (for loop) -- 列出所有好友的帳號和顯示圖片
- **格式化 Template 中的變數** -- 日期的格式化等等

[Django template tags](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/) 讓你可以在 HTML 檔案裡使用類似 Python 的語法，動態存取從 view function 傳過來的變數，或是在顯示到瀏覽器之前幫你做簡單的資料判斷、轉換、計算等等。

---

在這一章，我們將使用 Django ORM 存取資料庫，撈出旅遊日記全部的 posts 傳入 template，並使用 Django 的 template tags 與 filters，一步步產生旅遊日記的首頁。

---

## 建立旅遊日記的首頁

### 確認首頁需求

在開始動工之前，我們先確認需求。

旅遊日記的首頁應該會有：

1. 標題
2. 照片
3. 發佈日期
4. 部份的遊記內文


### 建立首頁的 View

首先我們建立一個新的 view function - `home()`：

```python
# trips/views.py
from django.shortcuts import render

from .models import Post

# ...

def home(request):
    post_list = Post.objects.all()
    return render(request,
                  'home.html',
                  {
                      'post_list': post_list,
                  }
    )

```

- **匯入所需的 model** -- 記得 import 需要用到的 Model `Post`
- **取得所有 posts** -- 透過 `Post.objects.all()` 從資料庫取得全部的 posts，並傳入 `home.html` 這個 template。

### 設定首頁的 URL

接下來，我們修改 **urls.py** ，將首頁指向 **home()** 這個 view function：

```python
# mysite/urls.py
from trips.views import hello_world, home

urlpatterns = [
    ...
    path('', home),
]
```

## Template Tags

### 建立首頁的 Template 並印出 `post_list`

首先，在 templates 資料夾底下新增 `home.html`：

```html
<!-- home.html -->

{{ post_list }}
```

打開瀏覽器進入首頁 [http://127.0.0.1:8000/](http://127.0.0.1:8000/)，可以看到 *post_list* 已呈現至網頁上了。

![Plain post_list in Template](./../images/plain-post-list-in-template.png)


### 顯示 Post 中的資料

仔細觀察印出的 `post_list`，會發現是以 list 的形式顯示。但我們希望的則是：**存取每個 Post 中的資料，並印出來**。

為了達成這個功能，我們會用到 `for` 這個 template tag。

#### `for` 迴圈

在寫 Python 時，若想存取 list 裡的每一個元素，我們會使用 `for` 迴圈。而在 Django Template 中，也提供了類似的 template tags -- [{% raw %}{% for %}{% endraw %}](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/#for)。

---

**{% raw %}{% for %}{% endraw %}**

在 template 中使用類似 Python 的 for 迴圈，使用方法如下：

```html
{% for <element> in <list> %}
    ...
{% endfor %}
```

---

瞭解了 **for** 的用法後，我們試著印出首頁所需的資訊。修改 `home.html` 如下：

```html
<!-- home.html -->

{% for post in post_list %}
    <div>
    {{ post.title }}
    {{ post.created_at }}
    {{ post.photo }}
    {{ post.content }}
    </div>
{% endfor %}
```
- 開始標籤為 `{% raw %}{% for %}{% endraw %}` 開始；結束標籤為 `{% raw %}{% endfor %}{% endraw %}`
- `post_list` 中有 3 個元素，所以 for 區塊中的內容會執行 3 次
- 迴圈中，使用標籤 `{% raw %}{{ var }}{% endraw %}`，反覆印出每個 post 中的標題、建立時間、照片網址和文章內容

重新整理瀏覽器，網頁上會有首頁所需的 post 資訊：

![](./../images/plain-post-content-in-template.png)

### 顯示照片

現在網頁已經有照片網址，我們稍微修改 template ，讓照片以圖片方式呈現。

把 `home.html` 的下面這一行：

```html
{{ post.photo }}
```

換成下面這樣：

```html
<div class="thumbnail">
    <img src="{{ post.photo }}" alt="">
</div>
```

### 處理沒有照片的遊記

#### `if`…`else`

另一個常用的 template tags 是 [{% raw %}{% if %}{% endraw %}](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/#if) 判斷式，用法如下：

```html
{% if post.photo %}
    <div class="thumbnail">
        <img src="{{ post.photo }}" alt="">
    </div>
{% else %}
    <div class="thumbnail thumbnail-default"></div>
{% endif %}
```

- 符合條件所想要顯示的 HTML 放在 `{% raw %}{% if `*`<condition>`*` %}{% endraw %}` 區塊裡
- 不符合的則放在 `{% raw %}{% else %}{% endraw %}` 區塊裡面
- 最後跟 **for** 一樣，要加上 `{% raw %}{% endif %}{% endraw %}` 作為判斷式結尾。

在這裡，我們判斷如果 `post.photo` 有值就顯示照片，否則就多加上一個 CSS class `thumbnail-default` 另外處理。


## Template Filter

除了 template tags ，Django 也內建也許多好用的 [template filters](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/#built-in-filter-reference)。它能在變數顯示之前幫你做計算、設定預設值，置中、或是截斷過長的內容等等。使用方法如下:

`{{<variable_name>|<filter_name>:<filter_arguments>}}`

- `<variable_name>` -- 變數名稱
- `<filter_name>` -- filter 名稱，例如 `add`、`cut` 等等
- `<filter_arguments>` -- 要傳入 filter 的參數

#### 變更時間的顯示格式

在這裡，我們只練習一種很常用的 filter [date](https://docs.djangoproject.com/en/2.0/ref/templates/builtins/#date)。它可以將 `datetime` 型別的物件，以指定的時間格式輸出。

我們試著將 `created_at` 時間顯示成**年 / 月 / 日**：

```html
{{ post.created_at|date:"Y / m / d" }}
```
---

### 完整的 HTML 與 CSS

接著，補上完整的 HTML 標籤，並加上 CSS 樣式後，旅遊日記首頁就完成了。

最終版 `home.html` 程式碼如下：

```html
<!-- home.html -->

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>A Django Girl's Adventure</title>
    <link href="//fonts.googleapis.com/css?family=Lemon" rel="stylesheet" type="text/css">
    <link href="//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css" rel="stylesheet" type="text/css">
    <link href="//djangogirlstaipei.github.io/assets/css/style.css" rel="stylesheet" type="text/css">
</head>
<body>
    <div class="header">
        <h1 class="site-title text-center">
            <a href="/">A Django Girl’s Adventure</a>
        </h1>
    </div>
    <div class="container">
        {% for post in post_list %}
        <div class="post-wrapper">
            <div class="post">
                <div class="post-heading">
                    <h2 class="title">
                        <a href="#">{{ post.title }}</a>
                    </h2>
                    <div class="date">{{ post.created_at|date:"Y / m / d" }}</div>
                </div>
                {% if post.photo %}
                <div class="thumbnail">
                    <img src="{{ post.photo }}" alt="">
                </div>
                {% else %}
                <div class="thumbnail thumbnail-default"></div>
                {% endif %}
                <div class="post-content read-more-block">
                    {{ post.content }}
                </div>
                <div class="post-footer">
                    <a class="read-more" href="#">
                        Read More <i class="fa fa-arrow-right"></i>
                    </a>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</body>
</html>
```

打開 <http://127.0.0.1:8000/> 看看你的成果吧！

![Django Girls Trips - Home page](./../images/djangogirlstrips-home.png)

## 小結
最後，我們複習一下本章學到的 **Template Tag** 與 **Template Filter**：

### Template Tags

| 語法 | 說明 |
| --- | --- |
| {% raw %}{% **for** ... **in** ... %}{% endraw %}...{% raw %}{% **endfor** %}{% endraw %} | 類似 Python 的 for 迴圈，反覆執行 for 區塊中的內容 |
| {% raw %}{% **if** %}{% endraw %} ... {% raw %}{% **else** %}{% endraw %} ... {% raw %}{% **endif** %}{% endraw %} | 在 Template Tags 中進行 if／else 的邏輯判斷 |


### Template Filters

<table>
<tr><th>語法</th><th>說明</th></tr>
<tr><td>{% raw %}{{{% endraw %} value<strong>|date:</strong><i>&lt;date_format&gt;</i> {% raw %}}}{% endraw %} </td><td>可以將`datetime`型別的物件，以指定的時間格式 Date Format 輸出</td></tr>
</table>



