---
title: 使用 DbContext-EF6
description: 在 Entity Framework 6 中使用 DbContext
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: aa980e102862b559c8f38418cf90a11f284cc48c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062798"
---
# <a name="working-with-dbcontext"></a>使用 DbContext

若要使用 Entity Framework 使用 .NET 物件來查詢、插入、更新和刪除資料，您必須先 [建立模型](xref:ef6/modeling/index) ，將模型中所定義的實體和關聯性對應至資料庫中的資料表。

一旦有模型之後，您的應用程式所互動的主要類別 `System.Data.Entity.DbContext` (通常稱為內容類別) 。 您可以使用與模型相關聯的 DbContext 來：
- 撰寫和執行查詢   
- 將查詢結果具體化為實體物件
- 追蹤對這些物件所做的變更
- 將物件變更保存回資料庫
- 將記憶體中的物件系結至 UI 控制項

此頁面提供有關如何管理內容類別的一些指引。  

## <a name="defining-a-dbcontext-derived-class"></a>定義 DbContext 衍生類別  

使用內容的建議方式是定義衍生自 DbContext 的類別，並公開 DbSet 屬性，以表示內容中指定之實體的集合。 如果您使用 EF 設計工具，將會為您產生內容。 如果您正在使用 Code First，通常會自行撰寫內容。  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

一旦有內容之後，您就可以使用或方法來查詢、新增 (`Add` ， `Attach`) 或 移除（`Remove`）。透過這些屬性在內容中使用實體來存取 `DbSet` 內容物件上的屬性代表會傳回指定型別之所有實體的起始查詢。 請注意，只存取屬性不會執行查詢。 當下列情況時，會執行查詢：  

- 它是由 `foreach` (C#) 或 `For Each` (Visual Basic) 陳述式所列舉。  
- 它是由集合作業（例如、或）所列舉 `ToArray` `ToDictionary` `ToList` 。  
- LINQ 運算子（例如 `First` 或 `Any` ）是在查詢的最外面部分指定的。  
- 如果找不到具有指定索引鍵的實體，則會呼叫下列其中一種方法：`Load`的擴充方法 、 `DbEntityEntry.Reload` 、 `Database.ExecuteSqlCommand` 和 `DbSet<T>.Find`。  

## <a name="lifetime"></a>存留期  

內容的存留期會在實例建立時開始，並在實例被處置或垃圾收集時結束。 如果您想要在區塊結尾處置內容控制項的所有資源，請使用 **[使用]** 。 **使用時，編譯器**會自動建立 try/finally 區塊，並且在**finally**區塊中呼叫 dispose。  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

以下是決定內容存留期時的一些一般指導方針：  

- 使用 Web 應用程式時，請針對每個要求使用內容實例。  
- 使用 Windows Presentation Foundation (WPF) 或 Windows Forms 時，請使用每個表單的內容實例。 這可讓您使用內容提供的變更追蹤功能。  
- 如果內容實例是由相依性插入容器所建立，則通常會負責處置內容的容器。
- 如果內容是在應用程式程式碼中建立的，請記得在不再需要內容時處置內容。  
- 使用長時間執行的內容時，請考慮下列事項：  
    - 當您將更多物件和其參考載入記憶體時，內容的記憶體耗用量可能會快速增加。 這可能會導致效能問題。  
    - 內容不是安全線程，因此不應該跨多個執行緒同時執行工作。
    - 如果例外狀況造成內容處於無法復原的狀態，整個應用程式可能會終止。  
    - 當查詢資料與更新資料之間的時間差距增加時，遇到並行相關問題的機率也會增加。  

## <a name="connections"></a>連接  

根據預設，內容會管理與資料庫的連接。 內容隨即開啟，並視需要關閉連接。 例如，內容會開啟連接來執行查詢，然後在處理所有結果集時關閉連接。  

在某些情況下，您會想要擁有更大的控制權來控制何時開啟及關閉連接。 例如，使用 SQL Server Compact 時，通常建議在應用程式的存留期內針對資料庫維護個別的開啟連接，以改善效能。 您可以使用 `Connection` 屬性來手動管理這個處理序。  
