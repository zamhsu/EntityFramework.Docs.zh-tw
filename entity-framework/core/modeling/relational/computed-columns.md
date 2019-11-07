---
title: 計算資料行-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: 4e92ed6d785f3b7bdf54015101bdcddb9e4e0e1c
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655923"
---
# <a name="computed-columns"></a><span data-ttu-id="5b224-102">計算資料行</span><span class="sxs-lookup"><span data-stu-id="5b224-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="5b224-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="5b224-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="5b224-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="5b224-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="5b224-105">計算資料行是在資料庫中計算其值的資料行。</span><span class="sxs-lookup"><span data-stu-id="5b224-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="5b224-106">計算資料行可以使用資料表中的其他資料行來計算其值。</span><span class="sxs-lookup"><span data-stu-id="5b224-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="5b224-107">慣例</span><span class="sxs-lookup"><span data-stu-id="5b224-107">Conventions</span></span>

<span data-ttu-id="5b224-108">依照慣例，計算資料行不會在模型中建立。</span><span class="sxs-lookup"><span data-stu-id="5b224-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="5b224-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="5b224-109">Data Annotations</span></span>

<span data-ttu-id="5b224-110">無法使用資料批註來設定計算資料行。</span><span class="sxs-lookup"><span data-stu-id="5b224-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="5b224-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5b224-111">Fluent API</span></span>

<span data-ttu-id="5b224-112">您可以使用流暢的 API 來指定屬性應對應至計算資料行。</span><span class="sxs-lookup"><span data-stu-id="5b224-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/ComputedColumn.cs?name=ComputedColumn&highlight=9)]
