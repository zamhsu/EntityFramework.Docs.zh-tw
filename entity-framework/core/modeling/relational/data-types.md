---
title: 資料類型-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9d2e647f-29e4-483b-af00-74269eb06e8f
uid: core/modeling/relational/data-types
ms.openlocfilehash: 26664ebe18abcdeaa2b9c8dc23a6410204f53c8e
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197189"
---
# <a name="data-types"></a><span data-ttu-id="f9b6f-102">資料類型</span><span class="sxs-lookup"><span data-stu-id="f9b6f-102">Data Types</span></span>

> [!NOTE]  
> <span data-ttu-id="f9b6f-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="f9b6f-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="f9b6f-105">「資料類型」指的是對應屬性之資料行的資料庫特定類型。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-105">Data type refers to the database specific type of the column to which a property is mapped.</span></span>

## <a name="conventions"></a><span data-ttu-id="f9b6f-106">慣例</span><span class="sxs-lookup"><span data-stu-id="f9b6f-106">Conventions</span></span>

<span data-ttu-id="f9b6f-107">依照慣例，資料庫提供者會根據屬性的 .NET 類型來選取資料類型。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-107">By convention, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="f9b6f-108">它也會考慮其他中繼資料，例如設定的[最大長度](../max-length.md)、屬性是否為主要金鑰的一部分等等。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-108">It also takes into account other metadata, such as the configured [Maximum Length](../max-length.md), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="f9b6f-109">例如，SQL Server 會使用`datetime2(7)`做`DateTime`為屬性， `nvarchar(max)`並`string`用於屬性（ `nvarchar(450)`或`string`用於做為索引鍵的屬性）。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-109">For example, SQL Server uses `datetime2(7)` for `DateTime` properties, and `nvarchar(max)` for `string` properties (or `nvarchar(450)` for `string` properties that are used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="f9b6f-110">資料註釋</span><span class="sxs-lookup"><span data-stu-id="f9b6f-110">Data Annotations</span></span>

<span data-ttu-id="f9b6f-111">您可以使用資料批註來指定資料行的精確資料類型。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-111">You can use Data Annotations to specify an exact data type for a column.</span></span>

<span data-ttu-id="f9b6f-112">例如`Url` ，下列程式碼會將設定為非 unicode 字串，其最大`200`長度為，並`Rating`將設定`5`為有效位數`2`和小數位數。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-112">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/DataType.cs?name=Entities&highlight=4,6)]

## <a name="fluent-api"></a><span data-ttu-id="f9b6f-113">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f9b6f-113">Fluent API</span></span>

<span data-ttu-id="f9b6f-114">您也可以使用流暢的 API 來為數據行指定相同的資料類型。</span><span class="sxs-lookup"><span data-stu-id="f9b6f-114">You can also use the Fluent API to specify the same data types for the columns.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DataType.cs?name=Model&highlight=9-10)]
