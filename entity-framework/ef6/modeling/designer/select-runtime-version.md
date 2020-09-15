---
title: 選取 EF Designer 模型 Entity Framework 執行階段版本-EF6
description: 在 Entity Framework 6 中選取 EF Designer 模型 Entity Framework 執行階段版本
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 2025ca5e46c7d9a38ba596d57c023a2c1b224129
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073262"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>選取 EF Designer 模型 Entity Framework 執行階段版本
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

從 EF6 開始，會將下列畫面新增至 EF 設計工具，讓您可以在建立模型時選取您想要設為目標的執行階段版本。 當專案中尚未安裝最新版本的 Entity Framework 時，畫面將會出現。 如果已安裝最新版本，則預設會使用它。

![選取執行階段版本](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>目標為 EF6. x

您可以選擇 [選擇您的版本] 畫面中的 [EF6]，將 EF6 執行時間新增至專案。 新增 EF6 之後，您將會在目前的專案中停止看到此畫面。

如果您已 (安裝舊版的 EF，將會停用 EF6，因為您無法從相同的專案) ，將執行時間的多個版本設為目標。 如果此處未啟用 EF6 選項，請依照下列步驟將您的專案升級至 EF6：

1.  在方案總管中的專案上按一下滑鼠右鍵，然後選取 [**管理 NuGet 套件 ...** ]
2.  選取 **更新**
3.  選取 **EntityFramework** (確定它會將它更新為您想要的版本) 
4.  按一下 [**更新**]

 

## <a name="targeting-ef5x"></a>目標為 EF5. x

您可以選擇 [選擇您的版本] 畫面中的 [EF5]，將 EF5 執行時間新增至專案。 新增 EF5 之後，您仍會看到已停用 EF6 選項的畫面。

如果您已安裝 EF4 的執行階段版本，則會看到該版本的 EF 列在畫面中，而不是 EF5。 在此情況下，您可以使用下列步驟來升級至 EF5：

1.  選取 **工具-連結 &gt; 庫封裝管理員- &gt; 封裝管理員主控台**
2.  執行 **Install-Package EntityFramework-第5.0.0 版**

 

## <a name="targeting-ef4x"></a>目標為 EF4. x

您可以使用下列步驟，將 EF4 執行時間安裝至您的專案：

1.  選取 **工具-連結 &gt; 庫封裝管理員- &gt; 封裝管理員主控台**
2.  執行 **Install-Package EntityFramework-version 4.3.0**
