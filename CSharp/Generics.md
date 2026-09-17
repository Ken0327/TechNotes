# C#題庫 - Generics 篇

# 主題
Generics (泛型)

## 問題
為什麼需要 Generic？

---

## 解法
Generic 的目的是提供型別安全(Type Safety)、提升程式重用性(Reusability)，並降低因型別轉換所產生的執行期錯誤。
在 .NET 2.0 以前，開發人員常使用 ArrayList 等非泛型集合來儲存資料。

解決以下問題
- 編譯器無法檢查型別, Generic 則能在編譯期間驗證型別。
- 執行期容易發生 InvalidCastException
- Value Type 會發生 Boxing / Unboxing

## 範例
```csharp
List<int> numbers = new List<int>();

numbers.Add(10);
numbers.Add(20);

int value = numbers[0];
```
---

## 注意事項

1. Generic 提供 Compile-Time Type Checking。
2. 避免大量型別轉換。
3. 提高 API 重用性。
4. 降低 Runtime Exception 發生機率。

---

## 參考資料

- CLR via C#
- Microsoft Learn - Generics



## 問題
Generic 如何避免 Boxing 與 Unboxing？

---

## 解法
當 Value Type 被轉換為 Object 時會發生 Boxing。

```text
int -> object
```

CLR 會將資料複製到 Heap 並建立新的 Object。

Generic 能保留實際型別，因此不需要先轉成 Object。

---

## 範例

### 非 Generic

```csharp
ArrayList list = new ArrayList();

list.Add(100); // Boxing

int value = (int)list[0]; // Unboxing
```

### Generic

```csharp
List<int> list = new List<int>();

list.Add(100);

int value = list[0];
```
---

## 注意事項

1. Boxing 會增加 Heap Allocation。
2. Unboxing 需要型別檢查。
3. 大量迴圈中特別影響效能。
4. Generic 可以有效減少 GC 壓力。

---

## 參考資料

- Pro .NET Memory Management
- CLR via C#


## 問題
Generic Constraint 有哪些？

---

## 解法
Constraint (型別約束) 用來限制泛型參數可接受的型別。

可增加 API 的可讀性與安全性。

where 條件約束（Constraints）可以限制型別
1. where T : struct （限制必須是「值型別」）意義：T 只能是 int, double, bool, DateTime 或自訂的 struct。不能是常規的物件類別（class）。實戰情境：通常用於數學運算、資料底層優化，保證資料絕對不會是 null。
2. where T : class （限制必須是「引用型別」）意義：與上面相反，T 只能是類別（如 string、自訂的物件 User、Order 等）。實戰情境：常用於資料庫、API 傳回值。因為限制了 class，你就可以給它預設值 null。
3. where T : new() （限制必須有「無參數建構子」）意義：保證這個 T 可以被 new 出來。實戰情境：如果你想在泛型類別內部自己動態建立 T 的實體，就必須加上這個，否則編譯器會怕有些類別沒有公開建構子而報錯。
4. where T : <基底類別名稱> （限制必須是某個類別或其子類別）意義：T 必須繼承自某個特定的父類別。實戰情境：遊戲開發中，限制只有「怪獸（Monster）」類別的子類別（如龍、哥布林）才能傳進這個方法。
5. where T : <介面名稱> （限制必須實作某個介面）—— 最常用！意義：T 必須實作指定的 Interface。實戰情境：當你需要物件具備某種特定的行為（例如：可存檔、可飛、可排序）。
6. 終極大絕：多重條件約束（組合拳）你可以同時要求 T 符合多個條件，只要用逗號隔開即可。如果有多個型別參數（如 T 和 U），就寫多個 where：csharp// 限制 T 必須是類別、有新創建構子、且必須實作 ICloneable 介面
=> public class DeepCopier<T> where T : class, ICloneable, new()

---

## 範例

```csharp
public class Repository<T>
    where T : class, new()
{
    public T Create()
    {
        return new T();
    }
}
```

---

## 注意事項

1. new() 表示必須具有無參數建構子。
2. class 表示 Reference Type。
3. struct 表示 Value Type。
4. 多個 Constraint 可以組合使用。

---

## 參考資料

- Microsoft Learn - Generic Constraints


# 主題
Generics (泛型)

## 問題
CLR 如何實作 Generic？

---

## 解法
CLR 在 Runtime 保留 Generic 的型別資訊。

JIT 編譯器會根據實際型別產生對應的程式碼。

