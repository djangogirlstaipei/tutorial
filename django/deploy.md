# Deploy

目前為止，我們所有的工作都是在自己的電腦完成，你也可以在自己的電腦上看到成果。但是，如果我們想要讓其他人隨時瀏覽這個網站，就必須將它部署（deploy）到穩定的伺服器上。

我們選擇 [PythonAnywhere](https://www.PythonAnywhere.com/) 作為範例。它對於 Python 的支援性相當好，免費帳號也足夠經營一個小型網站。

在開始部署之前，請先確定已經根據[課前準備](https://djangogirlstaipei.herokuapp.com/tutorials/setting-up-pythonanywhere/)，在 [PythonAnywhere](https://www.PythonAnywhere.com/) 註冊帳號：

![](./../images/PythonAnywhere-signup.png)


## 修改設定

打開 `settings.py`，找到下面這行：

```python
ALLOWED_HOSTS = []
```

這段設定是用來控制你的網站可以用哪些網址連線。我們之前都是用 `127.0.0.1` 或 `localhost`，代表本機連線，所以不需要指定，但當我們把網站放到雲端，就不能再用這個網址。

我們把 PythonAnywhere 的網址加入這個設定。在預設狀況下，這個網址會是 <https://你的帳號.pythonanywhere.com>，所以假設你的 PythonAnywhere username 是 `djangogirls`，就請把這個設定改成

```python
ALLOWED_HOSTS = ['djangogirls.pythonanywhere.com']
```

再提醒一次，記得要將 `djangogirls` 改成你在 PythonAnywhere 註冊的 *username*。


## 打包程式碼

為了將你的程式碼上傳到雲端，我們要先將整個專案打包成一個壓縮檔。在 `djangogirls` 專案目錄下，用以下的指令將整個專案壓縮成 `mysite.zip`：

```
(djangogirls_venv) ~/djangogirls$ python -m zipfile -c mysite.zip mysite
```


## 部署到雲端

### Step 1: 上傳專案

使用 PythonAnywhere 的檔案介面，將專案的壓縮檔上傳。首先切換到 **Files** 分頁，按下 *Upload a file* 按鈕：

![](./../images/PythonAnywhere-upload.png)

選擇剛剛壓縮好的 `mysite.zip`。當畫面上出現這個檔案時，便表示已經上傳完成。

![](./../images/PythonAnywhere-upload-done.png)

### Step 2: 開啟 Bash Console

切換到 **Consoles** 分頁，點選 *Bash* 開啟一個新的 Bash console。我們可以透過它下指令，以建置部署環境。

![](./../images/PythonAnywhere-consoles.png)

#### Step 2.1: 解壓縮專案

首先利用 `unzip` 指令將專案解壓縮：

```
~ $ unzip mysite.zip
```

![](./../images/PythonAnywhere-bash.png)

#### Step 2.2: 建立虛擬環境、安裝 Django

由於雲端的環境與我們本機端不同，我們需要為它建立一個新的虛擬環境，並在裡面安裝 Django：

```
~ $ virtualenv --python=python3.6 djangogirls_venv
~ $ source djangogirls_venv/bin/activate
(djangogirls_venv) ~ $ pip install "django<1.12"
```


### Step 3: 新增一個新的 web app

點擊左上角的 logo 回到主頁面，接著切換到 **Web** 分頁，點選 *Add a new web app* 按鈕：

![](./../images/PythonAnywhere-web.png)

在出現的視窗中首先會詢問你想使用的網址。由於免費方案無法設定，我們直接按下 *Next* 使用預設值。

![](./../images/PythonAnywhere-new-web-1.png)

接著需要選擇 Python Web framework。請特別注意**不要選擇 Django**。我們將手動設定虛擬環境，因此使用 *Manual configuration*。

![](./../images/PythonAnywhere-new-web-2.png)

在 Python 版本選擇畫面中使用 *Python 3.6*。

![](./../images/PythonAnywhere-new-web-3.png)

最後按下 *Next*，即完成建立 web app。

![](./../images/PythonAnywhere-new-web-4.png)

### Step 4: 修改 web app 設定

當你完成上一個步驟後，**Web** 分頁會顯示 web app 的設定介面，讓你可以修改設定、重載 web app、或是查看錯誤訊息等等。

![](./../images/PythonAnywhere-web-config.png)

首先在中間的 *Virtualenv* 區塊填入我們前面設定的虛擬環境位置`/home/<your-PythonAnywhere-username>/djangogirls_venv`，並點選按鈕儲存設定。

**注意：**請將 `<your-PythonAnywhere-username>` 改成你在 PythonAnywhere 註冊的 *username*。我們在後面還會不斷提到這個路徑，務必也要記得修改！

![](./../images/PythonAnywhere-venv.png)

接著設定 PythonAnywhere 與我們的網站如何溝通。用 Python 撰寫的網站程式是透過 Python 制定的 [WSGI - Web Server Gateway Interface](http://webpython.codepoint.net/wsgi_tutorial) 標準，來定義和伺服器溝通的介面。在 *Code* 區塊點選 `/var/www/<your-PythonAnywhere-username>_PythonAnywhere_com_wsgi.py`，打開編輯介面：

![](./../images/PythonAnywhere-wsgi.png)

刪除原有的程式碼，並覆蓋為：

```
import os
import sys

path = '/home/<your-PythonAnywhere-username>/mysite'  # use your own PythonAnywhere username here
if path not in sys.path:
    sys.path.append(path)

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

from django.core.wsgi import get_wsgi_application
from django.contrib.staticfiles.handlers import StaticFilesHandler
application = StaticFilesHandler(get_wsgi_application())
```

再次提醒，記得要將 `<your-PythonAnywhere-username>` 改成你在 PythonAnywhere 註冊的 *username*。

點選 *Save* 儲存修改後，即可點選 logo 離開編輯器。

![](./../images/PythonAnywhere-wsgi-save.png)

### Step 5: 重新載入（Reload）web app

回到 **Web** 分頁點選 *Reload* 按鈕，讓 PythonAnywhere 重新載入我們更新的設定：

![](./../images/PythonAnywhere-reload.png)

### Step 6: 開啟瀏覽器觀看你的網站

直接點選，或可以複製網址到瀏覽器打開網站：

![](./../images/PythonAnywhere-url.png)

大功告成，恭喜你成功發佈了自己的網站！這個網址可以直接分享給任何人：<https://djangogirlstaipei.PythonAnywhere.com/>——記得前面要替換成你自己的 PythonAnywhere `username`！

---

未來如果對網站進行任何修改，只要重複以下的章節，就可以更新程式：

1. 打包程式碼
2. 上傳專案
2. 開啟 Bash console，解壓縮專案
3. 重新載入（Reload）web app

就可以在 PythonAnywhere 上看到新的程式。

**注意：**在上傳、解壓縮新版程式時，也會讓雲端的所有資料庫被本機的版本覆蓋！
