---
title: 序列-EF Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 6343e58dd79837cc69308a070c9814030505d7dd
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78417437"
---
# <a name="sequences"></a><span data-ttu-id="4726e-102">序列</span><span class="sxs-lookup"><span data-stu-id="4726e-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="4726e-103">順序是通常只有關係資料庫才支援的功能。</span><span class="sxs-lookup"><span data-stu-id="4726e-103">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="4726e-104">如果您使用非關係資料庫（例如 Cosmos），請查看您的資料庫檔案，以瞭解如何產生唯一值。</span><span class="sxs-lookup"><span data-stu-id="4726e-104">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="4726e-105">序列會在資料庫中產生唯一的連續數值。</span><span class="sxs-lookup"><span data-stu-id="4726e-105">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="4726e-106">順序不會與特定資料表相關聯，而且可以設定多個資料表，以從相同的順序繪製值。</span><span class="sxs-lookup"><span data-stu-id="4726e-106">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="4726e-107">基本使用方式</span><span class="sxs-lookup"><span data-stu-id="4726e-107">Basic usage</span></span>

<span data-ttu-id="4726e-108">您可以在模型中設定序列，然後用它來產生屬性的值：</span><span class="sxs-lookup"><span data-stu-id="4726e-108">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="4726e-109">請注意，用來從序列產生值的特定 SQL 是資料庫特定的;上述範例適用于 SQL Server，但在其他資料庫上將會失敗。</span><span class="sxs-lookup"><span data-stu-id="4726e-109">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="4726e-110">如需詳細資訊，請參閱您的特定資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="4726e-110">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="4726e-111">進行順序設定</span><span class="sxs-lookup"><span data-stu-id="4726e-111">Configuring sequence settings</span></span>

<span data-ttu-id="4726e-112">您也可以設定序列的其他層面，例如其架構、開始值、增量等：</span><span class="sxs-lookup"><span data-stu-id="4726e-112">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