對於：

```text
Reference Type
```

CLR 通常共用一份 Generic Code。

對於：

```text
Value Type
```

CLR 可能建立獨立的實作版本。

---

## 範例

```csharp
List<int>
List<double>
List<string>
```

上述三個型別在 Runtime 會有不同的處理策略。

---

## 注意事項

1. Generic 是 CLR 原生支援功能。
2. 與 Java Type Erasure 不同。
3. Value Type Generic 更能發揮效能優勢。
4. 面試常搭配 Boxing 題目一起詢問。

---

## 參考資料

- CLR Internals
- C# in Depth


# 主題
Generics (泛型)

## 問題
什麼是 Covariance 與 Contravariance？

---

## 解法
變異性(Variance)允許泛型型別之間安全轉換。

Covariance 使用 out。

```text
子類別 -> 父類別
```

Contravariance 使用 in。

```text
父類別 -> 子類別
```

---

## 範例

假設我們有一個簡單的類別階層：基底類別 Animal，以及衍生類別 Dog。
```csharp
public class Animal {}
public class Dog : Animal {}
```

1. Covariance（共變數 - out）共變數允許你將 IEnumerable<Dog> 指定給 IEnumerable<Animal>，因為資料是用來讀取（輸出）的。
```csharp
// 使用out宣告共變數介面
public interface IReadOnlyContainer<out T>
{
    T GetItem(); // T只能在輸出位置（回傳值）
}

public class DogContainer : IReadOnlyContainer<Dog>
{
    public Dog GetItem() => new Dog();
}

// --- 使用方式 ---
IReadOnlyContainer<Dog> dogContainer = new DogContainer();

// 支援 Covariance：把 Dog 的容器指派給 Animal 的容器
IReadOnlyContainer<Animal> animalContainer = dogContainer; 
Animal animal = animalContainer.GetItem(); // 安全，取得的是 Animal（實際上是 Dog）
```

2. Contravariance（反變數 - in）反變數允許你將處理 Animal 的動作委派或介面，指定給處理 Dog 的變數，因為它是用來接收（輸入）參數的。
```csharp
// 使用in宣告反變數介面
public interface IActionContainer<in T>
{
    void Act(T item); // T只能在輸入位置（方法參數）
}

public class AnimalAction : IActionContainer<Animal>
{
    public void Act(Animal item) { /* 處理 Animal */ }
}

// --- 使用方式 ---
IActionContainer<Animal> animalAction = new AnimalAction();

// 支援 Contravariance：把處理 Animal 的容器指派給處理 Dog 的容器
IActionContainer<Dog> dogAction = animalAction; 

dogAction.Act(new Dog()); // 安全，傳入 Dog 完全符合 Animal 的需求
```
---

## 注意事項

1. out 表示 Covariance。
2. in 表示 Contravariance。
3. 常見於 Interface 與 Delegate。
4. 面試經常詢問設計原因與使用場景。

---

## 參考資料

- Microsoft Learn - Variance
- C# in Depth


# 使用泛型是否會增加 Heap Allocation?

## 結論

泛型（Generics）本身並不一定會增加 Heap Allocation，甚至在許多情況下能夠**顯著降低 Heap 配置與 GC 壓力**。

- ✅ **Value Type（值型別）**：泛型通常能降低 Heap Allocation。
- ⚠️ **Reference Type（參考型別）**：泛型本身不是問題，但仍可能因集合擴容、閉包（Closure）等機制而增加 Heap Allocation。
- 🚀 搭配 `ValueTask<T>`、`Span<T>`、泛型約束等進階技巧，可進一步降低記憶體配置成本。

---

# 1. Value Type：泛型能減少 Heap Allocation

若使用非泛型集合（例如 `ArrayList` 或 `object`）儲存值型別資料，會發生 **Boxing（裝箱）**，導致額外 Heap Allocation。

## ❌ 非泛型：發生 Boxing

```csharp
ArrayList list = new ArrayList();
list.Add(100); // int -> object，發生 Boxing
```

## ✅ 泛型：避免 Boxing

```csharp
List<int> list = new List<int>();
list.Add(100);
```

- 資料直接儲存在底層陣列
- 不需轉成 `object`
- 不會產生 Boxing
- 大幅減少 Heap Allocation

---

# 2. Reference Type：泛型可能帶來隱性 Heap 成本

