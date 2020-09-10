---
title: 使用屬性值-EF6
description: 使用 Entity Framework 6 中的屬性值
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 53c0829920c77768eae26d23c3da7d0553f44b62
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619965"
---
# <a name="working-with-property-values"></a>使用屬性值
在大部分的情況下，Entity Framework 會負責追蹤實體實例之屬性的狀態、原始值和目前值。 不過，在某些情況下可能會發生，例如已中斷連線的情況-您想要在其中查看或操作 EF 對屬性的相關資訊。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

Entity Framework 針對追蹤實體的每個屬性追蹤兩個值。 目前的值是指實體中屬性的目前值，如同名稱所示。 原始值是在從資料庫查詢或附加至內容的實體時，該屬性具有的值。  

使用屬性值的一般機制有兩種：  

- 您可以使用屬性方法，以強型別的方式取得單一屬性的值。  
- 實體的所有屬性值都可讀入 DbPropertyValues 物件中。 然後，DbPropertyValues 會作為類似字典的物件，以允許讀取和設定屬性值。 DbPropertyValues 物件中的值可以從另一個 DbPropertyValues 物件的值，或從其他物件中的值設定，例如實體的另一個複本或 (DTO) 的簡單資料傳輸物件。  

下列各節顯示使用上述兩種機制的範例。  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>取得和設定個別屬性的目前或原始值  

下列範例顯示如何讀取屬性的目前值，然後將其設定為新值：  

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

使用 OriginalValue 屬性，而非 CurrentValue 屬性來讀取或設定原始的值。  

請注意，當使用字串來指定屬性名稱時，傳回的值會輸入為「物件」。 另一方面，如果使用 lambda 運算式，則傳回的值為強型別。  

如果新值與舊值不同，則將屬性值設定為，就只會將屬性標示為已修改。  

以這種方式設定屬性值時，即使已關閉 AutoDetectChanges，也會自動偵測到變更。  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>取得和設定未對應之屬性的目前值  

尚未對應至資料庫的屬性目前值也可以讀取。 未對應的屬性範例可能是 Blog 上的 .Rsslink 屬性。 您可以根據 BlogId 來計算這個值，因此不需要將它儲存在資料庫中。 例如：  

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

如果屬性公開 setter，也可以設定目前的值。  

當執行未對應屬性的 Entity Framework 驗證時，讀取未對應屬性的值會很有用。 基於相同的原因，目前值可以針對目前未由內容追蹤的實體屬性進行讀取和設定。 例如：  

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

請注意，原始值不適用於未對應的屬性，或不是內容所追蹤之實體的屬性。  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>檢查屬性是否標記為已修改  

下列範例顯示如何檢查個別屬性是否標記為已修改：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

當呼叫 SaveChanges 時，已修改的屬性值會以更新的形式傳送至資料庫。  

##  <a name="marking-a-property-as-modified"></a>將屬性標示為已修改  

下列範例顯示如何強制個別屬性標示為已修改：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

將屬性標示為已修改時，會在呼叫 SaveChanges 時，強制將更新傳送至資料庫，即使屬性的目前值與原始值相同也一樣。  

目前無法在標記為修改之後，將個別的屬性重設為不修改。 這是我們計畫在未來版本中支援的功能。  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>讀取實體所有屬性的目前、原始和資料庫值  

下列範例顯示如何讀取實體中所有對應屬性的目前值、原始值，以及實際在資料庫中的值。  

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

目前的值是實體屬性目前包含的值。 原始值是查詢實體時，從資料庫讀取的值。 資料庫值是目前儲存在資料庫中的值。 當資料庫中的值在查詢實體之後可能已經變更（例如，當其他使用者對資料庫進行並行編輯時），取得資料庫值會很有用。  

## <a name="setting-current-or-original-values-from-another-object"></a>從另一個物件設定目前或原始的值  

您可以從另一個物件複製值，以更新已追蹤實體的目前或原始值。 例如：  

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

執行上述程式碼將會列印出：  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

使用從服務呼叫取得的值或多層式應用程式中的用戶端來更新實體時，有時會使用這項技術。 請注意，使用的物件不一定要與實體具有相同的類型，只要它的屬性名稱符合實體的名稱即可。 在上述範例中，會使用 BlogDTO 的實例來更新原始的值。  

請注意，從其他物件複製時，只有設定為不同值的屬性會標示為已修改。  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>從字典設定目前或原始的值  

您可以從字典或其他資料結構複製值，以更新已追蹤實體的目前或原始值。 例如：  

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

使用 OriginalValues 屬性，而非 CurrentValues 屬性來設定原始值。  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>使用屬性設定字典的目前或原始值  

如上面所示，使用 CurrentValues 或 OriginalValues 的替代方法是使用 Property 方法來設定每個屬性的值。 當您需要設定複雜屬性的值時，最好這麼做。 例如：  

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

在上述範例中，複雜屬性是使用點名稱來存取。 如需其他存取複雜屬性的方式，請參閱本主題稍後的兩個部分，具體而言就是複雜屬性。  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>建立包含目前、原始或資料庫值的複製物件  

從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 DbPropertyValues 物件，可以用來建立實體的複製。 這個複製將包含用來建立它之 DbPropertyValues 物件的屬性值。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

請注意，傳回的物件不是實體，而且不是由內容所追蹤。 傳回的物件也不會將任何關聯性設定為其他物件。  

複製的物件有助於解決與資料庫並行更新相關的問題，尤其是在使用涉及資料系結至特定類型物件的 UI 時。  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>取得和設定複雜屬性的目前或原始值  

您可以使用屬性方法來讀取和設定整個複雜物件的值，就像是針對基本屬性一樣。 此外，您可以向下切入至複雜物件，並讀取或設定該物件的屬性，甚至是嵌套物件。 以下是一些範例：  

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

使用 OriginalValue 屬性，而非 CurrentValue 屬性來取得或設定原始值。  

請注意，您可以使用屬性或 ComplexProperty 方法來存取複雜屬性。 但是，如果您想要向下切入具有其他屬性或 ComplexProperty 呼叫的複雜物件，則必須使用 ComplexProperty 方法。  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>使用 DbPropertyValues 存取複雜屬性  

當您使用 CurrentValues、OriginalValues 或 GetDatabaseValues 取得實體的所有目前、原始或資料庫值時，任何複雜屬性的值都會以嵌套 DbPropertyValues 物件的形式傳回。 然後可以使用這些嵌套物件來取得複雜物件的值。 例如，下列方法會列印出所有屬性的值，包括任何複雜屬性的值和嵌套的複雜屬性。  

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

若要列印出所有目前的屬性值，則會呼叫方法，如下所示：  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
