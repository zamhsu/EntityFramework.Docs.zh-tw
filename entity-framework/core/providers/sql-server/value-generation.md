---
title: Microsoft SQL Server 資料庫提供者-值產生-EF Core
description: SQL Server Entity Framework Core 資料庫提供者專用的值產生模式
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987149"
---
# <a name="sql-server-value-generation"></a><span data-ttu-id="d685e-103">產生 SQL Server 值</span><span class="sxs-lookup"><span data-stu-id="d685e-103">SQL Server Value Generation</span></span>

<span data-ttu-id="d685e-104">此頁面詳細說明 SQL Server 提供者特定的值產生設定和模式。</span><span class="sxs-lookup"><span data-stu-id="d685e-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="d685e-105">建議您先閱讀 [值產生的 [一般] 頁面](xref:core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="d685e-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="d685e-106">IDENTITY 資料行</span><span class="sxs-lookup"><span data-stu-id="d685e-106">IDENTITY columns</span></span>

<span data-ttu-id="d685e-107">依照慣例，設定為在 add 上產生值的數值資料行，會設定為 [SQL SERVER 識別欄位](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)。</span><span class="sxs-lookup"><span data-stu-id="d685e-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="d685e-108">種子和遞增</span><span class="sxs-lookup"><span data-stu-id="d685e-108">Seed and increment</span></span>

<span data-ttu-id="d685e-109">根據預設，識別資料行會從1開始 (種子) ，而且每次將資料列加入 (遞增) 時，每次遞增1。</span><span class="sxs-lookup"><span data-stu-id="d685e-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="d685e-110">您可以設定不同的種子和增量，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d685e-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d685e-111">在 EF Core 3.0 中引進了設定識別種子和遞增的功能。</span><span class="sxs-lookup"><span data-stu-id="d685e-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="d685e-112">將明確值插入識別欄位</span><span class="sxs-lookup"><span data-stu-id="d685e-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="d685e-113">根據預設，SQL Server 不允許將明確的值插入識別欄位中。</span><span class="sxs-lookup"><span data-stu-id="d685e-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="d685e-114">若要這樣做，您必須在呼叫之前手動啟用，如下所示 `IDENTITY_INSERT` `SaveChanges()` ：</span><span class="sxs-lookup"><span data-stu-id="d685e-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="d685e-115">在我們的待辦項目上有一個在 SQL Server 提供者內自動進行此操作的[功能要求](https://github.com/aspnet/EntityFramework/issues/703)。</span><span class="sxs-lookup"><span data-stu-id="d685e-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="d685e-116">序列</span><span class="sxs-lookup"><span data-stu-id="d685e-116">Sequences</span></span>

<span data-ttu-id="d685e-117">除了識別資料行之外，您還可以使用標準順序。</span><span class="sxs-lookup"><span data-stu-id="d685e-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="d685e-118">這在各種案例中都很有用;例如，您可能想要讓多個資料行從單一序列繪製其預設值。</span><span class="sxs-lookup"><span data-stu-id="d685e-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="d685e-119">SQL Server 可讓您建立序列並使用它們，如 [序列的 [一般] 頁面中所](xref:core/modeling/sequences)述。</span><span class="sxs-lookup"><span data-stu-id="d685e-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="d685e-120">您可以透過設定屬性來使用序列 `HasDefaultValueSql()` 。</span><span class="sxs-lookup"><span data-stu-id="d685e-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>
