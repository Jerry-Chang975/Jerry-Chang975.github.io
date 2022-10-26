---
title: 基因演算法介紹-概念篇
author: Miigun
date: 2022-10-27 00:15:00 +0800
categories: [Optimization, GA]
tags: [基因演算法, 最佳化]
math: true
pin: false
---

複習一下碩論用到的最佳化搜尋演算法… **只是個人的小小經驗，僅供參考**。

---

## 基本介紹

基因演算法(Genetic Algorithm; GA)是用來解決最佳化問題的一種演算法。顧名思義，演算過程中以數學方法模仿生物各個演化的階段，而從中迭代出最適應於當前環境的個體(解)，主要流程如下，本文將以此流程逐步說明GA的運作方式。

![GA.drawio.png](/assets/img/postpictures/genetic_algorithm/GA.drawio.png){: width="350"}

1. 生成第一代初始群體，由多個隨機產生的初始解(個體)組成。
2. 計算群體內每一個體對於環境(目標解)的適應程度(fitness)。
3. 檢查目前群體內是否已有達到目標解的個體，若未達標則進入演算法核心階段(選擇、交換及突變)。
    - 選擇 (Selection): 在群體中根據個體的適應度，篩選要保留下來的個體(通常保留一半數量)，其餘淘汰。
    - 交換 (Crossover): 從保留下的個體中隨機挑選兩個做為親代，倆倆之間進行基因序列的片段交換，產生出新子代。
    - 突變 (Mutation): 交換結束後的新群體將有機會發生突變，突變發生時，個體的基因序列將隨機改變。
4. 完成以上三階段後，將重新評估新群體內每一個體的適應度，若仍無個體達到目標解則持續進行三階段的迭代，直到找出達標的個體。



## 生成初始群體

在進行演算法迭代前，需產生多個隨機生成的個體，基於這些初始個體迭代出最佳解。這些個體在GA中通常以二進碼(binary)的序列來表示(也可以用其他形式，如: 十進碼)，其目的是讓個體能夠在演算法中的交換與突變階段順利運行。

![group.jpg](/assets/img/postpictures/genetic_algorithm/group.jpg){: width="450"}

## 適應度評估

初始產生的第一代個體或是進行完GA迭代後產生的新個體，都需要進行適應度評估(fitness)，來確認演算法是否已經找出符合目標的最佳解。

- **編碼與解碼**
    
    如果最佳化問題為連續問題，例如: 求解函數最最小值，其解為十進位小數的形式，這就必須將二進碼序列解碼(decode)成十進位形式的數值；相反的，由十進位轉換至二進位則為編碼(encode)。同時，二進碼序列的形式也可以對個體進行限制，避免迭代過程中脫離最佳化所設立的拘束條件(constraint)。
    
    > 例如: 可以將8bits二進碼之值以內插法表示解的限制範圍 -10.0 ~ 10.0，11111111表示為10.0; 00000000表示為-10.0，10111011透過內插法計算則可約表示為 4.6，如此只要確保GA迭代過程個體符合8bits-二進碼形式，即可限制解在-10 ~10 範圍內進行搜尋。
    {: .prompt-info }
    
    ![binary.jpg](/assets/img/postpictures/genetic_algorithm/binary.jpg){: width="450"}
    

- **適應函數**
    
    適應函數(fitness function)即用來計算每一個體所屬的適應度，當個體計算得出的適應度越佳時，則代表該個體離目標解越接近。可根據目標解設定合適的適應度，使演算法具備終止條件避免無止盡的迭代運算。
    
    >例如: 求$f(x)=x^2+4x-50$ 函數之最小值(舉個簡單例子，雖然這題可以秒解XD)，可以直接把此函數作成適應函數，求解目標適應值設定為-40。若個體經解碼後帶入此適應函數之輸出值≤-40，則判定找出最佳解並停止運算。

    
    ![fitness.jpg](/assets/img/postpictures/genetic_algorithm/fitness.jpg){: width="450"}
    
    > 雖然-40 並非$f(x)=x^2+4x-50$ 之最小值(-54)，但在無從得知最佳解的問題中，我們可以給予一個可接受的目標值讓演算法進行求解。此外，也有可能發生給予的目標值在問題中永遠找不出來(如在上述問題中目標值設為-60 )，這時就必須將演算法進行改良: 新增另一個停止條件，ex: 如果連續100次迭代都無法找出滿足目標解之個體，則停止迭代並回傳目前群體中最好的解。
    {: .prompt-info }

