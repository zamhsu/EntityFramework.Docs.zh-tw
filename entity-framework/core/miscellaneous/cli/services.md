---
title: 設計階段服務-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655827"
---
# <a name="design-time-services"></a><span data-ttu-id="f9783-102">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="f9783-102">Design-time services</span></span>

<span data-ttu-id="f9783-103">工具所使用的某些服務只會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="f9783-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="f9783-104">這些服務會與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="f9783-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="f9783-105">若要覆寫其中一個服務（例如，用來產生遷移檔案的服務），請將 `IDesignTimeServices` 的執行新增至您的啟始專案。</span><span class="sxs-lookup"><span data-stu-id="f9783-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
