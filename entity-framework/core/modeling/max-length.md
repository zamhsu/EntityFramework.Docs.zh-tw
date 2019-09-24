---
title: 最大長度-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: b6f0594fed0c491b4f79dcda5273cdebe9ecf35f
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197220"
---
# <a name="maximum-length"></a><span data-ttu-id="3bb35-102">長度上限</span><span class="sxs-lookup"><span data-stu-id="3bb35-102">Maximum Length</span></span>

<span data-ttu-id="3bb35-103">設定最大長度會針對要用於給定屬性的適當資料類型，提供資料存放區的提示。</span><span class="sxs-lookup"><span data-stu-id="3bb35-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="3bb35-104">最大長度僅適用于陣列資料類型，例如`string`和`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="3bb35-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="3bb35-105">Entity Framework 在傳遞資料給提供者之前，不會執行任何長度上限的驗證。</span><span class="sxs-lookup"><span data-stu-id="3bb35-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="3bb35-106">由提供者或資料存放區負責驗證是否適當。</span><span class="sxs-lookup"><span data-stu-id="3bb35-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="3bb35-107">例如，當以 SQL Server 為目標時，超過最大長度會導致例外狀況，因為基礎資料行的資料類型將不會允許儲存過量的資料。</span><span class="sxs-lookup"><span data-stu-id="3bb35-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="3bb35-108">慣例</span><span class="sxs-lookup"><span data-stu-id="3bb35-108">Conventions</span></span>

<span data-ttu-id="3bb35-109">依照慣例，它會留給資料庫提供者選擇適當的屬性資料類型。</span><span class="sxs-lookup"><span data-stu-id="3bb35-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="3bb35-110">對於長度為的屬性，資料庫提供者通常會選擇允許最長資料長度的資料類型。</span><span class="sxs-lookup"><span data-stu-id="3bb35-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="3bb35-111">例如，Microsoft SQL Server 將會使用`nvarchar(max)`做`string`為屬性（ `nvarchar(450)`如果資料行做為索引鍵，則為）。</span><span class="sxs-lookup"><span data-stu-id="3bb35-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="3bb35-112">資料註釋</span><span class="sxs-lookup"><span data-stu-id="3bb35-112">Data Annotations</span></span>

<span data-ttu-id="3bb35-113">您可以使用資料批註來設定屬性的最大長度。</span><span class="sxs-lookup"><span data-stu-id="3bb35-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="3bb35-114">在此範例中，以 SQL Server 為目標會導致`nvarchar(500)`使用的資料類型。</span><span class="sxs-lookup"><span data-stu-id="3bb35-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="3bb35-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="3bb35-115">Fluent API</span></span>

<span data-ttu-id="3bb35-116">您可以使用流暢的 API 來設定屬性的最大長度。</span><span class="sxs-lookup"><span data-stu-id="3bb35-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="3bb35-117">在此範例中，以 SQL Server 為目標會導致`nvarchar(500)`使用的資料類型。</span><span class="sxs-lookup"><span data-stu-id="3bb35-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
