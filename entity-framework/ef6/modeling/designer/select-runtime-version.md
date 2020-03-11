---
title: 選取 EF 設計工具模型 Entity Framework 執行階段版本-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418144"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>選取 EF 設計工具模型 Entity Framework 執行階段版本
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

從 EF6 開始，會在 EF 設計工具中加入下列畫面，讓您在建立模型時，選取您想要設為目標的執行階段版本。 當專案中尚未安裝最新版本的 Entity Framework 時，畫面將會出現。 如果已安裝最新版本，則預設會使用它。

![畫面](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a>以 EF6 為目標

您可以從 [選擇您的版本] 畫面中選擇 [EF6]，將 EF6 執行時間新增至您的專案。 加入 EF6 之後，您將會在目前的專案中停止看到此畫面。

如果您已安裝較舊版本的 EF，EF6 將會停用（因為您無法從相同的專案以多個版本的執行時間為目標）。 如果此處未啟用 EF6 選項，請遵循下列步驟將您的專案升級至 EF6：

1.  以滑鼠右鍵按一下方案總管中的專案，然後選取 [**管理 NuGet 套件 ...** ]
2.  選取**更新**
3.  選取 [ **EntityFramework** ] （請確定它會將它更新為您想要的版本）
4.  按一下 [更新]

 

## <a name="targeting-ef5x"></a>以 EF5 為目標

您可以從 [選擇您的版本] 畫面中選擇 [EF5]，將 EF5 執行時間新增至您的專案。 新增 EF5 之後，您仍會看到 [EF6] 選項已停用的畫面。

如果您已安裝 EF4. x 版的執行時間，則會看到該版本的 EF 列在畫面中，而不是 EF5。 在此情況下，您可以使用下列步驟來升級至 EF5：

1.  選取**工具-&gt; 程式庫套件管理員-&gt; 套件管理員主控台**
2.  執行**安裝-Package EntityFramework-5.0.0 版**

 

## <a name="targeting-ef4x"></a>以 EF4 為目標

您可以使用下列步驟，將 EF4 執行時間安裝到您的專案：

1.  選取**工具-&gt; 程式庫套件管理員-&gt; 套件管理員主控台**
2.  執行**安裝-Package EntityFramework-version 4.3.0**
