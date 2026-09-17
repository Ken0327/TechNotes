# C#題庫 - LINQ 篇

# 主題
LINQ (Language Integrated Query)

---

# 問題 1
## 什麼是 Deferred Execution（延遲執行）？

本指南深入探討 C# 與 LINQ 中最核心的特性之一：**延遲執行（Deferred Execution）**。內容涵蓋基本概念、優缺點分析、常見陷阱的解決方法，以及 `yield return` 與 Entity Framework 的進階應用。

---

## 解法
Deferred Execution（延遲執行）是 LINQ 最重要的特性之一。

當撰寫 LINQ 查詢時，查詢並不會立即執行，而是先建立查詢邏輯（Query Definition）。
真正執行查詢的時機是在列舉（Enumeration）資料時，例如使用：

- foreach
- ToList()
- ToArray()
- First()
- Count()

等終止運算（Terminal Operations）。

其核心概念為：

> 建立查詢 ≠ 執行查詢

因此資料來源若在查詢建立後發生變化，查詢結果也可能跟著改變。

## 範例

```csharp
List<int> numbers = new List<int> { 1, 2, 3 };

IEnumerable<int> query = numbers.Where(x => x > 1);

numbers.Add(4);

foreach(var item in query)
{
    Console.WriteLine(item);
}
```

輸出：

```text
2
3
4
```

原因：

```csharp
numbers.Add(4);
```

發生在查詢執行之前。

---

## 注意事項

1. LINQ 預設採用 Deferred Execution。
2. 每次列舉 IEnumerable 都可能重新執行查詢。
3. 查詢複雜時可能造成重複計算。
4. 若需要快照資料可使用 ToList() 或 ToArray()。

---

## 參考資料

- Microsoft Learn - LINQ Deferred Execution
- C# In Depth

---

## 使用時機與優缺點

### 什麼時機會使用？
1. **處理大型資料集**：例如從資料庫讀取數百萬筆記錄，或讀取數 GB 的 Log 檔案。不需要一次載入記憶體，而是「用多少、拿多少」。
2. **動態拼接查詢條件**：當系統需要根據前端使用者的勾選條件（如篩選器）來動態拼接 SQL 或 LINQ 時。可以先定義基礎查詢，再根據條件疊加，最後一併發送。
3. **實作資料串流處理**：資料像流水一樣經過多個關卡（A → B → C），每個關卡只定義規則，直到最後一刻才啟動處理流程。

### 優缺點直接比較

| 特性 | 延遲執行（Deferred Execution） | 立即執行（Immediate Execution） |
| :--- | :--- | :--- |
| **記憶體佔用** | 🟢 **極低**。只儲存查詢邏輯，不預先載入整份結果。 | 🔴 **較高**。會立刻在記憶體中建立一份完整的複本。 |
| **資料即時性** | 🟢 **最新**。每次讀取時，都會抓到當下最新的原始資料。 | 🔴 **過期**。抓到的是「執行當下」的快照（Snapshot）。 |
| **重複執行成本**| 🔴 **高**。每次使用 `foreach` 讀取，查詢就會**重新運算**一次。 | 🟢 **低**。結果已經存在記憶體中，重複讀取不花運算成本。 |
| **錯誤發生時機**| 🔴 **延後**。語法寫錯時當下不會有事，等到 `foreach` 執行時才拋出異常。 | 🟢 **立刻**。如果有錯誤（如連線失敗），在定義那行就會立刻拋出。 |

---

## 常見陷阱與解決方法：重複執行（Multiple Enumeration）

延遲執行最容易讓人踩雷的地方在於**效能浪費**。如果同一個延遲查詢被讀取多次，它就會重新運算多次。

### ❌ 修正前的錯誤程式碼（重複查詢資料庫兩次）
```csharp
// 這只是食譜，還沒做菜
var query = dbContext.Users.Where(u => u.IsActive); 

if (query.Any()) // 第一次執行：去資料庫數人數 (SELECT COUNT...)
{
    foreach (var user in query) // 第二次執行：又去資料庫撈資料 (SELECT * FROM...)
    {
        Console.WriteLine(user.Name);
    }
}
```

