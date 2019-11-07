---
title: 主要金鑰-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c78f8f42-564a-45a4-aca7-3ede9f7ed2bc
uid: core/modeling/relational/primary-keys
ms.openlocfilehash: c195cc37859ea0689b5c6fbb84051564cf96c83a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656055"
---
# <a name="primary-keys"></a><span data-ttu-id="9d68f-102">主索引鍵</span><span class="sxs-lookup"><span data-stu-id="9d68f-102">Primary Keys</span></span>

> [!NOTE]  
> <span data-ttu-id="9d68f-103">本節中的組態一般適用於關聯式資料庫。</span><span class="sxs-lookup"><span data-stu-id="9d68f-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="9d68f-104">當您因共用 *Microsoft.EntityFrameworkCore.Relational* 套件而安裝關聯式資料庫提供者時，這裡顯示的擴充方法會變成可用。</span><span class="sxs-lookup"><span data-stu-id="9d68f-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="9d68f-105">針對每個實體類型的索引鍵，引進了 primary key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="9d68f-105">A primary key constraint is introduced for the key of each entity type.</span></span>

## <a name="conventions"></a><span data-ttu-id="9d68f-106">慣例</span><span class="sxs-lookup"><span data-stu-id="9d68f-106">Conventions</span></span>

<span data-ttu-id="9d68f-107">依照慣例，資料庫中的主要金鑰將會命名為 `PK_<type name>`。</span><span class="sxs-lookup"><span data-stu-id="9d68f-107">By convention, the primary key in the database will be named `PK_<type name>`.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="9d68f-108">資料註釋</span><span class="sxs-lookup"><span data-stu-id="9d68f-108">Data Annotations</span></span>

<span data-ttu-id="9d68f-109">不能使用資料批註來設定主鍵的關係資料庫特定層面。</span><span class="sxs-lookup"><span data-stu-id="9d68f-109">No relational database specific aspects of a primary key can be configured using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="9d68f-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="9d68f-110">Fluent API</span></span>

<span data-ttu-id="9d68f-111">您可以使用流暢的 API 來設定資料庫中的 primary key 條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="9d68f-111">You can use the Fluent API to configure the name of the primary key constraint in the database.</span></span>

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Relational/KeyName.cs?name=KeyName&highlight=9)]
