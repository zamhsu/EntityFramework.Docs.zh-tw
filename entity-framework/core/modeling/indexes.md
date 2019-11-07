---
title: 索引-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: d1b5cd6853cd24f7e1aa3aace2f0a3455c657cc1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655704"
---
# <a name="indexes"></a><span data-ttu-id="0541c-102">索引</span><span class="sxs-lookup"><span data-stu-id="0541c-102">Indexes</span></span>

<span data-ttu-id="0541c-103">索引是許多資料存放區的通用概念。</span><span class="sxs-lookup"><span data-stu-id="0541c-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="0541c-104">雖然其在資料存放區中的執行可能會有所不同，但它們是用來讓根據資料行（或資料行集）進行查閱更有效率。</span><span class="sxs-lookup"><span data-stu-id="0541c-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="0541c-105">慣例</span><span class="sxs-lookup"><span data-stu-id="0541c-105">Conventions</span></span>

<span data-ttu-id="0541c-106">依照慣例，索引會在當做外鍵使用的每一個屬性（或一組屬性）中建立。</span><span class="sxs-lookup"><span data-stu-id="0541c-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="0541c-107">資料註釋</span><span class="sxs-lookup"><span data-stu-id="0541c-107">Data Annotations</span></span>

<span data-ttu-id="0541c-108">無法使用資料批註來建立索引。</span><span class="sxs-lookup"><span data-stu-id="0541c-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="0541c-109">Fluent API</span><span class="sxs-lookup"><span data-stu-id="0541c-109">Fluent API</span></span>

<span data-ttu-id="0541c-110">您可以使用流暢的 API，在單一屬性上指定索引。</span><span class="sxs-lookup"><span data-stu-id="0541c-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="0541c-111">根據預設，索引不是唯一的。</span><span class="sxs-lookup"><span data-stu-id="0541c-111">By default, indexes are non-unique.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=7,8)]

<span data-ttu-id="0541c-112">您也可以指定索引應該是唯一的，這表示沒有兩個實體可以有指定屬性的相同值。</span><span class="sxs-lookup"><span data-stu-id="0541c-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=ModelBuilder&highlight=3)]

<span data-ttu-id="0541c-113">您也可以在一個以上的資料行上指定索引。</span><span class="sxs-lookup"><span data-stu-id="0541c-113">You can also specify an index over more than one column.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=7,8)]

> [!TIP]  
> <span data-ttu-id="0541c-114">每一組不同的屬性只有一個索引。</span><span class="sxs-lookup"><span data-stu-id="0541c-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="0541c-115">如果您使用流暢的 API，在已定義索引（依慣例或先前的設定）的一組屬性上設定索引，則您將會變更該索引的定義。</span><span class="sxs-lookup"><span data-stu-id="0541c-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="0541c-116">如果您想要進一步設定依照慣例所建立的索引，這會很有用。</span><span class="sxs-lookup"><span data-stu-id="0541c-116">This is useful if you want to further configure an index that was created by convention.</span></span>