### 🟢 修正後的正確程式碼（使用立即執行快取結果）
要解決此問題，必須加上 `.ToList()`、`.ToArray()` 或 `.ToDictionary()` 將查詢結果強制**立即快取**。
```csharp
// 加上 .ToList() 轉為「立即執行」，此時只發送一次 SQL 抓出所有符合條件的資料
var activeUsers = dbContext.Users.Where(u => u.IsActive).ToList(); 

// 後續的所有操作，都是直接讀取記憶體裡的 activeUsers 列表，不會再動到資料庫
if (activeUsers.Any()) 
{
    foreach (var user in activeUsers) 
    {
        Console.WriteLine(user.Name);
    }
}
```

---

## 4. 進階討論

### 主題一：使用 `yield return` 自訂延遲執行

在 C# 中，除了 LINQ 之外，當一個方法的傳回值是 `IEnumerable<T>` 且內部使用了 `yield return` 關鍵字時，編編譯器會自動建立狀態機，實作出延遲執行的行為。**呼叫方法時內部不會執行**，直到外部進行迭代。

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        Console.WriteLine("1. 準備呼叫方法");
        var numbers = GetNumbersGreaterThanTwo(); // 此時方法內部程式碼「完全不會」執行
        
        Console.WriteLine("2. 方法呼叫完畢，準備開始 foreach 迭代");

        foreach (var num in numbers)
        {
            Console.WriteLine($"---> 主程式收到數字: {num}");
        }
    }

    static IEnumerable<int> GetNumbersGreaterThanTwo()
    {
        Console.WriteLine("[方法內部] 開始篩選資料...");
        var list = new List<int> { 1, 2, 3, 4 };
        foreach (var n in list)
        {
            if (n > 2)
            {
                Console.WriteLine($"[方法內部] 找到符合條件的數字: {n}，準備 yield return");
                yield return n; // 暫停方法，把控制權交回給 Main 的 foreach
            }
        }
    }
}
```

#### 輸出結果觀察
```text
1. 準備呼叫方法
2. 方法呼叫完畢，準備開始 foreach 迭代
[方法內部] 開始篩選資料...
[方法內部] 找到符合條件的數字: 3，準備 yield return
---> 主程式收到數字: 3
[方法內部] 找到符合條件的數字: 4，準備 yield return
---> 主程式收到數字: 4
```
* **分析**：主程式每 `foreach` 一次，`yield return` 方法就往下走一步，實現了極致的記憶體節約。

---

### 主題二：Entity Framework 中的延遲執行與 SQL 轉換

在 Entity Framework (EF / EF Core) 當中，延遲執行扮演著極為關鍵的角色，它對應的型態通常是 **`IQueryable<T>`**。

當你寫下 LINQ 語法時，C# 實際上是在建立一個 **表達式樹（Expression Tree）**（即 SQL 的藍圖）。因為延遲執行，你可以一關一關地動態拼接條件，最後才由 EF 翻譯成一條最優化的 SQL 發送給資料庫。

#### 動態拼接範例
```csharp
// 1. 基本查詢：此時只是 Expression Tree，完全沒碰資料庫
IQueryable<User> query = dbContext.Users;

// 2. 動態拼接條件：依然只是在豐富 Expression Tree
if (onlyActive)
{
    query = query.Where(u => u.IsActive);
}
if (!string.IsNullOrEmpty(searchName))
{
    query = query.Where(u => u.Name.Contains(searchName));
}

// 3. 觸發執行：此時 EF 會把上述所有條件結合成一條 SQL 語法發送
// 例如：SELECT * FROM Users WHERE IsActive = 1 AND Name LIKE '%...%'
var result = query.ToList(); 
```

#### 🚨 致命的效能陷阱：`IEnumerable` vs `IQueryable`
如果不小心把 `IQueryable` 轉型成了 `IEnumerable`，延遲執行的篩選行為會從**資料庫端**轉移到**網頁伺服器端**，造成嚴重的效能災難：

```csharp
// ❌ 錯誤示範
IEnumerable<User> query = dbContext.Users; // 轉成了 IEnumerable

