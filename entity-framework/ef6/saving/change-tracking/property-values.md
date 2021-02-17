---
title: 使用屬性值-EF6
description: 使用 Entity Framework 6 中的屬性值
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 4e9912df960481232c492f8a83d15595800edec3
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543558"
---
# <a name="working-with-property-values"></a><span data-ttu-id="df85d-103">使用屬性值</span><span class="sxs-lookup"><span data-stu-id="df85d-103">Working with property values</span></span>
<span data-ttu-id="df85d-104">在大部分的情況下，Entity Framework 會負責追蹤實體實例之屬性的狀態、原始值和目前值。</span><span class="sxs-lookup"><span data-stu-id="df85d-104">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="df85d-105">不過，在某些情況下可能會發生，例如已中斷連線的情況-您想要在其中查看或操作 EF 對屬性的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="df85d-105">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="df85d-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="df85d-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="df85d-107">Entity Framework 針對追蹤實體的每個屬性追蹤兩個值。</span><span class="sxs-lookup"><span data-stu-id="df85d-107">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="df85d-108">目前的值是指實體中屬性的目前值，如同名稱所示。</span><span class="sxs-lookup"><span data-stu-id="df85d-108">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="df85d-109">原始值是在從資料庫查詢或附加至內容的實體時，該屬性具有的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-109">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="df85d-110">使用屬性值的一般機制有兩種：</span><span class="sxs-lookup"><span data-stu-id="df85d-110">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="df85d-111">您可以使用屬性方法，以強型別的方式取得單一屬性的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-111">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="df85d-112">實體的所有屬性值都可讀入 DbPropertyValues 物件中。</span><span class="sxs-lookup"><span data-stu-id="df85d-112">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="df85d-113">然後，DbPropertyValues 會作為類似字典的物件，以允許讀取和設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="df85d-113">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="df85d-114">DbPropertyValues 物件中的值可以從另一個 DbPropertyValues 物件的值，或從其他物件中的值設定，例如實體的另一個複本或 (DTO) 的簡單資料傳輸物件。</span><span class="sxs-lookup"><span data-stu-id="df85d-114">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="df85d-115">下列各節顯示使用上述兩種機制的範例。</span><span class="sxs-lookup"><span data-stu-id="df85d-115">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="df85d-116">取得和設定個別屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="df85d-116">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="df85d-117">下列範例顯示如何讀取屬性的目前值，然後將其設定為新值：</span><span class="sxs-lookup"><span data-stu-id="df85d-117">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

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

<span data-ttu-id="df85d-118">使用 OriginalValue 屬性，而非 CurrentValue 屬性來讀取或設定原始的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-118">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="df85d-119">請注意，當使用字串來指定屬性名稱時，傳回的值會輸入為「物件」。</span><span class="sxs-lookup"><span data-stu-id="df85d-119">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="df85d-120">另一方面，如果使用 lambda 運算式，則傳回的值為強型別。</span><span class="sxs-lookup"><span data-stu-id="df85d-120">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="df85d-121">如果新值與舊值不同，則將屬性值設定為，就只會將屬性標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="df85d-121">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="df85d-122">以這種方式設定屬性值時，即使已關閉 AutoDetectChanges，也會自動偵測到變更。</span><span class="sxs-lookup"><span data-stu-id="df85d-122">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="df85d-123">取得和設定未對應之屬性的目前值</span><span class="sxs-lookup"><span data-stu-id="df85d-123">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="df85d-124">尚未對應至資料庫的屬性目前值也可以讀取。</span><span class="sxs-lookup"><span data-stu-id="df85d-124">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="df85d-125">未對應的屬性範例可能是 Blog 上的 .Rsslink 屬性。</span><span class="sxs-lookup"><span data-stu-id="df85d-125">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="df85d-126">您可以根據 BlogId 來計算這個值，因此不需要將它儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="df85d-126">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="df85d-127">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-127">For example:</span></span>  

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

