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
# <a name="working-with-property-values"></a><span data-ttu-id="47b3c-102">使用屬性值</span><span class="sxs-lookup"><span data-stu-id="47b3c-102">Working with property values</span></span>
<span data-ttu-id="47b3c-103">在大部分的情況下，Entity Framework 會負責追蹤實體實例屬性的狀態、原始值和目前的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="47b3c-104">不過，在某些情況下（例如，中斷連線的案例），您想要在其中查看或操作 EF 相關資訊的屬性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="47b3c-105">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="47b3c-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="47b3c-106">Entity Framework 會針對追蹤實體的每個屬性追蹤兩個值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="47b3c-107">目前的值是，如名稱所示，是實體中屬性的目前值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="47b3c-108">原始值是從資料庫查詢實體或附加至內容時，屬性所擁有的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="47b3c-109">使用屬性值的一般機制有兩種：</span><span class="sxs-lookup"><span data-stu-id="47b3c-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="47b3c-110">您可以使用屬性方法，以強型別方式取得單一屬性的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="47b3c-111">實體的所有屬性值都可以讀入 DbPropertyValues 物件中。</span><span class="sxs-lookup"><span data-stu-id="47b3c-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="47b3c-112">然後，DbPropertyValues 會作為類似字典的物件，以允許讀取和設定屬性值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="47b3c-113">DbPropertyValues 物件中的值可以從另一個 DbPropertyValues 物件中的值，或從其他物件的值（例如，實體的另一個複本或簡單的資料傳輸物件（DTO））進行設定。</span><span class="sxs-lookup"><span data-stu-id="47b3c-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="47b3c-114">下列各節顯示使用上述這兩種機制的範例。</span><span class="sxs-lookup"><span data-stu-id="47b3c-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="47b3c-115">取得和設定個別屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="47b3c-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="47b3c-116">下列範例顯示如何讀取屬性的目前值，然後將它設定為新的值：</span><span class="sxs-lookup"><span data-stu-id="47b3c-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

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

<span data-ttu-id="47b3c-117">使用 OriginalValue 屬性，而不是 CurrentValue 屬性來讀取或設定原始值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="47b3c-118">請注意，當使用字串來指定屬性名稱時，傳回的值會輸入為「物件」。</span><span class="sxs-lookup"><span data-stu-id="47b3c-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="47b3c-119">另一方面，如果使用 lambda 運算式，傳回的值就是強型別。</span><span class="sxs-lookup"><span data-stu-id="47b3c-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="47b3c-120">如果新值與舊值不同，設定屬性值（例如這樣）只會將屬性標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="47b3c-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="47b3c-121">以這種方式設定屬性值時，即使已關閉 AutoDetectChanges，也會自動偵測變更。</span><span class="sxs-lookup"><span data-stu-id="47b3c-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="47b3c-122">取得和設定未對應屬性的目前值</span><span class="sxs-lookup"><span data-stu-id="47b3c-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="47b3c-123">尚未對應至資料庫之屬性的目前值也可以讀取。</span><span class="sxs-lookup"><span data-stu-id="47b3c-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="47b3c-124">未對應屬性的範例可能是在 Blog 上的 .Rsslink 屬性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="47b3c-125">此值可能會根據 BlogId 計算，因此不需要儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="47b3c-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="47b3c-126">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-126">For example:</span></span>  

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

<span data-ttu-id="47b3c-127">如果屬性公開 setter，則也可以設定目前的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="47b3c-128">執行未對應屬性的 Entity Framework 驗證時，讀取未對應屬性的值會很有用。</span><span class="sxs-lookup"><span data-stu-id="47b3c-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="47b3c-129">基於相同原因，可以針對目前未由內容追蹤之實體的屬性，讀取和設定目前的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="47b3c-130">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-130">For example:</span></span>  

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

<span data-ttu-id="47b3c-131">請注意，原始值不適用於未對應的屬性，或未由內容追蹤之實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="47b3c-132">檢查屬性是否標示為已修改</span><span class="sxs-lookup"><span data-stu-id="47b3c-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="47b3c-133">下列範例顯示如何檢查個別屬性是否標示為已修改：</span><span class="sxs-lookup"><span data-stu-id="47b3c-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="47b3c-134">呼叫 SaveChanges 時，已修改的屬性值會當做更新傳送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="47b3c-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="47b3c-135">將屬性標示為已修改</span><span class="sxs-lookup"><span data-stu-id="47b3c-135">Marking a property as modified</span></span>  

<span data-ttu-id="47b3c-136">下列範例顯示如何強制將個別屬性標示為已修改：</span><span class="sxs-lookup"><span data-stu-id="47b3c-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="47b3c-137">將屬性標示為已修改，會在呼叫 SaveChanges 時，強制將更新傳送至資料庫，即使屬性的目前值與原始值相同也一樣。</span><span class="sxs-lookup"><span data-stu-id="47b3c-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="47b3c-138">目前無法在將個別屬性標示為已修改之後，將它重設為未修改。</span><span class="sxs-lookup"><span data-stu-id="47b3c-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="47b3c-139">這是我們計畫在未來版本中支援的專案。</span><span class="sxs-lookup"><span data-stu-id="47b3c-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="47b3c-140">讀取實體所有屬性的目前、原始和資料庫值</span><span class="sxs-lookup"><span data-stu-id="47b3c-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="47b3c-141">下列範例顯示如何讀取實體的所有對應屬性的目前值、原始值，以及實際在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="47b3c-142">目前的值是實體的屬性所包含的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="47b3c-143">原始值是查詢實體時，從資料庫讀取的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="47b3c-144">資料庫值是目前儲存在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="47b3c-145">當資料庫中的值在查詢實體之後可能已變更，例如當另一位使用者對資料庫進行並行編輯時，取得資料庫值會很有用。</span><span class="sxs-lookup"><span data-stu-id="47b3c-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="47b3c-146">設定另一個物件的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="47b3c-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="47b3c-147">您可以藉由複製另一個物件的值，來更新已追蹤實體的目前或原始值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="47b3c-148">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-148">For example:</span></span>  

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

