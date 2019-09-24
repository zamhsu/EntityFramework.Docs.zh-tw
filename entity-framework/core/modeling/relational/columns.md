---
title: 資料行對應-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: eaffc0cc1642f64edabeeef974f5f6de7a23b656
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197209"
---
# <a name="column-mapping"></a><span data-ttu-id="bafc2-102">資料行對應</span><span class="sxs-lookup"><span data-stu-id="bafc2-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="bafc2-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="bafc2-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="bafc2-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="bafc2-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="bafc2-105">資料行對應會識別應該在資料庫中從查詢並儲存到哪些資料行資料。</span><span class="sxs-lookup"><span data-stu-id="bafc2-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="bafc2-106">慣例</span><span class="sxs-lookup"><span data-stu-id="bafc2-106">Conventions</span></span>

<span data-ttu-id="bafc2-107">依照慣例，每個屬性都將設定為對應至名稱與屬性相同的資料行。</span><span class="sxs-lookup"><span data-stu-id="bafc2-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="bafc2-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="bafc2-108">Data Annotations</span></span>

<span data-ttu-id="bafc2-109">您可以使用資料批註來設定要對應屬性的資料行。</span><span class="sxs-lookup"><span data-stu-id="bafc2-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="bafc2-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="bafc2-110">Fluent API</span></span>

<span data-ttu-id="bafc2-111">您可以使用流暢的 API 來設定要對應屬性的資料行。</span><span class="sxs-lookup"><span data-stu-id="bafc2-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/Column.cs?highlight=11-13)]
