---
title: Python tkinter 做出簡單的視窗應用程式
author: Miigun
date: 2022-09-3 14:47:00 +0800
categories: [Python, tkinter]
tags: [Python, 視窗應用程式]
math: true
pin: false
---

與大家分享本人在Python開發上的筆記，**只是個人的小小經驗，僅供參考**。

---

tkinter為Python原生的視窗應用程式套件，所以只要有Python環境即可開發，不用下載其他的library。 tkinter雖然需要透過程式碼編寫介面，但還算易於使用，適合初學者開發功能較單純的使用介面。 其他Python GUI的開發模組還有PyQt, wxPython, Kivy等等，不過筆者目前只用過tkinter所以只能先分享它的用法啦~

## 視窗基礎設定

首先將tkinter的模組引用`import`進來，簡寫為tk

```python
import tkinter as tk
```

建立一個tkinter的視窗物件，並且加入維持視窗運行的method

```python
win = tk.Tk()
win.mainloop() # 維持視窗顯示的迴圈，沒有這行程式碼視窗無法維持顯示
```

執行後可以叫出一個空白視窗出來

![tksimpleGUI.JPG](/assets/img/postpictures/tkinter/tksimpleGUI.jpg)

呼叫視窗物件的`title`方法去設定視窗最上方的標題名稱

```python
win.title("Python tkinter")
```

也可以更改視窗左上的圖標，指定ico檔存放的位置

```python
win.iconbitmap("PythonIcon.ico") # 同目錄下的PythonIcon.ico
```

視窗物件的`geometry`方法可調整視窗大小

```python
win.geometry("400x300") # 400是橫軸長度; 300是縱軸長度
```

更改視窗的背景顏色，透過`config`去更改win視窗物件內的屬性

```python
win.config(bg="skyblue") # 更改bg屬性，顏色可以用字典key或16進位hex code指定
```

顯示視窗需要一個迴圈，讓執行後視窗可以持續顯示，請加入這行程式碼: 

```python
win.mainloop() # 維持視窗顯示的迴圈，沒有這行程式碼視窗無法維持顯示 (須放在最後一行)
```

上方程式碼寫完後，執行一下可以看到…

![tksimpleGUI1.JPG](/assets/img/postpictures/tkinter/tksimpleGUI1.jpg)

- 完整程式碼

```python
win = tk.Tk() 
win.title("Python tkinter") # 賦予標題名稱
win.geometry("400x300") # 設定視窗大小 
win.iconbitmap("PythonIcon.ico") # 同目錄下的PythonIcon.ico
win.config(bg="skyblue") # 背景顏色

win.mainloop() # 維持視窗顯示的迴圈，沒有這行程式碼視窗無法維持顯示
```

---

>一個具有基本功能的視窗應用程式除了主視窗之外，至少還需要能**顯示文字**、**按鈕**觸發事件、**輸入視窗**等等元件，以下就來說明tkinter如何建構這些元件吧~
{: .prompt-info }

## 視窗文字設定

要在視窗裡放置文字，須建立一個tkinter裡的Label物件

在創立Label物件時，會有幾個常見的屬性可以調整。

1. 第一個參數為Label要放置的容器物件名稱，目前只有主視窗win。tkinter還有其他容器物件如: Frame, Canvas
2. text為Label欲顯示的文字內容
3. font為字體的設定(字體樣式, 字體大小, 粗體字)
4. bg可調整Label背景顏色

```python
label_1 = tk.Label(win, # Label要顯示的容器
                  text="Python tkinter", # label的文字內容
                  font=("Microsoft JhengHei UI", 20, "bold"), # 設定字體樣式
                  bg="skyblue" # 背景顏色)
```

建立好文字(Label)物件後，要指示Label須放置在win容器中的哪個位置

> tkinter元件的放置位置主要有三種方法: pack, grid, place
{: .prompt-tip }

這邊先說明place的使用方式，place是透過絕對座標(x, y)來指定元件位置，用法如下:

```python
label_1.place(x=80, y=100) # 值的單位為pixel
```

![tksimpleGUI2.JPG](/assets/img/postpictures/tkinter/tksimpleGUI2.jpg)

## 按鈕元件與觸發事件

要新增按鈕在視窗上，須建立一個tkinter的Button物件，建立參數如下: 

1. 第一個參數為button要放置的容器名稱，這裡放在主視窗win
2. text為button欲顯示的文字內容
3. width, height分別可以調整Button的長寬大小
4. command為點擊此按鈕所要觸發的事件，輸入要執行的函式名

