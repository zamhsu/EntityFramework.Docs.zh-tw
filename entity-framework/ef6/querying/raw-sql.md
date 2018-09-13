---
title: 原始 SQL 查詢-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 6b00648939ccedffeed09b4e1d6e8d70fa262a36
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490580"
---
# <a name="raw-sql-queries"></a>原始 SQL 查詢
Entity Framework 可讓您與您的實體類別中使用 LINQ 進行查詢。 不過，可能有您想要使用原始的 SQL 直接對資料庫執行查詢的時間。 這包括呼叫預存程序，可以幫助您目前不支援對應至預存程序的 Code First 模型。 本主題所示範的技巧同樣適用於使用 Code First 和 EF 設計工具所建立的模型。  

## <a name="writing-sql-queries-for-entities"></a>撰寫 SQL 查詢實體  

DbSet SqlQuery 方法可讓原始的 SQL 查詢，以寫入，會傳回實體執行個體。 內容會追蹤傳回的物件，就如同它們由 LINQ 查詢，其方式是。 例如:   

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

請注意，如同 LINQ 查詢，查詢才可以執行結果會列舉，在上述範例中是使用 ToList 的呼叫。  

只要原始 SQL 查詢會寫入兩個原因，應該特別注意。 首先，應該撰寫查詢，以確保只會傳回實體的要求的型別實際上是。 例如，使用功能，例如繼承時很容易撰寫的查詢，將會建立屬於錯誤的 CLR 類型的實體。  

第二，某些類型的原始 SQL 查詢會公開潛在的安全性風險，尤其是 SQL 資料隱碼攻擊。 請確定在查詢中使用參數，以防範這類攻擊的正確方式。  

### <a name="loading-entities-from-stored-procedures"></a>從預存程序中載入的實體  

您可以使用 DbSet.SqlQuery 載入實體，從預存程序的結果。 例如，下列程式碼會呼叫 dbo。在資料庫中的 GetBlogs 程序：  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

您也可以將參數傳遞至預存程序，使用下列語法：  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>非實體類型的撰寫 SQL 查詢  

資料庫類別使用 SqlQuery 方法可以建立 SQL 查詢傳回的任何類型，包括基本類型的執行個體。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

即使物件是實體類型的執行個體，在資料庫上的 SqlQuery 從傳回的結果永遠不會追蹤內容。  

## <a name="sending-raw-commands-to-the-database"></a>將未經處理的命令傳送至資料庫  

非查詢命令可以傳送到使用 ExecuteSqlCommand 方法，在資料庫上的資料庫。 例如:   

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

請注意，使用 ExecuteSqlCommand 中資料庫的資料所做的變更是不透明的內容，直到載入或從資料庫重新載入實體。  

### <a name="output-parameters"></a>輸出參數  

如果 output 參數使用，直到已完全讀取結果，將無法使用它們的值。 這是因為基礎行為的 DbDataReader，請參閱 <<c0> [ 擷取的資料使用 DataReader](http://go.microsoft.com/fwlink/?LinkID=398589)如需詳細資訊。  
