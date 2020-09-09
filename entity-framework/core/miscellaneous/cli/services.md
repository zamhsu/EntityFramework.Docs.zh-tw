---
title: 設計階段服務-EF Core
description: Entity Framework Core 設計階段服務的資訊
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617854"
---
# <a name="design-time-services"></a>設計階段服務

工具所使用的某些服務只會在設計階段使用。 這些服務是與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。 若要覆寫這些服務的其中之一 (例如，用來產生遷移檔) 的服務，請將的執行新增 `IDesignTimeServices` 至啟始專案。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
