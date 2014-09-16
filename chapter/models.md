# Models

現今的網站，都不再只是僅單純展示網頁內容的靜態網頁。大多數網站，都會加上一些與使用者互動的功能，如留言版、討論區、投票...等。而這些使用者產出的資料，往往會儲存於資料庫中。

---

這一章，你會學到如何利用 Django Model 定義資料庫的結構 (Schema)，並透過 Django 指令創建資料庫、資料表及欄位。

---

## 使用 Django Model 的好處

雖然資料庫的語法有其標準，但是各家資料庫還是或多或少有差異。使用 Django Model 的來操作資料庫的優點之一，就是**資料庫轉換相當方便**。

在大部份情況下，不再需要為不同的資料庫，使用不同語法來撰寫程式。只要修改設定，就可以輕易地從 SQLite 轉換到 MySQL、PostgreSQL、或是 Oracle...等等。

## 設定資料庫

為了開發方便，我們使用 Django 預設的資料庫 `SQLite`:
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
- **ENGINE ** -- 你要使用的資料庫引擎，例如：
 - `MySQ`: django.db.backends.mysql
 - `SQLite`: django.db.backends.sqlite3
 - `PostgreSQL`: django.db.backends.postgresql_psycopg2
- **NAME ** -- 你的資料庫名稱


## Django Models


```
# trips/models.py

from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField(null=True, blank=True)
    photo = models.URLField()
    location = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now=True)
```

## 將新增的 Django app 加入設定檔

在前幾章，我們透過 Django 命令列工具建立了 **trips** 這個 app。但若要讓 Django 知道要管理哪些 app，還需再調整設定檔。打開 *mysite/settings.py*，找到 [INSTALLED_APPS](https://docs.djangoproject.com/en/dev/ref/settings/#std:setting-INSTALLED_APPS)，調整如下：

待修改
Model field
https://docs.djangoproject.com/en/dev/ref/models/fields/

##新增 app


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
Creating tables ...
Creating table trips_post
Installing custom SQL ...
Installing indexes ...
Installed 0 object(s) from 0 fixture(s)
```

[migrate](https://docs.djangoproject.com/en/dev/ref/django-admin/#django-admin-migrate) 指令會根據`INSTALLED_APPS`的設定，按照 app 順序建立或更新資料表，將你在 models.py 裡的更新跟資料庫同步。

如果是第一次同步資料庫，Django 會詢問你是否要創建 superuser，請回答`yes`，並設定管理者密碼。在後台管理 ( Django Admin ) 的章節會使用到。
```
yes
```


