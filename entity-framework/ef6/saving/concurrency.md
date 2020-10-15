---
title: 處理並行衝突-EF6
description: 處理 Entity Framework 6 中的並行衝突
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/concurrency
ms.openlocfilehash: 0cec285ab6071120e162567506d397a23c299177
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064506"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="62679-103">處理 (EF6) 的並行衝突</span><span class="sxs-lookup"><span data-stu-id="62679-103">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="62679-104">開放式平行存取牽涉到樂觀地嘗試將您的實體儲存至資料庫，希望資料在載入實體之後沒有變更。</span><span class="sxs-lookup"><span data-stu-id="62679-104">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="62679-105">如果結果是資料已變更，則會擲回例外狀況，而且您必須先解決衝突，然後再嘗試重新儲存。</span><span class="sxs-lookup"><span data-stu-id="62679-105">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="62679-106">本主題說明如何在 Entity Framework 中處理這類例外狀況。</span><span class="sxs-lookup"><span data-stu-id="62679-106">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="62679-107">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="62679-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="62679-108">這篇文章並不適合完整討論開放式平行存取的位置。</span><span class="sxs-lookup"><span data-stu-id="62679-108">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="62679-109">下列各節假設有一些並行解析的知識，並顯示一般工作的模式。</span><span class="sxs-lookup"><span data-stu-id="62679-109">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="62679-110">這些模式中有許多都使用了使用 [屬性值](xref:ef6/saving/change-tracking/property-values)的討論主題。</span><span class="sxs-lookup"><span data-stu-id="62679-110">Many of these patterns make use of the topics discussed in [Working with Property Values](xref:ef6/saving/change-tracking/property-values).</span></span>  

<span data-ttu-id="62679-111">解決使用獨立關聯的並行問題 (其中外鍵不會對應至您的實體中的屬性) 比使用外鍵關聯時更為困難。</span><span class="sxs-lookup"><span data-stu-id="62679-111">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="62679-112">因此，如果您要在應用程式中執行並行解析，建議您一律將外鍵對應至您的實體。</span><span class="sxs-lookup"><span data-stu-id="62679-112">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="62679-113">以下所有範例都假設您使用外鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="62679-113">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="62679-114">當嘗試儲存使用外鍵關聯的實體時，如果偵測到開放式平行存取例外狀況，則 SaveChanges 會擲回 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="62679-114">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="62679-115">使用重載 (資料庫 wins) 解析開放式平行存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="62679-115">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="62679-116">您可以使用重載方法，以目前在資料庫中的值來覆寫實體目前的值。</span><span class="sxs-lookup"><span data-stu-id="62679-116">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="62679-117">然後，實體通常會以某種形式傳回給使用者，而且必須嘗試再次進行變更並重新儲存。</span><span class="sxs-lookup"><span data-stu-id="62679-117">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="62679-118">例如︰</span><span class="sxs-lookup"><span data-stu-id="62679-118">For example:</span></span>  

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

<span data-ttu-id="62679-119">模擬平行存取例外狀況的一個好方法是在 SaveChanges 呼叫上設定中斷點，然後使用其他工具（例如 SQL Server Management Studio）來修改儲存在資料庫中的實體。</span><span class="sxs-lookup"><span data-stu-id="62679-119">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="62679-120">您也可以在 SaveChanges 之前插入一行，以使用 SqlCommand 直接更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="62679-120">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="62679-121">例如︰</span><span class="sxs-lookup"><span data-stu-id="62679-121">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="62679-122">DbUpdateConcurrencyException 上的專案方法會傳回無法更新之實體的 DbEntityEntry 實例。</span><span class="sxs-lookup"><span data-stu-id="62679-122">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="62679-123"> (這個屬性目前一律會針對並行問題傳回單一值。</span><span class="sxs-lookup"><span data-stu-id="62679-123">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="62679-124">它可能會傳回多個一般更新例外狀況的值。在某些情況下，) 替代方法，可能是取得可能需要從資料庫重載的所有實體的專案，並對每個實體呼叫重載。</span><span class="sxs-lookup"><span data-stu-id="62679-124">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="62679-125">解決用戶端獲勝的開放式平行存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="62679-125">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="62679-126">上述使用重載的範例有時稱為「資料庫獲勝」或「儲存 wins」，因為實體中的值會由資料庫中的值覆寫。</span><span class="sxs-lookup"><span data-stu-id="62679-126">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="62679-127">有時候您可能會想要相反地，並使用目前在實體中的值來覆寫資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="62679-127">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="62679-128">這有時稱為用戶端優先，而且可以藉由取得目前的資料庫值，並將其設定為實體的原始值來完成。</span><span class="sxs-lookup"><span data-stu-id="62679-128">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="62679-129"> (參閱 [使用屬性值](xref:ef6/saving/change-tracking/property-values) 來取得目前值和原始值的相關資訊 ) 。例如：</span><span class="sxs-lookup"><span data-stu-id="62679-129">(See [Working with Property Values](xref:ef6/saving/change-tracking/property-values) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="62679-130">自訂開放式平行存取例外狀況的解決方式</span><span class="sxs-lookup"><span data-stu-id="62679-130">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="62679-131">有時候您可能會想要將目前在資料庫中的值與實體中目前的值結合。</span><span class="sxs-lookup"><span data-stu-id="62679-131">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="62679-132">這通常需要一些自訂邏輯或使用者互動。</span><span class="sxs-lookup"><span data-stu-id="62679-132">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="62679-133">例如，您可能會向使用者呈現表單，其中包含目前的值、資料庫中的值，以及一組預設的已解決值。</span><span class="sxs-lookup"><span data-stu-id="62679-133">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="62679-134">然後，使用者會視需要編輯已解析的值，並將這些值儲存至資料庫。</span><span class="sxs-lookup"><span data-stu-id="62679-134">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="62679-135">您可以使用從 CurrentValues 傳回的 DbPropertyValues 物件，然後在實體的專案上 GetDatabaseValues，來完成這項工作。</span><span class="sxs-lookup"><span data-stu-id="62679-135">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="62679-136">例如︰</span><span class="sxs-lookup"><span data-stu-id="62679-136">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="62679-137">使用物件自訂開放式平行存取例外狀況的解決方式</span><span class="sxs-lookup"><span data-stu-id="62679-137">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="62679-138">上述程式碼使用 DbPropertyValues 實例來傳遞目前、資料庫和已解析的值。</span><span class="sxs-lookup"><span data-stu-id="62679-138">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="62679-139">有時候，使用實體類型的實例可能比較容易。</span><span class="sxs-lookup"><span data-stu-id="62679-139">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="62679-140">您可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法來完成這項工作。</span><span class="sxs-lookup"><span data-stu-id="62679-140">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="62679-141">例如︰</span><span class="sxs-lookup"><span data-stu-id="62679-141">For example:</span></span>  

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
