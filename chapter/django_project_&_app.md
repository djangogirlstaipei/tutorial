# Project and apps

## 建立 Django project

接下來，我們要利用 [django-admin](https://docs.djangoproject.com/en/dev/ref/django-admin/) 來建立第一個 Django project `mysite`:
```
django-admin.py startproject mysite
```

在指令完成以後，我們切換到`mysite`專案資料夾內部:
```
cd mysite
```
此時，用`ls`或`dir` ( Windows ) 檢視檔案結構，你會發現資料夾裡面已經自動創建了`manage.py`這個檔案，以及另一個跟 project 同名的資料夾`mysite`。

整個 project 的檔案結構如下:

```
mysite/
├── mysite
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── db.sqlite3
└── manage.py
```

[manage.py](https://docs.djangoproject.com/en/dev/ref/django-admin/) 是 Django 提供的命令列工具，我們可以利用它執行很多工作，例如同步資料庫、建立 app 等等，指令的使用方式如下:
```
python manage.py <command> [options]
```
如果你想要了解有什麼指令可以使用，輸入`help`或`-h`指令會列出所有指令列表:
```
python manage.py -h
```
而如果想了解其中一個指令，可以在指令名字後輸入`-h`，你會看到簡單的的指令介紹以及用法說明，以`runserver`為例：
```
(VENV) ~/djangogirls/mysite$ python manage.py runserver -h
Usage: manage.py runserver [options] [optional port number, or ipaddr:port]

Starts a lightweight Web server for development and also serves static files.

Options:
  -v VERBOSITY, --verbosity=VERBOSITY
                        Verbosity level; 0=minimal output, 1=normal output,
                        2=verbose output, 3=very verbose output
  --settings=SETTINGS   The Python path to a settings module, e.g.
                        "myproject.settings.main". If this isn't provided, the
                        DJANGO_SETTINGS_MODULE environment variable will be
                        used.
  --pythonpath=PYTHONPATH
                        A directory to add to the Python path, e.g.
                        "/home/djangoprojects/myproject".
  --traceback           Raise on exception
  --no-color            Don't colorize the command output.
  -6, --ipv6            Tells Django to use an IPv6 address.
  --nothreading         Tells Django to NOT use threading.
  --noreload            Tells Django to NOT use the auto-reloader.
  --nostatic            Tells Django to NOT automatically serve static files
                        at STATIC_URL.
  --insecure            Allows serving static files even if DEBUG is False.
  --version             show program's version number and exit
  -h, --help            show this help message and exit

```
[runserver](https://docs.djangoproject.com/en/dev/ref/django-admin/#runserver-port-or-address-port) 可以啟動一個簡單的 web server，以便於我們在`開發階段`使用:
```
(VENV) ~/djangogirls/mysite$ python manage.py runserver
...
Django version 1.7, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
```
打開瀏覽器輸入 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 或是 [http://localhost:8000/](http://localhost:8000/) ，你應該會看到 "It worked!" 的頁面，這就表示你已經成功建立你的第一個網頁，並且用 web server 啟動它。

it works image

如果想要關閉 web server，可以使用`CTRL+C`回到命令列。

## 建立 Django application (app)

每一個 Django project 裡面可以有一個至多個 Django app，我們通常會按照功能來切割成不同 app，這樣未來也比較好維護和再利用。

例如，我們要做一個像 Facebook 這種比較複雜的網站時，我們可能會切割成 users (使用者管理)、friends (好友管理)、timeline (塗鴉牆管理)、feed (動態消息管理) 等等。而下一個網站想寫購物平台時，users app (使用者管理) 這一塊的設計就可以再被重複使用。

現在，讓我們利用`startapp`建立第一個 Django app - `trips`:
```
python manage.py startapp trips
```
`startapp`會按照你的命名建立一個同名資料夾和 app 預設的檔案結構如下：
```
- trips
  - __init__.py
  - models.py
  - tests.py
  - views.py
  - admin.py
```
加上註解

## Django project layout

最後，讓我們再來回顧一下整個 Django project 和建立一個 Django app 後的檔案結構：

```
- mysite
  - mysite
    - __init__.py
    - settings.py
    - urls.py
    - wsgi.py
  - trips
    - __init__.py
    - models.py
    - tests.py
    - views.py
    - admin.py
  - manage.py
```
