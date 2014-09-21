# Models

現今的網站，都不再只是僅單純展示網頁內容的靜態網頁。大多數網站，都會加上一些與使用者互動的功能，如留言版、討論區、投票...等。而這些使用者產出的資料，往往會儲存於資料庫中。

---

這一章，你會學到如何利用 Django Model 定義資料庫的結構 ( Schema )，並透過 Django 指令創建資料庫、資料表及欄位。

---

## 使用 Django Model 的好處

雖然資料庫的語法有其標準，但是各家資料庫還是或多或少有差異。使用 Django Model 的來操作資料庫的優點之一，就是**資料庫轉換相當方便**。

在大部份情況下，不再需要為不同的資料庫，使用不同語法來撰寫程式。只要修改設定，就可以輕易地從 SQLite 轉換到 MySQL、PostgreSQL、或是 Oracle...等等。

## 設定資料庫

為了開發方便，我們使用 Django 預設的資料庫 `SQLite`。打開 `mysite/settings.py`，看看 `DATABASES` 的設定。它應該長得像下面這樣：

```
# mysite/settings.py

...

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

```

在這裡我們設定了資料庫連線的預設值：
- **ENGINE** -- 你要使用的資料庫引擎，例如：
 - `MySQL`: django.db.backends.mysql
 - `SQLite 3`: django.db.backends.sqlite3
 - `PostgreSQL`: django.db.backends.postgresql_psycopg2
- **NAME** -- 你的資料庫名稱

如果你使用 MySQL 或 PostgreSQL 等等資料庫的話，可能還要設定它的位置、名稱、使用者等等。不過我們這裡使用的 SQLite 3 不需要這些性質，所以可以省略。


## Django Models

我們在 *trips/models.py* 宣告一個`Post`物件，並定義裡面的屬性，而 Django 會依據這個建立資料表，以及資料表裡的欄位設定：

```
# trips/models.py

from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField(null=True, blank=True)
    photo = models.URLField(null=True, blank=True)
    location = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title
```

透過 [Model fields](https://docs.djangoproject.com/en/1.7/ref/models/fields/) ，我們可以定義不同型態的欄位:
- [CharField](https://docs.djangoproject.com/en/1.7/ref/models/fields/#charfield) -- 字串欄位，適合像 title、location 這種有長度限制的字串。
    - [max_length](https://docs.djangoproject.com/en/1.7/ref/models/fields/#django.db.models.CharField.max_length) -- 字串的最大長度，例如這裡我們設定不可以超過 100 個字元。
- [TextField](https://docs.djangoproject.com/en/1.7/ref/models/fields/#textfield) -- 適合放大量文字的欄位
    - [null=True](https://docs.djangoproject.com/en/1.7/ref/models/fields/#null) -- 當沒有輸入任何值時，會自動儲存 NULL ( 空值 ) 進資料庫
    - [blank=True](https://docs.djangoproject.com/en/1.7/ref/models/fields/#blank) -- 這個選項是為了在表單驗證時使用，`Flase`表示為必填欄位，`True`則表示非必填
- [URLField]() --　專為 URL 設計的欄位，預設`max_length=200`
- [DateTimeField](https://docs.djangoproject.com/en/1.7/ref/models/fields/#datetimefield) -- 同時儲存日期與時間的欄位，使用時會轉成 Python`datetime`型別
    - [auto_add_now=True](https://docs.djangoproject.com/en/1.7/ref/models/fields/#django.db.models.DateField.auto_now_add) -- 新建立一個物件時，會自動儲存此刻的日期及時間，因此只會儲存一次。如果想在每一次修改時，都自動更新此刻的日期時間，則使用 [auto_now=True](https://docs.djangoproject.com/en/1.7/ref/models/fields/#django.db.models.DateField.auto_now)

Django 預設會用`<Post: Post object>`來顯示一個 Post 物件，可是這種顯示方式會讓人看不懂到底是哪一個 Post。
我們利用 [def `__str__`](https://docs.djangoproject.com/en/1.7/ref/models/instances/#str) 重新定義成顯示 *Post.title*。



## 將新增的 Django app 加入設定檔

在前幾章，我們透過 Django 命令列工具建立了 **trips** 這個 app。但若要讓 Django 知道要管理哪些 app，還需再調整設定檔。

##新增 app

打開 *mysite/settings.py*，找到 [INSTALLED_APPS](https://docs.djangoproject.com/en/dev/ref/settings/#std:setting-INSTALLED_APPS)，調整如下：

```
# mysite/settings.py

...

# Application definition

INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'trips',
)
```
請注意 app 之間有時候需要特定先後順序。在此，我們將自訂的`trips`加在最後面。

---
**預設安裝的 Django app**

Django 已將常用的 app 設定為 `INSTALLED_APPS` 。例如，`auth` (使用者認證)、`admin` (管理後台) ...等等，我們可依需求自行增減。

---

## 同步資料庫

```
(VENV) ~/djangogirls/mysite$ python manage.py migrate
Operations to perform:
  Synchronize unmigrated apps: trips
  Apply all migrations: contenttypes, sessions, admin, auth
Synchronizing apps without migrations:
  Creating tables...
    Creating table trips_post
    Creating table trips_comment
  Installing custom SQL...
  Installing indexes...
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying sessions.0001_initial... OK
```

[migrate](https://docs.djangoproject.com/en/dev/ref/django-admin/#django-admin-migrate) 指令會根據`INSTALLED_APPS`的設定，按照 app 順序建立或更新資料表，將你在 models.py 裡的更新跟資料庫同步。

