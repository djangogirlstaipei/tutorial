# Admin


`mysite/settings.py`
```
INSTALLED_APPS = (
    'django.contrib.admin',
...
)
```

`mysite/urls.py`
```
from django.contrib import admin
admin.autodiscover()

urlpatterns = pattern('',
    ...
    url(r'^admin/', include(admin.site.urls)),
)
```
http://localhost:8000/admin

admin 登入 screenshot

空admin screenshot

## 註冊 Model class
`trips/admin.py`
```
code
```
有 model admin screenshot

create screenshot