<span data-ttu-id="df85d-128">如果屬性公開 setter，也可以設定目前的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-128">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="df85d-129">當執行未對應屬性的 Entity Framework 驗證時，讀取未對應屬性的值會很有用。</span><span class="sxs-lookup"><span data-stu-id="df85d-129">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="df85d-130">基於相同的原因，目前值可以針對目前未由內容追蹤的實體屬性進行讀取和設定。</span><span class="sxs-lookup"><span data-stu-id="df85d-130">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="df85d-131">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-131">For example:</span></span>  

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

<span data-ttu-id="df85d-132">請注意，原始值不適用於未對應的屬性，或不是內容所追蹤之實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="df85d-132">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="df85d-133">檢查屬性是否標記為已修改</span><span class="sxs-lookup"><span data-stu-id="df85d-133">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="df85d-134">下列範例顯示如何檢查個別屬性是否標記為已修改：</span><span class="sxs-lookup"><span data-stu-id="df85d-134">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="df85d-135">當呼叫 SaveChanges 時，已修改的屬性值會以更新的形式傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="df85d-135">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="df85d-136">將屬性標示為已修改</span><span class="sxs-lookup"><span data-stu-id="df85d-136">Marking a property as modified</span></span>  

<span data-ttu-id="df85d-137">下列範例顯示如何強制個別屬性標示為已修改：</span><span class="sxs-lookup"><span data-stu-id="df85d-137">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="df85d-138">將屬性標示為已修改時，會在呼叫 SaveChanges 時，強制將更新傳送至資料庫，即使屬性的目前值與原始值相同也一樣。</span><span class="sxs-lookup"><span data-stu-id="df85d-138">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="df85d-139">目前無法在標記為修改之後，將個別的屬性重設為不修改。</span><span class="sxs-lookup"><span data-stu-id="df85d-139">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="df85d-140">這是我們計畫在未來版本中支援的功能。</span><span class="sxs-lookup"><span data-stu-id="df85d-140">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="df85d-141">讀取實體所有屬性的目前、原始和資料庫值</span><span class="sxs-lookup"><span data-stu-id="df85d-141">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="df85d-142">下列範例顯示如何讀取實體中所有對應屬性的目前值、原始值，以及實際在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-142">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="df85d-143">目前的值是實體屬性目前包含的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-143">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="df85d-144">原始值是查詢實體時，從資料庫讀取的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-144">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="df85d-145">資料庫值是目前儲存在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-145">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="df85d-146">當資料庫中的值在查詢實體之後可能已經變更（例如，當其他使用者對資料庫進行並行編輯時），取得資料庫值會很有用。</span><span class="sxs-lookup"><span data-stu-id="df85d-146">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="df85d-147">從另一個物件設定目前或原始的值</span><span class="sxs-lookup"><span data-stu-id="df85d-147">Setting current or original values from another object</span></span>  

<span data-ttu-id="df85d-148">您可以從另一個物件複製值，以更新已追蹤實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="df85d-148">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="df85d-149">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-149">For example:</span></span>  

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

<span data-ttu-id="df85d-150">執行上述程式碼將會列印出：</span><span class="sxs-lookup"><span data-stu-id="df85d-150">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="df85d-151">使用從服務呼叫取得的值或多層式應用程式中的用戶端來更新實體時，有時會使用這項技術。</span><span class="sxs-lookup"><span data-stu-id="df85d-151">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="df85d-152">請注意，使用的物件不一定要與實體具有相同的類型，只要它的屬性名稱符合實體的名稱即可。</span><span class="sxs-lookup"><span data-stu-id="df85d-152">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="df85d-153">在上述範例中，會使用 BlogDTO 的實例來更新原始的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-153">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="df85d-154">請注意，從其他物件複製時，只有設定為不同值的屬性會標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="df85d-154">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="df85d-155">從字典設定目前或原始的值</span><span class="sxs-lookup"><span data-stu-id="df85d-155">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="df85d-156">您可以從字典或其他資料結構複製值，以更新已追蹤實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="df85d-156">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="df85d-157">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-157">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary<string, object>
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

