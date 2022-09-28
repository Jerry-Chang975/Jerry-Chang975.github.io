---
title: 影像辨識(1)-利用OpenCV進行影像處理
author: Miigun
date: 2022-09-28 00:20:00 +0800
categories: [Python, OpenCV]
tags: [Python, OpenCV, 影像辨識,]
math: true
pin: false
---

最近在進行影像辨識的相關專案開發，所以記錄一下學習的過程與筆記~ **只是個人的小小經驗，僅供參考**。

---

## 影像辨識

影像辨識是AI領域中最常被應用的一塊，如: 車牌辨識、人臉辨識與物件偵測等。

> **影像資料前處理** 是影像辨識中最重要的一環，因訓練通常需要大量資料，且未處理過的圖片資料量大，會使一般DNN模型訓練時間過長、產生overfitting等等問題，所以在訓練前通常會經過圖片濾波、降維等處理。
{: .prompt-info }


**卷積神經網路(Convolution Neural Networks; CNN)**為現今影像辨識最常使用的ML模型，在輸入前幾層神經網路將二維圖片與二維過濾矩陣進行卷積，輸出一個濾波後的圖片，一個CNN模型可能會有好幾層卷積層，目的是盡可能找圖片中重要的特徵。卷積層結束後還會有池化層(pooling layer)將圖片降維、全連接層(DNN)等組成完整的CNN，後續有機會再詳細介紹卷積神經網路的架構 。

![cnnConv.png](/assets/img/postpictures/opencv/cnnConv.png)

*卷積層運作示意圖* [Source](https://www.ibm.com/cloud/learn/convolutional-neural-networks)

## 影像處理-OpenCV

OpenCV為Intel主導開發的電腦視覺處理套件，支援的語言有C++, Java, Python等等。

### 以下就先用OpenCV(Python)來進行常見的影像處理:

先用pip安裝opencv的套件

```bash
pip install opencv-python
```

import opencv library 與讀取圖片`imread()`

```python
import cv2 as cv
# 讀取圖片檔存入img，第二個參數是讀取的圖片模式
img = cv.imread('hamster.jpg', cv.IMREAD_UNCHANGED)

"""
IMREAD_COLOR 載入型式為8-bit的RGB圖片(預設)
IMREAD_UNCHANGED 除RGB外，alpha透明度也載入
IMREAD_GRAYSCALE 載入型式為灰階
"""

```

cv.imread讀取到的圖片資料會存成numpy的ndarray型態，便於後續圖像處理運算

```python
type(img)
# Output: numpy.ndarray
```

`resize()`函式可以重新調整影像大小

```python
# 將圖片重新調整大小-->(256, 256)
img = cv.resize(img, (256, 256))
```

show出圖片視窗

```python
cv.imshow('hamster', img) # 第一個參數為視窗名稱
cv.waitKey() # 等待按鍵關閉圖片循環
cv.destroyWindow('hamster') # 關閉特定圖片視窗
```

![OpenCV_img1.jpg](/assets/img/postpictures/opencv/OpenCV_img1.jpg)

**將彩色圖片轉成灰階**

```python
# 可以在讀取圖片時，透過**IMREAD_GRAYSCALE** 參數轉換成灰階
img = cv.imread('cvTest.jpg', cv.IMREAD_GRAYSCALE)

# 或是利用cvtColor()函式與COLOR_BGR2GRAY參數進行轉換
img = cv.imread('cvTest.jpg', cv.IMREAD_UNCHANGED)
gray_img = cv.cvtColor(img, cv.COLOR_BGR2GRAY) 
```

![OpenCV_grayImg.jpg](/assets/img/postpictures/opencv/OpenCV_grayImg.jpg)

### 常見的圖片處理方法: 模糊化、邊緣化、膨脹、侵蝕

- **模糊化 (blur)**
    
    模糊化常用於去除圖片的雜訊，常見方法有: 平均模糊化、高斯模糊化。
    
    OpenCV要實現平均模糊化可使用`blur()`函式
    
    ```python
    img = cv.blur(img, (5, 5)) # 第二個參數為模糊化kernel矩陣大小，越大模糊效果越好
    ```
    
    高斯模糊化與平均模糊化差別在於kernel矩陣的權值為常態分布，使用高斯模糊可以讓影像較不容易失真，在OpenCV中引用`GaussianBlur()`函式
    
    ```python
    img = cv.GaussianBlur(img, (7, 7), 3) # 第二個參數也是kernal矩陣大小，大小值須為奇數
    # 第三個參數為矩陣權值標準差，越大模糊加權越大，效果越好
    ```
    
    ![OpenCV_blurImg.jpg](/assets/img/postpictures/opencv/OpenCV_blurImg.jpg)
    
- **邊緣化 (邊緣偵測 edge detection)**
    
    顧名思義就是將影像的邊緣特徵凸顯出來，引用`Canny()`函式
    
    ```python
    # 第二、三參數為判斷是否為邊緣的閥值
    # 低於第二參數不產生邊緣，高於第三參數一定產生邊緣
    # 介於兩者之間會由演算法判斷
    
    img = cv.Canny(img, 50, 120) 
    ```
    
    ![OpenCV_CannyImg.jpg](/assets/img/postpictures/opencv/OpenCV_CannyImg.jpg)
    
    在進行邊緣化前通常會先將圖片模糊化，避免將過多邊緣雜訊
    
    ```python
    img = cv.GaussianBlur(img, (7, 7), 3)
    img = cv.Canny(img, 50, 120) 
    ```
    
    ![OpenCV_CannyImg1.jpg](/assets/img/postpictures/opencv/OpenCV_CannyImg1.jpg)
    
    只剩眼睛跟耳朵的輪廓了… XD
    
- **膨脹 (dilate)**
    
    膨脹可以想像成將影像較高的灰階值(較白、較亮)進行擴張，將圖片較亮的地方會變大，使用OpenCV的`dilate()`函式實現
    
    ```python
    # 先設定kernel，越大效果越好
    kernel = np.one((3, 3), dtype="uint8")
    img = cv.dilate(img, kernel)
    ```
    
    ![OpenCV_dilateImg.jpg](/assets/img/postpictures/opencv/OpenCV_dilateImg.jpg)
    
    倉鼠眼睛的小白點變大了~
    
- **侵蝕 (erode)**
    
    侵蝕剛好跟膨脹相反，影像中較低的灰階值會進行擴張(被黑暗侵蝕的感覺@@)，可使用OpenCV的`erode()`函式實現
    
    ```python
    # 一樣需先設定kernel，越大效果越好
    kernel = np.one((3, 3), dtype="uint8")
    img = cv.erode(img, kernel)
    ```
    
    ![OpenCV_erodeImg.jpg](/assets/img/postpictures/opencv/OpenCV_erodeImg.jpg)
    
    倉鼠的眼睛變更黑了(有點可怕XD)

影像做完適當的前處理後，更能凸顯重要的特徵，增加影像辨識的成功率。後續將會再繼續說明建立影像辨識模型的方法~
    

### Reference

1. OpenCV官方文件: [https://docs.opencv.org/4.x/d2/d96/tutorial_py_table_of_contents_imgproc.html](https://docs.opencv.org/4.x/d2/d96/tutorial_py_table_of_contents_imgproc.html)
2. [*https://www.ibm.com/cloud/learn/convolutional-neural-networks*](https://www.ibm.com/cloud/learn/convolutional-neural-networks)
3. [https://zhuanlan.zhihu.com/p/110330329](https://zhuanlan.zhihu.com/p/110330329)