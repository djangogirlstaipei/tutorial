# Dynamic URL


除了在首頁顯示文章的概要外，通常也希望每篇文章能有自己的獨立網址。例如，我們可能會希望 `http://127.0.0.1/post/5` 能夠是 **id=5 **那篇文章的網址。

---

在這個章節，我們會學到如何設定動態網址的 URLConf。讓每篇旅遊日記，擁有獨一無二的網址。

---

## 新增單頁的 View

首先我們要先建立旅遊日記獨立頁面的 View。在 *trips/views.py* 中新增 **post_detail** 這個 View 如下：

```python
# trips/views.py

def post_detail(request, id):
    posts = Post.objects.get(id=id)
    return render(request, 'post.html', {'post': post})
```

我們以訪客瀏覽 `http://127.0.0.1/post/5` 的例子，來解釋以上程式：

- ** 目前瀏覽文章的 id 會傳入 View 中：** 當訪客瀏覽 `http://127.0.0.1/post/5` 時，傳入 View 的 id 會是 5。

    - URL 與 id 的對應，會在稍後設定。這裡只需知道 View 中傳入的是當前瀏覽文章 id 即可。

- ** 取得傳入 id 的那篇 Post 資料：** 當傳入的 id=5，代表訪客想看到 id=5 那篇文章。我們可以利用之前學過的 ORM 語法 `get`， 取得該 Post：

```python
post = Post.objects.get(id=id) # 此時 id = 5
```
- ** 回傳 HttpResponse：** 將取得的 post *( id=5 )* 傳入 Template *( post.html )*，並呈現 Render 後的結果。

## 設定獨立網址

## 設定動態網址的對應

### 解釋 regex

所以我們只要知道 `id`，就可以取出對應的 `Post` 物件。可是 `id` 要從哪裡來？

例如我們可能會希望 `http://127.0.0.1/post/5` ，會是

Django 可以在讀取 URL 時，為我們抓出 URL 的其中某個部分，並傳入 view 函式給我們使用。所以我們可以在 `mysite/urls.py` 中，加入以下的內容：


```python
# mysite/urls.py

urlpatterns = patterns('',
    ...
    url(r'^post/(?P<id>\d+)/$', 'trip.views.post_detail',
        name='trip_detail'),
)
```

這個 URL 似乎有些特別。我們在第一個參數中不是輸入一個普通的字串，而是包含了一個奇怪的東西：

```
(?P<id>\d+)
```

我們前面提過，Django 的 URL 是一個 *regular expression*。Regular expression 除了可以表示字串之外，還可以用來表示不確定的內容。我們一步一步解釋上面這串的意思：

1. `\d` 代表一個阿拉伯數字。

2. `+` 代表「一個以上」。

    所以 `\d+` 代表一個以上的阿拉伯數字，例如「0」、「99」、「12345」。可是像「8a」就不符合，因為「a」不是數字。

3. `(?P<id>)` 代表「把這一串東西抓出來，命名為 id。

    所以 `(?P<id>\d+)` 代表：抓出一個以上阿拉伯數字，並把抓出來的東西取名為 id。

所以 `r'^post/(?P<id>\d+)/$'` 會達成以下的效果：

URL       | 符合結果
----------|------------------------
http://127.0.0.1/<strong>posts/</strong>    | 不符合，因為前面不是 **post/** 開頭。
http://127.0.0.1/<strong>post/</strong>     | 不符合，因為後面抓不到數字。
http://127.0.0.1/<strong>post/1/</strong>   | 符合，抓到的 id 是 1。
http://127.0.0.1/<strong>post/1234/</strong>| 符合，抓到的 id 是 1234。
http://127.0.0.1/<strong>post/12ab/</strong>| 不符合，因為後面有不是數字的東西。

Django 抓出 `id` 後，會把這個值傳入 view function。所以我們可以把 function 寫成這樣：

```python
# trip/views.py

def post_detail(request, id):
    posts = Post.objects.get(id=id)
    return render(request, 'post.html', {'post': post})
```

Django 負責把 id 從 URL 抓出來，傳入 `post_detail`。我們用這個值取出合適的 post，再用 `render` 把它與 template 結合。

舉例而言：當 URL 是 http://127.0.0.1/**posts/1/**，`post_detail` 的 id 會是 1。所以我們會把 id 是 1 的 post 抓出來，傳入 `render`。

接著我們要為 `render` 寫出 `post.html`：

```html
<!-- post.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>My Trips</title>
    <link href='http://fonts.googleapis.com/css?family=Lemon' rel='stylesheet' type='text/css'>
</head>
<body>
    <div class="header">
        <h1 class="site-title text-center">
            <a href="/">A Django Girl's Advanture</a>
        </h1>
    </div>
    <div class="container post post-detail">
        <div class="post-heading">
            <h1 class="title"><a href="{% url 'trip_detail' id=post.id %}">{{ post.title }}</a>
            </h1>
            <div class="date">{{ post.created_at|date:'Y / m / d' }}</div>
        </div>
        <div class="location">
            <span class="icon-map-marker"></span>
            <span id="location">{{ post.location }}</span>
        </div>
        <div id="map-canvas" class="map"></div>
        <div class="post-content">
            {{ post.content }}
        </div>
        <hr class="fancy-line">
        <img class="photo" src="{{ post.photo }}" alt="">
    </div>
    <script src="https://maps.googleapis.com/maps/api/js?v=3.exp"></script>
</body>
</html>
```

和前面類似，我們用 `{{ post }}` 取出 `render` 裡面的 post 物件，將它顯示在 HTML 中。

現在我們的 post 可以被顯示了。但是我們還需要在首頁加上 `<a>` 連結，才有辦法進去這些頁面。打開 `home.html`，找到下面的內容：

```html
<!-- home.html -->
<h2 class="title"><a href="#">{{ post.title }}</a></h2>
```

將它改成

```html
<!-- home.html -->
<h2 class="title"><a href="{% url 'trip_detail' id=post.id %}">{{ post.title }}</a></h2>
```

再找到

```html
<a class="read-more" href="#">Read More <span class="icon-forward"></span></a>
```

改成

```html
<a class="read-more" href="{% url 'trip_detail' id=post.id %}">Read More <span class="icon-forward"></span></a>
```

現在再去 <http://127.0.0.1:8000/> 看看。現在只要你點擊各個方塊的標題或 **Read more**，就會顯示那篇 post 的內容！
