# Views and URLconfs

Views + urls 圖

在前面的介紹，我們有提到 Django 的 MTV 架構，Django View 從瀏覽器接到 request，可能會進行資料庫的操作或其他運算，然後產生 response，在網頁上呈現。

這一章，我們將透過小小的練習 - `Hello World`，了解其中的運作方式。

## Django Views
首先，在`views.py`輸入下列程式碼：

```
#trips/views.py

from django.http import HttpResponse


def hello_world(request):
    return HttpResponse("Hello World!")

```
Django 從網頁接收到 `request` 後，會自動產生一個 [HttpRequest](https://docs.djangoproject.com/en/1.7/ref/request-response/#httprequest-objects) 物件，裡面包含了 request body, request method, metadata，使用者資訊等等，並當成第一個參數傳入對應的 View function，也就是這裡的 def ( Python function ) `hello_world`。

在這個例子，我們並沒有用到 `request`，而是把一個字串`"Hello World!"`封裝成 [HttpResponse](https://docs.djangoproject.com/en/dev/ref/request-response/#django.http.HttpRequest) 物件 (需要先從 django.http 模組 import 此類別) ，其中也包含了 content type, status code 等等需要回傳給瀏覽器的資訊。

## Django Urls

到這裡我們還差最後一個步驟，就是當 request 進來時，如何找出對應的 View function。

`URLconf`( URL configuration )，它像 Django 網站的目錄一樣，當使用者從瀏覽器輸入 URL 時，我們透過這個目錄找到符合條件的 View function，進而執行裡面的程式碼。

打開 startproject 時自動產生的`urls.py`，在 urlpatterns 當中加入以下程式：

```
# mysite/urls.py

urlpatterns = patterns('',
    ...
    url(r'^$', 'trips.views.hello_world'),
)
```
我們透過 [url function](https://docs.djangoproject.com/en/1.7/ref/urls/#django.conf.urls.url) 傳入兩個參數：
1. Regular Expression: `^$`表示空值，也就是所謂的首頁。
2. View function: 在這裡我們指定對應到 Django app `trips`的`views.py`裡面的`hello_world`function。

## Hello World
現在，啟動你的 web server。 ( 如果剛剛沒關閉的話，通常 Django 會在你修改程式碼後，自動重新啟動 web server )
```
python manage.py runserver
```
在瀏覽器輸入[http://localhost:8000/](http://localhost:8000/)，你會看到網頁不再是"It worked!"，而是我們在 HttpResponse 傳入的文字`Hello World!`。

HelloWorld screenshot
