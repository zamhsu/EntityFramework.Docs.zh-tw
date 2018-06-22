---
title: 資料型別-EF 核心
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
ms.technology: entity-framework-core
uid: core/modeling/relational/data-types
ms.openlocfilehash: fd4668a3f9554eb9d3b1161d5dddce2fcdcac712
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053498"
---
# <a name="data-types"></a><span data-ttu-id="7b9f7-102">資料類型</span><span class="sxs-lookup"><span data-stu-id="7b9f7-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="7b9f7-103">本節中的設定是一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="7b9f7-104">當您安裝的關聯式資料庫提供者，如下所示的擴充方法會變成可用 (因為共用*Microsoft.EntityFrameworkCore.Relational*封裝)。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="7b9f7-105">資料類型是指屬性所對應的資料行的資料庫特定型別。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="7b9f7-106">慣例</span><span class="sxs-lookup"><span data-stu-id="7b9f7-106">Conventions</span></span>

<span data-ttu-id="7b9f7-107">依照慣例，資料庫提供者，請選取屬性的 CLR 型別為基礎的資料類型。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-107">By convention, the database provider selects a data type based on the CLR type of the property.</span></span> <span data-ttu-id="7b9f7-108">它也會考量其他中繼資料，例如設定[最大長度](../max-length.md)、 屬性是否為組件的主索引鍵等。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="7b9f7-109">例如，SQL Server 會使用`datetime2(7)`如`DateTime`屬性，和`nvarchar(max)`的`string`屬性 (或`nvarchar(450)`的`string`用做為索引鍵屬性)。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7b9f7-110">資料註釋</span><span class="sxs-lookup"><span data-stu-id="7b9f7-110">Data Annotations</span></span>

<span data-ttu-id="7b9f7-111">您可以使用資料註解來指定確切的資料類型資料行。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="7b9f7-112">下列程式碼的設定，例如`Url`為最大長度的非 unicode 字串`200`和`Rating`的有效位數為 decimal`5`及縮放的`2`。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="7b9f7-113">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="7b9f7-113">Fluent API</span></span>

<span data-ttu-id="7b9f7-114">您也可以使用 fluent 應用程式開發的應用程式開發介面來指定相同的資料類型的資料行。</span><span class="sxs-lookup"><span data-stu-id="7b9f7-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
