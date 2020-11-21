---
title: 記錄和攔截的總覽-EF Core
description: EF Core 的記錄、事件、攔截器和診斷總覽
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 2c44772b22112645f85cf0bffa680bc510ea5afb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003519"
---
# <a name="overview-of-logging-and-interception"></a>記錄和攔截的總覽

Entity Framework Core (EF Core) 包含數個產生記錄、回應事件和取得診斷的機制。 這些都是針對不同的情況而量身訂做的，因此請務必為手邊的工作選取最佳的機制，即使有多個機制可以運作也是一樣。 例如，資料庫攔截器可以用來記錄 SQL，但是這種方式較適合用來記錄的其中一種機制來處理。 此頁面提供每個機制的總覽，並說明每個機制的使用時機。

## <a name="quick-reference"></a>快速參考

下表提供此處所述機制之間差異的快速參考。

| 機制 |  Async | 影響範圍 | 已登錄 | 預定用途
|:----------|--------|-------|------------|-------------
| 簡單記錄 | No | 每個內容 | 內容設定 | 開發階段記錄
| Microsoft.Extensions.Logging | No | 每個內容 * | D.I. 或內容設定 | 生產記錄
| 事件 | No | 每個內容 | 任何時間 | 回應 EF 事件
| 攔截器 | 是 | 每個內容 | 內容設定 | 操作 EF 作業
| 診斷接聽程式 | No | Process | 全域 | 應用程式診斷

* 通常是透過相依性 `Microsoft.Extensions.Logging` 插入來設定每個應用程式，但在 EF 層級，您可以視需要使用不同的記錄器 _來_ 設定每個內容。

## <a name="simple-logging"></a>簡單記錄

> [!NOTE]
> 這項功能是在 EF Core 5.0 中引進。

您可以使用[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)從任何類型的應用程式存取 EF Core 記錄 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> 設定 [DbCoNtext 實例](xref:core/dbcontext-configuration/index)時。 這項設定通常是在的覆寫中完成 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

此概念類似于 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> EF6。

如需詳細資訊，請參閱 [簡單記錄](xref:core/logging-events-diagnostics/simple-logging) 。

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

針對許多常見的記錄系統，記錄是具有外掛程式提供者的可[延伸](/dotnet/core/extensions/logging)記錄機制。 EF Core 與完全整合 `Microsoft.Extensions.Logging` ，並且預設會針對 ASP.NET Core 應用程式使用這種形式的記錄。

如需詳細資訊，請參閱 [使用 EF Core 中的登入](xref:core/logging-events-diagnostics/extensions-logging) 。

## <a name="events"></a>事件

> [!NOTE]
> EF Core 5.0 中引進了其他事件。

EF Core 會在 EF Core 程式碼中發生特定情況時，公開 [.net 事件](/dotnet/standard/events/) 以作為回呼。 事件比攔截器更簡單，並允許更具彈性的註冊。 不過，它們只會進行同步處理，因此無法執行非封鎖的非同步 i/o。

每個 DbCoNtext 實例都會註冊事件，而且可以隨時完成此註冊。 您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。

如需詳細資訊，請參閱 [EF Core 中的 .Net 事件](xref:core/logging-events-diagnostics/events) 。

## <a name="interception"></a>Interception

> [!NOTE]
> 這項功能是在 EF Core 3.0 中引進。 其他攔截器是在 EF Core 5.0 中引進。

EF Core 攔截器可以攔截、修改及/或隱藏 EF Core 作業。 這包括執行命令等低層級的資料庫作業，以及較高層級的作業，例如對 SaveChanges 的呼叫。

攔截器與記錄和診斷不同，因為它們允許修改或隱藏正在攔截的作業。 [簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。

當設定內容時，會針對每個 DbCoNtext 實例註冊攔截器。 您可以使用 [診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式來取得相同資訊，但會針對進程中的所有 DbCoNtext 實例取得相同的資訊。

如需詳細資訊，請參閱 [攔截](xref:core/logging-events-diagnostics/interceptors) 。

## <a name="diagnostic-listeners"></a>診斷接聽程式

診斷接聽項允許接聽目前 .NET 進程中發生的任何 EF Core 事件。

診斷接聽程式不適合從單一 DbCoNtext 實例取得事件。 EF Core 攔截器提供對每個內容註冊之相同事件的存取權。

診斷接聽程式並非設計來進行記錄。 [簡易記錄](xref:core/logging-events-diagnostics/simple-logging) 或 [Microsoft Extensions。記錄](xref:core/logging-events-diagnostics/extensions-logging) 是較佳的記錄選擇。

如需詳細資訊，請參閱 [使用 EF Core 中的診斷](xref:core/logging-events-diagnostics/diagnostic-listeners) 接聽程式。
