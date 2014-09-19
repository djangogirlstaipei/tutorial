# Deploy

目前為止，我們所有的工作都是在本機端 ( localhost ) 完成，你可以在自己電腦上的瀏覽器看到成果。但是，一個網站必須放在穩定的 Server 上部屬 ( Deploy ) ，才能讓其他使用者隨時瀏覽觀看。

---

在眾多服務提供者中，我們選擇 [Heroku](https://www.heroku.com/) 作為這次的範例， 它的免費額度足夠經營一個小型網站，並擁有完善的開發者教學資源。

這一章，我們會根據 [官方教學 - "Getting Started with Django on Heroku"](https://devcenter.heroku.com/articles/getting-started-with-django) 稍作調整，教你如何準備部屬，並在 Heroku 上發佈你的網站。

---

## 安裝部屬工具

首先，利用`pip`安裝一些部屬時需要用到的套件：

```
(VENV) ~/djangogirls$ pip install dj-database-url gunicorn dj-static
```
當終端機顯示 *Successfully installed...* 時，表示必要的套件都已經安裝好了。

## 部屬準備

為了讓 Server 了解部屬時所需要的安裝環境，我們需要調整和準備一些設定檔案。


### requirements.txt

在`djangogirls`專案目錄底下，利用`pip freeze`將此虛擬環境裡的 Python 套件全部條列出來，除了套件名稱還會包含版本資訊，儲存於 **requirements.txt**：
```
(VENV) ~/djangogirls$ pip freeze > requirements.txt
```
由於 Heroku 使用 [Postgres](http://www.postgresql.org/) 資料庫， 我們在最後加上`psycopg2==2.5.4` ( Python 的 PostgreSQL 編譯器 ) ，檔案內容範例如下：
```
Django==1.7
dj-database-url==0.3.0
dj-static==0.0.6
gunicorn==19.1.1
psycopg2==2.5.4
```

### wsgi.py

[WSGI - Web Server Gateway Interface](http://webpython.codepoint.net/wsgi_tutorial)，簡單來說，它是 Python 定義網頁程式和伺服器溝通的介面，修改`mysite/mysite/wsgi.py`如下：

```
## mysite/wsgi.py

from django.core.wsgi import get_wsgi_application
from dj_static import Cling

application = Cling(get_wsgi_application())
```
import [dj_static](https://github.com/kennethreitz/dj-static) 幫我們部屬 static 檔案 ( 例如圖片，CSS 和 JavaScript 檔案等等 )


### Procfile

建立一個 [Procfile](https://devcenter.heroku.com/articles/procfile) 檔案，它告訴 Herok 要執行什麼指令來啟動我們的應用：

```
web:gunicorn --pythonpath mysite mysite.wsgi

```
這一行
*`<process_type>: <command>`*表示：

- **< process_type > ** -- 啟用`web`應用
- **< command > ** -- [Gunicorn ](http://gunicorn.org/) 是原生支援 Django 的 Python WSGI Server，我們透過指令下列指令來啟動網站：
    - `gunicorn --pythonpath`*`<directory_path> <project_name>.wsgi`*

### runtime.txt

為了讓 Heroku 知道要用哪一個版本的 Python，新增 [runtime.txt](https://devcenter.heroku.com/articles/python-runtimes) 輸入：
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

### .gitignore
有一些檔案或資料夾在新增 git repository 時，不想被加入進入。建立一個 [.gitignore](http://git-scm.com/docs/gitignore) 檔案將我們的本機設定，虛擬環境，資料庫等等放進去：
```
VENV
*.pyc
__pycache__
staticfiles
local_settings.py
db.sqlite3
```


### 小結

```
檔案結構
```


## Deploy to Heroku

在開始 Deploy 之前，請先確定你已經：
1. 註冊 Heroku 帳號：https://id.heroku.com/signup
2. 安裝 Heroku 工具箱：https://toolbelt.heroku.com/



### Step 1: 登入 Heroku

安裝完工具箱裡的 Heroku client 後，你就可以使用 heroku 指令，讓我們先來登入：
```
$ heroku login
```
輸入註冊時的 Email 帳號和密碼，當你看到
*Authentication successful.* 時，表示認證成功。


### Step 2: 新增一個新的 git repository

在`djangogirls`資料夾底下新增一個 git repository：

```
~/djangogirls$ git init
~/djangogirls$ git add .
~/djangogirls$ git commit -m "my djangogirls app"
```
### Step 3: 新增 Heroku app

接下來，我們需要新增一個可以上傳 repository 的地方：
```
$ heroku create
```
預設`create`後面不放名字時，會自動產生隨機名稱的 Heroku app，如果想要命名自己的 app，如下：
```
$ heroku create djangogirlstrips
```
注意：
- Heroku app 是不能重名的，所以如果你也輸入`djangogirlstrips`，會得到` !    Name is already taken`的警告。
- Heroku app 名稱會顯示在 deploy 成功後的網址上，例如：http://djangogirlstrips.herokuapp.com

### Step 4: 利用 git push 上傳到 Heroku

使用`git push`指令上傳 git repository 後，你會發現它按照 **runtime.txt** 安裝 python-3.4.1，也透過 pip 安裝我們在 **requirements.txt** 上列出的所有套件：

```
$ git push heroku master
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

如果你遇到下列的錯誤訊息：
```
Permission denied (publickey).
fatal: The remote end hung up unexpectedly
```
請透過下列指令新增 public key ( 一種安全性機制 )
```
$ heroku keys:add
```
然後再重新`git push`。

### Step 5: 啟動 web process

先前建立了 **Procfile** 檔案告訴 Heroku 啟動時要執行的指令，現在我們使用指令啟動 web process，並指定只需要`1`個 instance：
```
$ heroku ps:scale web=1
```
### Step 6: Django project 初始化

Django 已經成功啟動了，但是我們還需要進行資料庫初始化，利用`heroku run`可以在 Heroku 執行指令：

```
heroku run python mysite/manage.py migrate
```
以及重新建立一個 superuser：
```
$ heroku run python mysite/manage.py createsuperuser
```

### Step 7: 開始瀏覽器觀看你的網站
最後，透過`open`指令會自動在瀏覽器打開你的網站：

```
$ heroku open
```
恭喜你成功地把網站發佈到網路上了！

你可以分享網址給任何人：
http://djangogirlstrips.herokuapp.com ( 記得前面要替換成你自己的 Heroku app 名稱 )
