# 安裝 Django

---

在這一章，我們會開始建立第一個 Django 專案，並瞭解如何使用虛擬環境。

---

首先，請開啟**終端機**，確定現在的位置是在**家目錄**底下。

我們先建立一個名為 `djangogirls` 的資料夾：

```
mkdir djangogirls
```

並切換至剛剛建立的目錄：

```
cd djangogirls
```

## 虛擬環境（virtualenv）

在安裝 Django 之前，我們要先建立一個**虛擬環境**（virtual environment）。

我們可以直接開始安裝 Django，但實務上，大多數人都會搭配使用虛擬環境。使用虛擬環境有許多優點：

- 你的專案會擁有一個專屬的獨立 Python 環境。
- 不需要 root 權限，就可以安裝新套件。
- 方便控管不同版本的套件，不用擔心升級套件會影響到其他專案。
- 如果需要多人協作或在不同機器上跑同一個專案時，使用虛擬環境也可以確保環境一致性。

### 創建虛擬環境

在較舊的 Python 版本中，建立處擬環境需要另外安裝。但 Python 3.3 之後已經加入 `venv` 模組，可以直接使用。

那我們立刻開始，首先要創建一個虛擬環境資料夾 `djangogirls_venv`。

#### Windows

如果有按照安裝教學，使用 **Django Environment** 開啟終端機後，輸入以下指令：

    C:\Users\YOUR_NAME\djangogirls> python -m venv djangogirls_venv

#### Linux / OS X

Linux 或 OS X 需要使用 `python3` 來建立虛擬環境，指令如下：

```
~/djangogirls$ python3 -m venv djangogirls_venv
```

### 切換虛擬環境
虛擬環境建立完成後，我們可以透過 `activate` 這個 script 來啟動它。

記得未來在安裝新套件，或是要執行 Django 相關指令時，都要先啟動該專案的虛擬環境。

#### Windows

    C:\Users\YOUR_NAME\djangogirls> djangogirls_venv\Scripts\activate

#### Linux / OS X

    ~/djangogirls$ source djangogirls_venv/bin/activate

如果無法使用 `source` 的話，可以用下列指令替代：

    ~/djangogirls$ . djangogirls_venv/bin/activate


### 目前的虛擬環境

如果看到前面多了 `(虛擬資料夾名稱)`，則表示已經成功切換至該虛擬環境。

#### Windows

    (djangogirls_venv) C:\Users\YOUR_NAME\djangogirls>

#### Linux / OS X

    (djangogirls_venv) ~/djangogirls$


## 安裝 Django 2.0 最新版本

### 開始安裝

Python 3.4 預先安裝了 `pip` 這個強大的套件管理工具，我們將使用它來安裝 Django：

```
(djangogirls_venv) ~/djangogirls$ pip install django~=2.0
```

輸入以後應該會看到如下的訊息，表示安裝成功。

```
Installing collected packages: pytz, django
Successfully installed django-2.0.5 pytz-2018.4
```

註：如果你看到以 *Fatal error in launcher* 開頭的輸出，而不是上面的安裝成功訊息，請改用 `python -m pip install django` 試試看。之後如果在使用 `pip` 時遇到類似問題，也可以試著在前面加上 `python -m`。


### 確認安裝成功

最後，讓我們來確定一下安裝的版本是否正確，請在虛擬環境下輸入:

```
(djangogirls_venv) ~/djangogirls$ python -m django --version
2.0.5
```

出現的數字代表虛擬環境中 Django 的版本資訊，如果看見類似上面的訊息，就代表安裝成功囉！