當使用 `List<MyClass>`、`Dictionary<string, MyClass>` 時，Heap 壓力通常不是來自泛型本身，而是集合內部機制。

## Capacity Expansion（容量擴充）

`List<T>` 底層其實是一個固定大小陣列。

```text
4 → 8 → 16 → 32 → 64 ...
```

當容量不足時：

1. 配置更大的新陣列（Heap）
2. 複製舊資料
3. 舊陣列等待 GC 回收

### ❌ 壞做法

```csharp
List<int> list1 = new List<int>();
```

### ✅ 好做法

```csharp
List<int> list2 = new List<int>(10000);
```

---

# Heap Allocation 優化技巧

## 1. 預先指定集合容量

```csharp
List<int> list = new List<int>(10000);
```

優點：

- 避免動態擴容
- 減少垃圾陣列
- 提升執行效率

---

## 2. 使用 ValueTask<T> 取代 Task<T>

### ❌

```csharp
public async Task<string> GetConfigTaskAsync(string key)
{
    ...
}
```

### ✅

```csharp
public async ValueTask<string> GetConfigValueTaskAsync(string key)
{
    ...
}
```

快取命中時可避免建立額外 Task 物件。

---

## 3. 利用泛型約束避免 Boxing

### ❌ 非泛型介面參數

```csharp
public interface IUpdatable
{
    void Update();
}

public struct CharacterPhysics : IUpdatable
{
    public void Update()
    {
        // 物理計算
    }
}

public class GameEngine
{
    public void ProcessPhysics(IUpdatable physics)
    {
        physics.Update();
    }
}
```

### ✅ 泛型 + 約束

```csharp
public class AdvancedGameEngine
{
    public void ProcessPhysics<T>(T physics)
        where T : IUpdatable
    {
        physics.Update();
    }
}
```

優點：

- 避免 Boxing
- JIT 產生專屬最佳化程式碼
- 降低 Heap Allocation

---

## 4. 終極優化：Span<T> 與 Memory<T>

### ❌ 傳統字串切割

```csharp
string rawData = "SERVER_LOG:20260826:ERROR_404";

string date = rawData.Substring(11, 8);
string code = rawData.Substring(20, 9);
```

### ✅ 使用 ReadOnlySpan<char>

```csharp
string rawData = "SERVER_LOG:20260826:ERROR_404";

ReadOnlySpan<char> span = rawData.AsSpan();

ReadOnlySpan<char> dateSpan = span.Slice(11, 8);
ReadOnlySpan<char> codeSpan = span.Slice(20, 9);
```

特性：

- `Span<T>` 為 `ref struct`
- 僅存在 Stack
- Slice 不建立新物件
- 0 Heap Allocation

---

# 深入探討：泛型委派與閉包（Closure）

## ❌ 觸發 Closure

```csharp
public List<User> GetUsersByAge(
    List<User> users,
    int targetAge)
{
    return users
        .Where(u => u.Age == targetAge)
        .ToList();
}
```

因為 Lambda 捕捉了外部變數 `targetAge`，編譯器會產生隱藏類別（DisplayClass），並在 Heap 配置額外物件。

### 編譯器概念模型

```csharp
[CompilerGenerated]
private sealed class DisplayClass
{
    public int targetAge;

    public bool AnonymousMethod(User u)
    {
        return u.Age == targetAge;
    }
}
```

每次呼叫方法都可能建立新的 Closure 物件。

---

## ✅ 使用 Static Lambda 避免 Closure Allocation

```csharp
// users.Where(static u => u.Age == targetAge);
```

優點：

- 禁止捕捉外部變數
- 編譯期就能發現問題
- 避免隱藏 Heap Allocation

---

# 重點總結

| 情境 | Heap Allocation |
|--------|--------|
| List<int> 取代 ArrayList | ✅ 減少 |
| Value Type 避免 Boxing | ✅ 減少 |
| 泛型約束避免介面 Boxing | ✅ 減少 |
| ValueTask<T> | ✅ 減少 |
| Span<T> / Memory<T> | ✅ 大幅減少 |
| List<T> 自動擴容 | ⚠️ 增加 |
| Closure 捕捉外部變數 | ⚠️ 增加 |
| LINQ + Closure | ⚠️ 增加 |

> 泛型本身通常不是 Heap Allocation 的來源；真正增加 Heap 的原因多半是 Boxing、集合擴容、Task 建立、字串複製與 Closure 捕捉等隱性成本。
