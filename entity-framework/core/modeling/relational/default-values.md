---
title: 預設值-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e541366a-130f-47dd-9997-1b110a11febe
uid: core/modeling/relational/default-values
ms.openlocfilehash: b6ac283d551e2c6ee119f7de6933363b5d8793a1
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655909"
---
# <a name="default-values"></a><span data-ttu-id="7c7fc-102">預設值</span><span class="sxs-lookup"><span data-stu-id="7c7fc-102">Default Values</span></span>

> [!NOTE]  
> <span data-ttu-id="7c7fc-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="7c7fc-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="7c7fc-105">如果插入新的資料列，但未指定資料行的值，則資料行的預設值就是將插入的值。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-105">The default value of a column is the value that will be inserted if a new row is inserted but no value is specified for the column.</span></span>

## <a name="conventions"></a><span data-ttu-id="7c7fc-106">慣例</span><span class="sxs-lookup"><span data-stu-id="7c7fc-106">Conventions</span></span>

<span data-ttu-id="7c7fc-107">依照慣例，預設值為 [未設定]。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-107">By convention, a default value is not configured.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7c7fc-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="7c7fc-108">Data Annotations</span></span>

<span data-ttu-id="7c7fc-109">您不能使用資料批註來設定預設值。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-109">You can not set a default value using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="7c7fc-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7c7fc-110">Fluent API</span></span>

<span data-ttu-id="7c7fc-111">您可以使用流暢的 API 來指定屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-111">You can use the Fluent API to specify the default value for a property.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValue.cs?name=DefaultValue&highlight=9)]

<span data-ttu-id="7c7fc-112">您也可以指定用來計算預設值的 SQL 片段。</span><span class="sxs-lookup"><span data-stu-id="7c7fc-112">You can also specify a SQL fragment that is used to calculate the default value.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultValueSql.cs?name=DefaultValueSql&highlight=9)]
