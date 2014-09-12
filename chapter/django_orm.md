# Django ORM

[ORM (Object-relational mapper)](https://docs.djangoproject.com/en/dev/topics/db/queries/) 是 Django 非常吸引人的一個特點，當你利用 Models 編寫好你的物件結構後，Django 會自動提供與資料庫溝通的 API，讓你可以透過它新增`Create`、讀取`Read`、修改`Update`、及刪除`Delete` 資料。( 也就是所謂的`CRUD` )

## QuerySet
[Django Shell](https://docs.djangoproject.com/en/dev/ref/django-admin/#django-admin-shell) 是在 Django project 環境下的`互動式命令列`，除了跟 Python 互動命令列一樣可以使用 Python 相關的函式和資源以外，你還可以透過 [QuerySet](https://docs.djangoproject.com/en/dev/ref/models/querysets/) API 操作資料庫。
```
python manage.py shell
```
### Create

首先，讓我們來試著新增幾筆資料：

```
>>> from trips.models import Post
>>>
```

### Read

如果想要看 Post 裡面所有的資料只要輸入：

```
>>> Post.objects.all()
```

而只想顯示部分資料時有以下兩種方式：
```
>>> Post.objects.get()

>>> Post.objects.filter()
```
- `get()`:
- `filter()`:

### Update



```
>>>
```

### Delete

```
>>>
```

