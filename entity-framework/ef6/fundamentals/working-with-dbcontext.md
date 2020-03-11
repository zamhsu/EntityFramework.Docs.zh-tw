---
title: 使用 DbCoNtext-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
ms.openlocfilehash: d961ffd8bed7f5b2f82dcfa30fc0241b7437be50
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416319"
---
# <a name="working-with-dbcontext"></a>使用 DbContext

若要使用 Entity Framework 來查詢、插入、更新及刪除使用 .NET 物件的資料，您必須先[建立模型](~/ef6/modeling/index.md)，將模型中所定義的實體和關聯性對應至資料庫中的資料表。

當您擁有模型之後，您的應用程式所互動的主要類別是 `System.Data.Entity.DbContext` （通常稱為內容類別）。 您可以使用與模型相關聯的 DbCoNtext 來執行下列動作：
- 撰寫和執行查詢   
- 將查詢結果具體化為實體物件
- 追蹤對這些物件所做的變更
- 將物件變更保存回資料庫
- 將記憶體中的物件系結至 UI 控制項

本頁面提供如何管理內容類別的一些指引。  

## <a name="defining-a-dbcontext-derived-class"></a>定義 DbCoNtext 衍生類別  

使用內容的建議方式是定義衍生自 DbCoNtext 的類別，並公開代表內容中指定之實體集合的 DbSet 屬性。 如果您使用的是 EF Designer，則會為您產生內容。 如果您使用 Code First，通常會自行撰寫內容。  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

一旦您擁有內容，就會查詢、新增（使用 `Add` 或 `Attach` 方法），或透過這些屬性移除（使用 `Remove`）實體。 存取內容物件上的 `DbSet` 屬性，代表傳回指定類型之所有實體的起始查詢。 請注意，只存取屬性並不會執行查詢。 執行查詢的時機為：  

- 它是由 `foreach` (C#) 或 `For Each` (Visual Basic) 陳述式所列舉。  
- 它是由集合作業（例如 `ToArray`、`ToDictionary`或 `ToList`）所列舉。  
- LINQ 運算子（例如 `First` 或 `Any`）是在查詢的最外面部分指定。  
- 系統會呼叫下列其中一種方法：如果在內容中找不到具有指定索引鍵的實體，`Load` 擴充方法、`DbEntityEntry.Reload`、`Database.ExecuteSqlCommand`和 `DbSet<T>.Find`。  

## <a name="lifetime"></a>存留期  

內容的存留期會在實例建立時開始，並在實例被處置或垃圾收集時結束。 如果您想要在區塊結尾處置內容控制項的所有資源，請使用 [**使用**]。 當您使用**using**時，編譯器會自動建立 try/finally 區塊，並在**finally**區塊中呼叫 dispose。  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

以下是決定內容存留期的一些一般指導方針：  

- 當使用 Web 應用程式時，請針對每個要求使用內容實例。  
- 使用 Windows Presentation Foundation （WPF）或 Windows Forms 時，請使用每個表單的內容實例。 這可讓您使用內容所提供的變更追蹤功能。  
- 如果內容實例是由相依性插入容器所建立，通常會負責處置內容的容器。
- 如果內容是在應用程式代碼中建立，請記得在不再需要時處置內容。  
- 使用長時間執行的內容時，請考慮下列事項：  
    - 當您將更多物件及其參考載入記憶體時，內容的記憶體耗用量可能會快速增加。 這可能會導致效能問題。  
    - 內容不是安全線程，因此不應該在多個執行緒上共用，同時執行工作。
    - 如果例外狀況導致內容處於無法復原的狀態，整個應用程式可能會終止。  
    - 當查詢資料與更新資料之間的時間差距增加時，遇到並行相關問題的機率也會增加。  

## <a name="connections"></a>連接  

根據預設，內容會管理與資料庫的連接。 內容隨即開啟，並視需要關閉連接。 例如，內容會開啟連接來執行查詢，然後在處理所有結果集時關閉連接。  

在某些情況下，您會想要擁有更大的控制權來控制何時開啟及關閉連接。 例如，使用 SQL Server Compact 時，通常建議您在應用程式的存留期內，為資料庫維護個別的開啟連接，以改善效能。 您可以使用 `Connection` 屬性來手動管理這個處理序。  
