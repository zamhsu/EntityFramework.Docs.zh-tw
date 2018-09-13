---
title: 使用屬性值-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: 97902021a671dea9854a365dc2f10eaecb9e5ab8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488827"
---
# <a name="working-with-property-values"></a><span data-ttu-id="b221b-102">使用屬性值</span><span class="sxs-lookup"><span data-stu-id="b221b-102">Working with property values</span></span>
<span data-ttu-id="b221b-103">大部分的情況下 Entity Framework 會負責追蹤狀態、 原始值，以及您的實體執行個體的屬性目前值。</span><span class="sxs-lookup"><span data-stu-id="b221b-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="b221b-104">不過，可能有某些情況下-例如中斷連線的案例-您想要用來檢視或處理 EF 具有屬性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="b221b-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="b221b-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="b221b-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="b221b-106">Entity Framework 會追蹤的追蹤實體的每個屬性的兩個值。</span><span class="sxs-lookup"><span data-stu-id="b221b-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="b221b-107">目前的值是，正如其名，在實體中屬性的目前值。</span><span class="sxs-lookup"><span data-stu-id="b221b-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="b221b-108">原始的值是屬性有從資料庫查詢或附加至內容實體時的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="b221b-109">有兩種一般的機制，來使用屬性值：</span><span class="sxs-lookup"><span data-stu-id="b221b-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="b221b-110">強類型的方式，使用屬性的方法可以取得單一屬性的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="b221b-111">實體的所有屬性的值可以讀入 DbPropertyValues 物件。</span><span class="sxs-lookup"><span data-stu-id="b221b-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="b221b-112">DbPropertyValues 再做為字典式物件，以允許讀取和設定的屬性值。</span><span class="sxs-lookup"><span data-stu-id="b221b-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="b221b-113">從另一個 DbPropertyValues 物件中的值或一些其他物件，例如另一份簡單的資料傳輸物件 (DTO) 或實體中的值，則可以設定 DbPropertyValues 物件中的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="b221b-114">下列各節示範這兩個以上的機制使用的範例。</span><span class="sxs-lookup"><span data-stu-id="b221b-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="b221b-115">取得和設定的個別屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="b221b-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="b221b-116">下列範例示範可以如何讀取，然後設定新的值屬性的目前值：</span><span class="sxs-lookup"><span data-stu-id="b221b-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

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

<span data-ttu-id="b221b-117">使用而非 CurrentValue; 屬性的 OriginalValue 屬性，來讀取或設定原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="b221b-118">請注意，傳回的值型別為"object"字串來指定屬性名稱時。</span><span class="sxs-lookup"><span data-stu-id="b221b-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="b221b-119">相反地，傳回的值是強型別如果會使用 lambda 運算式。</span><span class="sxs-lookup"><span data-stu-id="b221b-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="b221b-120">將屬性值，這類設定在為新的值不同於舊的值是否已修改，只會將屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="b221b-121">當以這種方式設定屬性值變更會自動偵測即使 AutoDetectChanges 已關閉。</span><span class="sxs-lookup"><span data-stu-id="b221b-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="b221b-122">取得和設定目前未對應的屬性的值</span><span class="sxs-lookup"><span data-stu-id="b221b-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="b221b-123">也可以讀取此屬性，不會對應到資料庫的目前值。</span><span class="sxs-lookup"><span data-stu-id="b221b-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="b221b-124">未對應的範例可能是屬性的部落格上的 RssLink 屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="b221b-125">這個值可能會根據 BlogId，計算，因此不需要儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="b221b-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="b221b-126">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-126">For example:</span></span>  

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

<span data-ttu-id="b221b-127">如果屬性會公開 setter，也可以設定目前的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="b221b-128">執行 Entity Framework 驗證未對應的屬性時，讀取未對應之屬性的值很有用。</span><span class="sxs-lookup"><span data-stu-id="b221b-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="b221b-129">基於相同原因可以讀取和設定都沒有目前追蹤內容的實體屬性的目前值。</span><span class="sxs-lookup"><span data-stu-id="b221b-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="b221b-130">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-130">For example:</span></span>  

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

<span data-ttu-id="b221b-131">請注意，不會有原始值未對應的屬性或沒有內容所追蹤的實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="b221b-132">檢查屬性是否標示為已修改</span><span class="sxs-lookup"><span data-stu-id="b221b-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="b221b-133">下列範例示範如何檢查標示為已修改的個別屬性：</span><span class="sxs-lookup"><span data-stu-id="b221b-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="b221b-134">已修改屬性的值會以傳送更新到資料庫讓呼叫 SaveChanges 時。</span><span class="sxs-lookup"><span data-stu-id="b221b-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="b221b-135">標示為已修改的屬性</span><span class="sxs-lookup"><span data-stu-id="b221b-135">Marking a property as modified</span></span>  

<span data-ttu-id="b221b-136">下列範例示範如何強制要被標示為已修改的個別屬性：</span><span class="sxs-lookup"><span data-stu-id="b221b-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="b221b-137">將屬性標示為要更新將傳送至資料庫中的屬性，即使目前屬性的值做為其原始值相同讓呼叫 SaveChanges 時的已修改強制中。</span><span class="sxs-lookup"><span data-stu-id="b221b-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="b221b-138">它不是目前無法重設該網站已標示為已修改後未修改的個別屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="b221b-139">這是我們計劃在未來版本中支援的項目。</span><span class="sxs-lookup"><span data-stu-id="b221b-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="b221b-140">讀取目前、 原始值和實體的所有屬性的資料庫值</span><span class="sxs-lookup"><span data-stu-id="b221b-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="b221b-141">下列範例示範如何讀取目前的值、 原始的值，以及實際實體的所有對應屬性的資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="b221b-142">目前的值是實體的目前包含屬性的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="b221b-143">原始的值會是實體的查詢時，從資料庫所讀取的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="b221b-144">目前儲存在資料庫中，資料庫值會是值。</span><span class="sxs-lookup"><span data-stu-id="b221b-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="b221b-145">在資料庫中的值可能已變更，因為實體的查詢，例如當並行編輯已經由另一位使用者對資料庫時，取得資料庫值非常有用的。</span><span class="sxs-lookup"><span data-stu-id="b221b-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="b221b-146">設定目前或原始值，從另一個物件</span><span class="sxs-lookup"><span data-stu-id="b221b-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="b221b-147">另一個物件的值複製，可以更新追蹤的實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="b221b-148">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-148">For example:</span></span>  

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

