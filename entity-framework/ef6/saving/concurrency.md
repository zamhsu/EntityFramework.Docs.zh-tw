---
title: 處理並行衝突-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
caps.latest.revision: 3
ms.openlocfilehash: b8608dbb4cadd60ff4ff4984583f8a9d843b3949
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120221"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="29fa9-102">處理並行存取衝突</span><span class="sxs-lookup"><span data-stu-id="29fa9-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="29fa9-103">開放式並行存取包括樂觀地嘗試將您的實體儲存到該處的資料實體之後並未變更，希望在資料庫已載入。</span><span class="sxs-lookup"><span data-stu-id="29fa9-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="29fa9-104">事實上，如果資料已變更，則會擲回例外狀況，您必須解決衝突，然後再嘗試再次儲存。</span><span class="sxs-lookup"><span data-stu-id="29fa9-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="29fa9-105">本主題涵蓋如何處理 Entity Framework 中的這類例外狀況。</span><span class="sxs-lookup"><span data-stu-id="29fa9-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="29fa9-106">本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。</span><span class="sxs-lookup"><span data-stu-id="29fa9-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="29fa9-107">這篇文章不是適合的開放式並行存取的完整討論。</span><span class="sxs-lookup"><span data-stu-id="29fa9-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="29fa9-108">下列各節假設並行處理解決方式的一些知識，並顯示為一般工作的模式。</span><span class="sxs-lookup"><span data-stu-id="29fa9-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="29fa9-109">有多項這些模式的使用中所討論的主題[使用屬性值](~/ef6/saving/change-tracking/property-values.md)。</span><span class="sxs-lookup"><span data-stu-id="29fa9-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="29fa9-110">解決並行存取問題，當您使用獨立關聯 （其中外部索引鍵未對應到您的實體中的屬性） 會比當您使用外部索引鍵關聯更為困難。</span><span class="sxs-lookup"><span data-stu-id="29fa9-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="29fa9-111">因此如果您要在您的應用程式中的並行解析它會建議您一律將外部索引鍵對應至您的實體。</span><span class="sxs-lookup"><span data-stu-id="29fa9-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="29fa9-112">以下所有範例中都假設您使用的外部索引鍵關聯。</span><span class="sxs-lookup"><span data-stu-id="29fa9-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="29fa9-113">嘗試儲存會使用外部索引鍵關聯的實體時偵測到的開放式並行存取例外狀況時，由 SaveChanges 會擲回 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="29fa9-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="29fa9-114">解決開放式並行存取例外狀況，以重新載入 (資料庫 wins)</span><span class="sxs-lookup"><span data-stu-id="29fa9-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="29fa9-115">Reload 方法可用來覆寫實體的目前值，現在在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="29fa9-116">實體然後通常還給某種形式的使用者，他們必須試著再次進行其變更並重新儲存。</span><span class="sxs-lookup"><span data-stu-id="29fa9-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="29fa9-117">例如: </span><span class="sxs-lookup"><span data-stu-id="29fa9-117">For example:</span></span>  

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

<span data-ttu-id="29fa9-118">模擬並行例外狀況的好方法是以 SaveChanges 呼叫上設定中斷點，然後修改 實體儲存在資料庫中使用 SQL Management Studio 等其他工具。</span><span class="sxs-lookup"><span data-stu-id="29fa9-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Management Studio.</span></span> <span data-ttu-id="29fa9-119">您也可以插入之前要直接使用 SqlCommand 更新資料庫的 SaveChanges 行。</span><span class="sxs-lookup"><span data-stu-id="29fa9-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="29fa9-120">例如: </span><span class="sxs-lookup"><span data-stu-id="29fa9-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="29fa9-121">DbUpdateConcurrencyException 的項目方法會傳回無法更新實體的 DbEntityEntry 執行個體。</span><span class="sxs-lookup"><span data-stu-id="29fa9-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="29fa9-122">（這個屬性目前一律會傳回單一值的並行處理問題。</span><span class="sxs-lookup"><span data-stu-id="29fa9-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="29fa9-123">它可能會傳回多個值的一般更新例外狀況。）某些情況下的替代方式是以取得可能需要重新載入從資料庫的所有實體的項目並呼叫重新載入的每一種。</span><span class="sxs-lookup"><span data-stu-id="29fa9-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="29fa9-124">為用戶端獲勝解決開放式並行存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="29fa9-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="29fa9-125">上述範例會重新載入有時也稱為資料庫 wins，或存放區獲勝，因為在實體中的值會覆寫資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="29fa9-126">有時候您可能想要反過來，並以目前在實體中的值覆寫在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="29fa9-127">這有時稱為用戶端獲勝，而且可藉由取得目前資料庫的值，並將它們設定為實體的原始值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="29fa9-128">(請參閱[屬性值使用](~/ef6/saving/change-tracking/property-values.md)如需目前和原始值。)例如: </span><span class="sxs-lookup"><span data-stu-id="29fa9-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="29fa9-129">自訂解析開放式並行存取例外狀況</span><span class="sxs-lookup"><span data-stu-id="29fa9-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="29fa9-130">有時候您可能要結合目前在實體中的值中的目前資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="29fa9-131">這通常需要一些自訂邏輯或使用者互動。</span><span class="sxs-lookup"><span data-stu-id="29fa9-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="29fa9-132">例如，您可能會包含目前的值，在資料庫中，值對使用者顯示表單和一組預設解析的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="29fa9-133">使用者可以再編輯解析的值，必要時，也會解析取得儲存到資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="29fa9-134">這可以使用 DbPropertyValues 物件 CurrentValues 和 GetDatabaseValues 上傳回實體的項目。</span><span class="sxs-lookup"><span data-stu-id="29fa9-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="29fa9-135">例如: </span><span class="sxs-lookup"><span data-stu-id="29fa9-135">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="29fa9-136">使用物件的開放式並行存取例外狀況的自訂解析</span><span class="sxs-lookup"><span data-stu-id="29fa9-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="29fa9-137">上述程式碼會使用 DbPropertyValues 執行個體的目前傳遞、 資料庫和解析的值。</span><span class="sxs-lookup"><span data-stu-id="29fa9-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="29fa9-138">有時可能會用於這個實體型別的執行個體的工作變得更容易。</span><span class="sxs-lookup"><span data-stu-id="29fa9-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="29fa9-139">這可以利用 DbPropertyValues ToObject 和 SetValues 的方法。</span><span class="sxs-lookup"><span data-stu-id="29fa9-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="29fa9-140">例如: </span><span class="sxs-lookup"><span data-stu-id="29fa9-140">For example:</span></span>  

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
