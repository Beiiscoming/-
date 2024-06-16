# 如何定義一波疫情

## 概要
- 現行未有一套標準流程來定義疫情區間，故疫情上難以用不同時間區間的資料做比較與研究。故本專案希望提供一套演算法來定義一波疫情，以比較各地區數波疫情之下的各種資訊，例如不同波數疫情之下的防疫疲乏。
- 本專案參考並優化此篇論文的方法，提供了全新的演算法來訂義疫情區間：
Chatterjee, A., Gupta, D., & Jain, S. (2021). COVID-19 Forecasting Using Time Series Models: An Exploratory Study on Indian Data. Journal of Epidemiology and Global Health, 11(4), 443-452. [Link to paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8583384/)



## 分析工具與資料說明

### 程式語言：python
  - 網路爬蟲：Requests（下載網頁）＋ BeautifulSoup（解析網頁）
  - 數據平滑處理：Pandas
  - 找出局部最小值、局部最大值：SciPy
  - 計算中位數和過濾局部最大值：Numpy
  - 畫趨勢圖：matplotlib、seaborn
    
### 資料說明：
  - 本專案套用了46個國家,在2020/02/01~2022/03/25期間的每日新增案例數。
  - 資料來源：每日新增病例數 (單位為「數量/萬」) (https://ourworldindata.org/covid-cases)

## 演算法與階段描述

![定義一波疫情流程 drawio](https://github.com/Beiiscoming/Exploring-Pandemic-Fatigue/assets/171532457/39079db2-23d6-4371-ab1c-ed657675902d)

### Step1 : 網路爬蟲
- 每次執行此專案時，重跑一次爬蟲程式，確保每天的資料是可以被更新的。

### Step2 : 平滑處理
- 因為每日新增案例數在趨勢線上容易有上下跳動的情況（如下參考圖深藍長條圖），故透過簡單移動平均法讓數據趨勢的變動較為平滑（如下參考圖淺綠折線圖），以消除短期的上下波動，突顯出長期趨勢。
（ 圖片來源：https://covid-19.nchc.org.tw/2023_city_confirmed.php ）
- 將每日新增病例數用移動平均法計算七天平均。
  ![image](https://github.com/Beiiscoming/Exploring-Pandemic-Fatigue/assets/171532457/682b082a-9595-4b70-92d8-275fa3f059ca)

### Step3 : 取出每週的平均值
- 將平滑後的數據取週平均。

### Step4 : 確定第一波疫情的起始日期
- 第一波的起始日期即為該國開始記錄病例數的第一天。

### Step5 : 找出整個資料庫的局部最小值、局部最大值
- 找出局部最小值和局部最大值。
- 令當下資料點為 ε：

    若此時 ε 的左右相鄰一個資料點值皆大於 ε，則令 ε 為局部最小值；
    若此時 ε 的左右一個資料點值皆小於 ε，則令 ε 為局部最大值。

### Step6 : 過濾掉小於每日新增病例數資料庫中位數的區域最大值
- 我希望定義出的疫情區間，以整體上來說會是趨勢明顯的大高起，因此過濾掉小於中位數的局部最大值，避免程式誤認小高起也是疫情，也避免噪音數據的干擾。

### Step7 : 定義起始週
- 起始週定義為局部最小值所對應的週數。

### Step8 : 定義終止週
- 我希望程式算出的疫情區間，與人們認定的疫情區機相似。
- 經過不同參數的測試，將終止週定義為局部最大值降至10%時所對應的週數，因10%這個參數較符合個人肉眼認定的疫情區間。
- 如果程式沒有算出最後的終止週，會直接將最後一波的終止週設為研究資料的最後一週。
  
### Step9 : 求起始日跟終止日
- 求得起始週和中止週的最中間的日期，即為疫情起始日期和疫情終止日期。

## 分析結果
- 這是一個定義疫情區間的演算法，不過還需要更多的佐證來證明這個演算法的通用性是夠高的，例如應收集不同人們主觀認定疫情波段的準確日期區間來跟程式定義的疫情區間做比較。
- 除了疫情波段的分析，也可以應用於心率數據分析或是股票趨勢分析等需要時間序列分析的專案。