<span data-ttu-id="47b3c-149">執行上述程式碼將會列印出來：</span><span class="sxs-lookup"><span data-stu-id="47b3c-149">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="47b3c-150">當您使用從服務呼叫或多層式應用程式中的用戶端取得的值來更新實體時，有時會使用這項技術。</span><span class="sxs-lookup"><span data-stu-id="47b3c-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="47b3c-151">請注意，使用的物件不一定要與實體具有相同的類型，只要它有名稱符合實體的屬性即可。</span><span class="sxs-lookup"><span data-stu-id="47b3c-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="47b3c-152">在上述範例中，會使用 BlogDTO 的實例來更新原始值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="47b3c-153">請注意，從其他物件複製時，只有設定為不同值的屬性會標示為已修改。</span><span class="sxs-lookup"><span data-stu-id="47b3c-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="47b3c-154">從字典設定目前或原始值</span><span class="sxs-lookup"><span data-stu-id="47b3c-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="47b3c-155">已追蹤實體的目前或原始值可以藉由從字典或其他資料結構複製值來進行更新。</span><span class="sxs-lookup"><span data-stu-id="47b3c-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="47b3c-156">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-156">For example:</span></span>  

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

<span data-ttu-id="47b3c-157">使用 OriginalValues 屬性，而不是 CurrentValues 屬性來設定原始值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="47b3c-158">使用屬性設定字典中的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="47b3c-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="47b3c-159">如上面所示使用 CurrentValues 或 OriginalValues 的替代方式，是使用 Property 方法來設定每個屬性的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="47b3c-160">當您需要設定複雜屬性的值時，這會是較理想的作法。</span><span class="sxs-lookup"><span data-stu-id="47b3c-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="47b3c-161">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-161">For example:</span></span>  

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

<span data-ttu-id="47b3c-162">在上述範例中，會使用點名稱來存取複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="47b3c-163">如需存取複雜屬性的其他方式，請參閱本主題稍後關於複雜屬性的兩個章節。</span><span class="sxs-lookup"><span data-stu-id="47b3c-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="47b3c-164">建立包含目前、原始或資料庫值的複製物件</span><span class="sxs-lookup"><span data-stu-id="47b3c-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="47b3c-165">從 CurrentValues、OriginalValues 或 GetDatabaseValues 傳回的 DbPropertyValues 物件可以用來建立實體的複本。</span><span class="sxs-lookup"><span data-stu-id="47b3c-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="47b3c-166">這個複製將包含用來建立它的 DbPropertyValues 物件的屬性值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="47b3c-167">例如:</span><span class="sxs-lookup"><span data-stu-id="47b3c-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="47b3c-168">請注意，傳回的物件不是實體，而且不是由內容進行追蹤。</span><span class="sxs-lookup"><span data-stu-id="47b3c-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="47b3c-169">傳回的物件也沒有任何設定為其他物件的關聯性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="47b3c-170">複製的物件有助於解決與資料庫並行更新相關的問題，尤其是在使用牽涉到特定類型物件之資料系結的 UI 時。</span><span class="sxs-lookup"><span data-stu-id="47b3c-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="47b3c-171">取得和設定複雜屬性的目前或原始值</span><span class="sxs-lookup"><span data-stu-id="47b3c-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="47b3c-172">您可以使用 Property 方法來讀取和設定整個複雜物件的值，就像是針對基本屬性一樣。</span><span class="sxs-lookup"><span data-stu-id="47b3c-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="47b3c-173">此外，您可以向下切入至複雜物件，並讀取或設定該物件的屬性，甚至是嵌套的物件。</span><span class="sxs-lookup"><span data-stu-id="47b3c-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="47b3c-174">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="47b3c-174">Here are some examples:</span></span>  

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

<span data-ttu-id="47b3c-175">請使用 OriginalValue 屬性來取代 CurrentValue 屬性，以取得或設定原始值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="47b3c-176">請注意，屬性或 ComplexProperty 方法都可以用來存取複雜屬性。</span><span class="sxs-lookup"><span data-stu-id="47b3c-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="47b3c-177">不過，如果您想要向下切入至具有其他屬性或 ComplexProperty 呼叫的複雜物件，則必須使用 ComplexProperty 方法。</span><span class="sxs-lookup"><span data-stu-id="47b3c-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="47b3c-178">使用 DbPropertyValues 存取複雜屬性</span><span class="sxs-lookup"><span data-stu-id="47b3c-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="47b3c-179">當您使用 CurrentValues、OriginalValues 或 GetDatabaseValues 取得實體的所有目前、原始或資料庫值時，任何複雜屬性的值都會以 nested DbPropertyValues 物件的形式傳回。</span><span class="sxs-lookup"><span data-stu-id="47b3c-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="47b3c-180">然後，這些嵌套物件就可以用來取得複雜物件的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="47b3c-181">例如，下列方法會列印出所有屬性的值，包括任何複雜屬性和嵌套複雜屬性的值。</span><span class="sxs-lookup"><span data-stu-id="47b3c-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="47b3c-182">若要列印出所有目前的屬性值，方法會被呼叫，如下所示：</span><span class="sxs-lookup"><span data-stu-id="47b3c-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
