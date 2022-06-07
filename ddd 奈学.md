## 架构师 VS 技术大牛

**架构师**  

能将业务转换为技术，产生价值

**技术大牛**

只懂技术，不懂业务

**业务架构师**

- 分析业务流程
- 理解业务规则
- 挖掘业务痛点

**顶级架构师**

能够将技术落地，产生业务价值

**如何成为顶级架构师**

![image-20210820173152859](https://raw.githubusercontent.com/mengziup/pic/main/image-20210820173152859.png)

方法：DDD

![image-20210820174119867](https://raw.githubusercontent.com/mengziup/pic/main/image-20210820174119867.png)

![image-20210824120753754](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824120753754.png)

![image-20210824120822501](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824120822501.png)

*軟件退化的根本原因：不是需求變更，而是沒有適時對軟件進行調整，解耦，擴展 DDD就是這樣一個方法*

**DDD思想**

*軟件的本質是對現實世間的模擬*

![image-20210824105843085](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824105843085.png)

**統一語言**

使用用戶的業務術語來描述需求，具體的做法是「事件風暴法」

**事件風暴法**

![image-20210824160039829](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824160039829.png)

1. 和客戶一起梳理領域事件，所謂領域事件是已經發生的非常重要，需要記錄的事件，圍繞領域事件進行設計和建設

   ![image-20210824115314012](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824115314012.png)

   ![image-20210824115536509](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824115536509.png)

![image-20210823122241495](https://raw.githubusercontent.com/mengziup/pic/main/image-20210823122241495.png)

2. 分析領域事件相關練元素之間的關係，聚合？ 

   **聚合** 即整體和部分的關係，部分的生命週期在整體之內

   **領域建模**

   模型 --> 限界上下文-->上下文地圖

   ![image-20210823122940386](https://raw.githubusercontent.com/mengziup/pic/main/image-20210823122940386.png)



**設計實現**

- 數據庫設計

- 程序設計

  - 充血模型

  - 貧血模型

    Martin Fowler提出，作爲反模式

​    服務  實體 值對象

 傳統微服務：煙筒模式

現在的微服務：小而專

![image-20210823163939459](https://raw.githubusercontent.com/mengziup/pic/main/image-20210823163939459.png)

![image-20210824095921718](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824095921718.png)

**四色建模法**

四種原型：

- 時間原型 粉色  表示在某個時刻或者時段內發生的事件
- 角色原型 黃色 
- 參與者-地點-物品原型 綠色
- 描述原型 藍色
- 

建模過程：

- 尋找事件
- 尋找對象
- 尋找人事物
- 角色
- 人事物相關信息



![image-20210823180415680](https://raw.githubusercontent.com/mengziup/pic/main/image-20210823180415680.png)

![image-20210824101327441](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824101327441.png)

![image-20210824101903736](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824101903736.png)

![image-20210824102220530](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824102220530.png)



防腐層：

![image-20210824102338438](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824102338438.png)

![image-20210824104122425](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824104122425.png)





![image-20210824104600413](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824104600413.png)





**事件風暴/四色建模法->業務領域建模->軟件設計和數據庫設計：充血模型 貧血模型 聚合 工廠 倉庫**





## 問題

設計工具：

手寫-->viso

ddd和oo的關係？



3NF？

列式存儲

整潔架構

六邊形架構

CQRS

軟件的本質是對真實世間的模擬

軟件設計七大原則

*單一職責原則 SRP ：一個職責是軟件變化的一個原因*

原文分析法

事件風暴法

四色建模



![image-20210824155623526](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824155623526.png)

![image-20210824155633553](https://raw.githubusercontent.com/mengziup/pic/main/image-20210824155633553.png)

理解業務->領域建模->變更發生->模型變更->開發

事件風暴識別領域事件及其相關元素和元素之間的關係->整理建模->限界上下文->軟件設計和數據庫設計
