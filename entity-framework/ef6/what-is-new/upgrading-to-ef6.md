---
title: 升級至 Entity Framework 6-EF6
description: 升級至 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: b27504e3b18c668a1c41142b9cd5e697944cb5fa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619702"
---
# <a name="upgrading-to-entity-framework-6"></a>升級至 Entity Framework 6

在舊版的 EF 中，程式碼是在核心程式庫 (，主要是在 .NET Framework 和頻外 (OOB) 程式庫中隨附的 System.Data.Entity.dll) ， (主要 EntityFramework.dll NuGet 套件中所附的) 。 EF6 會從核心程式庫取得程式碼，並將其併入 OOB 程式庫中。 這是必要的，以便讓 EF 成為開放原始碼，讓它能夠以不同于 .NET Framework 的步調演進。 結果是必須針對已移動的類型重建應用程式。

如果應用程式使用 EF 4.1 和更新版本隨附的 DbCoNtext，這應該是很簡單的。 使用 ObjectCoNtext 的應用程式需要更多工作，但它仍然不難進行。

以下是將現有應用程式升級至 EF6 所需的事項檢查清單。

## <a name="1-install-the-ef6-nuget-package"></a>1. 安裝 EF6 NuGet 套件

您必須升級到新的 Entity Framework 6 執行時間。

1. 以滑鼠右鍵按一下您的專案，然後選取 [**管理 NuGet 套件 ...** ]  
2. 在 [**線上**] 索引標籤下選取**EntityFramework** ，然後按一下 [**安裝**]。  
   > [!NOTE]
   > 如果已安裝舊版的 EntityFramework NuGet 套件，則會將它升級至 EF6。

或者，您也可以從封裝管理員主控台執行下列命令：

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. 確定已移除 System.Data.Entity.dll 的元件參考

安裝 EF6 NuGet 套件應該會自動從您的專案移除任何對 system.string 的參考。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. 將任何 EF 設計工具 (EDMX) 模型交換為使用 EF 6.x 程式碼產生

如果您有使用 EF 設計工具建立的任何模型，您將需要更新程式碼產生範本，以產生 EF6 相容的程式碼。

> [!NOTE]
> 目前只有 EF 6.x DbCoNtext 產生器範本適用于 Visual Studio 2012 和2013。

1. 刪除現有的程式碼產生範本。 這些檔案通常會命名為** \<edmx_file_name\> tt**和** \<edmx_file_name\> 。CoNtext.tt** ，並將其內嵌在方案總管的 edmx 檔案底下。 您可以在方案總管中選取範本，然後按下 **Del** 鍵將它們刪除。  
   > [!NOTE]
   > 在網站專案中，範本不會在您的 edmx 檔案下進行嵌套，而是在方案總管中與它一起列出。  

   > [!NOTE]
   > 在 VB.NET 專案中，您必須啟用 [顯示所有檔案]，才能看到嵌套的範本檔案。
2. 新增適當的 EF 6.x 程式碼產生範本。 在 EF 設計工具中開啟您的模型，以滑鼠右鍵按一下設計介面，然後選取 [**加入程式碼產生專案 ...** ]
    - 如果您使用 DbCoNtext API (建議) 則會在 [**資料**] 索引標籤下提供**EF 6.x DbCoNtext**產生器。  
      > [!NOTE]
      > 如果您使用 Visual Studio 2012，則必須安裝 EF 6 工具才能使用此範本。 如需詳細資訊，請參閱 [Get Entity Framework](xref:ef6/fundamentals/install) 。  

    - 如果您使用 ObjectCoNtext API，則必須選取 [ **線上** ] 索引標籤，並搜尋 **EF 6.x EntityObject**產生器。  
3. 如果您將任何自訂套用至程式碼產生範本，則必須將它們重新套用至更新的範本。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. 更新所使用之任何核心 EF 類型的命名空間

DbCoNtext 和 Code First 類型的命名空間尚未變更。 這表示使用 EF 4.1 或更新版本的許多應用程式都不需要變更任何變更。

先前在 System.Data.Entity.dll 中的類型（例如 ObjectCoNtext）已移至新的命名空間。 這表示您可能需要更新 *使用* 或匯 *入* 指示詞，以針對 EF6 進行建立。

命名空間變更的一般規則是，系統中的任何類型都會移至 system.string. *。 換句話說，只需要插入**Entity Core** 。 在 System. 資料之後。 例如：

- EntityException => System.object。**Core**。EntityException  
- System.string => 的 System.object。**Core**。物件. ObjectCoNtext  
- Dataclasses.dll. RelationshipManager => 的 System.object。**Core**。Dataclasses.dll. RelationshipManager  

這些類型是在 *核心* 命名空間中，因為它們不會直接用於大部分以 DbCoNtext 為基礎的應用程式。 某些屬於 System.Data.Entity.dll 一部分的型別仍然經常用於 DbCoNtext 型應用程式，所以尚未移至 *核心* 命名空間。 這些警告是：

- EntityState => System.object。**實體**。EntityState  
- Dataclasses.dll. EdmFunctionAttribute => 的 System.object。**DbFunctionAttribute**  
  > [!NOTE]
  > 此類別已重新命名;舊名稱的類別仍然存在且正常運作，但現在已標示為過時。  
- EntityFunctions => 的 System.object。**DbFunctions**  
  > [!NOTE]
  > 此類別已重新命名;舊名稱的類別仍然存在且正常運作，但現在已標示為過時。 )   
- 空間類別 (例如，DbGeography、DbGeometry) 已從移動。空間 => 的資料。**實體**。空間

> [!NOTE]
> System. Data 命名空間中的某些類型不是 EF 元件 System.Data.dll。 這些類型尚未移動，因此其命名空間會保持不變。
