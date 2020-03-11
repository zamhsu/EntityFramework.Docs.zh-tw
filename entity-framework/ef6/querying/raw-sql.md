---
title: 原始 SQL 查詢-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 168aee67186535bf2a50705e86bfc5a88147e369
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417092"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢
Entity Framework 可讓您使用 LINQ 搭配實體類別來進行查詢。 不過，有時候您可能會想要直接針對資料庫使用原始 SQL 來執行查詢。 這包括呼叫預存程式，對於目前不支援對應至預存程式的 Code First 模型而言，這很有説明。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="writing-sql-queries-for-entities"></a>撰寫實體的 SQL 查詢  

DbSet 上的 SqlQuery 方法可讓您撰寫會傳回實體實例的原始 SQL 查詢。 傳回的物件會由內容追蹤，就如同 LINQ 查詢所傳回的一樣。 例如：  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

請注意，就像 LINQ 查詢一樣，查詢不會執行，直到列舉結果為止-在上述範例中，這是透過呼叫 ToList 來完成。  

每次寫入原始 SQL 查詢時都應該小心，原因有兩個。 首先，應撰寫查詢，以確保它只會傳回實際為所要求類型的實體。 例如，使用繼承之類的功能時，很容易就能撰寫查詢來建立屬於錯誤 CLR 類型的實體。  

其次，某些類型的原始 SQL 查詢會公開潛在的安全性風險，特別是在 SQL 插入式攻擊方面。 請確定您以正確的方式在查詢中使用參數，以防範這類攻擊。  

### <a name="loading-entities-from-stored-procedures"></a>從預存程式載入實體  

您可以使用 DbSet，從預存程式的結果載入實體。 例如，下列程式碼會呼叫 dbo。資料庫中的 GetBlogs 程式：  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

您也可以使用下列語法，將參數傳遞至預存程式：  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>撰寫非實體類型的 SQL 查詢  

您可以使用資料庫類別上的 SqlQuery 方法，來建立傳回任何類型實例的 SQL 查詢，包括基本類型。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

即使物件是實體類型的實例，內容也永遠不會追蹤從資料庫上的 SqlQuery 傳回的結果。  

## <a name="sending-raw-commands-to-the-database"></a>將原始命令傳送至資料庫  

您可以使用資料庫上的 ExecuteSqlCommand 方法，將非查詢命令傳送到資料庫。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

請注意，使用 ExecuteSqlCommand 對資料庫中的資料所做的任何變更，都是不透明的內容，直到從資料庫載入或重載實體為止。  

### <a name="output-parameters"></a>輸出參數  

如果使用輸出參數，則在完全讀取結果之前，其值將無法使用。 這是因為 DbDataReader 的基礎行為，請參閱[使用 DataReader 抓取資料](https://go.microsoft.com/fwlink/?LinkID=398589)以取得詳細資訊。  
