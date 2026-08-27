我建議不要只停留在 LeetCode 題目，而是要了解：

題目
↓
考什麼資料結構
↓
真實工作怎麼應用
↓
可以做什麼 Project


Array（陣列）Top 10
題目
Two Sum → Hash Table
Best Time to Buy and Sell Stock → Greedy
Product of Array Except Self → Prefix/Suffix
Merge Sorted Array → Two Pointers (從尾端合併)
Remove Duplicates from Sorted Array → Fast/Slow Pointers

1. Two Sum (#1)
Beginner: Use 2 for loop to check nums[i] + nums[j] = target => O(n²)
Intermediate: Hash Table => Use Dictionary to check. => Time Complexity : O(n), Space Complexity: O(n)

Technical: Hash Table / Dictionary
Pattern: Lookup Optimization
Application: 快取查詢, API Response Cache, JWT / Session 驗證, Excel 匯入資料比對

2. Best Time to Buy and Sell Stock (#121)

Brute Force: O(n²)
Intermediate: O(n) 1 for loop

Pattern: Running Minimum, One Pass Scan
Application: KPI 最大成長率, 系統監控CPU Usage 
真正學到的是,不要看所有組合O(n²), 而是維護狀態:目前最佳解, 這是大量Streaming Data的核心概念
Technical: Greedy 應用: 
活動選擇問題, 
分數背包問題 (Fractional Knapsack Problem)-問題描述：有一個背包可以裝載固定重量的物品。每種物品有各自的「總價值」與「總重量」。物品可以被切碎裝入（例如：黃金、沙子）。如何裝填才能讓背包內的總價值最高？, 
霍夫曼編碼 (Huffman Coding)-問題描述：在檔案壓縮中，如何幫不同出現頻率的字元編碼，使得壓縮後的檔案體積最小？每次都挑選頻率最小的兩個節點合併，建立一個新節點，其頻率為兩者相加。建立二元樹並編碼

貪婪演算法失敗的反例
零錢找零問題 (Coin Change Problem)-當硬幣面額不是倍數關係（例如 [11, 5, 1] 找 15 元），貪婪演算法會失效。此時必須改用動態規劃 (Dynamic Programming, DP) 來確保找到全域最佳解。動態規劃的核心思路動態規劃將大問題拆解成小問題。我們用一個陣列 dp[i] 來代表湊出 i 元所需要的最少硬幣數量。
0/1 背包問題 (0-1 Knapsack Problem)

3. Product of Array Except Self (#238)
Key: Prefix / Suffix Pattern
Beginner: Product Except Self = Prefix Product × Suffix Product
Intermediate: result[] = Left[]

4. Merge Sorted Array (#88)

Key:Two Pointers（雙指標）+ In-place Array Modification (空間: O(1)) + Reverse Thinking
核心技巧: 從後面開始放, 所以最大的數字應該先放最後面。

# Two Pointers 常見應用有哪些？
Two Pointers 的核心：用兩個索引追蹤資料狀態，避免重複掃描。
通常把 O(n²) 降成 O(n)。

5. Remove Duplicates from Sorted Array (#26)
Key: Two Pointers 又稱：Fast & Slow Pointer
時間複雜度：O(n)
空間複雜度：O(1)
可以用HashSet, 但Space = O(n) 且題目要求In-Place

這題真正考的是：已排序陣列 + In-place 修改 + Two Pointers
看到下面特徵時，要立刻想到它：
sorted array
remove duplicates
in-place
O(1) extra space

直接聯想到：
Fast Pointer 掃描
Slow Pointer 維護答案區間

6. Maximum Subarray (#53)
面試最常出現的 Dynamic Programming (DP) 入門題
核心觀念: 對每個位置：要嘛延續之前的子陣列, 要嘛從自己重新開始
Kadane's Algorithm 這題最經典解法。 其實是 DP 的空間優化版。原本O(n) space 變成：O(1) space
dp[i]=max(nums[i],dp[i-1] + nums)

面試看到什麼關鍵字要想到這題？
如果看到：
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

---
Rotate Array (#189)
Move Zeroes (#283)
Find Pivot Index (#724)
Majority Element (#169)

工作應用

常見於：

排行榜
遊戲數據統計
即時監控
資料分析

練習 Project
Leaderboard System

功能：

Top100玩家
排序
排名更新

學到：

Plain Text
Array
Sorting
Search


---

Hash Table Top 10
題目
Contains Duplicate (#217)
Valid Anagram (#242)
Two Sum (#1)
Group Anagrams (#49)
Longest Consecutive Sequence (#128)
Happy Number (#202)
Isomorphic Strings (#205)
Word Pattern (#290)
Intersection of Two Arrays (#349)
Top K Frequent Elements (#347)
工作應用
Plain Text
1
快取(Cache)
2
Session管理
3
玩家資料查詢
4
Redis
5
Dictionary
6
 
Show more lines
Project
Achievement Service
Plain Text
1
玩家
2
↓
3
事件
4
↓
5
成就
Show more lines

使用：

C#
1
Dictionary<PlayerId,List<Achievement>>
Show more lines
Two Pointers Top 10
題目
Valid Palindrome (#125)
Two Sum II (#167)
Container With Most Water (#11)
3Sum (#15)
Remove Duplicates (#26)
Move Zeroes (#283)
Reverse String (#344)
Squares of Sorted Array (#977)
Merge Strings Alternately (#1768)
Reverse Vowels (#345)
工作應用
Plain Text
1
文字處理
2
搜尋引擎
3
字串過濾
Show more lines
Project
Chat Filter Service

功能：

Plain Text
1
髒話過濾
2
文字正規化
Show more lines
Sliding Window Top 10
題目
Longest Substring Without Repeating Characters (#3)
Minimum Window Substring (#76)
Permutation in String (#567)
Maximum Average Subarray (#643)
Fruit Into Baskets (#904)
Find All Anagrams (#438)
Longest Repeating Character Replacement (#424)
Max Consecutive Ones III (#1004)
Subarray Product Less Than K (#713)
Minimum Size Subarray Sum (#209)
工作應用
Plain Text
1
即時監控
2
串流分析
3
遊戲行為分析
Show more lines
Project
Real-time Analytics

分析：

Plain Text
1
最近5分鐘登入人數
2
最近10分鐘活躍玩家
3
``
Show more lines
Linked List Top 10
題目
Reverse Linked List (#206)
Linked List Cycle (#141)
Merge Two Sorted Lists (#21)
Remove Nth Node (#19)
Palindrome Linked List (#234)
Middle of Linked List (#876)
Reorder List (#143)
Add Two Numbers (#2)
Intersection of Linked Lists (#160)
Copy List with Random Pointer (#138)
工作應用
Plain Text
1
Queue
2
Task Scheduler
3
Job System
Show more lines
Project
Job Queue Engine
Plain Text
1
Background Job
2
Task Queue
Show more lines
Stack Top 10
題目
Valid Parentheses (#20)
Min Stack (#155)
Daily Temperatures (#739)
Evaluate Reverse Polish Notation (#150)
Basic Calculator (#224)
Decode String (#394)
Remove K Digits (#402)
Asteroid Collision (#735)
Largest Rectangle in Histogram (#84)
Next Greater Element (#496)
工作應用
Plain Text
1
Undo
2
Expression Parser
3
Workflow Engine
Show more lines
Project
Workflow Engine
Plain Text
1
Approval Flow
2
Rule Engine
Show more lines
Binary Search Top 10
題目
Binary Search (#704)
Search Insert Position (#35)
Search Rotated Array (#33)
First Bad Version (#278)
Koko Eating Bananas (#875)
Peak Index Mountain Array (#852)
Find Minimum Rotated Array (#153)
Capacity to Ship Packages (#1011)
Time Based Key Value Store (#981)
Median of Two Arrays (#4)
工作應用
Plain Text
1
排行榜查詢
2
搜尋引擎
3
索引系統
Show more lines
Project
Matchmaking Search
Plain Text
1
Elo Rating
2
快速搜尋
Show more lines
Tree Top 10
題目
Max Depth Binary Tree (#104)
Same Tree (#100)
Invert Binary Tree (#226)
Level Order Traversal (#102)
Validate BST (#98)
Lowest Common Ancestor (#236)
Diameter Binary Tree (#543)
Path Sum (#112)
Construct Tree (#105)
Serialize Tree (#297)
工作應用
Plain Text
1
選單
2
權限管理
3
組織結構
Show more lines
Project
Permission System
Plain Text
1
Company
2
├ Department
3
│ ├ Team
4
│ └ Team
Show more lines
Graph Top 10
題目
Number of Islands (#200)
Clone Graph (#133)
Course Schedule (#207)
Pacific Atlantic (#417)
Graph Valid Tree (#261)
Network Delay Time (#743)
Reconstruct Itinerary (#332)
Word Ladder (#127)
Alien Dictionary (#269)
Cheapest Flights (#787)
工作應用
Plain Text
1
社交網路
2
好友系統
3
推薦系統
Show more lines
Project
Friend System
Plain Text
1
玩家好友圖
2
推薦好友
3
共同好友
Show more lines
Heap / Priority Queue Top 10
題目
Kth Largest Element (#215)
Top K Frequent Elements (#347)
Find Median Stream (#295)
Merge K Lists (#23)
Task Scheduler (#621)
Last Stone Weight (#1046)
IPO (#502)
K Closest Points (#973)
Reorganize String (#767)
Furthest Building (#1642)
工作應用
Plain Text
1
排行榜
2
排程系統
3
推薦系統
Show more lines
Project
Global Leaderboard
Plain Text
1
Top100
2
Top1000
3
Top全球
Show more lines
Backtracking Top 10
題目
Subsets (#78)
Combination Sum (#39)
Permutations (#46)
Word Search (#79)
N Queens (#51)
Generate Parentheses (#22)
Letter Combination (#17)
Sudoku Solver (#37)
Restore IP Addresses (#93)
Partition Palindrome (#131)
工作應用
Plain Text
1
AI探索
2
路徑搜尋
3
規則組合
Show more lines
Project
Skill Builder
Plain Text
1
RPG技能配置系統
Show more lines
Dynamic Programming Top 10
題目
Climbing Stairs (#70)
House Robber (#198)
Coin Change (#322)
Longest Increasing Subsequence (#300)
Longest Common Subsequence (#1143)
Decode Ways (#91)
Word Break (#139)
Unique Paths (#62)
Partition Equal Subset Sum (#416)
Best Time Stock II (#122)
工作應用
Plain Text
1
最佳化
2
路徑規劃
3
成本計算
4
推薦系統
Show more lines
Project
Matchmaking Optimizer

功能：

Plain Text
1
玩家評分
2
延遲
3
地區
4
技能
Show more lines

輸出：

Plain Text
1
最佳配對結果
Show more lines
Microsoft Gaming 最值得優先刷的 30 題
必刷
Plain Text
1
Two Sum
2
Group Anagrams
3
Top K Frequent Elements
4
Longest Substring Without Repeating Characters
5
Valid Parentheses
6
Daily Temperatures
7
Reverse Linked List
8
Linked List Cycle
9
Binary Search
10
Validate BST
11
Level Order Traversal
12
Lowest Common Ancestor
13
Number of Islands
14
Clone Graph
15
Course Schedule
16
Kth Largest Element
17
Subsets
18
Permutations
19
Coin Change
20
Longest Increasing Subsequence
Show more lines
進階必刷
Plain Text
1
3Sum
2
Minimum Window Substring
3
Word Ladder
4
Network Delay Time
5
Find Median Stream
6
Word Break
7
LCS
8
Task Scheduler
9
Serialize Tree
10
Cheapest Flights
Show more lines

這份 120 題（12 類 × 10 題）已足以覆蓋 Microsoft Gaming、Microsoft、Amazon、Meta 大部分 Software Engineer 面試所需的資料結構與演算法核心能力。