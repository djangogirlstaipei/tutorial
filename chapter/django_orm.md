# Django ORM

[ORM (Object-relational mapper)](https://docs.djangoproject.com/en/dev/topics/db/queries/) 是 Django 非常吸引人的一個特點，當你利用 Models 編寫好你的物件結構後，Django 會自動提供與資料庫溝通的 API，讓你可以透過它新增 ( Create ) 、讀取 ( Read ) 、修改 ( Update ) 、及刪除 ( Delete ) 資料。( 也就是所謂的 **CRUD** )

## QuerySet

讓我們先用 [shell](https://docs.djangoproject.com/en/dev/ref/django-admin/#django-admin-shell) 指令開啟 Django Shell：

```
python manage.py shell
```

**Django Shell** 是在 Django project 環境下的互動式命令列，除了跟 Python 互動命令列一樣可以使用 Python 相關的函式和資源以外，你還可以透過 [QuerySet](https://docs.djangoproject.com/en/dev/ref/models/querysets/) API 操作資料庫。
### Create

首先，讓我們來試著新增幾筆資料：

```
>>> from trips.models import Post
>>>
```

### Read

如果想要看 Post 裡面所有的資料只要使用
[all()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#django.db.models.query.QuerySet.all)：

```
>>> Post.objects.all()

所有資料 example

```



而只想顯示部分資料時有以下兩種方式：
```
>>> Post.objects.get()

單筆資料 example

>>> Post.objects.filter()

List example

```
- [get()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#get)：返回符合條件的唯一一筆資料。 ( 注意：如果找不到或找到多筆符合條件的資料，都會產生 exception )
- [filter()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#filter)：返回符合條件的陣列，如果找不到任何資料則會返回空陣列。

### Update

當你想修改資料時，可以使用 [update()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#django.db.models.query.QuerySet.update) 更新一筆或多筆資料：

```
>>>
```

### Delete

同樣的，使用 [delete()](https://docs.djangoproject.com/en/dev/ref/models/querysets/#django.db.models.query.QuerySet.delete) 可以刪除一筆或多筆資料：

```
>>>
```

