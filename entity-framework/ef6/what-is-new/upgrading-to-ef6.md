---
title: 升級至 Entity Framework 6-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 4395a9c117a6cf38e7fc08f11ee689d6fffa6fed
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182104"
---
# <a name="upgrading-to-entity-framework-6"></a>升級至 Entity Framework 6

在舊版 EF 中，程式碼會分割為核心程式庫（主要為 EntityFramework）隨附于 NuGet 套件中隨附的 .NET Framework 和頻外（OOB）程式庫（主要是）。 EF6 會採用核心程式庫中的程式碼，並將其併入 OOB 程式庫中。 這是為了讓 EF 成為開放原始碼，讓它能夠以與 .NET Framework 不同的步調進行進化的必要動作。 結果是，應用程式必須針對已移動的類型進行重建。

對於利用 EF 4.1 和更新版本隨附之 DbCoNtext 的應用程式而言，這應該很簡單。 使用 ObjectCoNtext 的應用程式需要更多工作，但它仍然不難執行。

以下是將現有應用程式升級至 EF6 時所需執行之工作的檢查清單。

## <a name="1-install-the-ef6-nuget-package"></a>1.安裝 EF6 NuGet 套件

您必須升級至新的 Entity Framework 6 執行時間。

1. 以滑鼠右鍵按一下您的專案，然後選取 [**管理 NuGet 套件 ...** ]  
2. 在 [**線上**] 索引標籤下，選取**EntityFramework** ，然後按一下 [**安裝**]  
   > [!NOTE]
   > 如果已安裝舊版的 EntityFramework NuGet 套件，則會將它升級至 EF6。

或者，您可以從 [套件管理員主控台] 執行下列命令：

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2.確定已移除 system.string 的元件參考

安裝 EF6 NuGet 套件時，應該會自動從您的專案中移除對 system.string 的任何參考。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3.交換任何 EF Designer （EDMX）模型，以使用 EF 6.x 程式碼產生

如果您有使用 EF 設計工具建立的任何模型，則必須更新程式碼產生範本，以產生 EF6 相容的程式碼。

> [!NOTE]
> 目前只有 EF 6. x DbCoNtext 產生器範本可供 Visual Studio 2012 和2013。

1. 刪除現有的程式碼產生範本。 這些檔案通常會命名為 **\<edmx_file_name\>.tt**和 **\<edmx_file_name @ no__t-5。CoNtext.tt** ，並在方案總管的 .edmx 檔案底下加以嵌套。 您可以在方案總管中選取範本，然後按下**Del**鍵將它們刪除。  
   > [!NOTE]
   > 在網站專案中，範本不會在您的 edmx 檔案底下加以嵌套，而是在方案總管中一起列出。  

   > [!NOTE]
   > 在 VB.NET 專案中，您必須啟用 [顯示所有檔案] 才能看到嵌套的範本檔案。
2. 新增適當的 EF 6.x 程式碼產生範本。 在 EF 設計工具中開啟您的模型，以滑鼠右鍵按一下設計介面，然後選取 [**新增程式碼產生專案**]。
    - 如果您使用 DbCoNtext API （建議選項），則會在 [**資料**] 索引標籤下提供**EF 6. x DbCoNtext**產生器。  
      > [!NOTE]
      > 如果您使用 Visual Studio 2012，您將需要安裝 EF 6 工具以擁有此範本。 如需詳細資訊，請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md) 。  

    - 如果您使用 ObjectCoNtext API，則必須選取 [**線上**] 索引標籤，並搜尋**EF 6.x EntityObject**產生器。  
3. 如果您將任何自訂套用至程式碼產生範本，則必須將其重新套用至更新的範本。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4.更新所使用之任何核心 EF 類型的命名空間

DbCoNtext 和 Code First 類型的命名空間尚未變更。 這表示許多使用 EF 4.1 或更新版本的應用程式都不需要變更任何專案。

先前在 system.string 中的類型（如 ObjectCoNtext）已移至新的命名空間。 這表示您可能需要更新*使用*或匯*入*指示詞，以針對 EF6 建立。

命名空間變更的一般規則是，系統. Data. * 中的任何類型都會移至 system.string. Data. Core. *。 換句話說，只要插入**Entity Core 即可。** 在 System. Data 之後。 例如:

- EntityException = > System.object。**Entity. Core**。EntityException  
- System.string = > 的 System.object。**Entity. Core**。物件. ObjectCoNtext  
- Dataclasses.dll. RelationshipManager = > 的資料。**Entity. Core**。Dataclasses.dll. RelationshipManager  

這些類型是在*核心*命名空間中，因為它們不會直接用於大部分以 DbCoNtext 為基礎的應用程式。 某些屬於 system.string 的類型仍然經常用於 DbCoNtext 架構的應用程式，因此尚未移至*核心*命名空間中的資料。 這些是：

- EntityState = > System.object。**實體**。EntityState  
- Dataclasses.dll. EdmFunctionAttribute = > 的資料。**DbFunctionAttribute**  
  > [!NOTE]
  > 這個類別已重新命名;具有舊名稱的類別仍然存在且可運作，但現在已標示為過時。  
- EntityFunctions = > 的 System.object。**DbFunctions**  
  > [!NOTE]
  > 這個類別已重新命名;具有舊名稱的類別仍然存在且可運作，但現在已標示為過時。）  
- 空間類別（例如，DbGeography、DbGeometry）已從 System.web （空間 =）移動到 > System.object。**實體**。空間

> [!NOTE]
> System. Data 命名空間中的某些類型是在不是 EF 元件的 System.object 中。 這些類型尚未移動，因此其命名空間保持不變。
