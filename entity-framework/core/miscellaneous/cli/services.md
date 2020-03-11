---
title: 設計階段服務-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416725"
---
# <a name="design-time-services"></a><span data-ttu-id="62416-102">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="62416-102">Design-time services</span></span>

<span data-ttu-id="62416-103">工具所使用的某些服務只會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="62416-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="62416-104">這些服務會與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="62416-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="62416-105">若要覆寫其中一個服務（例如，用來產生遷移檔案的服務），請將 `IDesignTimeServices` 的執行新增至您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="62416-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
