---
title: 處理並行衝突-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 81ae186201fdfac331b1d4e7836b222545fe78b5
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419689"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="638b6-102">處理並行存取衝突</span><span class="sxs-lookup"><span data-stu-id="638b6-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="638b6-103">開放式平行存取牽涉到樂觀地嘗試將實體儲存至資料庫，希望資料在載入實體之後尚未變更。</span><span class="sxs-lookup"><span data-stu-id="638b6-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="638b6-104">如果資料已變更，就會擲回例外狀況，而且您必須先解決衝突，然後再嘗試重新儲存。</span><span class="sxs-lookup"><span data-stu-id="638b6-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="638b6-105">本主題涵蓋如何在 Entity Framework 中處理這類例外狀況。</span><span class="sxs-lookup"><span data-stu-id="638b6-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="638b6-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="638b6-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="638b6-107">這篇文章並不是完整討論開放式平行存取的適當位置。</span><span class="sxs-lookup"><span data-stu-id="638b6-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="638b6-108">下列各節假設有一些並行解析知識，並顯示一般工作的模式。</span><span class="sxs-lookup"><span data-stu-id="638b6-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="638b6-109">這些模式中有許多都是利用使用[屬性值](~/ef6/saving/change-tracking/property-values.md)中所討論的主題。</span><span class="sxs-lookup"><span data-stu-id="638b6-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="638b6-110">解決使用獨立關聯（其中外鍵未對應至實體中的屬性）時的並行問題，比使用外鍵關聯更為棘手。</span><span class="sxs-lookup"><span data-stu-id="638b6-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="638b6-111">因此，如果您要在應用程式中進行並行解析，建議您一律將外鍵對應至您的實體。</span><span class="sxs-lookup"><span data-stu-id="638b6-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="638b6-112">下列所有範例都假設您使用的是外鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="638b6-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="638b6-113">當嘗試儲存使用外鍵關聯的實體時，在偵測到開放式平行存取例外狀況時，SaveChanges 會擲回 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="638b6-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="638b6-114">使用重載來解析開放式平行存取例外狀況（資料庫勝出）</span><span class="sxs-lookup"><span data-stu-id="638b6-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="638b6-115">Reload 方法可以用來以目前在資料庫中的值來覆寫實體目前的值。</span><span class="sxs-lookup"><span data-stu-id="638b6-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="638b6-116">接著，實體會以某種形式傳回給使用者，而且必須嘗試再次進行變更並重新儲存。</span><span class="sxs-lookup"><span data-stu-id="638b6-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="638b6-117">例如：</span><span class="sxs-lookup"><span data-stu-id="638b6-117">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="638b6-118">模擬並行例外狀況的好方法是在 SaveChanges 呼叫上設定中斷點，然後使用 SQL Management Studio 之類的其他工具來修改儲存在資料庫中的實體。</span><span class="sxs-lookup"><span data-stu-id="638b6-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Management Studio.</span></span> <span data-ttu-id="638b6-119">您也可以在 SaveChanges 之前插入一行，以使用 SqlCommand 直接更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="638b6-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="638b6-120">例如：</span><span class="sxs-lookup"><span data-stu-id="638b6-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="638b6-121">DbUpdateConcurrencyException 上的專案方法會傳回無法更新之實體的 DbEntityEntry 實例。</span><span class="sxs-lookup"><span data-stu-id="638b6-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="638b6-122">（此屬性目前一律會針對並行問題傳回單一值。</span><span class="sxs-lookup"><span data-stu-id="638b6-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="638b6-123">它可能會傳回多個一般更新例外狀況的值）。在某些情況下，有一個替代方法，就是取得可能需要從資料庫重載的所有實體專案，並針對每個實體呼叫重載。</span><span class="sxs-lookup"><span data-stu-id="638b6-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="638b6-124">解決用戶端獲勝的開放式平行存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="638b6-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="638b6-125">上述使用重載的範例有時稱為資料庫 wins 或存放區 wins，因為實體中的值會由資料庫中的值覆寫。</span><span class="sxs-lookup"><span data-stu-id="638b6-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="638b6-126">有時候，您可能會想要執行相反的動作，並將資料庫中的值覆寫為目前在實體中的值。</span><span class="sxs-lookup"><span data-stu-id="638b6-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="638b6-127">這有時稱為用戶端 wins，可以藉由取得目前的資料庫值並將其設定為實體的原始值來完成。</span><span class="sxs-lookup"><span data-stu-id="638b6-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="638b6-128">（如需目前和原始值的相關資訊，請參閱[使用屬性值](~/ef6/saving/change-tracking/property-values.md)）。例如：</span><span class="sxs-lookup"><span data-stu-id="638b6-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="638b6-129">開放式平行存取例外狀況的自訂解析</span><span class="sxs-lookup"><span data-stu-id="638b6-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="638b6-130">有時候，您可能會想要將目前在資料庫中的值與實體中目前的值結合。</span><span class="sxs-lookup"><span data-stu-id="638b6-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="638b6-131">這通常需要一些自訂邏輯或使用者互動。</span><span class="sxs-lookup"><span data-stu-id="638b6-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="638b6-132">例如，您可能會向使用者呈現一個表單，其中包含目前的值、資料庫中的值，以及一組預設的已解析值。</span><span class="sxs-lookup"><span data-stu-id="638b6-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="638b6-133">然後，使用者會視需要編輯已解析的值，而這會是儲存到資料庫中的這些已解析的值。</span><span class="sxs-lookup"><span data-stu-id="638b6-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="638b6-134">您可以使用從 CurrentValues 傳回的 DbPropertyValues 物件，並 GetDatabaseValues 實體的專案來完成這項作業。</span><span class="sxs-lookup"><span data-stu-id="638b6-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="638b6-135">例如：</span><span class="sxs-lookup"><span data-stu-id="638b6-135">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="638b6-136">使用物件的開放式平行存取例外狀況自訂解析</span><span class="sxs-lookup"><span data-stu-id="638b6-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="638b6-137">上述程式碼會使用 DbPropertyValues 實例來傳遞目前、資料庫和已解析的值。</span><span class="sxs-lookup"><span data-stu-id="638b6-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="638b6-138">有時候，您可以更輕鬆地使用實體類型的實例來進行此作業。</span><span class="sxs-lookup"><span data-stu-id="638b6-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="638b6-139">這可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法來完成。</span><span class="sxs-lookup"><span data-stu-id="638b6-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="638b6-140">例如：</span><span class="sxs-lookup"><span data-stu-id="638b6-140">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
