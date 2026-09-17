# C#題庫 - LINQ 篇

# 主題
LINQ (Language Integrated Query)

---

# 問題 1
## 什麼是 Deferred Execution（延遲執行）？

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