// 這時候因為變成了 LINQ to Objects 的延遲執行，
// EF 沒辦法把 Where 轉成 SQL，它被迫發送 "SELECT * FROM Users"
// 把整個資料庫幾百萬筆資料抓回網頁伺服器記憶體中，才在記憶體裡做篩選！
var result = query.Where(u => u.IsActive).ToList(); 
```
* **結論**：在進行資料庫篩選時，務必保持 `IQueryable` 型態，確保過濾條件在資料庫端執行。

---


# 問題 2
## IEnumerable 與 IQueryable 的差異是什麼？

---

## 解法

IEnumerable 與 IQueryable 都支援 LINQ 查詢，但執行位置完全不同。

### IEnumerable

- 查詢在記憶體中執行
- 使用 LINQ to Objects
- 委派型別為 Func<T>
- 適用於 List、Array 等集合

### IQueryable

- 查詢交由資料提供者處理
- 常用於 Entity Framework
- 會轉換成 SQL
- 委派型別為 Expression<T>

其最大差異在於：

```text
IEnumerable -> 資料取回後再過濾
IQueryable  -> 資料庫先過濾再取回
```

## 範例

### IEnumerable

```csharp
var users = db.Users.AsEnumerable();

var result = users.Where(x => x.Age > 18);
```

實際流程：

```text
Database
 ↓
全部資料載入 Memory
 ↓
C# 執行 Where
```

### IQueryable

```csharp
var users = db.Users.AsQueryable();

var result = users.Where(x => x.Age > 18);
```

轉換 SQL：

```sql
SELECT *
FROM Users
WHERE Age > 18
```

---

## 注意事項

1. IQueryable 適合大型資料集。
2. IEnumerable 適合記憶體中的資料。
3. 過早使用 ToList() 會破壞 IQueryable 最佳化。
4. Entity Framework 建議盡可能保持 IQueryable 到最後。

---

## 參考資料

- Microsoft Learn - IQueryable
- Entity Framework Core Documentation

---
# C# 進階指南：Expression Tree 與 IQueryable 高效分頁

本指南深入探討 C# 的兩大進階核心主題：**表達式樹（Expression Tree）** 的運作原理，以及如何利用 `IQueryable` 延遲執行特性，實作出對資料庫效能最佳化的 **API 分頁查詢（Skip/Take）**。

---

## 主題一：深入了解 Expression Tree（表達式樹）

在 Entity Framework (EF / EF Core) 當中，`IQueryable` 儲存的不是篩選後的資料，也不是編譯好的二進位碼，而是 SQL 的藍圖。這個藍圖在 C# 中的專有名詞就叫做 **Expression Tree（表達式樹）**。

### 1. 什麼是表達式樹？
簡單來說，表達式樹是一種**把「程式碼」當作「資料結構（樹狀圖）」來儲存**的技術。
* **一般的委派（Delegate / Lambda）**：是一段已經編譯成 IL 碼的**可執行程式**。
* **表達式樹（Expression）**：是一堆節點組成的**資料結構**。它不直接執行，而是記錄了「左邊是一個變數、中間是一個大於、右邊是一個常數」這樣的邏輯結構。

因為它只是「資料」，所以 EF 可以在執行期（Runtime）去讀取這張樹狀圖，並把它**翻譯成符合各種資料庫（如 SQL Server、PostgreSQL）的 SQL 語法**。

### 2. 表達式樹與一般委派（Func / Action）的不同

| 特性 | 一般委派：`Func<T, bool>` | 表達式樹：`Expression<Func<T, bool>>` |
| :--- | :--- | :--- |
| **本質** | 已經編譯好的二進位程式碼。 | 描述程式碼邏輯的資料結構（樹狀圖）。 |
| **執行對象** | 由 **CPU** 直接執行。 | 由 **EF 翻譯器** 讀取並轉成 SQL。 |
| **應用場景** | `IEnumerable` (LINQ to Objects / 記憶體篩選)。 | `IQueryable` (LINQ to Entities / 資料庫篩選)。 |

### 3. 如何自訂表達式樹？
在開發進階功能（例如動態組合前端篩選器）時，我們會手動組裝表達式樹。以下示範如何用程式碼動態蓋出一棵樹：

```csharp
using System;
using System.Linq.Expressions;

