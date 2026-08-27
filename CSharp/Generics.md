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

```csharp
IEnumerable<string> names = new List<string>();
IEnumerable<object> objects = names;
```

Covariance:

```csharp
public interface IRepository<out T>
{
    T Get();
}
```

Contravariance:

```csharp
public interface IProcessor<in T>
{
    void Process(T item);
}
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


# 使用泛型是否會增加Heap Allocation?
1. 面對 Value Type：泛型是「減少」Heap 的大功臣正如前文所述，如果不用泛型而使用 ArrayList 或 object，傳入 int 時會引發 Boxing（裝箱），這會在 Heap 上平白無故產生一個包裝物件。使用 List<int> 代替 ArrayList：資料直接以連續的二進位形式存在底層的陣列中，完全不會觸發 Boxing，因此大幅減少了 Heap Allocation。

2. 面對 Reference Type：泛型可能引發「隱蔽的 Heap 增加」當你使用 List<MyClass> 或 Dictionary<string, MyClass> 時，真正的 Heap 殺手不在於泛型本身，而是以下兩點：底層陣列的自動擴容（Capacity Expansion）：以 List<T> 為例，它的底層其實是一個固定大小的陣列（預期預設長度很小，例如 4）。當你不斷 Add 東西進去，超過容量時，List 會在 Heap 上配置一個兩倍大的全新陣列，並把舊資料複製過去，再把舊陣列丟給 GC 回收。


1. 預先指定集合容量（防範擴容殺手）
// ❌ 壞做法：一邊加一邊擴容，Heap 產生一堆廢棄陣列
List<int> list1 = new List<int>(); 

//  好做法：在 Heap 一次配置好 10,000 個 int 的空間，0 次擴容
List<int> list2 = new List<int>(10000); 

2. 使用 ValueTask<T> 代替 Task<T>（異步泛型優化)
// ❌ 每次呼叫，若快取有資料，依然會在 Heap 配置一個 Task 物件
public async Task<string> GetConfigTaskAsync(string key) { ... }
//  高效能做法：快取命中時，直接從 Stack 回傳 struct 數值，完全不佔用 Heap
public async ValueTask<string> GetConfigValueTaskAsync(string key) { ... }

3. 利用 struct 實作泛型介面（阻斷 Boxing）
❌ 隱式轉型導致裝箱csharppublic interface IUpdatable { void Update(); }
public struct CharacterPhysics : IUpdatable { public void Update() { /* 計算物理 */ } }

public class GameEngine
{
    // 非泛型寫法，直接接收介面
    public void ProcessPhysics(IUpdatable physics)
    {
        physics.Update(); // 💥 傳入 struct 時，會發生 Boxing，在 Heap 產生無數臨時物件
    }
}

🚀 優化做法：泛型方法約束當你改成泛型方法並加上約束，.NET 的 JIT 編譯器在編譯 ProcessPhysics<CharacterPhysics> 時，會生成專屬於該 struct 的機器碼，直接呼叫 struct 內部的方法，完全不經過介面轉型。csharppublic class AdvancedGameEngine
{
    // 泛型寫法 + 條件約束
    public void ProcessPhysics<T>(T physics) where T : IUpdatable
    {
        physics.Update(); // 🎉 JIT 強大優化：直通 struct 內部，0 次 Boxing！
    }
}

4. 終極大絕：使用 Span<T> 與 Memory<T>

❌ 傳統做法（文字處理的 Heap 惡夢）csharpstring rawData = "SERVER_LOG:20260826:ERROR_404";

// 為了拿到日期與錯誤碼，進行 Substring
string date = rawData.Substring(11, 8); // 💥 Heap 產生新字串 "20260826"
string code = rawData.Substring(20, 9); // 💥 Heap 產生新字串 "ERROR_404"

🚀 優化做法：使用 ReadOnlySpan<char>Span<T> 就像一扇虛擬的窗戶。它只記錄兩個東西：「記憶體起點指標」與「長度」。它本身是 ref struct，只能待在 Stack，絕對進不去 Heap。csharpstring rawData = "SERVER_LOG:20260826:ERROR_404";
// 將整段字串視為一個唯讀的記憶體區間
ReadOnlySpan<char> span = rawData.AsSpan();

// 進行 Slice（切片），這只是移動指標和改成長度，完全不 new 新物件
ReadOnlySpan<char> dateSpan = span.Slice(11, 8); // 🎉 0 Heap Allocation!
ReadOnlySpan<char> codeSpan = span.Slice(20, 9); // 🎉 0 Heap Allocation!



## 深入細談：泛型委派與閉包（Closure / Lambda 變數捕捉）LINQ 是 C# 的核心靈魂，它大量運用了泛型委派。例如 list.Where(x => x.IsActive)。如果 Lambda 運算式純粹只用到物件內部屬性，C# 編譯器會對其進行優化（通常會生成靜態快取），不會增加 Heap 負載。但是！只要你捕捉了「外部變數」，情況就會完全改觀。❌ 觸發閉包
（隱藏的 Heap 彈）
csharppublic List<User> GetUsersByAge(List<User> users, int targetAge)
{
    // targetAge 是外部傳進來的變數
    // 為了在 Lambda 內部存取 targetAge，編譯器在幕後做了一件壞事...
    return users.Where(u => u.Age == targetAge).ToList(); 
}

🔍 編譯器在幕後私下幹了什麼？因為 targetAge 在 Stack 中會隨著方法結束而消失，為了讓 LINQ 的 Where 執行時還能讀到它，C# 編譯器會在編譯時自動幫你偷偷生出一個隱藏的類別（Class）：csharp// 編譯器自動產生的影子類別
[CompilerGenerated]
private sealed class DisplayClass
{
    public int targetAge; // 外部變數被綁架到這裡
    public bool AnonymousMethod(User u) => u.Age == this.targetAge;
}

// 實際執行的程式碼被代換成：
public List<User> GetUsersByAge(List<User> users, int targetAge)
{
    DisplayClass closure = new DisplayClass(); // 💥 驚呆！在 Heap 偷偷 new 了一個物件
    closure.targetAge = targetAge;
    
    return users.Where(closure.AnonymousMethod).ToList();
}

這意味著，每呼叫一次這個方法，Heap 就會被塞入一個隱藏的閉包物件。如果這個方法位於每秒執行萬次的迴圈中，GC 就會立刻崩潰。如何處理：避免在 Lambda 內捕捉外部變數。在現代 C#（C# 9+）中，你可以使用 static Lambda 來強迫編譯器檢查。如果你不小心捕捉了外部變數，編譯器會立刻報錯，從根本上防範這個 Heap 殺手：csharp// 加上 static，如果不小心用了 targetAge，編譯會直接報錯，逼你改用其他不傷效能的作法
// users.Where(static u => u.Age == targetAge); 

