---
title: 資料型別-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 9060f66c752be01090ce40be6bf3a32f348ce571
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993517"
---
# <a name="data-types"></a><span data-ttu-id="cd754-102">資料類型</span><span class="sxs-lookup"><span data-stu-id="cd754-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="cd754-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="cd754-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="cd754-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="cd754-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="cd754-105">資料類型是指屬性所對應的資料行的資料庫特定型別。</span><span class="sxs-lookup"><span data-stu-id="cd754-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="cd754-106">慣例</span><span class="sxs-lookup"><span data-stu-id="cd754-106">Conventions</span></span>

<span data-ttu-id="cd754-107">依照慣例，資料庫提供者，請選取屬性的 CLR 型別為基礎的資料類型。</span><span class="sxs-lookup"><span data-stu-id="cd754-107">By convention, the database provider selects a data type based on the CLR type of the property.</span></span> <span data-ttu-id="cd754-108">它也會納入考慮，請在其他中繼資料，例如已設定[最大長度](../max-length.md)、 屬性是否為組件的主索引鍵等。</span><span class="sxs-lookup"><span data-stu-id="cd754-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="cd754-109">比方說，SQL Server 會使用`datetime2(7)`for`DateTime`屬性，和`nvarchar(max)`如`string`屬性 (或`nvarchar(450)`的`string`來做為金鑰的屬性)。</span><span class="sxs-lookup"><span data-stu-id="cd754-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="cd754-110">資料註釋</span><span class="sxs-lookup"><span data-stu-id="cd754-110">Data Annotations</span></span>

<span data-ttu-id="cd754-111">您可以使用資料註解來指定確切的資料類型資料行。</span><span class="sxs-lookup"><span data-stu-id="cd754-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="cd754-112">例如下列程式碼會設定`Url`為非 unicode 字串的最大長度`200`並`Rating`為十進位整數位數為`5`及調整的`2`。</span><span class="sxs-lookup"><span data-stu-id="cd754-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="cd754-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="cd754-113">Fluent API</span></span>

<span data-ttu-id="cd754-114">您也可以使用 Fluent API，來指定相同的資料類型的資料行。</span><span class="sxs-lookup"><span data-stu-id="cd754-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/DataType.cs?name=Model&highlight=9-10)]
