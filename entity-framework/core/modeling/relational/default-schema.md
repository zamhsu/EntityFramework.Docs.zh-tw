---
title: 預設架構-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e6e58473-9f5e-4a1f-ac0f-b87d2cbb667e
uid: core/modeling/relational/default-schema
ms.openlocfilehash: 1579fed007997aa4cf49b4c1290aee86c81c0000
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655973"
---
# <a name="default-schema"></a><span data-ttu-id="5bf1e-102">預設結構描述</span><span class="sxs-lookup"><span data-stu-id="5bf1e-102">Default Schema</span></span>

> [!NOTE]  
> <span data-ttu-id="5bf1e-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="5bf1e-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="5bf1e-105">如果未針對該物件明確設定架構，預設架構就是將在其中建立物件的資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-105">The default schema is the database schema that objects will be created in if a schema is not explicitly configured for that object.</span></span>

## <a name="conventions"></a><span data-ttu-id="5bf1e-106">慣例</span><span class="sxs-lookup"><span data-stu-id="5bf1e-106">Conventions</span></span>

<span data-ttu-id="5bf1e-107">依照慣例，資料庫提供者會選擇最適當的預設架構。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-107">By convention, the database provider will choose the most appropriate default schema.</span></span> <span data-ttu-id="5bf1e-108">例如，Microsoft SQL Server 會使用 `dbo` 架構，而 SQLite 不會使用架構（因為 SQLite 中不支援架構）。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-108">For example, Microsoft SQL Server will use the `dbo` schema and SQLite will not use a schema (since schemas are not supported in SQLite).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="5bf1e-109">資料註釋</span><span class="sxs-lookup"><span data-stu-id="5bf1e-109">Data Annotations</span></span>

<span data-ttu-id="5bf1e-110">您不能使用資料批註來設定預設的架構。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-110">You can not set the default schema using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="5bf1e-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5bf1e-111">Fluent API</span></span>

<span data-ttu-id="5bf1e-112">您可以使用流暢的 API 來指定預設架構。</span><span class="sxs-lookup"><span data-stu-id="5bf1e-112">You can use the Fluent API to specify a default schema.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/DefaultSchema.cs?name=DefaultSchema&highlight=7)]
