# LeetCode 面試學習地圖（整理版）

## 學習原則

不要只停留在 LeetCode 題目本身，而是理解：

```text
題目
↓
資料結構 / 演算法
↓
真實工作應用
↓
Project 實作
```

\---

# LeetCode 分類地圖

# Array Top 10
- [1. Two Sum (#1)](#1-two-sum-1)
- [2. Best Time to Buy and Sell Stock (#121)](#2-best-time-to-buy-and-sell-stock-121)
- [3. Product of Array Except Self (#238)](#3-product-of-array-except-self-238)
- [4. Merge Sorted Array (#88)](#4-merge-sorted-array-88)
- [5. Remove Duplicates from Sorted Array (#26)](#5-remove-duplicates-from-sorted-array-26)
- [6. Maximum Subarray (#53)](#6-maximum-subarray-53)
- [7. Rotate Array (#189)](#7-rotate-array-189)
- [8. Move Zeroes (#283)](#8-move-zeroes-283)
- [9. Find Pivot Index (#724)](#9-find-pivot-index-724)
- [10. Majority Element (#169)](#10-majority-element-169)

# Hash Table Top 10
- [1. Contains Duplicate (#217)](#1-contains-duplicate-217)
- [2. Contains Duplicate II (#219)](#2-contains-duplicate-ii-219)
- [3. Valid Anagram (#242)](#3-valid-anagram-242)
- [4. Two Sum (同上)(#1)](#1-two-sum-1)
* [5. Group Anagrams (#49)](#5-group-anagrams-49)
* Longest Consecutive Sequence (#128)
* Happy Number (#202)
* Isomorphic Strings (#205)
* Word Pattern (#290)
* Intersection of Two Arrays (#349)
* Top K Frequent Elements (#347)

  
# 常見 Pattern 對照表

|題目|Pattern|
|-|-|
|1 Two Sum|Hash Map (查找補數)|
|Best Time to Buy and Sell Stock|Greedy|
|Product of Array Except Self|Prefix / Suffix|
|Merge Sorted Array|Two Pointers|
|Remove Duplicates|Fast / Slow Pointer|
|Maximum Subarray|DP / Kadane|
|Rotate Array|Reverse Technique|
|Move Zeroes|Fast / Slow Pointer|
|Find Pivot Index|Prefix Sum|
|169 Majority Element|Boyer Moore Voting Algorithm (消除配對)|
|217 Contains Duplicate|HashSet（檢查是否出現過）|
\---

## 1\. Two Sum (#1)

### 核心技術

* Hash Table
* Dictionary
* Lookup Optimization

### 複雜度

* Brute Force: O(n²)
* Hash Table: O(n)

### 工作應用

* Cache
* API Response Cache
* JWT / Session 驗證
* Excel 資料比對

\---

## 2\. Best Time to Buy and Sell Stock (#121)

### 核心技術

* Greedy
* Running Minimum
* One Pass Scan

### 關鍵觀念

維護：

* 歷史最低價格
* 目前最大獲利

### 複雜度

* Brute Force: O(n²)
* Greedy: O(n)
  
### 工作應用

* KPI 成長率分析
* CPU Usage 分析
* Streaming Data

真正學到的是,不要看所有組合O(n²), 而是維護狀態:目前最佳解, 這是大量Streaming Data的核心概念
### Technical: Greedy 應用: 
1. 活動選擇問題, 
2. 分數背包問題 (Fractional Knapsack Problem)-問題描述：有一個背包可以裝載固定重量的物品。每種物品有各自的「總價值」與「總重量」。物品可以被切碎裝入（例如：黃金、沙子）。如何裝填才能讓背包內的總價值最高？, 
3. 霍夫曼編碼 (Huffman Coding)-問題描述：在檔案壓縮中，如何幫不同出現頻率的字元編碼，使得壓縮後的檔案體積最小？每次都挑選頻率最小的兩個節點合併，建立一個新節點，其頻率為兩者相加。建立二元樹並編碼

* 貪婪演算法失敗的反例
零錢找零問題 (Coin Change Problem)-當硬幣面額不是倍數關係（例如 [11, 5, 1] 找 15 元），貪婪演算法會失效。此時必須改用動態規劃 (Dynamic Programming, DP) 來確保找到全域最佳解。動態規劃的核心思路動態規劃將大問題拆解成小問題。我們用一個陣列 dp[i] 來代表湊出 i 元所需要的最少硬幣數量。
0/1 背包問題 (0-1 Knapsack Problem)

\---

## 3\. Product of Array Except Self (#238)

### 核心技術

* Prefix Product
* Suffix Product

### Pattern

```text
Answer = Prefix × Suffix

Intermediate: result[] = Left[]
```

### 工作應用

* 統計分析
* 聚合計算
* 分散式系統可用率分析

\---

## 4\. Merge Sorted Array (#88)

### 核心技術

* Two Pointers
* In-place Modification
* Reverse Thinking
* 
Key:Two Pointers（雙指標）+ In-place Array Modification (空間: O(1)) + Reverse Thinking
核心技巧: 從後面開始放, 所以最大的數字應該先放最後面。

### 關鍵觀念

從尾端開始合併。

### 複雜度

* Time: O(m+n)
* Space: O(1)

# Two Pointers 常見應用有哪些？
Two Pointers 的核心：用兩個索引追蹤資料狀態，避免重複掃描。
通常把 O(n²) 降成 O(n)。

\---

## 5\. Remove Duplicates from Sorted Array (#26)

### 核心技術

* Fast Pointer
* Slow Pointer

### 關鍵字

* Sorted Array
* Remove Duplicates
* In-place

### 複雜度

可以用HashSet, 但Space = O(n) 且題目要求In-Place
* Time: O(n)
* Space: O(1)

這題真正考的是：已排序陣列 + In-place 修改 + Two Pointers
看到下面特徵時，要立刻想到它：
sorted array
remove duplicates
in-place
O(1) extra space

直接聯想到：
Fast Pointer 掃描
Slow Pointer 維護答案區間

\---

## 6\. Maximum Subarray (#53)

面試最常出現的 Dynamic Programming (DP) 入門題
核心觀念: 對每個位置：要嘛延續之前的子陣列, 要嘛從自己重新開始
Kadane's Algorithm 這題最經典解法。 其實是 DP 的空間優化版。原本O(n) space 變成：O(1) space
dp[i]=max(nums[i],dp[i-1] + nums)

面試看到什麼關鍵字要想到這題？
Maximum
Minimum
Largest Sum
Contiguous
Subarray
Continuous

通常先想：
Dynamic Programming: 大問題變小問題, 保存小問題答案, 避免重複計算 ex:Fibonacci => dp[i] = Max(nums[i], dp[i-1] + nums[i])
Kadane Algorithm: 只依賴dp[i-1] => currentMax = Max(nums[i], currentMax + nums[i]) => 空間優化 O(n) => O(1)

這是 LeetCode Array 類題目中最重要的 DP Pattern 之一，也是許多進階區間最佳化題的基礎。

### 核心技術

* Dynamic Programming
* Kadane Algorithm

### DP

```text
dp\[i] = max(nums\[i], dp\[i-1] + nums\[i])
```

### Kadane

```text
currentMax = max(nums\[i], currentMax + nums\[i])
```

### 工作應用

* 最大收益區間
* 股票分析
* KPI 分析

\---

## 7\. Rotate Array (#189)
是一道很典型的：

Array Manipulation + Two Pointers + Reverse Technique

很多人第一眼會想到搬移元素，但最佳解其實是利用 Reverse（三次反轉）
1. 暴力解 => 很慢 O(n × k)
2. 額外開陣列temp => Time: O(n), Space: O(n)
3. 最佳解：Reverse Algorithm => Time: O(n), Space: O(1)

### 核心技術

* Reverse Technique
* Two Pointers
* Array Manipulation

### 最佳解

三次反轉：

1. 全部反轉
2. 前 k 個反轉
3. 剩餘部分反轉

### 複雜度

* Time: O(n)
* Space: O(1)

\---

## 8\. Move Zeroes (#283)
是 LeetCode 最經典的 Two Pointers（快慢指標） 題目之一。它跟你前面做的：

Remove Duplicates from Sorted Array
Merge Sorted Array

其實是同一個 Pattern。
1. 額外temp => Space O(n)
2. Two Pointers => slow下一個非0元素應放的位置, fast負責掃描陣列
3. 更進階寫法（交換）:當看到非零值時直接交換

為什麼這題和 Remove Duplicates 很像?
本質完全相同：
Fast 掃描
Slow 維護有效區域

### 核心技術

* Fast / Slow Pointer
* In-place Array Compaction

### Pattern

```text
Fast 掃描
Slow 維護有效區域
```

### 工作應用

* ETL 資料清洗
* Log 壓縮
* 資料過濾

\---
## 9\. Find Pivot Index (#724)
這題是 LeetCode Array 題中的經典 Prefix Sum（前綴和） 題型。

pivot 左邊元素總和 = pivot 右邊元素總和

### 核心技術

* Prefix Sum 維護前面累積總和
* Running Sum 一路維護目前狀態 而非重複計算
* Space Optimization => leftSum和total 最佳解 Space O(1), Time O(n)

* 面試看到以下關鍵字：
Left Sum
Right Sum
Balance
Equilibrium
Pivot
Range Sum
Subarray Sum

優先想到：
Prefix Sum
* Pivot Index = Total Sum - Left Sum - Current Value = Right Sum


### 工作應用
* 財務分析 - 收入支出 找到平衡點
* KPI 分析 - 找到數據分界點
* Log / Traffic 分析 - 流量累積, 前後負載平衡點

\---

## 10\. Majority Element (#169)
給定一個整數陣列 nums，找出出現次數超過 ⌊n/2⌋ 的元素。
題目保證：
Majority Element 一定存在, 陣列長度至少為 1

### 核心技術

* Hash Table : 利用 Hash Map 記錄每個數字出現次數 => Time O(n), Space O(n)
* Sort : 若某個元素出現超過一半, 排序後依定佔據中間位置 =>Time O(n log n), Space O(1) or  O(log n)
* Best Solution => Boyer Moore Voting Algorithm : 因為 Majority Element 出現次數 > n/2, 所以它的數量一定比其他所有元素加起來還多。 => Time: O(n), Space: O(1)
Boyer-Moore 的本質就是「不同數字互相抵消」，因為 Majority Element 的數量超過其他所有元素總和，所以最後剩下的一定是它。

### 工作應用
* Log 分析（最常見）
  1. 日誌分析
  2. 網路封包分析
  3. * Click Stream 分析
* 網路投票系統
* Streaming Data (資料流)
* 分散式系統監控
* AI / Machine Learning

###為什麼面試愛考？
因為它考的是一個重要思想： Pair Cancellation (配對消除)
如果面試官問：
Boyer-Moore 在實務上有什麼用途？
你可以回答：
它適合用在大量資料串流（Streaming Data）、Log 分析、監控系統、分散式系統事件統計等場景。當我們只需要找出是否存在一個出現超過 50% 的元素時，Boyer-Moore 可以只用 O(1) 記憶體完成統計，比 HashMap 更適合超大規模資料處理。
這就是這題真正想考的核心價值，不是演算法本身，而是 「用常數空間找出資料流中的 Majority」。


\---


## Hash Table

## 1\. Contains Duplicate (#217)
Hash Set 為什麼這麼重要？
這題其實是在考：

「快速判斷元素是否出現過」

這是工程上極常見的問題。

### 核心技術
* Hash Set

### 工作應用
* 帳號註冊檢查
* 資料庫 Unique Constraint
* Log 過濾重複資料
* 網頁爬蟲 - 避免重複抓取
* UI Automation 測試 - 驗證資料是否重複
* Telemetry / Log Analytics - 檢查是否有節點重複上報

### 面試回答模板

如果面試官問：為什麼這題用 HashSet？

可以回答：
因為題目本質是在判斷元素是否曾經出現過。HashSet 的查找時間平均為 O(1)，遍歷一次陣列即可完成，因此整體時間複雜度 O(n)，是最優解。該思路廣泛應用於帳號唯一性檢查、資料去重、快取查詢、爬蟲 URL 去重、事件重複處理與 UI 測試資料驗證等場景。

看到：
判斷是否出現過
判斷是否重複
快速查找

應立即想到：
HashSet
HashMap

217. Contains Duplicate
HashSet
↓
219. Contains Duplicate II
HashMap + Index
↓
1. Two Sum
HashMap
↓
49. Group Anagrams
HashMap
↓
128. Longest Consecutive Sequence
HashSet

\---

## 2\. Contains Duplicate II (#219)
給定整數陣列 nums 和整數 k，判斷是否存在兩個不同 index i 與 j
219 的關鍵不是找 duplicate，而是把 abs(i-j)<=k 轉換成「大小為 k 的 Sliding Window」。

### 核心技術
* Hash Map 記錄最後出現位置
Time: O(n)
Space: O(n)
* 更好的解法: Sliding Window + HashSet 只需要關心距離 k 內的元素
Time: O(n)
Space: O(k)

### 工作應用
* API 請求去重複（Deduplication）ex只保留某段時間的提交
* Fraud Detection（詐欺偵測）最近N分鐘的交易
* Log 監控系統
* 網路封包防重送
* Event Hub / Kafka
* UI Automation
* Cyber Security

* 如果面試官問：
219 Contains Duplicate II 在實務上有什麼用途？
  
你可以回答：
它本質上是在固定範圍（Sliding Window）內判斷資料是否重複，因此常應用於事件去重（Deduplication）、API 重送檢測、Kafka/Event Hub 訊息處理、網路封包檢查、詐欺交易偵測以及資安登入監控等場景。核心思想是只保留最近 N 筆或最近 N 秒的資料，利用 HashSet 在 O(1) 時間內檢查是否出現重複事件。

這就是為什麼這題經常被歸類為 Sliding Window + Hashing 的經典實戰題。

\---

## 3\. Valid Anagram (#242)
給定兩個字串 s 和 t，判斷 t 是否為 s 的 Anagram。
Anagram（字母異位詞）表示：
字元種類完全相同
每個字元出現次數完全相同
順序可以不同

### 核心技術
面試官想考什麼？
1. Frequency Counting 排序法 Sort

Time: O(n log n)
Space: O(n)

2. Hash Table 計數法
var map = new Dictionary<char, int>();

Time: O(n)
Space: O(k)

3. Array Mapping：26 個字母陣列（最優）
 int[] count = new int[26];  所以根本不用 Dictionary。
count[s[i] - 'a']++;
count[t[i] - 'a']--;

=> 核心不是排序，而是「比較每個字元的出現次數是否完全相同」，最佳解為 Frequency Count + int[26]

Time O(n)
Space O(1)

### 工作應用
* 應用1：搜尋引擎拼字校正
* 應用2：文字分析(NLP)
文字探勘
關鍵字分析
搜尋最佳化
* 應用3：作弊偵測
* 應用4：資料一致性驗證

\---

## 5\. Group Anagrams (#49)
### 如何思考
如何判斷兩個字串是不是 Anagram？

排序後的字串
=
Anagram 的唯一 Key

### 核心技術
Hash Table + Canonical Key
Key     Value
--------------------
aet  -> eat, tea, ate
ant  -> tan, nat
abt  -> bat

1. Sorting
Time: O(n × k log k)
Space: 

2. Character Count（進階）
不排序也可以
Time: O(n × k)
Space: 

### 面試會考什麼？
把複雜物件轉成唯一 Key

這是一種非常重要的技巧：
Normalization
Canonical Representation

### 工作應用
* 檔案內容去重
* 搜尋引擎關鍵字分析
* 商品標籤分類
* Fraud Detection

### 這題考什麼能力？
1. Hash Table
Dictionary<TKey,TValue>

快速分組。

2. String Manipulation
Array.Sort(chars);

建立標準 Key。

3. Grouping

本質其實是：
Group By 概念。

有點像 SQL：
SQL
SELECT *
FROM Words
GROUP BY SortedWord

\---
* Longest Consecutive Sequence (#128)
* Happy Number (#202)
* Isomorphic Strings (#205)
* Word Pattern (#290)
* Intersection of Two Arrays (#349)
* Top K Frequent Elements (#347)



### 工作應用

* Cache
* Session 管理
* Redis
* Dictionary 查詢

### Project

Achievement Service

\---

## Two Pointers

* Valid Palindrome (#125)
* Two Sum II (#167)
* Container With Most Water (#11)
* 3Sum (#15)
* Remove Duplicates (#26)
* Move Zeroes (#283)
* Reverse String (#344)
* Squares of Sorted Array (#977)
* Merge Strings Alternately (#1768)
* Reverse Vowels (#345)

### Project

Chat Filter Service

\---

## Sliding Window

* Longest Substring Without Repeating Characters (#3)
* Minimum Window Substring (#76)
* Permutation in String (#567)
* Maximum Average Subarray (#643)
* Fruit Into Baskets (#904)
* Find All Anagrams (#438)
* Longest Repeating Character Replacement (#424)
* Max Consecutive Ones III (#1004)
* Subarray Product Less Than K (#713)
* Minimum Size Subarray Sum (#209)

### Project

Real-time Analytics

\---

## Linked List

* Reverse Linked List (#206)
* Linked List Cycle (#141)
* Merge Two Sorted Lists (#21)
* Remove Nth Node (#19)
* Palindrome Linked List (#234)
* Middle of Linked List (#876)
* Reorder List (#143)
* Add Two Numbers (#2)
* Intersection of Linked Lists (#160)
* Copy List with Random Pointer (#138)

### Project

Job Queue Engine

\---

## Stack

* Valid Parentheses (#20)
* Min Stack (#155)
* Daily Temperatures (#739)
* Evaluate Reverse Polish Notation (#150)
* Basic Calculator (#224)
* Decode String (#394)
* Remove K Digits (#402)
* Asteroid Collision (#735)
* Largest Rectangle in Histogram (#84)
* Next Greater Element (#496)

### Project

Workflow Engine

\---

## Binary Search

* Binary Search (#704)
* Search Insert Position (#35)
* Search Rotated Array (#33)
* First Bad Version (#278)
* Koko Eating Bananas (#875)
* Peak Index Mountain Array (#852)
* Find Minimum Rotated Array (#153)
* Capacity to Ship Packages (#1011)
* Time Based Key Value Store (#981)
* Median of Two Arrays (#4)

### Project

Matchmaking Search

\---

## Tree

* Max Depth Binary Tree (#104)
* Same Tree (#100)
* Invert Binary Tree (#226)
* Level Order Traversal (#102)
* Validate BST (#98)
* Lowest Common Ancestor (#236)
* Diameter Binary Tree (#543)
* Path Sum (#112)
* Construct Tree (#105)
* Serialize Tree (#297)

### Project

Permission System

\---

## Graph

* Number of Islands (#200)
* Clone Graph (#133)
* Course Schedule (#207)
* Pacific Atlantic (#417)
* Graph Valid Tree (#261)
* Network Delay Time (#743)
* Reconstruct Itinerary (#332)
* Word Ladder (#127)
* Alien Dictionary (#269)
* Cheapest Flights (#787)

### Project

Friend System

\---

## Heap / Priority Queue

* Kth Largest Element (#215)
* Top K Frequent Elements (#347)
* Find Median Stream (#295)
* Merge K Lists (#23)
* Task Scheduler (#621)
* Last Stone Weight (#1046)
* IPO (#502)
* K Closest Points (#973)
* Reorganize String (#767)
* Furthest Building (#1642)

### Project

Global Leaderboard

\---

## Backtracking

* Subsets (#78)
* Combination Sum (#39)
* Permutations (#46)
* Word Search (#79)
* N Queens (#51)
* Generate Parentheses (#22)
* Letter Combinations (#17)
* Sudoku Solver (#37)
* Restore IP Addresses (#93)
* Palindrome Partitioning (#131)

### Project

Skill Builder

\---

## Dynamic Programming

* Climbing Stairs (#70)
* House Robber (#198)
* Coin Change (#322)
* Longest Increasing Subsequence (#300)
* Longest Common Subsequence (#1143)
* Decode Ways (#91)
* Word Break (#139)
* Unique Paths (#62)
* Partition Equal Subset Sum (#416)
* Best Time Stock II (#122)

### 工作應用

* 最佳化
* 路徑規劃
* 成本計算
* 推薦系統

### Project

Matchmaking Optimizer

\---

# Microsoft Gaming 面試必刷

## 必刷 20 題

1. Two Sum
2. Group Anagrams
3. Top K Frequent Elements
4. Longest Substring Without Repeating Characters
5. Valid Parentheses
6. Daily Temperatures
7. Reverse Linked List
8. Linked List Cycle
9. Binary Search
10. Validate BST
11. Level Order Traversal
12. Lowest Common Ancestor
13. Number of Islands
14. Clone Graph
15. Course Schedule
16. Kth Largest Element
17. Subsets
18. Permutations
19. Coin Change
20. Longest Increasing Subsequence

## 進階必刷

* 3Sum
* Minimum Window Substring
* Word Ladder
* Network Delay Time
* Find Median Stream
* Word Break
* Longest Common Subsequence
* Task Scheduler
* Serialize Tree
* Cheapest Flights

