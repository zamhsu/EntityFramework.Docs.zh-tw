---
title: Entity Framework 6 概觀 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8ae74d63-6bad-4686-b325-bbf9d68f3743
uid: ef6/index
ms.openlocfilehash: 9561a7c4b645896cb4e248cb094c6954ed4bcdf1
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181421"
---
# <a name="entity-framework-6"></a>Entity Framework 6
Entity Framework 6 (EF6) 是通過試驗及測試的 .NET 物件關聯式對應程式 (O/RM)，歷經多年的功能開發和穩固。

作為 O/RM，EF6 可減少關聯式與物件導向環境之間的阻抗不相符情況，讓開發人員能夠撰寫使用強型別 .NET 物件 (其代表應用程式網域) 與關聯式資料庫中儲存的資料進行互動的應用程式，因此通常不需要撰寫大部分的資料存取「配管」程式碼。

EF6 會實作許多常用的 O/RM 功能：
- 對應 [POCO](~/ef6/resources/glossary.md#poco) 實體類別，其不相依於任何 EF 類型
- 自動變更追蹤
- 識別解析和工作單位
- 積極式、消極式和明確式載入
- 使用 LINQ (Language INtegrated Query) 的強型別查詢轉譯
- 豐富的對應功能，包括下列項目的支援：
  - 一對一、一對多及多對多關聯性
  - 繼承 (每個階層的資料表、每個類型的資料表，以及每個實體類別的資料表)
  - 複雜類型
  - 預存程序
- 用來建立實體模型的視覺化設計工具。
- 藉由撰寫程式碼來建立實體模型的「Code First」體驗。
- 您可以從現有資料庫產生模型，然後手動編輯，也可以從頭開始建立模型，然後用來產生新的資料庫。
- 與 .NET Framework 應用程式模型 (包括 ASP.NET) 的整合，或透過資料繫結與 WPF 和 WinForms 的整合。
- 依據 ADO.NET 的資料庫連線和許多提供者可用來連線至 SQL Server、Oracle、MySQL、SQLite、PostgreSQL、DB2 等等。

## <a name="should-i-use-ef6-or-ef-core"></a>我應該使用 EF6 還是 EF Core？

EF Core 是 Entity Framework　的新式輕量型可延伸版本，具有非常類似於 EF6 的功能和優點。
EF Core 是完全重新撰寫的版本，包含許多 EF6 並未提供的新功能，但它仍然缺少一些 EF6 最先進的對應功能。
如果功能集符合您的需求，請考慮在新的應用程式中使用 EF Core。
[比較 EF Core 與 EF6](xref:efcore-and-ef6/index)可更詳細地檢查這項選擇。

## <a name="get-startedef6get-startedmd"></a>[開始使用](~/ef6/get-started.md)

將 EntityFramework NuGet 套件新增至您的專案，或安裝 Entity Framework Tools for Visual Studio。 接著觀看影片、閱讀教學課程和進階文件，以協助您充分利用 EF6。

## <a name="past-entity-framework-versions"></a>過去的 Entity Framework 版本

這是適用於最新版 Entity Framework 6 的文件，但其中的大部分內容也適用於過去的版本。
如需 EF 版本和它們所引進功能的完整清單，請參閱[新功能](~/ef6/what-is-new/index.md)和[過去的版本](~/ef6/what-is-new/past-releases.md)。
