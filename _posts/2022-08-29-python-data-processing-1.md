---
title: Python 基本資料處理-list複製與排序
author: Miigun
date: 2022-08-29 05:33:00 +0800
categories: [Python, data]
tags: [Python, data]
math: true
pin: false
---

此系列跟大家分享本人在Python開發上，遇到一些特殊資料處理的方法，**只是個人的小小經驗，僅供參考**。


## list串列的複製問題

  list在Python中很常用來儲存大量資料的物件，有時候會遇到需要複製list的問題，會希望複製後的新list不會被原本的list影響。
  例如:  下方程式碼執行結果，b_list記憶體位址指向a_list，所以當a_list元素改變時，b_list也會跟隨改變。
    
  ```python
  a_list = [[1,2,3], 3, 3, 1]
  b_list = a_list # 此行是將b_list變數指向a_list的位址
  a_list[0][1] = 0 # 當a_list內元素改變時，b_list也會跟著改變
  
  #result
  # a_list = [[1,0,3], 3, 3, 1]
  # b_list = [[1,0,3], 3, 3, 1]
  ```
  實際去查詢a_list, b_list的記憶體位址確實也是相同的

  ```python
  # id 查詢記憶體編號
  id(a_list)
  Out[20]: 1802798924800
  id(b_list)
  Out[21]: 1802798924800
  ```
    
  但如果a_list重新宣告新的list，則新a_list會指派到新的記憶體，b_list則繼續留在原來的記憶體。
    
  ```python
  a_list = [[1,2,3], 3, 3, 1]
  b_list = a_list
  a_list = [1, 2, 3] # 重新宣告一個list
  
  #result
  # a_list = [1, 2, 3]
  # b_list = [[1,2,3], 3, 3, 1]
  ```

  用id查詢後兩變數位址確實也不相同

  ```python
  # id 查詢記憶體編號
  id(a_list)
  Out[25]: 1802797521024
  
  id(b_list)
  Out[27]: 1802794696128
  
  ```

  > 會發生上述這種情況的原因是，在Python中任何變數都是**Reference Type**，變數宣告是透過指標指向值的位址，使用等號 `=` 將"舊變數" 賦值給"新變數"就會是複製"舊變數"的**位址(address)**而不是"舊變數"的**值(value)**，這也就是為何Python是一個**動態型別**的程式語言。
  {: .prompt-tip }
    
  Python有提供一個安全複製方法`copy`，確認複製到的是值(value)而不是位址(addreass)
    
  ```python
  import copy
  a_list = [1, 2, 3, 4]
  b_list = copy.copy(a)
  
  id(a_list) 
  OUT[31]: 2340117387520
  
  id(b_list)
  OUT[32]: 2340087539584
  # 兩者id位址不同，確定是copy到value
  ```
    
  如果是二維以上的list或是list內部具有多層級物件，則複製必須使用`deepcopy`
    
  ```python
  import copy
  a_list = [[2,3],[1,4]]
  b_list = copy.deepcopy(a_list)
  # copy.copy淺複製只能處理一維的list複製，copy.deepcopy深複製則可以處理二維以上的list複製
  ```
    
## list特殊排序

若有兩個陣列分別為a_list, b_list，a_list做大小排序，a_list內元素對應b_list的元素也要跟隨著排列，有幾種方法可以實現...

- 第一種方法
    
  可以將a_list排序的index紀錄下來，再套用在b_list。

  EX: 
  
  ```python
  a_list = [3,1,4,6,2,1]
  b_list = [4,5,2,1,2,3]
  ```
  
  a 做 小→大 排序
  
  ```python
  #sort(a)
  index_list = sorted(range(len(a)), key = lambda k: a[k])
  """說明: range(len(a)) = [0, 1, 2, 3, 4, 5]內元素依依帶入lambda，迭代出a排列小至大的key 
  list 再利用key list排序range(len(a))存入index_list"""
  # OUT: 
  a = [1,1,2,3,4,6]
  index_list = [1,5,4,0,2,3]
  ```
    
  index_list為a_list排序紀錄下來的index變動位置，b_list可以依照index_list進行排列
    
  ```python
  temp = []
  for i in index_list:
      temp.append(b[i])
  b = copy(temp)
  ```
    
- 第二種方法
    
  先利用zip將a_list與b_list鏈起來
  
  ```python
  zip_list = list(zip(a, b))
  # OUT: [(3, 4), (1, 5), (4, 2), (6, 1), (2, 2), (1, 3)]
  ```
  
  再透過sorted排序zip_list的子串列zip_list[0]
  
  ```python
  arr_zip_list = sorted(zip_list, key=lambda z: zip_list[0])
  # OUT: [(1, 5), (1, 3), (2, 2), (3, 4), (4, 2), (6, 1)]
  ```
  
  最後再解zip
  
  ```python
  unzip_list = list(zip(*arr_zip_list))
  # OUT: [(1, 1, 2, 3, 4, 6), (5, 3, 2, 4, 2, 1)]
  unzip_list[1] # 即為跟著a_list排序完成的b_list
  ```
    
## list元素交換
    
  若有a, b兩陣列，可直接透過程式碼第4行的方法進行交換，不需藉由第三變數暫存，但numpy.array無法支援此方法。
    
  ```python
  a = [[1,1],[2,2]]
  b = [[3,3],[4,4]]
  
  a[0], b[0] = b[0], a[0]
  
  # a = [[3, 3], [2, 2]]
  # b = [[1, 1], [4, 4]]
  ```

  以上為筆者過去利用Python進行資料分析時，處理list資料可能會遇到的一些坑，在此分享處理方法，**僅供參考~**
    
    

### Reference

1. **Pythonic 實踐：實用的 python 慣用法整** [https://mropengate.blogspot.com/2020/07/pythonic-python.html](https://mropengate.blogspot.com/2020/07/pythonic-python.html)
2. **Python - 淺複製(shallow copy)與深複製(deep copy)** [https://ithelp.ithome.com.tw/articles/10221255](https://ithelp.ithome.com.tw/articles/10221255)
