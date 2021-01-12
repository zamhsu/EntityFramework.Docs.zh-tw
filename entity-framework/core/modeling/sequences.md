---
title: 序列-EF Core
description: 在 Entity Framework Core 模型中設定順序
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: e388bde11b1251564b37227e8884d2b8c34dc02e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128520"
---
# <a name="sequences"></a><span data-ttu-id="2e1dc-103">序列</span><span class="sxs-lookup"><span data-stu-id="2e1dc-103">Sequences</span></span>

> [!NOTE]
> <span data-ttu-id="2e1dc-104">序列是通常只有關係資料庫才支援的功能。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-104">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="2e1dc-105">如果您使用非關係資料庫（例如 Cosmos），請查看您的資料庫檔案，以產生唯一值。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-105">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="2e1dc-106">順序會在資料庫中產生唯一的連續數值。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-106">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="2e1dc-107">序列不會與特定資料表相關聯，而且可以設定多個資料表，以從相同的序列中繪製值。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-107">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="2e1dc-108">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="2e1dc-108">Basic usage</span></span>

<span data-ttu-id="2e1dc-109">您可以在模型中設定序列，然後使用它來產生屬性的值：</span><span class="sxs-lookup"><span data-stu-id="2e1dc-109">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="2e1dc-110">請注意，用來從序列產生值的特定 SQL 是資料庫特定的;上述範例適用于 SQL Server，但在其他資料庫上將會失敗。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-110">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="2e1dc-111">如需詳細資訊，請參閱您的特定資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="2e1dc-111">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="2e1dc-112">設定順序設定</span><span class="sxs-lookup"><span data-stu-id="2e1dc-112">Configuring sequence settings</span></span>

<span data-ttu-id="2e1dc-113">您也可以設定順序的其他層面，例如其架構、開始值、增量等：</span><span class="sxs-lookup"><span data-stu-id="2e1dc-113">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
