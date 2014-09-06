# Models

## 設定資料庫
`mysite/settings.py`

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

## Django Models

`trips/models.py`
```
code
```


## 同步資料庫
```
python manage.py syncdb
```

### 創建 superuser
```
yes
```
