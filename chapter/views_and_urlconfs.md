# Views and URLconfs

Views + urls 圖

## Django Views
`trips/views.py`
```
from django.http import HttpResponse


def hello_world(request):
    return HttpResponse("Hello World!")

```

## Django Urls
`mysite/urls.py`

```
urlpatterns = patterns('',
    ...
    url(r'^$', 'trips.views.hello_world'),
)
```


## Hello World

```
python manage.py runserver
```
HelloWorld screenshot
