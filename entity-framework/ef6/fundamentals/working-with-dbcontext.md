---
title: 使用 DbContext-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
ms.openlocfilehash: f95f503c4e40e65503d5af0c1b686d0055728bfe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998142"
---
# <a name="working-with-dbcontext"></a>使用 DbContext

若要使用 Entity Framework 來查詢、 插入、 更新和刪除使用.NET 物件的資料，您必須先[建立模型](~/ef6/modeling/index.md)其對應的實體和您的模型資料庫中的資料表中所定義的關聯性。

一旦您有一個模型，您的應用程式互動的主要類別是`System.Data.Entity.DbContext`（通常稱為內容類別）。 您可以使用 DbContext，相關聯的模型：
- 撰寫及執行查詢   
- 做為實體物件具體化查詢結果
- 追蹤對這些物件所做的變更
- 保存回資料庫上的物件變更
- 在記憶體中將物件繫結至 UI 控制項

此頁面會提供有關如何管理內容類別的一些指引。  

## <a name="defining-a-dbcontext-derived-class"></a>定義衍生 DbContext 類別  

若要使用內容的建議的方式是定義類別衍生自 DbContext，代表集合的內容中指定之實體的 DbSet 屬性公開 （expose）。 如果您正在使用 EF 設計工具，則會為您產生的內容。 如果您正在使用 Code First，您會通常內容自行撰寫。  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

內容之後，您會針對查詢中，加入 (使用`Add`或是`Attach`方法) 或移除 (使用`Remove`) 透過這些屬性的內容中的實體。 存取`DbSet`內容物件上的屬性代表開始查詢，傳回指定之型別的所有實體。 請注意，只存取屬性時，會執行查詢。 執行查詢時：  

- 它是由 `foreach` (C#) 或 `For Each` (Visual Basic) 陳述式所列舉。  
- 這類列舉集合作業所`ToArray`， `ToDictionary`，或`ToList`。  
- LINQ 運算子，例如`First`或`Any`查詢的最外面部分中所指定。  
- 在呼叫下列方法的其中一個：`Load`擴充方法`DbEntityEntry.Reload`， `Database.ExecuteSqlCommand`，和`DbSet<T>.Find`，如果具有指定之索引鍵的實體找不到已載入內容中。  

## <a name="lifetime"></a>存留期  

執行個體建立，而且當執行個體遭到處置或記憶體回收結束時，就會開始內容的存留期。 使用**使用**如果您想要在區塊結尾處置內容所控制的所有資源。 當您使用**使用**，編譯器會自動建立 try/finally 區塊中的呼叫 dispose**最後**區塊。  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

決定內容的存留期，以下是一些一般指導方針：  

- 當使用 Web 應用程式，請使用每個要求的內容執行個體。  
- 當使用 Windows Presentation Foundation (WPF) 或 Windows Form，使用每個表單的內容執行個體。 這可讓您可以使用變更追蹤功能提供該內容。  
- 如果內容執行個體由相依性插入容器，但它通常是處置內容容器的責任。
- 如果應用程式程式碼建立內容時，請記得處置內容時不再需要。  
- 使用長時間執行的內容時請考慮下列各項：  
    - 隨著您將更多的物件和其參考載入記憶體，內容的記憶體耗用量可能快速增加。 這可能會導致效能問題。  
    - 內容不是安全執行緒，因此它不應該共用跨多個執行緒同時執行工作。
    - 如果例外狀況會導致內容處於無法復原的狀態，整個應用程式可能會終止。  
    - 當查詢資料與更新資料之間的時間差距增加時，遇到並行相關問題的機率也會增加。  

## <a name="connections"></a>連接  

根據預設，內容會管理資料庫的連接。 內容會開啟，並視需要關閉連接。 比方說，內容開啟的連接執行查詢，並在處理所有結果集時，然後關閉連線。  

在某些情況下，您會想要擁有更大的控制權來控制何時開啟及關閉連接。 例如，當使用 SQL Server Compact，通常建議維護個別開啟資料庫的連接來改善效能的應用程式的存留期。 您可以使用 `Connection` 屬性來手動管理這個處理序。  
