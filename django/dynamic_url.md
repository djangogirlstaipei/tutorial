# Dynamic URL

現在我們要來為我們的每次旅行建立一個頁面。我們前面學到，可以用 `get` 來抓出一筆資料：

```python
post = Post.objects.get(id=id)
```

所以我們只要知道 `id`，就可以取出對應的 `Post` 物件。可是 `id` 要從哪裡來？

Django 可以在讀取 URL 時，為我們抓出 URL 的其中某個部分，並傳入 view 函式給我們使用。所以我們可以在 `mysite/urls.py` 中，加入以下的內容：


```python
# mysite/urls.py

urlpatterns = patterns('',
    ...
    url(r'^post/(?P<id>\d+)/$', 'trips.views.post_detail',
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
http://127.0.0.1/**posts/**    | 不符合，因為前面不是 **post/** 開頭。
http://127.0.0.1/**post/**     | 不符合，因為後面抓不到數字。
http://127.0.0.1/**post/1/**   | 符合，抓到的 id 是 1。
http://127.0.0.1/**post/1234/**| 符合，抓到的 id 是 1234。
http://127.0.0.1/**post/12ab/**| 不符合，因為後面有不是數字的東西。

Django 抓出 `id` 後，會把這個值傳入 view function。所以我們可以把 function 寫成這樣：

```python
# trips/views.py

def post_detail(request, id):
    posts = Post.objects.get(id=id)
    return render(request, 'post.html', {'post': post})
```

Django 負責把 id 從 URL 抓出來，傳入 `post_detail`。我們用這個值取出合適的 post，再用 `render` 把它與 template 結合。

舉例而言：當 URL 是 http://127.0.0.1/**posts/1/**，`post_detail` 的 id 會是 1。所以我們會把 id 是 1 的 post 抓出來，傳入 `render`。

接著我們要為 `render` 寫出 `post.html`：

```html
<!-- post.html -->
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
