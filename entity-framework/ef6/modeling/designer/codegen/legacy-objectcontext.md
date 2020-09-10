---
title: 還原為 Entity Framework Designer 中的 ObjectCoNtext-EF6
description: Entity Framework Designer 中的 ObjectCoNtext 還原至 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: e09cdfe994033bc8dea26bb09c4560788dd746f8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620533"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Entity Framework Designer 中的 ObjectCoNtext 還原
使用舊版 Entity Framework 使用 EF 設計工具建立的模型會產生衍生自 ObjectCoNtext 的內容，以及衍生自 EntityObject 的實體類別。

從 EF 4.1 開始，建議您交換至程式碼產生範本，以產生衍生自 DbCoNtext 和 POCO 實體類別的內容。

在 Visual Studio 2012 中，您會針對以 EF 設計工具建立的所有新模型，取得預設產生的 DbCoNtext 程式碼。 除非您決定要換至 DbCoNtext 型程式碼產生器，否則現有的模型將繼續產生 ObjectCoNtext 型程式碼。

## <a name="reverting-back-to-objectcontext-code-generation"></a>還原回 ObjectCoNtext 程式碼產生

### <a name="1-disable-dbcontext-code-generation"></a>1. 停用 DbCoNtext 程式碼產生

衍生的 DbCoNtext 和 POCO 類別的產生是由您專案中的兩個 tt 檔案所處理，如果您在 [方案瀏覽器] 中展開 .edmx 檔案，就會看到這些檔案。 從您的專案中刪除這兩個檔案。

![程式碼 Gen 檔案](~/ef6/media/codegenfiles.png)

如果您使用的是 VB.NET，則必須選取 [ **顯示所有** 檔案] 按鈕，才能看到這些嵌套的檔案。

![顯示所有檔案](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. 重新啟用 ObjectCoNtext 程式碼產生

在 EF 設計工具中開啟您的模型，以滑鼠右鍵按一下設計介面的空白區段，然後選取 [ **屬性**]。

在屬性視窗將程式 **代碼產生策略** 從 [ **無** ] 變更為 **預設值**。

![程式碼產生策略](~/ef6/media/codegenstrategy.png)
