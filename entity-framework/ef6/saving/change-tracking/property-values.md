---
title: 使用屬性值-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
caps.latest.revision: 3
ms.openlocfilehash: 07b71c9efe4e1fc3fd25a52c9cfb25f61e92f859
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120242"
---
# <a name="working-with-property-values"></a>使用屬性值
大部分的情況下 Entity Framework 會負責追蹤狀態、 原始值，以及您的實體執行個體的屬性目前值。 不過，可能有某些情況下-例如中斷連線的案例-您想要用來檢視或處理 EF 具有屬性的相關資訊。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

Entity Framework 會追蹤的追蹤實體的每個屬性的兩個值。 目前的值是，正如其名，在實體中屬性的目前值。 原始的值是屬性有從資料庫查詢或附加至內容實體時的值。  

有兩種一般的機制，來使用屬性值：  

- 強類型的方式，使用屬性的方法可以取得單一屬性的值。  
- 實體的所有屬性的值可以讀入 DbPropertyValues 物件。 DbPropertyValues 再做為字典式物件，以允許讀取和設定的屬性值。 從另一個 DbPropertyValues 物件中的值或一些其他物件，例如另一份簡單的資料傳輸物件 (DTO) 或實體中的值，則可以設定 DbPropertyValues 物件中的值。  

下列各節示範這兩個以上的機制使用的範例。  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>取得和設定的個別屬性的目前或原始值  

下列範例示範可以如何讀取，然後設定新的值屬性的目前值：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(name).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

使用而非 CurrentValue; 屬性的 OriginalValue 屬性，來讀取或設定原始值。  

請注意，傳回的值型別為"object"字串來指定屬性名稱時。 相反地，傳回的值是強型別如果會使用 lambda 運算式。  

將屬性值，這類設定在為新的值不同於舊的值是否已修改，只會將屬性。  

當以這種方式設定屬性值變更會自動偵測即使 AutoDetectChanges 已關閉。  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>取得和設定目前未對應的屬性的值  

也可以讀取此屬性，不會對應到資料庫的目前值。 未對應的範例可能是屬性的部落格上的 RssLink 屬性。 這個值可能會根據 BlogId，計算，因此不需要儲存在資料庫中。 例如:   

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

如果屬性會公開 setter，也可以設定目前的值。  

執行 Entity Framework 驗證未對應的屬性時，讀取未對應之屬性的值很有用。 基於相同原因可以讀取和設定都沒有目前追蹤內容的實體屬性的目前值。 例如:   

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

請注意，不會有原始值未對應的屬性或沒有內容所追蹤的實體的屬性。  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>檢查屬性是否標示為已修改  

下列範例示範如何檢查標示為已修改的個別屬性：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

已修改屬性的值會以傳送更新到資料庫讓呼叫 SaveChanges 時。  

##  <a name="marking-a-property-as-modified"></a>標示為已修改的屬性  

下列範例示範如何強制要被標示為已修改的個別屬性：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

將屬性標示為要更新將傳送至資料庫中的屬性，即使目前屬性的值做為其原始值相同讓呼叫 SaveChanges 時的已修改強制中。  

它不是目前無法重設該網站已標示為已修改後未修改的個別屬性。 這是我們計劃在未來版本中支援的項目。  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>讀取目前、 原始值和實體的所有屬性的資料庫值  

下列範例示範如何讀取目前的值、 原始的值，以及實際實體的所有對應屬性的資料庫中的值。  

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

目前的值是實體的目前包含屬性的值。 原始的值會是實體的查詢時，從資料庫所讀取的值。 目前儲存在資料庫中，資料庫值會是值。 在資料庫中的值可能已變更，因為實體的查詢，例如當並行編輯已經由另一位使用者對資料庫時，取得資料庫值非常有用的。  

## <a name="setting-current-or-original-values-from-another-object"></a>設定目前或原始值，從另一個物件  

另一個物件的值複製，可以更新追蹤的實體的目前或原始值。 例如:   

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

執行上述程式碼會印出：  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

使用從服務呼叫或多層式架構應用程式中的用戶端取得的值更新實體時，有時會用這項技術。 請注意，所使用的物件不必是實體類型相同，只要它的名稱符合實體的屬性。 在上述範例中，BlogDTO 的執行個體用來更新原始值。  

請注意，只有設定為不同的值，當從另一個物件複製的屬性會標示為已修改。  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>字典中的設定目前或原始值  

字典或一些其他資料結構的值複製，可以更新追蹤的實體的目前或原始值。 例如:   

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

可用於 OriginalValues 屬性而非 CurrentValues; 屬性設定原始值。  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>字典中使用屬性的設定目前或原始值  

使用 CurrentValues 或 OriginalValues 如上所示的替代方法是使用屬性方法來設定每個屬性的值。 當您需要設定複雜屬性的值，這很適合。 例如:   

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

在上例中的複雜屬性會使用存取點線的名稱。 如需其他方式存取複雜屬性，請參閱需複雜屬性專門針對本主題稍後的兩個區段。  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>建立複製的物件，包含目前、 原始或資料庫值  

DbPropertyValues 物件傳回 CurrentValues，OriginalValues，或 GetDatabaseValues 可用來建立實體的複製品。 此複本將會包含用來建立它的 DbPropertyValues 物件的屬性值。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

請注意，傳回的物件不是實體，而且沒有由內容所追蹤。 傳回的物件也沒有設定為其他物件的任何關聯性。  

複製的物件可用於解決並行更新的資料庫，尤其是牽涉到資料繫結至物件的特定類型的 UI 的使用位置的相關問題。  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>取得和設定複雜屬性的目前或原始值  

讀取和使用屬性的方法，就像它可以是基本的屬性集，可以是整個複雜物件的值。 此外您可以鑽研到複雜的物件，該物件，或甚至是巢狀的物件的讀取或設定屬性。 以下是一些範例：  

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

使用而非 CurrentValue; 屬性的 OriginalValue 屬性，取得或設定的原始值。  

請注意，屬性或 ComplexProperty 方法可用來存取複雜屬性。 不過，如果您想要向下切入至複雜的物件與其他屬性，或呼叫的 ComplexProperty，必須使用的 ComplexProperty 方法。  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>若要存取複雜屬性使用 DbPropertyValues  

您可以使用 CurrentValues、 OriginalValues 或 GetDatabaseValues 取得所有目前的原始碼，或任何複雜屬性的值會傳回資料庫實體的值，做為巢狀 DbPropertyValues 物件。 這些物件可以巢狀結構，然後用來取得的複雜物件的值。 例如，下列方法會印出所有的屬性，包括任何複雜的屬性和巢狀複雜屬性的值的值。  

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

若要印出所有目前的屬性值的方法會呼叫，像這樣：  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
