# 安裝 Django

首先，請開啟`終端機`，確定現在的位置是在`家目錄`底下， 建立新資料夾`djangogirls`，然後切換進去。
```
mkdir djangogirls
cd djangogirls
```

## 建立虛擬環境 ( virtualenv )

在安裝 Django 之前，我們通常會為每一個專案建立一個虛擬環境 ( virtual environment ) 。使用虛擬環境，會為專案建立一個獨立且專屬的環境，它的好處如下：

1.   不需要使用 root 權限，就可以安裝新套件。
2.   方便不同專案控管不同版本的套件，不用擔心升級套件會影響到其他專案。
3.   如果需要多人協作或在不同機器上跑同一個專案時，使用虛擬環境也可以確保環境一致性。

而在這裡我們使用 Python 3.3 以上新加入的模組`venv`，創建一個虛擬環境資料夾`VENV`。

```
python -m venv VENV
```

### Windows
如果有按照安裝教學，使用`Django Environment`開啟終端機時，這時候只要輸入以下指令：

    C:\Users\YOUR_NAME\djangogirls> python -m venv VENV

### Linux / OS X

Linux 或 OS X 需要使用`python3`來建立虛擬環境，指令如下：
```
~/djangogirls$ python3 -m venv VENV
```

## 切換虛擬環境
創建好虛擬環境以後，記得未來在替專案安裝新套件，或是要執行 Django 相關指令時，都要先切換成該專案的虛擬環境。

### Windows

    C:\Users\YOUR_NAME\djangogirls> VENV\Scripts\activate

### Linux / OS X

    ~/djangogirls$ source VENV/bin/activate
如果無法使用`source`的話，可以用下列指令替代:

    ~/djangogirls$ . myvenv/bin/activate

如果看到前面多了`(虛擬資料夾名稱)`，則表示已經成功切換成該虛擬環境，Windows 如下:

    (VENV)  C:\Users\YOUR_NAME\djangogirls>

Linux 或 OS X:

    (VENV) ~/djangogirls$

## 安裝 Django 1.7 最新版本

成功切換進虛擬環境以後，我們要使用`pip`這個強大的套件管理工具來幫我們安裝 Django，指令如下:

    pip install "django<1.8"

這邊使用`"django<1.8"`的目的是為了確保我們安裝的是 Django 1.7 最新版本，輸入了以後會看到以下的訊息，就表示安裝成功。
```
Downloading/unpacking django<1.8
Installing collected packages: django
Successfully installed django
Cleaning up...
```
讓我們最後來測試一下安裝是否成功，以及安裝的版本。請在虛擬環境下指令輸入`python`，進入`互動式命令列`環境，輸入以下的指令取得 Django 版本資訊：
```
>>> import django
>>> django.VERSION
(1, 7, 0, 'final, 0')
```
