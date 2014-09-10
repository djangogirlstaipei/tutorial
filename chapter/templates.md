# Templates



## 加上 HTML / CSS & 動態內容

上一章的例子，只是很簡單的顯示一行字串。讓我們加上一些 HTML/CSS 美化網頁，並動態顯示每次進來這個頁面的時間:

```
# trips/views.py

from datetime import datetime
from django.http import HttpResponse


def hello_world(request):
    output = """
        <!DOCTYPE html>
        <html>
            <head>
            </head>
            <body>
                Hello World! {current_time}
            </body>
        </html>
    """.format(current_time=datetime.now())

    return HttpResponse(output)
```
1. `"""`是用來包住長字串的寫法，這裡我們使用它來包住 HTML 語法。
2. 為了顯示動態內容，我們 import [datetime](https://docs.python.org/3.1/library/datetime.html) 時間模組，並用`datetime.now()`取得現在的時間。
3. [format()](https://docs.python.org/3/library/string.html#string-formatting) 是一個相當實用的內建 method，不用 import 就可以使用。在這裡，我們利用它幫我們把 datetime.now() 產生的值，塞進字串裡`{current_time}`的位置。

如果你的 web server 沒有關閉，在瀏覽器輸入 [http://localhost:8000/hello/](http://localhost:8000/hello/) 後，會發現網頁不只變比較漂亮，還多了現在的時間。( 你可以重新載入同一個頁面試試 )

HelloWorld Now screenshot


## 建立 templates 資料夾

雖然前一個例子，我們把 HTML/CSS 放在 View function 裡，但通常為了可讀性，以及分工方便，我們會把前端的程式碼切割出來，獨立於 templates 資料夾裡。

`djangogirls/mysite/`

( 如果不想關閉 web server，可以再開新一個新的終端機視窗 )

```
mkdir templates
```

```
- mysite
  - mysite
  - templates
    - hello_world.html
  - trips
  - manage.py
```

`index.html`

`{{ current_time }}`

## render to template

最後，我們回到 View 把 HTML 的部分移除，並使用 [render](https://docs.djangoproject.com/en/1.7/topics/http/shortcuts/) 這個 Django shortcut function，幫助我們把變數傳到指定的 template:

```
# trips/views.py

from datetime import datetime
from django.shortcuts import render


def hello_world(request):
    return render(request,
                  'hello_world.html',
                  current_time=datetime.now())
```

HelloWorld Now screenshot2
