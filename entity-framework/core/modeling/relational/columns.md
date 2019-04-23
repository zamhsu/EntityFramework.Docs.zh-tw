---
title: 資料行對應的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 05a47de9-1078-488e-a823-b516a4208f33
uid: core/modeling/relational/columns
ms.openlocfilehash: 22fcafbfcf9daf765c94e6ca9c42d7770d3e7d07
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929858"
---
# <a name="column-mapping"></a><span data-ttu-id="b7690-102">資料行對應</span><span class="sxs-lookup"><span data-stu-id="b7690-102">Column Mapping</span></span>

> [!NOTE]  
> <span data-ttu-id="b7690-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="b7690-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="b7690-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="b7690-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="b7690-105">資料行對應會識別哪一個資料行的資料應該從查詢，並儲存到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="b7690-105">Column mapping identifies which column data should be queried from and saved to in the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="b7690-106">慣例</span><span class="sxs-lookup"><span data-stu-id="b7690-106">Conventions</span></span>

<span data-ttu-id="b7690-107">依照慣例，請將對應至名稱與屬性相同的資料行設定的每個屬性。</span><span class="sxs-lookup"><span data-stu-id="b7690-107">By convention, each property will be set up to map to a column with the same name as the property.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="b7690-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="b7690-108">Data Annotations</span></span>

<span data-ttu-id="b7690-109">若要設定屬性所對應的資料行，您可以使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="b7690-109">You can use Data Annotations to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/DataAnnotations/Samples/Relational/Column.cs?highlight=13)]

## <a name="fluent-api"></a><span data-ttu-id="b7690-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="b7690-110">Fluent API</span></span>

<span data-ttu-id="b7690-111">您可以使用 Fluent API 來設定屬性所對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="b7690-111">You can use the Fluent API to configure the column to which a property is mapped.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/Column.cs?highlight=11-13)]
