---
title: 升級至 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 2e2dacfe67238bdb7fd1f31f784319049f0f2cb0
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490944"
---
# <a name="upgrading-to-entity-framework-6"></a>升級至 Entity Framework 6

在舊版的 EF 的程式碼是核心程式庫 (主要 System.Data.Entity.dll 中) 隨附於.NET Framework 與頻外 (OOB) 程式庫 (主要 EntityFramework.dll) 隨附於 NuGet 套件之間有分割。 EF6 會從核心程式庫的程式碼，並納入 OOB 程式庫。 若要允許進行開放原始碼的 EF 必須進行此動作，讓它成為能夠發展以不同的步調，從.NET Framework。 結果就是應用程式必須重建針對移動的型別。

這應該是直接使用 DbContext 為隨附於 EF 4.1 和更新版本的應用程式。 多一點的工作是為了利用 ObjectContext 的應用程式，但它仍不難做到。

以下是您要升級現有 ef6 應用程式所做的事情的檢查清單。

## <a name="1-install-the-ef6-nuget-package"></a>1.安裝 EF6 的 NuGet 套件

您要升級至新的 Entity Framework 6 執行階段。

1. 以滑鼠右鍵按一下專案，然後選取**管理 NuGet 套件...**  
2. 底下**線上**索引標籤上選取**EntityFramework**按一下**安裝**  
   > [!NOTE]
   > 如果舊版 EntityFramework NuGet 套件已安裝這會將它升級到 EF6。

或者，您也可以從套件管理員主控台中執行下列命令：

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2.請確定已移除 System.Data.Entity.dll 的組件參考

安裝 EF6 的 NuGet 套件應該會自動會為您移除任何參考 System.Data.Entity 從您的專案。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3.交換使用 EF 6.x 的程式碼產生的任何 EF 設計工具 (EDMX) 模型

如果您有使用 EF 設計工具建立的任何模型，您必須更新程式碼產生範本，來產生 EF6 相容的程式碼。

> [!NOTE]
> 目前沒有只有 EF 6.x DbContext 產生器的範本適用於 Visual Studio 2012 和 2013年。

1. 刪除現有的程式碼產生範本。 這些檔案通常會命名為 **\<edmx_file_name\>.tt**並 **\<edmx_file_name\>。Context.tt**和您的 edmx 檔案，在 [方案總管] 下進行巢狀。 您可以選取範本方案總管，然後按下**Del**鍵來刪除它們。  
   > [!NOTE]
   > 在網站專案範本將不下您的 edmx 檔案，巢狀但與它一起列在 [方案總管]。  

   > [!NOTE]
   > 在 VB.NET 專案中，您必須啟用 顯示所有檔案 ' 能夠查看巢狀的範本的檔案。
2. 新增適當的 EF 6.x 的程式碼產生範本。 開啟您的模型，在 EF 設計工具中，以滑鼠右鍵按一下設計介面，並選取**加入的程式碼產生項目...**
    - 如果您使用 DbContext API （建議選項） 再**EF 6.x DbContext Generator**會位在**資料** 索引標籤。  
      > [!NOTE]
      > 如果您使用 Visual Studio 2012，您必須安裝 EF 6 工具，將此範本。 請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)如需詳細資訊。  

    - 如果您使用 ObjectContext API，則您必須選取**線上**索引標籤，然後搜尋**EF 6.x EntityObject Generator**。  
3. 如果您的程式碼產生範本，您必須將它們重新套用至已更新範本以套用任何自訂項目。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4.更新正在使用的任何 EF core 型別命名空間

DbContext 和第一個程式碼類型的命名空間都沒有變更。 這表示對於許多應用程式使用 EF 4.1 或更新版本您不需要變更任何項目。

型別 ObjectContext 如先前在 System.Data.Entity.dll 中都已移至新的命名空間中。 這表示您可能需要更新您*使用*或是*匯入*指示詞，針對 EF6 進行建置。

命名空間變更的一般規則是 System.Data.* 中的任何型別會移至 System.Data.Entity.Core.*。 換句話說，只需要插入**Entity.Core。** 之後 System.Data。 例如: 

- System.Data.EntityException = > System.Data。**Entity.Core。** EntityException  
- System.Data.Objects.ObjectContext = > System.Data。**Entity.Core。** Objects.ObjectContext  
- System.Data.Objects.DataClasses.RelationshipManager = > System.Data。**Entity.Core。** Objects.DataClasses.RelationshipManager  

這些型別是在*Core*命名空間因為不會針對大部分的 DbContext 應用程式的直接用。 屬於 System.Data.Entity.dll 某些類型仍用於通常和直接 DbContext 為基礎的應用程式，並因此不已移到*Core*命名空間。 這些是：

- System.Data.EntityState = > System.Data。**實體。** EntityState  
- System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data。**Entity.DbFunctionAttribute**  
  > [!NOTE]
  > 這個類別已重新命名;使用舊名稱的類別仍然存在，且可運作，但它現在會標示為過時。  
- System.Data.Objects.EntityFunctions = > System.Data。**Entity.DbFunctions**  
  > [!NOTE]
  > 這個類別已重新命名;使用舊名稱的類別仍然存在，且可運作，但它現在會標示為已過時）。  
- 空間的類別 （例如 DbGeography、 DbGeometry） 已從 System.Data.Spatial = > System.Data。**實體。** 空間

> [!NOTE]
> System.Data 命名空間中的某些類型是在 System.Data.dll 中不是 EF 組件。 未將這些型別，因此其命名空間保持不變。
