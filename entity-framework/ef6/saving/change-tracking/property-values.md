---
title: 使用屬性值-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: d8a18182754980d79b71df3f227b30c4ce40366f
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182135"
---
# <a name="working-with-property-values"></a>使用屬性值
在大部分的情況下，Entity Framework 會負責追蹤實體實例屬性的狀態、原始值和目前的值。 不過，在某些情況下（例如，中斷連線的案例），您想要在其中查看或操作 EF 相關資訊的屬性。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

Entity Framework 會針對追蹤實體的每個屬性追蹤兩個值。 目前的值是，如名稱所示，是實體中屬性的目前值。 原始值是從資料庫查詢實體或附加至內容時，屬性所擁有的值。  

使用屬性值的一般機制有兩種：  

- 您可以使用屬性方法，以強型別方式取得單一屬性的值。  
- 實體的所有屬性值都可以讀入 DbPropertyValues 物件中。 然後，DbPropertyValues 會作為類似字典的物件，以允許讀取和設定屬性值。 DbPropertyValues 物件中的值可以從另一個 DbPropertyValues 物件中的值，或從其他物件的值（例如，實體的另一個複本或簡單的資料傳輸物件（DTO））進行設定。  

下列各節顯示使用上述這兩種機制的範例。  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>取得和設定個別屬性的目前或原始值  

下列範例顯示如何讀取屬性的目前值，然後將它設定為新的值：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

使用 OriginalValue 屬性，而不是 CurrentValue 屬性來讀取或設定原始值。  

請注意，當使用字串來指定屬性名稱時，傳回的值會輸入為「物件」。 另一方面，如果使用 lambda 運算式，傳回的值就是強型別。  

如果新值與舊值不同，設定屬性值（例如這樣）只會將屬性標示為已修改。  

以這種方式設定屬性值時，即使已關閉 AutoDetectChanges，也會自動偵測變更。  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>取得和設定未對應屬性的目前值  

尚未對應至資料庫之屬性的目前值也可以讀取。 未對應屬性的範例可能是在 Blog 上的 .Rsslink 屬性。 此值可能會根據 BlogId 計算，因此不需要儲存在資料庫中。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

如果屬性公開 setter，則也可以設定目前的值。  

執行未對應屬性的 Entity Framework 驗證時，讀取未對應屬性的值會很有用。 基於相同原因，可以針對目前未由內容追蹤之實體的屬性，讀取和設定目前的值。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

請注意，原始值不適用於未對應的屬性，或未由內容追蹤之實體的屬性。  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>檢查屬性是否標示為已修改  

下列範例顯示如何檢查個別屬性是否標示為已修改：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

呼叫 SaveChanges 時，已修改的屬性值會當做更新傳送至資料庫。  

##  <a name="marking-a-property-as-modified"></a>將屬性標示為已修改  

下列範例顯示如何強制將個別屬性標示為已修改：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

將屬性標示為已修改，會在呼叫 SaveChanges 時，強制將更新傳送至資料庫，即使屬性的目前值與原始值相同也一樣。  

目前無法在將個別屬性標示為已修改之後，將它重設為未修改。 這是我們計畫在未來版本中支援的專案。  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>讀取實體所有屬性的目前、原始和資料庫值  

下列範例顯示如何讀取實體的所有對應屬性的目前值、原始值，以及實際在資料庫中的值。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

目前的值是實體的屬性所包含的值。 原始值是查詢實體時，從資料庫讀取的值。 資料庫值是目前儲存在資料庫中的值。 當資料庫中的值在查詢實體之後可能已變更，例如當另一位使用者對資料庫進行並行編輯時，取得資料庫值會很有用。  

## <a name="setting-current-or-original-values-from-another-object"></a>設定另一個物件的目前或原始值  

您可以藉由複製另一個物件的值，來更新已追蹤實體的目前或原始值。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

執行上述程式碼將會列印出來：  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

當您使用從服務呼叫或多層式應用程式中的用戶端取得的值來更新實體時，有時會使用這項技術。 請注意，使用的物件不一定要與實體具有相同的類型，只要它有名稱符合實體的屬性即可。 在上述範例中，會使用 BlogDTO 的實例來更新原始值。  

請注意，從其他物件複製時，只有設定為不同值的屬性會標示為已修改。  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>從字典設定目前或原始值  

已追蹤實體的目前或原始值可以藉由從字典或其他資料結構複製值來進行更新。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

使用 OriginalValues 屬性，而不是 CurrentValues 屬性來設定原始值。  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>使用屬性設定字典中的目前或原始值  

如上面所示使用 CurrentValues 或 OriginalValues 的替代方式，是使用 Property 方法來設定每個屬性的值。 當您需要設定複雜屬性的值時，這會是較理想的作法。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

在上述範例中，會使用點名稱來存取複雜屬性。 如需存取複雜屬性的其他方式，請參閱本主題稍後關於複雜屬性的兩個章節。  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>建立包含目前、原始或資料庫值的複製物件  

從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 DbPropertyValues 物件可以用來建立實體的複本。 這個複製將包含用來建立它的 DbPropertyValues 物件的屬性值。 例如:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

請注意，傳回的物件不是實體，而且不是由內容進行追蹤。 傳回的物件也沒有任何設定為其他物件的關聯性。  

複製的物件有助於解決與資料庫並行更新相關的問題，尤其是在使用牽涉到特定類型物件之資料系結的 UI 時。  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>取得和設定複雜屬性的目前或原始值  

您可以使用 Property 方法來讀取和設定整個複雜物件的值，就像是針對基本屬性一樣。 此外，您可以向下切入至複雜物件，並讀取或設定該物件的屬性，甚至是嵌套的物件。 以下是一些範例：  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

請使用 OriginalValue 屬性來取代 CurrentValue 屬性，以取得或設定原始值。  

請注意，屬性或 ComplexProperty 方法都可以用來存取複雜屬性。 不過，如果您想要向下切入至具有其他屬性或 ComplexProperty 呼叫的複雜物件，則必須使用 ComplexProperty 方法。  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>使用 DbPropertyValues 存取複雜屬性  

當您使用 CurrentValues、OriginalValues 或 GetDatabaseValues 取得實體的所有目前、原始或資料庫值時，任何複雜屬性的值都會以 nested DbPropertyValues 物件的形式傳回。 然後，這些嵌套物件就可以用來取得複雜物件的值。 例如，下列方法會列印出所有屬性的值，包括任何複雜屬性和嵌套複雜屬性的值。  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

若要列印出所有目前的屬性值，方法會被呼叫，如下所示：  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
