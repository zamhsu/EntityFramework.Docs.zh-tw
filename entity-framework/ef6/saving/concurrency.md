---
title: 處理並行衝突-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672717"
---
# <a name="handling-concurrency-conflicts"></a>處理並行存取衝突
開放式平行存取牽涉到樂觀地嘗試將實體儲存至資料庫，希望資料在載入實體之後尚未變更。 如果資料已變更，就會擲回例外狀況，而且您必須先解決衝突，然後再嘗試重新儲存。 本主題涵蓋如何在 Entity Framework 中處理這類例外狀況。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

這篇文章並不是完整討論開放式平行存取的適當位置。 下列各節假設有一些並行解析知識，並顯示一般工作的模式。  

這些模式中有許多都是利用使用[屬性值](~/ef6/saving/change-tracking/property-values.md)中所討論的主題。  

解決使用獨立關聯（其中外鍵未對應至實體中的屬性）時的並行問題，比使用外鍵關聯更為棘手。 因此，如果您要在應用程式中進行並行解析，建議您一律將外鍵對應至您的實體。 下列所有範例都假設您使用的是外鍵關聯。  

當嘗試儲存使用外鍵關聯的實體時，在偵測到開放式平行存取例外狀況時，SaveChanges 會擲回 DbUpdateConcurrencyException。  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>使用重載來解析開放式平行存取例外狀況（資料庫勝出）  

Reload 方法可以用來以目前在資料庫中的值來覆寫實體目前的值。 接著，實體會以某種形式傳回給使用者，而且必須嘗試再次進行變更並重新儲存。 例如：  

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

模擬平行存取例外狀況的好方法是在 SaveChanges 呼叫上設定中斷點，然後使用另一項工具（例如 SQL Server Management Studio）來修改儲存在資料庫中的實體。 您也可以在 SaveChanges 之前插入一行，以使用 SqlCommand 直接更新資料庫。 例如：  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

DbUpdateConcurrencyException 上的專案方法會傳回無法更新之實體的 DbEntityEntry 實例。 （此屬性目前一律會針對並行問題傳回單一值。 它可能會傳回多個一般更新例外狀況的值）。在某些情況下，有一個替代方法，就是取得可能需要從資料庫重載的所有實體專案，並針對每個實體呼叫重載。  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>解決用戶端獲勝的開放式平行存取例外狀況  

上述使用重載的範例有時稱為資料庫 wins 或存放區 wins，因為實體中的值會由資料庫中的值覆寫。 有時候，您可能會想要執行相反的動作，並將資料庫中的值覆寫為目前在實體中的值。 這有時稱為用戶端 wins，可以藉由取得目前的資料庫值並將其設定為實體的原始值來完成。 （如需目前和原始值的相關資訊，請參閱[使用屬性值](~/ef6/saving/change-tracking/property-values.md)）。例如：  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>開放式平行存取例外狀況的自訂解析  

有時候，您可能會想要將目前在資料庫中的值與實體中目前的值結合。 這通常需要一些自訂邏輯或使用者互動。 例如，您可能會向使用者呈現一個表單，其中包含目前的值、資料庫中的值，以及一組預設的已解析值。 然後，使用者會視需要編輯已解析的值，而這會是儲存到資料庫中的這些已解析的值。 您可以使用從 CurrentValues 傳回的 DbPropertyValues 物件，並 GetDatabaseValues 實體的專案來完成這項作業。 例如：  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>使用物件的開放式平行存取例外狀況自訂解析  

上述程式碼會使用 DbPropertyValues 實例來傳遞目前、資料庫和已解析的值。 有時候，您可以更輕鬆地使用實體類型的實例來進行此作業。 這可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法來完成。 例如：  

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
