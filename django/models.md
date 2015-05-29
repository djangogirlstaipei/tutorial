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

```python
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

```python
# trips/models.py

from django.db import models


class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField(blank=True)
    photo = models.URLField(blank=True)
    location = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)
```
- Django 預設會為每一個 Model 加上 `id` 欄位 (型態為 auto-incrementing primary key)，並且 **每一筆資料的 id 都會是獨一無二的**。

- 為 Post 定義以下屬性：

| 屬性 | 資料型態 | 說明 |參數|
| -----------|-----------| -------  |--------------------------------------------|
| title      | CharField | 標題     |`max_length=100` -- 標題不可以超過 100 個字元|
| content    | TextField | 內文     | `blank=True` -- 非必填欄位（表單驗證時使用），預設所有欄位都是 `blank=False` |
| photo      | URLField  | 照片網址 | 同 content，非必填欄位|
| location   | CharField | 地點     | 同 title |
| created_at | DateTime  | 建立時間 | `auto_now_add=True` -- 物件新增的時間<br>p.s. 若想設成物件修改時間，則用 `auto_now=True`|

---
 [**Model fields**](https://docs.djangoproject.com/en/1.7/ref/models/fields/) ：可為 Django Model 定義不同型態的屬性。

- **CharField** -- **字串欄位**，適合像 title、location 這種有長度限制的字串。

- **TextField** -- **合放大量文字的欄位**

- **URLField** -- **URL 設計的欄位**

- **DateTimeField** -- **日期與時間的欄位**，使用時會轉成 Python`datetime`型別

更多 Model Field 與其參數，請參考 [Django 文件 ](https://docs.djangoproject.com/en/1.7/ref/models/fields/)

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
  Installing custom SQL...
  Installing indexes...
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying sessions.0001_initial... OK
```

[migrate](https://docs.djangoproject.com/en/1.7/ref/django-admin/#django-admin-migrate) 指令會根據`INSTALLED_APPS`的設定，按照 app 順序建立或更新資料表，將你在 models.py 裡的更新跟資料庫同步。

---

如果你不是第一次執行`migrate`，在此之前需要先執行 [makemigrations](https://docs.djangoproject.com/en/1.7/ref/django-admin/#django-admin-makemigrations)：

```
(VENV) ~/djangogirls/mysite$ python manage.py makemigrations
Migrations for 'trips':
  0001_initial.py:
    - Create model Post
```
這個指令會根據你對 Model 的修改刪除建立一個新的 [migration 檔案](https://docs.djangoproject.com/en/1.7/topics/migrations/#migration-files)，讓`migrate`指令執行時，可以照著這份紀錄更新資料庫。
```
(VENV) ~/djangogirls/mysite$ python manage.py migrate
python manage.py migrate
Operations to perform:
  Apply all migrations: auth, admin, contenttypes, sessions, trips
Running migrations:
  Applying trips.0001_initial... OK
```
---
