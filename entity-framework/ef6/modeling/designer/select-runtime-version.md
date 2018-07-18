---
title: EF 設計工具模型 EF6 的選取 Entity Framework 執行階段版本
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
caps.latest.revision: 3
ms.openlocfilehash: 75f7b4ed81528683801893c31de490ce15be6733
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120482"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>選取 Entity Framework 執行階段版本的 EF 設計工具模型
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

從 EF6 開始 EF 設計工具，可讓您選取您想要建立模型時，目標執行階段版本已加入下列的畫面。 當 Entity Framework 的最新版本尚未安裝在專案中，會顯示畫面。 如果已安裝最新版預設將只會使用。

![螢幕](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a>目標 EF6.x

您可以從 [選擇您版本 '] 畫面將 EF6 執行階段新增至您的專案選擇 EF6。 一旦您已新增 EF6，您將停止看到這個畫面中目前的專案。

如果您已經有較舊版本的 EF 安裝 （因為您無法將目標從相同的專案執行階段的多個版本），將會停用 EF6。 如果這裡未啟用 EF6 選項，請遵循這些步驟來升級您的專案 ef6:

1.  以滑鼠右鍵按一下方案總管 中的專案，然後選取**管理 NuGet 套件...**
2.  選取**更新**
3.  選取  **EntityFramework** （請確定它會將其更新為您想要的版本）
4.  按一下 **更新**

 

## <a name="targeting-ef5x"></a>目標 EF5.x

您可以選擇 EF5 從 EF5 執行階段加入您的專案的 [選擇您 Version'] 畫面。 一旦您已新增 EF5，您還是會看到畫面使用 EF6 選項停用。

如果您有已安裝的執行階段 EF4.x 版本，您會看到畫面，而不是 EF5 中所列的 EF 該的版本。 在此情況下，您可以升級至 EF5 使用下列步驟：

1.  選取 [**工具]-&gt;程式庫套件管理員-&gt;套件管理員主控台**
2.  執行**Install-package EntityFramework-5.0.0 版**

 

## <a name="targeting-ef4x"></a>目標 EF4.x

您可以安裝 EF4.x 執行階段，以您的專案使用下列步驟：

1.  選取 [**工具]-&gt;程式庫套件管理員-&gt;套件管理員主控台**
2.  執行**Install-package EntityFramework-4.3.0 版**
