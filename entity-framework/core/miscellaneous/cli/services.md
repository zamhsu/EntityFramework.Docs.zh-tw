---
title: 設計階段服務-EF Core
description: Entity Framework Core 設計階段服務的資訊
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061940"
---
# <a name="design-time-services"></a><span data-ttu-id="10bcf-103">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="10bcf-103">Design-time services</span></span>

<span data-ttu-id="10bcf-104">工具所使用的某些服務只會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="10bcf-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="10bcf-105">這些服務是與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="10bcf-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="10bcf-106">若要覆寫這些服務的其中之一 (例如，用來產生遷移檔) 的服務，請將的執行新增 `IDesignTimeServices` 至啟始專案。</span><span class="sxs-lookup"><span data-stu-id="10bcf-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