<span data-ttu-id="df85d-158">使用 OriginalValues 屬性，而非 CurrentValues 屬性來設定原始值。</span><span class="sxs-lookup"><span data-stu-id="df85d-158">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="df85d-159">使用屬性設定字典的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="df85d-159">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="df85d-160">如上面所示，使用 CurrentValues 或 OriginalValues 的替代方法是使用 Property 方法來設定每個屬性的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-160">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="df85d-161">當您需要設定複雜屬性的值時，最好這麼做。</span><span class="sxs-lookup"><span data-stu-id="df85d-161">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="df85d-162">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-162">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary<string, object>
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

<span data-ttu-id="df85d-163">在上述範例中，複雜屬性是使用點名稱來存取。</span><span class="sxs-lookup"><span data-stu-id="df85d-163">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="df85d-164">如需其他存取複雜屬性的方式，請參閱本主題稍後的兩個部分，具體而言就是複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="df85d-164">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="df85d-165">建立包含目前、原始或資料庫值的複製物件</span><span class="sxs-lookup"><span data-stu-id="df85d-165">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="df85d-166">從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 DbPropertyValues 物件，可以用來建立實體的複製。</span><span class="sxs-lookup"><span data-stu-id="df85d-166">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="df85d-167">這個複製將包含用來建立它之 DbPropertyValues 物件的屬性值。</span><span class="sxs-lookup"><span data-stu-id="df85d-167">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="df85d-168">例如：</span><span class="sxs-lookup"><span data-stu-id="df85d-168">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="df85d-169">請注意，傳回的物件不是實體，而且不是由內容所追蹤。</span><span class="sxs-lookup"><span data-stu-id="df85d-169">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="df85d-170">傳回的物件也不會將任何關聯性設定為其他物件。</span><span class="sxs-lookup"><span data-stu-id="df85d-170">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="df85d-171">複製的物件有助於解決與資料庫並行更新相關的問題，尤其是在使用涉及資料系結至特定類型物件的 UI 時。</span><span class="sxs-lookup"><span data-stu-id="df85d-171">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="df85d-172">取得和設定複雜屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="df85d-172">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="df85d-173">您可以使用屬性方法來讀取和設定整個複雜物件的值，就像是針對基本屬性一樣。</span><span class="sxs-lookup"><span data-stu-id="df85d-173">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="df85d-174">此外，您可以向下切入至複雜物件，並讀取或設定該物件的屬性，甚至是嵌套物件。</span><span class="sxs-lookup"><span data-stu-id="df85d-174">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="df85d-175">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="df85d-175">Here are some examples:</span></span>  

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

<span data-ttu-id="df85d-176">使用 OriginalValue 屬性，而非 CurrentValue 屬性來取得或設定原始值。</span><span class="sxs-lookup"><span data-stu-id="df85d-176">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="df85d-177">請注意，您可以使用屬性或 ComplexProperty 方法來存取複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="df85d-177">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="df85d-178">但是，如果您想要向下切入具有其他屬性或 ComplexProperty 呼叫的複雜物件，則必須使用 ComplexProperty 方法。</span><span class="sxs-lookup"><span data-stu-id="df85d-178">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="df85d-179">使用 DbPropertyValues 存取複雜屬性</span><span class="sxs-lookup"><span data-stu-id="df85d-179">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="df85d-180">當您使用 CurrentValues、OriginalValues 或 GetDatabaseValues 取得實體的所有目前、原始或資料庫值時，任何複雜屬性的值都會以嵌套 DbPropertyValues 物件的形式傳回。</span><span class="sxs-lookup"><span data-stu-id="df85d-180">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="df85d-181">然後可以使用這些嵌套物件來取得複雜物件的值。</span><span class="sxs-lookup"><span data-stu-id="df85d-181">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="df85d-182">例如，下列方法會列印出所有屬性的值，包括任何複雜屬性的值和嵌套的複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="df85d-182">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="df85d-183">若要列印出所有目前的屬性值，則會呼叫方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="df85d-183">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
