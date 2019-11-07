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
# <a name="design-time-services"></a>設計階段服務

工具所使用的某些服務只會在設計階段使用。 這些服務會與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。 若要覆寫其中一個服務（例如，用來產生遷移檔案的服務），請將 `IDesignTimeServices` 的執行新增至您的啟始專案。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