## 選擇

選擇(selection)是進入GA核心的首要步驟，藉由適應度篩選出可以繼續存活的個體(通常保留半數)，常見的選擇方法有: 菁英式選擇、分裂式選擇、平衡式選擇與輪盤式選擇等。

- **菁英式:** 保留群體中適應度較佳的個體
- **分裂式:** 同時保留適應度較佳與較差的個體，中間的部分則淘汰
- **平衡式:** 與分裂式相反，保留中間淘汰頭尾

![selection.jpg](/assets/img/postpictures/genetic_algorithm/selection.jpg){: width="450"}

- **輪盤式:** 根據適應度優劣分配被選中的機率，適應度越佳選中機率越高。好比輪盤遊戲，適應度越好代表輪盤佔比的面積越大，愈容易被指針選中。
    
    ![roulette_method.jpg](/assets/img/postpictures/genetic_algorithm/roulette_method.jpg){: width="350"}
    

> 前三個方法可直接從適應度進行篩選，輪盤式則需要再進一步換算機率，但篩選效果會比較好
{: .prompt-tip }

## 交換

經選擇保留下來的個體將有機會被挑選為親代，進行複製後兩兩成對進行基因序列片段之交換(crossover)，進而產生出新子代。常見的交換方法有: 單點交換、雙點交換與多點(遮罩)交換。

- **單點交換:** 在親代基因序列中，隨機選擇一位置作為切斷點，再將切下來的片段兩兩互換

    ![單點交換.jpg](/assets/img/postpictures/genetic_algorithm/sp_crossover.jpg){: width="450"}

- **雙點交換:** 在親代基因序列中，隨機選擇兩個位置(不重複)作為切斷點，再將切下來的片段兩兩互換
    
    ![雙點交換.jpg](/assets/img/postpictures/genetic_algorithm/dp_crossover.jpg){: width="450"}
    

- **多點(遮罩)交換:** 先隨機產生與個體相同長度的二進位序列作為遮罩，在親代基因序列中，對應於遮罩位置之值為1的點位進行交換。#若不用遮罩也可以直接隨機選擇序列中數個隨機點進行互換
    
    ![多點.jpg](/assets/img/postpictures/genetic_algorithm/mp_crossover.jpg){: width="450"}
    

## 突變

交換後無論是親代或子代有機率(≤5%)發生突變(mutation)，其主要用意是為了提高群體多樣性，避免迭代過程容易陷入局部解(local optimum)的情況。常見的突變方法: 單點突變、多點突變等

- **單點突變:** 在個體的基因序列中隨機選擇一位置，改變其值(二進位1→0, 0→1)。
    
    ![sp_mutation.jpg](/assets/img/postpictures/genetic_algorithm/sp_mutation.jpg){: width="400"}
    
- **多點突變:** 在個體的基因序列中隨機選擇多個位置，改變其值
    
    ![mp_mutation.jpg](/assets/img/postpictures/genetic_algorithm/mp_mutation.jpg){: width="400"}
    

> 選擇、交換與突變除了以上常見的方法，也可以自己針對問題的特性設計一個新方法。另外，在選擇演算法的參數時，可以透過參數組合搜尋找出效果最好的參數。常見找最佳參數組合的方法有: 窮舉法、隨機搜尋法等。
{: .prompt-info }

## 小結

本文透過GA的迭代流程，逐步說明了每個步驟中所執行的方法與目的。基因演算法的特性適合用來解離散的最佳化問題，如: 旅行推銷員、最佳組合與排程問題等等；藉由二進碼序列的編碼與解碼，則可進一步用來解連續數值的最佳化問題。

下一篇文章就會介紹如何寫出GA的code來解決最佳化問題~

本文若有任何講錯的地方再請大方地Email告知我 😀

## Reference

1. Worthy N. Martin, William M. Spears, 2001, *Foundations of Genetic Algorithms*, Morgan Kaufmann, Inc.
2. GA Note - 基因演算法的世界: [https://ithelp.ithome.com.tw/users/20111679/ironman/2577](https://ithelp.ithome.com.tw/users/20111679/ironman/2577)
3. Wiki: [https://zh.wikipedia.org/zh-tw/遗传算法](https://zh.wikipedia.org/zh-tw/%E9%81%97%E4%BC%A0%E7%AE%97%E6%B3%95)