<span data-ttu-id="b221b-149">執行上述程式碼會印出：</span><span class="sxs-lookup"><span data-stu-id="b221b-149">Running the code above will print out:</span></span>  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="b221b-150">使用從服務呼叫或多層式架構應用程式中的用戶端取得的值更新實體時，有時會用這項技術。</span><span class="sxs-lookup"><span data-stu-id="b221b-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="b221b-151">請注意，所使用的物件不必是實體類型相同，只要它的名稱符合實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="b221b-152">在上述範例中，BlogDTO 的執行個體用來更新原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="b221b-153">請注意，只有設定為不同的值，當從另一個物件複製的屬性會標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="b221b-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="b221b-154">字典中的設定目前或原始值</span><span class="sxs-lookup"><span data-stu-id="b221b-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="b221b-155">字典或一些其他資料結構的值複製，可以更新追蹤的實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="b221b-156">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-156">For example:</span></span>  

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

<span data-ttu-id="b221b-157">可用於 OriginalValues 屬性而非 CurrentValues; 屬性設定原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="b221b-158">字典中使用屬性的設定目前或原始值</span><span class="sxs-lookup"><span data-stu-id="b221b-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="b221b-159">使用 CurrentValues 或 OriginalValues 如上所示的替代方法是使用屬性方法來設定每個屬性的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="b221b-160">當您需要設定複雜屬性的值，這很適合。</span><span class="sxs-lookup"><span data-stu-id="b221b-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="b221b-161">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-161">For example:</span></span>  

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

<span data-ttu-id="b221b-162">在上例中的複雜屬性會使用存取點線的名稱。</span><span class="sxs-lookup"><span data-stu-id="b221b-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="b221b-163">如需其他方式存取複雜屬性，請參閱需複雜屬性專門針對本主題稍後的兩個區段。</span><span class="sxs-lookup"><span data-stu-id="b221b-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="b221b-164">建立複製的物件，包含目前、 原始或資料庫值</span><span class="sxs-lookup"><span data-stu-id="b221b-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="b221b-165">DbPropertyValues 物件傳回 CurrentValues，OriginalValues，或 GetDatabaseValues 可用來建立實體的複製品。</span><span class="sxs-lookup"><span data-stu-id="b221b-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="b221b-166">此複本將會包含用來建立它的 DbPropertyValues 物件的屬性值。</span><span class="sxs-lookup"><span data-stu-id="b221b-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="b221b-167">例如: </span><span class="sxs-lookup"><span data-stu-id="b221b-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="b221b-168">請注意，傳回的物件不是實體，而且沒有由內容所追蹤。</span><span class="sxs-lookup"><span data-stu-id="b221b-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="b221b-169">傳回的物件也沒有設定為其他物件的任何關聯性。</span><span class="sxs-lookup"><span data-stu-id="b221b-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="b221b-170">複製的物件可用於解決並行更新的資料庫，尤其是牽涉到資料繫結至物件的特定類型的 UI 的使用位置的相關問題。</span><span class="sxs-lookup"><span data-stu-id="b221b-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="b221b-171">取得和設定複雜屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="b221b-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="b221b-172">讀取和使用屬性的方法，就像它可以是基本的屬性集，可以是整個複雜物件的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="b221b-173">此外您可以鑽研到複雜的物件，該物件，或甚至是巢狀的物件的讀取或設定屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="b221b-174">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="b221b-174">Here are some examples:</span></span>  

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

<span data-ttu-id="b221b-175">使用而非 CurrentValue; 屬性的 OriginalValue 屬性，取得或設定的原始值。</span><span class="sxs-lookup"><span data-stu-id="b221b-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="b221b-176">請注意，屬性或 ComplexProperty 方法可用來存取複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="b221b-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="b221b-177">不過，如果您想要向下切入至複雜的物件與其他屬性，或呼叫的 ComplexProperty，必須使用的 ComplexProperty 方法。</span><span class="sxs-lookup"><span data-stu-id="b221b-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="b221b-178">若要存取複雜屬性使用 DbPropertyValues</span><span class="sxs-lookup"><span data-stu-id="b221b-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="b221b-179">您可以使用 CurrentValues、 OriginalValues 或 GetDatabaseValues 取得所有目前的原始碼，或任何複雜屬性的值會傳回資料庫實體的值，做為巢狀 DbPropertyValues 物件。</span><span class="sxs-lookup"><span data-stu-id="b221b-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="b221b-180">這些物件可以巢狀結構，然後用來取得的複雜物件的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="b221b-181">例如，下列方法會印出所有的屬性，包括任何複雜的屬性和巢狀複雜屬性的值的值。</span><span class="sxs-lookup"><span data-stu-id="b221b-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="b221b-182">若要印出所有目前的屬性值的方法會呼叫，像這樣：</span><span class="sxs-lookup"><span data-stu-id="b221b-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
