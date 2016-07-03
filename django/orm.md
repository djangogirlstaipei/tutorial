# 使用 Django ORM 操作資料庫

在前一章，我們利用 **Django Admin** 新增、修改及刪除 Post 。而實際在寫程式時，我們會使用 Django 提供的 QuerySet API，來達成類似的資料庫操作。

---

本章你會學到：如何使用 Django QuerySet API 與資料庫互動 (CRUD)。

**CRUD** 指的是，**C**reate (新增)、**R**ead (讀取)、**U**pdate (修改)、**D**elete (刪除) 等常見的資料庫操作。

---

## 使用 Django Shell

與先前不同的是，在這裡我們不使用 Python Shell，而是 **Django Shell**。


使用 [shell](https://docs.djangoproject.com/en/1.8/ref/django-admin/#django-admin-shell) 指令，進入 Django Shell：

```
(djangogirls_venv) ~/djangogirls/mysite$ python manage.py shell
```

這個 shell 和我們之前輸入 `python` 執行的 shell 長得一樣，只是它會預先為我們設定 Django 需要的環境，方便我們執行 Django 相關的程式。

---

**IPython**

[IPython](http://ipython.org/) 是強化版的 Python 互動式命令列介面，它比預設的命令列介面多了許多進階功能，例如：

- 按 tab 鍵可以補齊未輸入完的指令、檔案及資料夾名稱。
- 按 ↑ 鍵和 ↓ 鍵可以瀏覽輸入過的程式碼，便於微調先前的程式碼（修改參數等等）。
- 在套件、模組或函數名稱後加上 `?` 可查看與之相關的資訊。
- `history` 指令可查看所有輸入過的指令。
- 可以使用 shell 指令，如：`ls`、`cd`。

我們一樣可以用 pip 來安裝這個強大的套件：

```
(djangogirls_venv) ~/djangogirls/mysite$ pip install ipython
```

---

## QuerySet API

### Create

首先，讓我們來試著新增幾筆資料：

```
>>> from trips.models import Post

>>> Post.objects.create(title='My First Trip', content='肚子好餓，吃什麼好呢?',  location='台北火車站')
<Post: My First Trip>

>>> Post.objects.create(title='My Second Trip', content='去散散步吧',  location='台北火車站')
<Post: My Second Trip>

>>> Post.objects.create(title='Django 大冒險', content='從靜態到動態',  location='台北市大安區復興南路一段293號')
<Post: Django 大冒險>
```

### Read

若想顯示所有的 Post ，可以使用
[all()](https://docs.djangoproject.com/en/1.8/ref/models/querysets/#django.db.models.query.QuerySet.all)：

```
>>> from trips.models import Post
>>> Post.objects.all()
[<Post: My First Trip>, <Post: My Second Trip>, <Post: Django 大冒險>]
```

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
