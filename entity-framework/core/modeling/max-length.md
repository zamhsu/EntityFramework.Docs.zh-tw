---
title: 最大長度為 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: 3220518cb0a409b6e802d2f3a98acdb949ffbf56
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929845"
---
# <a name="maximum-length"></a><span data-ttu-id="af1f9-102">最大長度</span><span class="sxs-lookup"><span data-stu-id="af1f9-102">Maximum Length</span></span>

<span data-ttu-id="af1f9-103">設定最大長度，提供資料存放區，指定屬性来使用適當的資料類型的相關提示。</span><span class="sxs-lookup"><span data-stu-id="af1f9-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="af1f9-104">最大長度只適用於陣列資料類型，例如`string`和`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="af1f9-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="af1f9-105">Entity Framework 不會執行任何驗證的最大長度，然後將資料傳遞給提供者。</span><span class="sxs-lookup"><span data-stu-id="af1f9-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="af1f9-106">這是由提供者或資料存放區，以驗證是否適當。</span><span class="sxs-lookup"><span data-stu-id="af1f9-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="af1f9-107">比方說，當目標為 SQL Server，超出最大長度時，會導致例外狀況為基礎的資料行的資料類型將不允許超過儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="af1f9-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="af1f9-108">慣例</span><span class="sxs-lookup"><span data-stu-id="af1f9-108">Conventions</span></span>

<span data-ttu-id="af1f9-109">依照慣例，再由資料庫提供者來選擇適當的資料類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="af1f9-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="af1f9-110">具有長度屬性，如資料庫提供者通常會選擇可讓資料的最長長度的資料類型。</span><span class="sxs-lookup"><span data-stu-id="af1f9-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="af1f9-111">例如，將會使用 Microsoft SQL Server `nvarchar(max)` for`string`屬性 (或`nvarchar(450)`如果資料行來做為金鑰)。</span><span class="sxs-lookup"><span data-stu-id="af1f9-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="af1f9-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="af1f9-112">Data Annotations</span></span>

<span data-ttu-id="af1f9-113">若要設定屬性的最大長度，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="af1f9-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="af1f9-114">在此範例中，目標 SQL Server，這會導致`nvarchar(500)`所使用的資料類型。</span><span class="sxs-lookup"><span data-stu-id="af1f9-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="af1f9-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="af1f9-115">Fluent API</span></span>

<span data-ttu-id="af1f9-116">您可以使用 Fluent API 來設定屬性的最大長度。</span><span class="sxs-lookup"><span data-stu-id="af1f9-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="af1f9-117">在此範例中，目標 SQL Server，這會導致`nvarchar(500)`所使用的資料類型。</span><span class="sxs-lookup"><span data-stu-id="af1f9-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=11-13)]
