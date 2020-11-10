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
# <a name="custom-migrations-operations"></a><span data-ttu-id="12288-103">自訂遷移作業</span><span class="sxs-lookup"><span data-stu-id="12288-103">Custom Migrations Operations</span></span>

<span data-ttu-id="12288-104">MigrationBuilder API 可讓您在遷移期間執行許多不同種類的作業，但其實遠不多。</span><span class="sxs-lookup"><span data-stu-id="12288-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="12288-105">不過，此 API 也是可擴充的，可讓您定義自己的作業。</span><span class="sxs-lookup"><span data-stu-id="12288-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="12288-106">有兩種方式可以擴充 API：使用 `Sql()` 方法，或定義自訂 `MigrationOperation` 物件。</span><span class="sxs-lookup"><span data-stu-id="12288-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="12288-107">為了說明，讓我們看看如何使用每個方法來建立資料庫使用者。</span><span class="sxs-lookup"><span data-stu-id="12288-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="12288-108">在我們的遷移中，我們想要啟用撰寫下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="12288-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="12288-109">使用 MigrationBuilder ( # A1</span><span class="sxs-lookup"><span data-stu-id="12288-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="12288-110">執行自訂作業最簡單的方式，就是定義呼叫的擴充方法 `MigrationBuilder.Sql()` 。</span><span class="sxs-lookup"><span data-stu-id="12288-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="12288-111">以下是產生適當 Transact-sql 的範例。</span><span class="sxs-lookup"><span data-stu-id="12288-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="12288-112">`EXEC`當語句必須是 SQL 批次中的第一個或唯一一個語句時，請使用函數。</span><span class="sxs-lookup"><span data-stu-id="12288-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="12288-113">您也可能需要在等冪性遷移腳本中解決剖析器錯誤，這可能會在參考的資料行目前不存在於資料表上時發生。</span><span class="sxs-lookup"><span data-stu-id="12288-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="12288-114">如果您的遷移需要支援多個資料庫提供者，您可以使用 `MigrationBuilder.ActiveProvider` 屬性。</span><span class="sxs-lookup"><span data-stu-id="12288-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="12288-115">以下是支援 Microsoft SQL Server 和于 postgresql 的範例。</span><span class="sxs-lookup"><span data-stu-id="12288-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="12288-116">只有當您知道將套用自訂作業的每個提供者時，此方法才會運作。</span><span class="sxs-lookup"><span data-stu-id="12288-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="12288-117">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="12288-117">Using a MigrationOperation</span></span>

<span data-ttu-id="12288-118">若要將自訂作業與 SQL 分離，您可以定義自己的操作 `MigrationOperation` 來代表它。</span><span class="sxs-lookup"><span data-stu-id="12288-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="12288-119">作業接著會傳遞給提供者，以便判斷要產生的適當 SQL。</span><span class="sxs-lookup"><span data-stu-id="12288-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="12288-120">使用這個方法時，擴充方法只需要將這些作業的其中一項加入至 `MigrationBuilder.Operations` 。</span><span class="sxs-lookup"><span data-stu-id="12288-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="12288-121">這種方法需要每個提供者知道如何在其服務中為這項作業產生 SQL `IMigrationsSqlGenerator` 。</span><span class="sxs-lookup"><span data-stu-id="12288-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="12288-122">以下範例會覆寫 SQL Server 的產生器，以處理新的作業。</span><span class="sxs-lookup"><span data-stu-id="12288-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="12288-123">以更新的服務取代預設的遷移 sql 產生器服務。</span><span class="sxs-lookup"><span data-stu-id="12288-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
