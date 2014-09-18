# Deploy

目前為止，我們所有的工作都是在本機端 ( localhost ) 完成，你可以在自己電腦上的瀏覽器看到成果。但是，一個網站必須放在穩定的 Server 上部屬 ( Deploy ) ，才能讓其他使用者隨時瀏覽觀看。

---

在眾多服務提供者中，由於 [Heroku](https://www.heroku.com/) 的免費額度足夠經營一個小型網站，並擁有完善的開發者教學資源。

這一章，我們會根據 [官方教學 - "Getting Started with Django on Heroku"](https://devcenter.heroku.com/articles/getting-started-with-django) 稍作調整，教你如何準備部屬，並在 Heroku 上發佈你的網站。

---
## 安裝 django-toolbelt

首先，我們需要安裝 **django-toolbelt**，它將我們在部屬時需要用到的套件全部包含在內。當終端機顯示 *Successfully installed...* 時，表示必要的套件都已經安裝好了：

```
(VENV) ~/djangogirls$ pip install django-toolbelt
Downloading/unpacking django-toolbelt
...
Successfully installed django-toolbelt django psycopg2 gunicorn dj-database-url dj-static static3
Cleaning up...
```

## 部屬準備

為了讓 Server 了解部屬時所需要的安裝環境，我們需要調整和準備一些設定檔案。

### requirements.txt

在`djangogirls`專案目錄底下，利用`pip freeze`將此虛擬環境裡的 Python 套件全部條列出來，儲存於 **requirements.txt**：
```
(VENV) ~/djangogirls$ pip freeze > requirements.txt
```
除了套件名稱還會包含版本資訊，檔案內容範例如下：
```
Django==1.7
dj-database-url==0.3.0
dj-static==0.0.6
django-toolbelt==0.0.1
gunicorn==19.1.1
psycopg2==2.5.4
static3==0.5.1
```

### Procfile
```
web: gunicorn --pythonpath mysite mysite.wsgi

```

### runtime.txt
```
python-3.4.1
```

### local_settings.py
`mysite/mysite/local_settings.py`

```
# mysite/local_settings.py

import os
BASE_DIR = os.path.dirname(os.path.dirname(__file__))

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}

DEBUG = True
```

`mysite/mysite/settings.py`

```
# mysite/settings.py

...
# Parse database configuration from $DATABASE_URL
import dj_database_url
DATABASES['default'] =  dj_database_url.config()

# Honor the 'X-Forwarded-Proto' header for request.is_secure()
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# Allow all host headers
ALLOWED_HOSTS = ['*']

# Static asset configuration
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
STATIC_ROOT = 'staticfiles'
STATIC_URL = '/static/'

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)

# Close DEBUG mode
DEBUG = False

# Import all of local settings if the file exists
try:
    from .local_settings import *
except ImportError:
    pass
```


### wsgi.py
```
from django.core.wsgi import get_wsgi_application
from dj_static import Cling

application = Cling(get_wsgi_application())
```

### 小結

```
檔案結構
```


## Deploy to Heroku
```
(VENV) ~/djangogirls$ git init
(VENV) ~/djangogirls$ git add .
(VENV) ~/djangogirls$ git commit -m "my djangogirls app"
```


```
(VENV) ~/djangogirls$ git push heroku master
Initializing repository, done.
Total 0 (delta 0), reused 0 (delta 0)

-----> Python app detected
-----> Installing runtime (python-3.4.1)
-----> Installing dependencies with pip
...
-----> Compressing... done, 40.0MB
-----> Launching... done, v5
       http://djangogirlstrips.herokuapp.com/ deployed to Heroku

To git@heroku.com:djangogirlstrips.git
 * [new branch]      master -> master
```

```
heroku ps:scale web=1
```

```
heroku run python mysite/manage.py migrate
```

```
heroku run python mysite/manage.py createsuperuser
```

```
heroku open
```
http://djangogirlstrips.herokuapp.com