class Program
{
    static void Main()
    {
        // 目標：用程式碼動態產生像是 (user => user.Age > 18) 的邏輯

        // 1. 定義參數：user
        ParameterExpression param = Expression.Parameter(typeof(User), "user");

        // 2. 定義左側屬性：user.Age
        MemberExpression left = Expression.Property(param, "Age");

        // 3. 定義右側常數：18
        ConstantExpression right = Expression.Constant(18);

        // 4. 組合邏輯：user.Age > 18
        BinaryExpression body = Expression.GreaterThan(left, right);

        // 5. 包裝成 Lambda 表達式：Expression<Func<User, bool>>
        Expression<Func<User, bool>> expressionTree = Expression.Lambda<Func<User, bool>>(body, param);

        // 這棵「樹」現在可以直接丟給 EF 的 dbContext.Users.Where(expressionTree) 運用！
    }
}

class User { public string Name { get; set; } public int Age { get; set; } }
```

---

## 主題二：在 `IQueryable` 下進行最省效能的 API 分頁查詢

在後端開發中，分頁 API（Pagination）是保護伺服器與資料庫的必備技能。利用 `IQueryable` 的延遲執行特性，我們可以實作出**只讓資料庫回傳特定頁數資料**的高效分頁。

### 1. 核心方法：`Skip()` 與 `Take()`
* **`Skip(n)`**：跳過前 n 筆資料。
* **`Take(m)`**：只取出 m 筆資料。

### 2. 實戰範例：標準分頁 API 邏輯
假設前端傳來兩個參數：`pageIndex`（第幾頁，從 1 開始）與 `pageSize`（每頁幾筆）。

```csharp
public PagedResult<UserDto> GetPagedUsers(int pageIndex, int pageSize, string searchName)
{
    // 1. 建立基礎 IQueryable 查詢（此時完全沒有親近資料庫）
    IQueryable<User> query = _dbContext.Users;

    // 2. 動態拼接篩選條件（依然只是在 Expression Tree 疊加節點）
    if (!string.IsNullOrEmpty(searchName))
    {
        query = query.Where(u => u.Name.Contains(searchName));
    }

    // 3. 取得過濾後的「總筆數」（這會觸發第一次資料庫查詢，發送 SELECT COUNT）
    int totalCount = query.Count();

    // 計算應該跳過多少筆
    int skipRows = (pageIndex - 1) * pageSize;

    // 4. 動態拼接排序與分頁條件（注意：使用 Skip/Take 前通常必須先 OrderBy）
    IQueryable<User> pagedQuery = query
        .OrderBy(u => u.Id)
        .Skip(skipRows)
        .Take(pageSize);

    // 5. 觸發執行！加上 .ToList() 
    // 此時 EF 會將上述所有的 Where、OrderBy、Skip、Take 結合
    // 翻譯成一條高效能的 SQL 發送給資料庫
    var users = pagedQuery
        .Select(u => new UserDto { Id = u.Id, Name = u.Name })
        .ToList();

    // 6. 包裝成前端需要的格式回傳
    return new PagedResult<UserDto>
    {
        TotalCount = totalCount,
        Data = users
    };
}
```

### 3. EF 翻譯出來的 SQL 觀察
當執行到第 5 步的 `.ToList()` 時，微軟的 SQL Server 收到由 EF 翻譯出來的語法如下：

```sql
SELECT [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] LIKE N'%張%'            -- 這是 Where 的翻譯
ORDER BY [u].[Id]                       -- 這是 OrderBy 的翻譯
OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY; -- 這是 Skip(20).Take(10) 的翻譯！
```

### 💡 關鍵效能心法
透過 `IQueryable` 的延遲執行，資料庫**只會傳回當頁需要的那 10 筆資料**經過網路線來到你的伺服器，記憶體與網路頻寬的消耗降到最低。這就是延遲執行在現代後端開發中強大且無法取代的原因。

如果想要再繼續擴充這份筆記，我們可以聊聊：
1. 如何將這段分頁程式碼包裝成一個 泛型擴充方法（Generic Extension Method），讓所有 LINQ 查詢都能一秒套用？
2. 了解在大數據量時，為什麼 Skip/Take 效能會變慢，以及如何用 Keyset Pagination（鍵值分頁） 來進行優化？

# 問題 3
## First、FirstOrDefault、Single、SingleOrDefault 差異？

---

## 解法

這四個方法最大的差別在於：

- 找不到資料怎麼辦？
- 找到多筆資料怎麼辦？

### First

取得第一筆資料。

```csharp
var user = users.First();
```

特性：

- 找不到資料 → Exception
- 多筆資料 → 回傳第一筆

---

### FirstOrDefault

```csharp
var user = users.FirstOrDefault();
```

特性：

- 找不到資料 → null 或 default
- 多筆資料 → 回傳第一筆

---

### Single

```csharp
var user = users.Single();
```

特性：

- 找不到資料 → Exception
- 超過一筆 → Exception
- 必須且只能有一筆

---

### SingleOrDefault

```csharp
var user = users.SingleOrDefault();
```

特性：

- 找不到資料 → default
- 超過一筆 → Exception

---

## 使用時機

### First

當資料至少存在一筆。

例如：

```csharp
最新一筆訂單
第一位登入使用者
```

### Single

當資料本質上應該唯一。

例如：

```csharp
身分證字號
會員編號
Email
```

---

## 注意事項

1. Single 具有資料完整性驗證效果。
2. First 通常效能略優於 Single。
3. 查詢唯一值優先考慮 Single。
4. 不確定是否存在資料可選擇 OrDefault 版本。

---

## 參考資料

- Microsoft Learn - Enumerable.First
- Microsoft Learn - Enumerable.Single


# 問題 4
## Select 與 SelectMany 差異？

---

## 解法

兩者都用來投影（Projection）資料。

### Select

一筆資料對應一筆結果。

```csharp
1 -> 1
1 -> 1
```

### SelectMany

將多層集合攤平（Flatten）。

```csharp
1 -> N
N -> 1
```

---

## Select 範例

```csharp
var names = users.Select(x => x.Name);
```

結果：

```text
Tom
Mary
John
```

---

## SelectMany 範例

```csharp
var allOrders = customers.SelectMany(x => x.Orders);
```

原始資料：

```text
Customer A
 ├ Order1
 └ Order2

