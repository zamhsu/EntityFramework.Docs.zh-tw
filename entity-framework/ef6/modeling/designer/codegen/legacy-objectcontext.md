---
title: 還原成在 Entity Framework Designer EF6 的 ObjectContext
author: divega
ms.date: 10/23/2016
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
ms.openlocfilehash: 3e436f0d9cf94720be9c424b327816438d571ae8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488935"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>還原成 Entity Framework 設計工具中的 ObjectContext
與上一個版本的 Entity Framework 的模型，建立與 EF 設計工具會產生衍生自 ObjectContext 的內容和實體類別，衍生自 EntityObject。

從 EF4.1 建議交換來產生衍生自 DbContext 及 POCO 實體類別的內容的程式碼產生範本。

Visual Studio 2012 中，您會取得所有新的模型使用 EF 設計工具建立的預設值所產生的 DbContext 程式碼。 現有的模型將會繼續產生 ObjectContext 基礎程式碼，除非您決定要切換至 DbContext 基礎程式碼產生器。

## <a name="reverting-back-to-objectcontext-code-generation"></a>正在回復到 ObjectContext 的程式碼產生

### <a name="1-disable-dbcontext-code-generation"></a>1.停用 DbContext 的程式碼產生

產生的衍生 DbContext 及 POCO 類別由您的專案中的兩個.tt 檔案中，如果您展開 [方案總管] 中的.edmx 檔案，您會看到這些檔案。 刪除兩個專案中。

![程式碼的一般檔案](~/ef6/media/codegenfiles.png)

如果您使用 VB.NET 您必須選取**顯示所有檔案**按鈕，即可查看巢狀的檔案。

![顯示所有檔案](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2.重新啟用 ObjectContext 的程式碼產生

開啟您的模型在 EF 設計工具中，以滑鼠右鍵按一下設計介面，然後選取 [空白] 區段**屬性**。

在 [屬性] 視窗變更**程式碼產生策略**從**無**來**預設**。

![程式碼的一般策略](~/ef6/media/codegenstrategy.png)