```python
btn_1 = tk.Button(win, # Button要顯示的視窗
                  text="按鈕一", # Button的文字內容
                  width=15, # Button的寬度
                  height=1, # Button的高度
                  command=btn_cmd) # 按鈕點擊事件，呼叫function名

btn_1.place(x=80, y=150) # 按鈕放置位置
```

button事件須建立一個function進行觸發，這裡簡單建立一個function名為btn_cmd，按鈕點擊後會改變按鈕的文字。

```python
def btn_cmd():
    btn_1.config(text="你已點擊此按鈕!") # 利用config方法更改Button物件的text屬性
```

> tkinter的所有元件物件幾乎都有`config`方法，可以在物件已建立後再透過config修改物件的其他屬性。
{: .prompt-tip }

**點擊按鈕前**

![tksimpleGUI3.JPG](/assets/img/postpictures/tkinter/tksimpleGUI3.jpg)

**點擊後…**

![tksimpleGUI4.JPG](/assets/img/postpictures/tkinter/tksimpleGUI4.jpg)

## 文字輸入窗格

使用者有時需要輸入一些文字與程式溝通或傳送資料，在tkinter中可以使用Entry物件或Text物件，建立文字輸入的窗格

- Entry: 高度為一行文字的輸入窗格，較適合用來輸入較短的資料，如: 姓名、帳戶號碼等
- Text: 可以自由定義height與width屬性，適合用來輸入較長的資料，如: 一段文字，網頁HTML等等

這邊先使用Entry進行演示，建立參數如下:

1. 基本上元件都要指定一個容器物件放置，這邊也是使用主視窗win
2. 設定輸入的字體樣式
3. 輸入窗格的長度

```python
entrybox_1 = tk.Entry(win,
                     font=("Microsoft JhengHei UI", 10, "bold"), # 設定字體樣式
                     width=10)
entrybox_1.place(x=80, y=150)
```

執行後，就會多出一個文字輸入窗格啦~

![tksimpleGUI5.png](/assets/img/postpictures/tkinter/tksimpleGUI5.png)

- 綜合運用
1. 使用者輸入一些東西到文字窗格
2. 點擊按鈕，將文字窗格的內容顯示在使視窗的Label上

按鈕的觸發事件要進行修改，首先要先讀取entrybox_1上使用者輸入的內容，再將其內容config到label_1的`text`屬性。

```python
def btn_cmd():
		usertext = entrybox_1.get() # 利用get()方法擷取entry的內容
		label_1.config(text=usertext)
```
- 完整程式碼

```python
import tkinter as tk

def btn_cmd():
    usertext = entrybox_1.get() # 利用get()方法擷取entry的內容
    label_1.config(text=usertext)

win = tk.Tk() 
win.title("Python tkinter") # 賦予標題名稱
win.geometry("400x300") # 設定視窗大小 
win.iconbitmap("PythonIcon.ico") # 同目錄下的PythonIcon.ico
win.config(bg="skyblue") # 背景顏色

# 視窗文字設定
label_1 = tk.Label(win, # Label要顯示的容器
                  text="Python tkinter", # label的文字內容
                  font=("Microsoft JhengHei UI", 20, "bold"), # 設定字體樣式
                  bg="skyblue" # 背景顏色
                  )
label_1.place(x=80, y=100)

# 按鈕觸發事件
btn_1 = tk.Button(win, # Button要顯示的視窗
                  text="按鈕一", # Button的文字內容
                  width=10, # Button的寬度
                  height=1, # Button的高度
                  command=btn_cmd) # 按鈕點擊事件，呼叫function名

btn_1.place(x=180, y=150) # 按鈕放置位置

entrybox_1 = tk.Entry(win,
                     font=("Microsoft JhengHei UI", 10, "bold"), # 設定字體樣式
                     width=10)
entrybox_1.place(x=80, y=150)

win.mainloop() # 維持視窗顯示的迴圈，沒有這行程式碼視窗無法維持顯示
```


執行後…

![tksimpleGUI6.png](/assets/img/postpictures/tkinter/tksimpleGUI6.png)

輸入完文字點擊按鈕後…

![tksimpleGUI7.png](/assets/img/postpictures/tkinter/tksimpleGUI7.png)

以上是簡單的視窗程式開發分享，後續會再更新其他tkinter更進階的視窗元件與應用~

### Reference

1. **Python tkinter官方文件** [https://docs.python.org/3/library/tk.html](https://docs.python.org/3/library/tk.html)