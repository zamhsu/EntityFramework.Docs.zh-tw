---
title: 自訂遷移作業-EF Core
description: 使用 Entity Framework Core 管理資料庫架構管理的自訂和原始 SQL 遷移
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429828"
---
# <a name="custom-migrations-operations"></a>自訂遷移作業

MigrationBuilder API 可讓您在遷移期間執行許多不同種類的作業，但其實遠不多。 不過，此 API 也是可擴充的，可讓您定義自己的作業。 有兩種方式可以擴充 API：使用 `Sql()` 方法，或定義自訂 `MigrationOperation` 物件。

為了說明，讓我們看看如何使用每個方法來建立資料庫使用者。 在我們的遷移中，我們想要啟用撰寫下列程式碼：

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>使用 MigrationBuilder ( # A1

執行自訂作業最簡單的方式，就是定義呼叫的擴充方法 `MigrationBuilder.Sql()` 。 以下是產生適當 Transact-sql 的範例。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> `EXEC`當語句必須是 SQL 批次中的第一個或唯一一個語句時，請使用函數。 您也可能需要在等冪性遷移腳本中解決剖析器錯誤，這可能會在參考的資料行目前不存在於資料表上時發生。

如果您的遷移需要支援多個資料庫提供者，您可以使用 `MigrationBuilder.ActiveProvider` 屬性。 以下是支援 Microsoft SQL Server 和于 postgresql 的範例。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

只有當您知道將套用自訂作業的每個提供者時，此方法才會運作。

## <a name="using-a-migrationoperation"></a>使用 MigrationOperation

若要將自訂作業與 SQL 分離，您可以定義自己的操作 `MigrationOperation` 來代表它。 作業接著會傳遞給提供者，以便判斷要產生的適當 SQL。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

使用這個方法時，擴充方法只需要將這些作業的其中一項加入至 `MigrationBuilder.Operations` 。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

這種方法需要每個提供者知道如何在其服務中為這項作業產生 SQL `IMigrationsSqlGenerator` 。 以下範例會覆寫 SQL Server 的產生器，以處理新的作業。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

以更新的服務取代預設的遷移 sql 產生器服務。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
