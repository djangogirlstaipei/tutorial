# 使用 Django ORM 操作資料庫

在上一章，我們學到如何使用 Django Model 抽象地表達資料庫結構。 在完成 Model 的定義後，我們即可使用 Django 提供的 QuerySet API，來與資料庫互動。

---

本章你會學到：如何使用 Django QuerySet API 與資料庫互動 (CRUD)。

**CRUD** 指的是，**C**reate (新增)、**R**ead (讀取)、**U**pdate (修改)、**D**elete (刪除) 等常見的資料庫操作。

---

## 使用 Django Shell

與先前不同的是，在這裡我們不使用 Python Shell，而是 **Django Shell**。若要在 Python Shell 使用 Django 的功能或模組，還需另外載入設定。

---

**Django Shell**

與 Python Shell 類似的互動式命令列。會預先載入 Django 的相關設定，所以可以在此執行 Django 的 API。

---

使用 [shell](https://docs.djangoproject.com/en/1.8/ref/django-admin/#django-admin-shell) 指令，進入 Django Shell：

```
python manage.py shell
```

這個 shell 和我們之前輸入 `python` 執行的 shell 長得一樣，只是它會預先為我們設定 Django 需要的環境，方便我們執行 Django 相關的程式。

## QuerySet API

### Create

首先，讓我們來試著新增幾筆資料：

```
>>> from trips.models import Post

>>> Post.objects.create(title='My First Trip', content='肚子好餓，吃什麼好呢?',  location='台北火車站')
<Post: Post object>

>>> Post.objects.create(title='My Second Trip', content='去散散步吧',  location='台北火車站')
<Post: Post object>

>>> Post.objects.create(title='Django 大冒險', content='從靜態到動態',  location='台北市大安區復興南路一段293號')
<Post: Post object>
```

### Read

若想顯示所有的 Post ，可以使用
[all()](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#django.db.models.query.QuerySet.all)：

```
>>> Post.objects.all()
[<Post: Post object>, <Post: Post object>, <Post: Post object>]
```

---

Django 通常以 `<Post: Post object>` 來表示 Post 物件，但此種顯示不易辨別。我們可以透過 [`def __str__`](https://docs.djangoproject.com/en/1.8/ref/models/instances/#str)  更改 Post 的表示方式。

修改 `trips/models.py`：

```python
# trips/models.py

from django.db import models


class Post(models.Model):
    ...
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

退出 Django Shell 後再重新進入，Post 已經被重新定義成顯示標題，如 `<Post: Your_Post_Title>`。

```
>>> from trips.models import Post
>>> Post.objects.all()
[<Post: My First Trip>, <Post: My Second Trip>, <Post: Django 大冒險>]
```

---

只想顯示部分資料時，則可以使用 `get` 或 `filter`：

```
>>> Post.objects.get(pk=1)
<Post: My First Trip>

>>> Post.objects.filter(pk__gt=1)
[<Post: My Second Trip>, <Post: Django 大冒險>]
```

- [**get**](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#get)：返回符合條件的**唯一一筆資料**。（*注意：*如果找不到符合條件的資料、或是有多筆資料符合條件，都會產生 exception）

- [**filter**](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#filter)：返回符合條件的陣列。如果找不到任何資料則會返回空陣列。


### Update

當想修改資料時，可以使用 [update](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#django.db.models.query.QuerySet.update) 更新一筆或多筆資料：

首先，先取得欲更新的 Post。這裡使用 `pk < 3` 的條件篩選

```
>>> posts = Post.objects.filter(pk__lt=3)
```

共有 2 個 Post 符合上面的條件

```
>>> posts
[<Post: My First Trip>, <Post: My Second Trip>]
```

我們將 location 的值印出

```
>>> posts[0].location
'台北火車站'

>>> posts[1].location
'台北火車站'
>>>
```

印出後發現， 兩個 Post 的 location 都是台北火車站。現在我們試試用 `update` 指令，把它改成 **'捷運大安站'**

```
>>> posts.update(location='捷運大安站')
2
```

回傳的數字 `2` 指的是已被更新的資料筆數。我們可以驗證一下 `location` 是否皆已被正確更新

```
>>> posts[0].location
'捷運大安站'

>>> posts[1].location
'捷運大安站'
```

### Delete

我們也可以使用 [delete](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#django.db.models.query.QuerySet.delete) 刪除資料：

我們試著使用 `delete`，將剛剛的那兩筆 Post 刪除。

```
>>> posts.delete()
```

確認一下，資料是否刪除

```
>>> Post.objects.all()
[<Post: Django 大冒險>]
```
