# Chapter 14 - 元件耦合性

本章會討論到關於元件耦合性的三個原則：

- 無環依賴原則
  - The Acyclic Dependencies Priciple
  - 簡稱 ADP
- 穩定依賴原則
  -  The Stable Dependencies Principle
  - 簡稱 SDP
- 穩定抽象原則
  - The Stable Abstractions Principle
  - 簡稱 SAP

## 無環依賴原則 (ADP)

    在元件的依賴關係圖中不允許出現環

![](/CleanArchitecture/resource/14-2.png)

如14-2圖中，`Database`的發佈必須相容於`Entities`，而遵循這個依賴關係，我們也必須讓`Authorizer`也相容，同時`Database`跟`Authorizer`都相容於`Interractors`，這使任何改動與發佈要付出很大的成本。這些元件之間的改動及行為必須要完全一致，事實上他們已經變成了同一個大元件。

### 解除依賴環

1. 使用依賴反轉原則(DIP)，新增介面至`Entities`中
![](/CleanArchitecture/resource/14-3.png)

2. 新建立一個`Entities`與`Authorizer`都依賴的元件(`Permission`)，不過此解決方案在存在改變的前提下，元件的結構是不穩定的。<br />
   ![](/CleanArchitecture/resource/14-4.png)<br />
   不過此解決方案在存在改變的前提下，元件的結構是不穩定的。

## 穩定依賴原則 (SDP)

    朝著穩定的方向進行依賴

透過符合共同封閉原則(CCP)，我們設計出一些元件是可變的，我們預期他變化，但其他模組只要建立一個對該模組了依賴，則該元件會變得難以更改。透過遵循穩定依賴原則(SDP)，我們可以確保那些易於更改的元件不會被那些比他們難以更改的元件依賴。

### 穩定性

圖14-5展現了一個穩定的元件X。因為有3個元件依賴他，所以有個3合理的理由不去改變它。而X不依賴於任何元件，因此所有外部影響都不會驅使X做出改變。我們稱X是無依賴性的(independent)<br/>

![](/CleanArchitecture/resource/14-5.png)<br/>

圖14-6展現了一個非常不穩定的元件Y。沒有任何元件依賴Y，而且Y還依賴其他3個元件，所以他具有三個淺在的因素可能驅使自身的更改。我們稱Y是依賴性的。<br/>

![](/CleanArchitecture/resource/14-6.png)<br/>

### 計算穩定性的度量

$I=\frac{Fan-out}{Fan-in + Fan-out}$ <br/>

- $I$ : 不穩定性(Instability), $I\in[0,1]$
- Fan-out : 輸入依賴度，意即有多少元件依賴著要測量的元件。(就是有多少箭頭往要測量的元件指的意思)
- Fan-in : 輸出依賴度，亦即要測量的元件依賴多少外部元件。(就是有幾根往元件外面指的箭頭的意思)
  
如範例，$Fan-out=1,Fan-in=3,I=\frac{1}{1+3}=\frac{1}{4}$ <br/>

![](/CleanArchitecture/resource/14-7.png) <br/>

當$I=1$值為1時，意味著沒有任何的元件依賴該元件，而該元件卻依賴於其他元件，此為一個元件的最不穩定狀態。<br/>
當$I=0$時，意味著所有元件都依賴於該元件，而該元件卻不依賴於任何其他元件，此為一個元件的最穩定狀態<br/>

    穩定依賴原則(SDP)的規定是，一個元件的I值必須大於他所依賴的元件的I值。換言之，I值必須順著依賴的方向減少

### 並非所有元件都應該是穩定的

如同共同封閉原則(CCP)，我們將易變的模組與類別封裝至同一個元件，並且期望該元件是容易改動的。事實上，我們希望我們設計出來的元件架構中，有一些元件是不穩定的，有些元件是穩定的，在不違反穩定依賴原則(SDP)的狀態之下。

## 穩定抽象原則 (SAP)
