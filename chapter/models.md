# Models

現今大多數的網站，都不再是靜態網頁，把要顯示的內容直接寫在 HTML 檔案裡，而是透過跟資料庫的溝通，將資料取出並呈現在網頁上。

這一章，你會學到如果利用 Django Models 定義資料庫的結構，並透過 Django 指令創建資料庫、資料表及欄位。

## 設定資料庫

使用 Django Models 的其中一個優點是資料庫轉換相當方便。不再需要為不同的資料庫寫不同的程式來操作資料庫，只要修改設定，就可以輕易地從 sqlite 轉換到 MySQL、PostgresSQL、或是 Oracle等等。

在這裡，為了開發方便，我們使用 Django 預設的資料庫 `sqlite`:
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

    def __str__(self):
        return self.title
```


## 同步資料庫
```
python manage.py syncdb
```

創建 superuser
```
yes
```
