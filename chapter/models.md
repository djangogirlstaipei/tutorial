# Models

現今大多數的網站，都不再是靜態網頁，把要顯示的內容直接寫在 HTML 檔案裡，而是透過跟資料庫的溝通，將資料取出並呈現在網頁上。

這一章，你會學到如果利用 Django Models 定義資料庫的結構，並透過 Django 指令創建資料庫、資料表及欄位。

## 設定資料庫

使用 Django Models 的其中一個優點是資料庫轉換相當方便。不再需要為不同的資料庫寫不同的程式來操作，只要修改設定，就可以輕易地從 SQLite 轉換到 MySQL、PostgreSQL、或是 Oracle等等。

在這裡，為了開發方便，我們使用 Django 預設的資料庫 `SQLite`:
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
- ENGINE: 你要使用的資料庫引擎，例如：
 - `MySQL`: django.db.backends.mysql
 - `SQLite`: django.db.backends.sqlite3
 - `PostgreSQL`: django.db.backends.postgresql_psycopg2
- NAME: 你的資料庫名稱


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

待修改
Model field
https://docs.djangoproject.com/en/dev/ref/models/fields/

##新增 app

雖然之前透過 Django 命令列工具建立了 app，但實際上我們還需要修改設定檔裡的 [INSTALLED_APPS](https://docs.djangoproject.com/en/dev/ref/settings/#std:setting-INSTALLED_APPS)：

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
Django 在預設時已經幫你將幾個常用的 app 放入`INSTALLED_APPS`，例如使用者認證`auth`、後台管理`admin`等等。
請注意 app 之間有時候會有先後順序，我們將自訂的`trips`加在最後面。

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

如果是第一次同步資料庫，Django 會詢問你是否要創建 superuser，請回答`yes`，並輸入你的密碼，我們在後台管理 ( Django Admin ) 的章節會用到。
```
yes
```


