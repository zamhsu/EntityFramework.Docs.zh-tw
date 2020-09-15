---
title: 開始使用 Entity Framework 6 - EF6
description: 開始使用 Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: 825fe3716ade947f1660c8131100a1c3acba5a1d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072131"
---
# <a name="get-started-with-entity-framework-6"></a>開始使用 Entity Framework 6

本指南包含一系列精選文件文章、逐步解說及影片的連結，以協助您快速開始使用。

## <a name="fundamentals"></a>基礎

* [取得 Entity Framework](xref:ef6/fundamentals/install)

  您在這裡會學到如何將 Entity Framework 新增到應用程式，而您如果想使用 EF Designer，務必在 Visual Studio 中加以安裝。

* [建立模型：Code First、EF Designer 及 EF 工作流程](xref:ef6/modeling/index)

  您偏好指定撰寫程式碼還是建構輪廓的 EF 模型？
您要使用 EF 將物件對應到現有的資料庫，還是希望 EF 建立專為物件打造的資料庫？
在這裡，您將瞭解使用 EF6 的兩種不同方法： EF 設計工具及 Code First。
請務必追蹤討論，並觀看有關差異的影片。

* [使用 DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext 是第一個也最重要的 EF 類型，值得您學習如何使用。 其用途是資料庫查詢的啟動控制區，並會持續追蹤您對物件進行的變更，以便存回資料庫。

* [詢問問題](xref:ef6/resources/get-help)

  了解如何取得專家的協助，以及對社群貢獻您自己的答案。

* [參與](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 使用開放的開發模型。 歡迎瀏覽我們的 GitHub 存放庫，看看您可以如何讓 EF 更臻完美。

## <a name="code-first-resources"></a>Code First 資源

  - [現有資料庫工作流程的 Code First](xref:ef6/modeling/code-first/workflows/existing-database)
  - [新資料庫工作流程的 Code First](xref:ef6/modeling/code-first/workflows/new-database)
  - [使用 Code First 對應列舉](xref:ef6/modeling/code-first/data-types/enums)
  - [使用 Code First 對應空間類型](xref:ef6/modeling/code-first/data-types/spatial)
  - [撰寫自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)
  - [搭配 Visual Basic 使用 Code First Fluent 組態](xref:ef6/modeling/code-first/fluent/vb)
  - [Code First 移轉](xref:ef6/modeling/code-first/migrations/index)
  - [小組環境中的 Code First 移轉](xref:ef6/modeling/code-first/migrations/teams)
  - [自動 Code First 移轉](xref:ef6/modeling/code-first/migrations/automatic) (不再建議使用)

## <a name="ef-designer-resources"></a>EF Designer 資源
  - [Database First 工作流程](xref:ef6/modeling/designer/workflows/database-first)
  - [Model First 工作流程](xref:ef6/modeling/designer/workflows/model-first)
  - [對應列舉](xref:ef6/modeling/designer/data-types/enums)
  - [對應空間類型](xref:ef6/modeling/designer/data-types/spatial)
  - [單表繼承對應](xref:ef6/modeling/designer/inheritance/tph)
  - [一類一表繼承對應](xref:ef6/modeling/designer/inheritance/tpt)
  - [更新的預存程序對應](xref:ef6/modeling/designer/stored-procedures/cud)
  - [查詢的預存程序對應](xref:ef6/modeling/designer/stored-procedures/query)
  - [實體分割](xref:ef6/modeling/designer/entity-splitting)
  - [資料表分割](xref:ef6/modeling/designer/table-splitting)
  - [定義查詢](xref:ef6/modeling/designer/advanced/defining-query) (進階)
  - [資料表值函式](xref:ef6/modeling/designer/advanced/tvfs) (進階)

## <a name="other-resources"></a>其他資源
  - [非同步查詢與儲存](xref:ef6/fundamentals/async)
  - [使用 WinForms 的資料繫結](xref:ef6/fundamentals/databinding/winforms)
  - [使用 WPF 的資料繫結](xref:ef6/fundamentals/databinding/wpf)
  - [自我追蹤實體的中斷連線案例](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (不再建議使用)
