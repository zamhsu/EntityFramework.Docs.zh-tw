---
title: 處理並行衝突-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: f233af217287dd6bf35e5b7fea8e44974168b312
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997806"
---
# <a name="handling-concurrency-conflicts"></a>處理並行存取衝突
開放式並行存取包括樂觀地嘗試將您的實體儲存到該處的資料實體之後並未變更，希望在資料庫已載入。 事實上，如果資料已變更，則會擲回例外狀況，您必須解決衝突，然後再嘗試再次儲存。 本主題涵蓋如何處理 Entity Framework 中的這類例外狀況。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

這篇文章不是適合的開放式並行存取的完整討論。 下列各節假設並行處理解決方式的一些知識，並顯示為一般工作的模式。  

有多項這些模式的使用中所討論的主題[使用屬性值](~/ef6/saving/change-tracking/property-values.md)。  

解決並行存取問題，當您使用獨立關聯 （其中外部索引鍵未對應到您的實體中的屬性） 會比當您使用外部索引鍵關聯更為困難。 因此如果您要在您的應用程式中的並行解析它會建議您一律將外部索引鍵對應至您的實體。 以下所有範例中都假設您使用的外部索引鍵關聯。  

嘗試儲存會使用外部索引鍵關聯的實體時偵測到的開放式並行存取例外狀況時，由 SaveChanges 會擲回 DbUpdateConcurrencyException。  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>解決開放式並行存取例外狀況，以重新載入 (資料庫 wins)  

Reload 方法可用來覆寫實體的目前值，現在在資料庫中的值。 實體然後通常還給某種形式的使用者，他們必須試著再次進行其變更並重新儲存。 例如:   

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

模擬並行例外狀況的好方法是以 SaveChanges 呼叫上設定中斷點，然後修改 實體儲存在資料庫中使用 SQL Management Studio 等其他工具。 您也可以插入之前要直接使用 SqlCommand 更新資料庫的 SaveChanges 行。 例如:   

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

DbUpdateConcurrencyException 的項目方法會傳回無法更新實體的 DbEntityEntry 執行個體。 （這個屬性目前一律會傳回單一值的並行處理問題。 它可能會傳回多個值的一般更新例外狀況。）某些情況下的替代方式是以取得可能需要重新載入從資料庫的所有實體的項目並呼叫重新載入的每一種。  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>為用戶端獲勝解決開放式並行存取例外狀況  

上述範例會重新載入有時也稱為資料庫 wins，或存放區獲勝，因為在實體中的值會覆寫資料庫中的值。 有時候您可能想要反過來，並以目前在實體中的值覆寫在資料庫中的值。 這有時稱為用戶端獲勝，而且可藉由取得目前資料庫的值，並將它們設定為實體的原始值。 (請參閱[屬性值使用](~/ef6/saving/change-tracking/property-values.md)如需目前和原始值。)例如:   

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>自訂解析開放式並行存取例外狀況  

有時候您可能要結合目前在實體中的值中的目前資料庫中的值。 這通常需要一些自訂邏輯或使用者互動。 例如，您可能會包含目前的值，在資料庫中，值對使用者顯示表單和一組預設解析的值。 使用者可以再編輯解析的值，必要時，也會解析取得儲存到資料庫中的值。 這可以使用 DbPropertyValues 物件 CurrentValues 和 GetDatabaseValues 上傳回實體的項目。 例如:   

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>使用物件的開放式並行存取例外狀況的自訂解析  

上述程式碼會使用 DbPropertyValues 執行個體的目前傳遞、 資料庫和解析的值。 有時可能會用於這個實體型別的執行個體的工作變得更容易。 這可以利用 DbPropertyValues ToObject 和 SetValues 的方法。 例如:   

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