Customer B
 ├ Order3
 └ Order4
```

結果：

```text
Order1
Order2
Order3
Order4
```

---

## 注意事項

1. Select 保持原本資料結構。
2. SelectMany 用於展平巢狀集合。
3. SelectMany 常搭配 GroupBy 使用。
4. Entity Framework 中可能轉換為 JOIN。

---

## 參考資料

- Microsoft Learn - Select
- Microsoft Learn - SelectMany


# 問題 5
## LINQ 常見效能問題有哪些？

---

## 解法

LINQ 雖然提高可讀性，但若使用不當容易造成效能問題。

### 問題一：重複列舉 IEnumerable

#### ❌

```csharp
var query = users.Where(x => x.IsActive);

var count = query.Count();
var list = query.ToList();
```

可能執行兩次查詢。

#### ✅

```csharp
var list = users
    .Where(x => x.IsActive)
    .ToList();

var count = list.Count;
```

---

### 問題二：過早 ToList()

#### ❌

```csharp
var users = db.Users
              .ToList()
              .Where(x => x.Age > 18);
```

全部資料先進記憶體再過濾。

#### ✅

```csharp
var users = db.Users
              .Where(x => x.Age > 18)
              .ToList();
```

直接轉換 SQL。

---

### 問題三：N+1 Query

#### ❌

```csharp
foreach(var user in users)
{
    Console.WriteLine(user.Orders.Count);
}
```

可能產生：

```text
1 次 User Query
+ N 次 Order Query
```

#### ✅

```csharp
.Include(x => x.Orders)
```

提前載入關聯資料。

---

### 問題四：Closure Allocation

#### ❌

```csharp
int targetAge = 18;

users.Where(x => x.Age > targetAge);
```

可能建立 Closure 物件。

#### ✅

避免高頻率迴圈中捕捉外部變數。

---

### 問題五：錯誤使用 Count()

#### ❌

```csharp
if(users.Count() > 0)
```

#### ✅

```csharp
if(users.Any())
```

因為：

```text
Count() 可能掃描全部資料
Any() 找到第一筆即停止
```

---

## 注意事項

1. 避免重複列舉 IEnumerable。
2. 避免過早呼叫 ToList()。
3. 使用 Any() 取代 Count() > 0。
4. 注意 Closure Allocation。
5. 注意 Entity Framework N+1 Query 問題。
6. 善用 IQueryable 讓 SQL 在資料庫端執行。

---

## 參考資料

- Microsoft Learn - LINQ Performance
- Entity Framework Core Performance Guide
- CLR via C#
