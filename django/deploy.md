# Deploy

目前為止，我們所有的工作都是在本機端 ( localhost ) 完成，你可以在自己電腦上的瀏覽器看到成果。但是，一個網站必須放在穩定的 Server 上部署 ( Deploy ) ，才能讓其他使用者隨時瀏覽觀看。

---

在眾多服務提供者中，我們選擇 [Heroku](https://www.heroku.com/) 作為這次的範例， 它的免費額度足夠經營一個小型網站，並擁有完善的開發者教學資源。

這一章，我們會根據 [官方教學 - "Getting Started with Django on Heroku"](https://devcenter.heroku.com/articles/getting-started-with-django) 稍作調整，教你如何準備部署，並在 Heroku 上發佈你的網站。

---

## 安裝部署工具

首先，利用`pip`安裝一些部署時需要用到的套件：

```
(VENV) ~/djangogirls$ pip install dj-database-url gunicorn dj-static
```
當終端機顯示 *Successfully installed...* 時，表示必要的套件都已經安裝好了。

## 部署準備

為了讓 Server 了解部署時所需要的安裝環境，我們需要調整和準備一些設定檔案。


### requirements.txt

在`djangogirls`專案目錄底下，利用`pip freeze`將此虛擬環境裡的 Python 套件全部條列出來，除了套件名稱還會包含版本資訊，儲存於 [requirements.txt](https://devcenter.heroku.com/articles/python-pip#the-basics)：

```
(VENV) ~/djangogirls$ pip freeze > requirements.txt
```

由於 Heroku 使用 [PostgreSQL](http://www.postgresql.org/) 資料庫， 我們在最後加上`psycopg2==2.5.4` ( Python 的 PostgreSQL 模組 ) ，檔案內容範例如下，版本可能會稍有不同：

```
Django==1.7.7
dj-database-url==0.3.0
dj-static==0.0.6
gunicorn==19.3.0
static3==0.5.1
psycopg2==2.5.4
```

### Procfile

建立一個 [Procfile](https://devcenter.heroku.com/articles/procfile) 檔案，它告訴 Heroku 要執行什麼指令來啟動我們的應用：

```
web: gunicorn --pythonpath mysite mysite.wsgi

```
這一行
*`<process_type>: <command>`*表示：

- **< process_type >** -- 啟用`web`應用
- **< command >** -- [Gunicorn ](http://gunicorn.org/) 是原生支援 Django 的 Python WSGI Server，我們透過指令下列指令來啟動網站：
    - `gunicorn --pythonpath`*`<directory_path> <project_name>.wsgi`*

### runtime.txt

為了讓 Heroku 知道要用哪一個版本的 Python，新增 [runtime.txt](https://devcenter.heroku.com/articles/python-runtimes) 輸入：
```
python-3.4.1
```

### production_settings.py

前面的章節，我們透過修改`settings.py`來調整 Django project 的設定，但是通常正式上線 ( production ) 的環境會和開發/本機 ( development / local ) 環境有所不同。

首先，Django 在部署時會將所有 [靜態檔案](https://devcenter.heroku.com/articles/django-assets) (ex: css, javascript, image...) ，都匯集在同一個資料夾，所以在`settings.py`加上 STATIC_ROOT 的設定：
```python
# mysite/settings.py

...

# Static asset configuration
STATIC_ROOT = 'staticfiles'

```
然後在同一個資料夾，也就是 mysite/mysite/ 底下新建 `production_settings.py`，專門放部署時所需要的設定：

```python
# import all default settings
from .settings import *

import dj_database_url
DATABASES = {
    'default': dj_database_url.config()
}

# Honor the 'X-Forwarded-Proto' header for request.is_secure()
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# Allow all host headers
ALLOWED_HOSTS = ['*']

# Turn off DEBUG mode
DEBUG = False

TEMPLATE_DEBUG = False

```

### wsgi.py

[WSGI - Web Server Gateway Interface](http://webpython.codepoint.net/wsgi_tutorial)，簡單來說，它是 Python 定義網頁程式和伺服器溝通的介面，修改`mysite/mysite/wsgi.py`如下：

```python
## mysite/wsgi.py

import os
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.production_settings")

from django.core.wsgi import get_wsgi_application
from dj_static import Cling
application = Cling(get_wsgi_application())
```
在 DJANGO_SETTINGS_MODULE 這邊要指向剛剛所建立的部署專用設定 `mysite.production_settings`

而 import [dj_static](https://github.com/kennethreitz/dj-static) 幫我們部署 static 檔案 ( 例如圖片，CSS 和 JavaScript 檔案等等 )


### .gitignore
有一些檔案或資料夾在新增 git repository 時，不想被加入進入。建立一個 [.gitignore](http://git-scm.com/docs/gitignore) 檔案將我們的虛擬環境，本機資料庫等等放進去：
```
VENV
*.pyc
__pycache__
staticfiles
db.sqlite3
```


### 小結

最後的檔案結構如下：

```
djangogirls
├──mysite
│   ├── mysite
│   │   ├── __init__.py
│   │   ├── production_settings.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── templates
│   ├── trips
│   └── manage.py
├── VENV
├── .gitignore
├── Procfile
├── requirements.txt
└── runtime.txt
```


## Deploy to Heroku

在開始部署 ( Deploy ) 之前，請先確定你已經按照[教學手冊](http://djangogirlstaipei.herokuapp.com/tutorials/setting-up-heroku/)：
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
### Step 3-1: 新增新的 Heroku app

接下來，我們需要新增一個可以上傳 repository 的地方，如果你之前已經新增過 app，請跳到 **Step 3-2**：
```
~/djangogirls$ heroku create
```
預設`create`後面不放名字時，會自動產生隨機名稱的 Heroku app，如果想要命名自己的 app，如下：
```
~/djangogirls$ heroku create djangogirlsdiary
```
注意：
- Heroku app 是不能重名的，所以如果你也輸入`djangogirlsdiary`，會得到` !    Name is already taken`的警告。
- Heroku app 名稱會顯示在 deploy 成功後的網址上，例如：https://djangogirlsdiary.herokuapp.com

### Step 3-2: 指定已經存在的 app

如果你之前已經新增過 app ，並且想發佈在已經存在的 app 上時，可以先用指令`heroku apps`查看 app 的名稱：

```
$ heroku apps
=== My Apps
djangogirlsdiary
```
然後設定成你想要上傳的 app：

```
$  heroku git:remote -a djangogirlsdiary
Git remote heroku added.
```
最後透過`git remote -v`檢查一下是否設定到正確的位置：
```
$ git remote -v
heroku	https://git.heroku.com/djangogirlsdiary.git (fetch)
heroku	https://git.heroku.com/djangogirlsdiary.git (push)
```

### Step 4: 利用 git push 上傳到 Heroku

使用`git push`指令上傳 git repository 後，你會發現它按照 **runtime.txt** 安裝 python-3.4.1，也透過 pip 安裝我們在 **requirements.txt** 上列出的所有套件：

```
~/djangogirls$ git push heroku master
...
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Python app detected
remote: -----> Installing runtime (python-3.4.1)
remote: -----> Installing dependencies with pip
...
remote: -----> Compressing... done, 50.8MB
remote: -----> Launching... done, v5
remote:        https://djangogirlsdiary.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/djangogirlsdiary.git
 * [new branch]      master -> master
```

如果你遇到下列的錯誤訊息：
```
Permission denied (publickey).
fatal: The remote end hung up unexpectedly
```
請透過下列指令新增 public key，然後再重新`git push`。
```
~/djangogirls$ heroku keys:add
```

### Step 5: 啟動 web process

先前建立了 **Procfile** 檔案告訴 Heroku 啟動時要執行的指令，現在我們使用指令啟動 web process，並指定只需要`1`個 instance：
```
~/djangogirls$ heroku ps:scale web=1
```
### Step 6: Django project 初始化

Django 已經成功啟動了，但是我們還需要進行資料庫初始化，利用`heroku run`可以在 Heroku 執行指令。

migrate 時需要指定使用部署專用設定檔 mysite.production-settings：
```
~/djangogirls$ heroku run python mysite/manage.py migrate --settings=mysite.production-settings
```
並為新資料庫建立一個 superuser：
```
~/djangogirls$ heroku run python mysite/manage.py createsuperuser
```

### Step 7: 開啟瀏覽器觀看你的網站
最後，透過`open`指令會自動在瀏覽器打開你的網站：

```
~/djangogirls$ heroku open
```
恭喜你成功地把網站發佈到網路上了！
因為資料庫是不同的，之前在本機端的日記都需要再重新輸入喔。

你可以分享網址給任何人：
https://djangogirlsdiary.herokuapp.com/ ( 記得前面要替換成你自己的 Heroku app 名稱 )

---

未來如果對網站進行任何修改並想更新到 Heroku，只要先確定 **git commit** 完成後再 push 到 Heroku 即可。

```
$ git push heroku master
```

---
