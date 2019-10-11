---
title: 使用已中斷連線的實體 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 12138003-a373-4817-b1b7-724130202f5f
ms.openlocfilehash: f1ce44e7b00ec4c60a81ed850ce5c9d866495e1b
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181761"
---
# <a name="working-with-disconnected-entities"></a>使用已中斷連線的實體
在 Entity Framework 架構應用程式中，內容類別負責偵測套用至追蹤實體的變更。 呼叫 SaveChanges 方法會將內容所追蹤的變更保存到資料庫。 使用多層式架構 (N-Tier) 應用程式時，實體物件通常會在中斷與內容的連線時進行修改；因此您必須決定如何追蹤變更，以及如何將這些變更回報至內容。 本主題討論搭配使用 Entity Framework 與已中斷連線的實體時可用的不同選項。   

## <a name="web-service-frameworks"></a>Web 服務架構

Web 服務技術通常支援一些模式，而這些模式可用來保存已中斷連線之個別物件的變更。 例如，ASP.NET Web API 可讓您選寫控制器動作的程式碼，其中包含 EF 的呼叫，用來保存對資料庫上的物件所做的變更。 事實上，Visual Studio 中的 Web API 工具可讓您輕鬆地從 Entity Framework 6 模型中 Scaffold Web API 控制器。 如需詳細資訊，請參閱[搭配使用 Web API 與 Entity Framework 6](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/)。   

在過去，已有數項其他的 Web 服務技術提供與 Entity Framework 的整合，例如 [WCF Data Services](https://docs.microsoft.com/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) 和 [RIA Services](https://docs.microsoft.com/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91))。

## <a name="low-level-ef-apis"></a>低層級 EF API

如果您不想要使用現有的多層式架構 (N-Tier) 解決方案，或者想要自訂 Web API 服務中控制器動作所發生的狀況，Entity Framework 提供的 API 可讓您套用在中斷連線的層上所做的變更。 如需詳細資訊，請參閱[新增、附加和實體狀態](~/ef6/saving/change-tracking/entity-state.md)。  

## <a name="self-tracking-entities"></a>自我追蹤實體  

在中斷與 EF 內容的連線時追蹤任意實體圖形的變更是一道難題。 嘗試解決此問題的其中一個方法是自我追蹤實體的程式碼產生範本。 此範本會產生實體類別，其中包含的邏輯可用來追蹤已中斷連線層所做的變更，以作為實體本身的狀態。 還會產生一組擴充方法，將這些變更套用到內容。

這個範本可與使用 EF 設計工具建立的模型搭配使用，但無法與 Code First 模型搭配使用。 如需詳細資訊，請參閱[自我追蹤實體](self-tracking-entities/index.md)。  

> [!IMPORTANT]
> 我們不再建議使用自我追蹤實體範本。 繼續提供該範本只是為了支援現有應用程式。 如果您的應用程式需要使用已中斷連線的實體圖形，請考慮使用 [Trackable Entities](https://trackableentities.github.io/) (可追蹤的實體) 之類的其他替代項目，這是一項類似於自我追蹤實體的技術，可由社群更積極地進行開發或使用低層級變更追蹤 API 來撰寫自訂程式碼。
