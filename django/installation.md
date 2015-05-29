# 安裝 Django

---

在這一章，我們會開始建立第一個 Django 專案，並瞭解如何使用虛擬環境。

---

首先，請開啟`終端機`，確定現在的位置是在`家目錄`底下：

我們先建立一個名為`djangogirls`的資料夾
```
mkdir djangogirls
```
並切換至剛剛建立的目錄下
```
cd djangogirls
```

## 虛擬環境 ( virtualenv )

在安裝 Django 之前，我們要先建立一個**虛擬環境** ( virtual environment ) 。

我們可以直接開始安裝 Django ，但實務上，大多數人都會搭配使用虛擬環境。
使用虛擬環境有許多優點：

- 你的專案會擁有一個專屬的獨立 Python 環境
- 不需要 root 權限，就可以安裝新套件。
- 方便控管不同版本的套件，不用擔心升級套件會影響到其他專案。
- 如果需要多人協作或在不同機器上跑同一個專案時，使用虛擬環境也可以確保環境一致性。

### 創建虛擬環境

在較舊的 Python 版本中，建立處擬環境需要另外安裝。但 Python 3.3 已經加入 `venv`模組，可以直接使用。

那我們立刻開始，首先要創建一個虛擬環境資料夾`VENV`

#### Windows
如果有按照安裝教學，使用`Django Environment`開啟終端機後，輸入以下指令：

    C:\Users\YOUR_NAME\djangogirls> python -m venv VENV

#### Linux / OS X

Linux 或 OS X 需要使用`python3`來建立虛擬環境，指令如下：
```
~/djangogirls$ python3 -m venv VENV
```

### 切換虛擬環境
虛擬環境建立完成後，我們可以透過`activate` 這個 script 來啟動它。

記得未來在安裝新套件，或是要執行 Django相關指令時，都要先啟動該專案的虛擬環境。

#### Windows

    C:\Users\YOUR_NAME\djangogirls> VENV\Scripts\activate

#### Linux / OS X

    ~/djangogirls$ source VENV/bin/activate
如果無法使用`source`的話，可以用下列指令替代:

    ~/djangogirls$ . VENV/bin/activate


### 目前的虛擬環境

如果看到前面多了`(虛擬資料夾名稱)`，則表示已經成功切換至該虛擬環境

#### Windows

    (VENV) C:\Users\YOUR_NAME\djangogirls>

#### Linux / OS X

    (VENV) ~/djangogirls$


## 安裝 Django 1.7 最新版本

### 開始安裝

Python 3.4 預先安裝了`pip`這個強大的套件管理工具，我們將使用它來安裝 Django：
```
pip install "django<1.8"
```
這裡需要特別注意，我們使用的指令是`"django`**`<1.8`**`"`。這樣一來才可以**確保我們安裝的是 Django 1.7 的最新版本**

輸入了應該會看到如下的訊息，表示安裝成功

```
Downloading/unpacking django<1.8
Installing collected packages: django
Successfully installed django
Cleaning up...
```

註：如果你看到以 *Fatal error in launcher* 開頭的輸出，而不是上面的安裝成功訊息，請改用 `python -m pip install "django<1.8"` 試試看。之後如果在使用 `pip` 時遇到類似問題，也可以試著在前面加上 `python -m`。

### 確認安裝成功
最後，讓我們最後來測試一下。

請在虛擬環境下指令輸入`python`，進入`互動式命令列`環境

    (VENV) ~/djangogirls$ python

輸入以下的指令取得 Django 版本資訊：
```
>>> import django
>>> django.VERSION
(1, 7, 8, 'final, 0')
```

如果看見類似上面的訊息，就代表安裝成功囉！
