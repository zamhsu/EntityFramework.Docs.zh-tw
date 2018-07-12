---
title: 使用已中斷連線的實體 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 12138003-a373-4817-b1b7-724130202f5f
caps.latest.revision: 3
ms.openlocfilehash: 5419215a77b57ab3c92fb88a512510070ea23bd6
ms.sourcegitcommit: 45494121254ad4fdcec613d1dd22d850068d6f39
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "37913439"
---
# <a name="working-with-disconnected-entities"></a><span data-ttu-id="620cb-102">使用已中斷連線的實體</span><span class="sxs-lookup"><span data-stu-id="620cb-102">Working with disconnected entities</span></span>
<span data-ttu-id="620cb-103">在 Entity Framework 架構應用程式中，內容類別負責偵測套用至追蹤實體的變更。</span><span class="sxs-lookup"><span data-stu-id="620cb-103">In an Entity Framework-based application, a context class is responsible for detecting changes applied to tracked entities.</span></span> <span data-ttu-id="620cb-104">呼叫 SaveChanges 方法會將內容所追蹤的變更保存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="620cb-104">Calling the SaveChanges method persists the changes tracked by the context to the database.</span></span> <span data-ttu-id="620cb-105">使用多層式架構 (N-Tier) 應用程式時，實體物件通常會在中斷與內容的連線時進行修改；因此您必須決定如何追蹤變更，以及如何將這些變更回報至內容。</span><span class="sxs-lookup"><span data-stu-id="620cb-105">When working with n-tier applications, entity objects are usually modified while disconnected from the context, and you must decide how to track changes and report those changes back to the context.</span></span> <span data-ttu-id="620cb-106">本主題討論搭配使用 Entity Framework 與已中斷連線的實體時可用的不同選項。</span><span class="sxs-lookup"><span data-stu-id="620cb-106">This topic discusses different options that are available when using Entity Framework with disconnected entities.</span></span>   

## <a name="web-service-frameworks"></a><span data-ttu-id="620cb-107">Web 服務架構</span><span class="sxs-lookup"><span data-stu-id="620cb-107">Web service frameworks</span></span>

<span data-ttu-id="620cb-108">Web 服務技術通常支援一些模式，而這些模式可用來保存已中斷連線之個別物件的變更。</span><span class="sxs-lookup"><span data-stu-id="620cb-108">Web services technologies typically support patterns that can be used to persist changes on individual disconnected objects.</span></span> <span data-ttu-id="620cb-109">例如，ASP.NET Web API 可讓您選寫控制器動作的程式碼，其中包含 EF 的呼叫，用來保存對資料庫上的物件所做的變更。</span><span class="sxs-lookup"><span data-stu-id="620cb-109">For example, ASP.NET Web API allows you to code controller actions that can include calls to EF to persist changes made to an object on a database.</span></span> <span data-ttu-id="620cb-110">事實上，Visual Studio 中的 Web API 工具可讓您輕鬆地從 Entity Framework 6 模型中 Scaffold Web API 控制器。</span><span class="sxs-lookup"><span data-stu-id="620cb-110">In fact, the Web API tooling in Visual Studio makes it easy to scaffold a Web API controller from your Entity Framework 6 model.</span></span> <span data-ttu-id="620cb-111">如需詳細資訊，請參閱[搭配使用 Web API 與 Entity Framework 6](https://docs.microsoft.com/en-us/aspnet/web-api/overview/data/using-web-api-with-entity-framework/)。</span><span class="sxs-lookup"><span data-stu-id="620cb-111">For more information, see [using Web API with Entity Framework 6](https://docs.microsoft.com/en-us/aspnet/web-api/overview/data/using-web-api-with-entity-framework/).</span></span>   

<span data-ttu-id="620cb-112">在過去，已有數項其他的 Web 服務技術提供與 Entity Framework 的整合，例如 [WCF Data Services](https://docs.microsoft.com/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) 和 [RIA Services](https://docs.microsoft.com/en-us/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91))。</span><span class="sxs-lookup"><span data-stu-id="620cb-112">Historically, there have been several other Web services technologies that offered integration with Entity Framework, like [WCF Data Services](https://docs.microsoft.com/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) and [RIA Services](https://docs.microsoft.com/en-us/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91)).</span></span>

## <a name="low-level-ef-apis"></a><span data-ttu-id="620cb-113">低層級 EF API</span><span class="sxs-lookup"><span data-stu-id="620cb-113">Low-level EF APIs</span></span>

<span data-ttu-id="620cb-114">如果您不想要使用現有的多層式架構 (N-Tier) 解決方案，或者想要自訂 Web API 服務中控制器動作所發生的狀況，Entity Framework 提供的 API 可讓您套用在中斷連線的層上所做的變更。</span><span class="sxs-lookup"><span data-stu-id="620cb-114">If you don't want to use an existing n-tier solution, or if you want to customize what happens inside a controller action in a Web API services, Entity Framework provides APIs that allow you to apply changes made on a disconnected tier.</span></span> <span data-ttu-id="620cb-115">如需詳細資訊，請參閱[新增、附加和實體狀態](~/ef6/saving/change-tracking/entity-state.md)。</span><span class="sxs-lookup"><span data-stu-id="620cb-115">For more information, see [Add, Attach, and entity state](~/ef6/saving/change-tracking/entity-state.md).</span></span>  

## <a name="self-tracking-entities"></a><span data-ttu-id="620cb-116">自我追蹤實體</span><span class="sxs-lookup"><span data-stu-id="620cb-116">Self-Tracking Entities</span></span>  

<span data-ttu-id="620cb-117">在中斷與 EF 內容的連線時追蹤任意實體圖形的變更是一道難題。</span><span class="sxs-lookup"><span data-stu-id="620cb-117">Tracking changes on arbitrary graphs of entities while disconnected from the EF context is a hard problem.</span></span> <span data-ttu-id="620cb-118">嘗試解決此問題的其中一個方法是自我追蹤實體的程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="620cb-118">One of the attempts to solve it was the Self-Tracking Entities code generation template.</span></span> <span data-ttu-id="620cb-119">此範本會產生實體類別，其中包含的邏輯可用來追蹤已中斷連線層所做的變更，以作為實體本身的狀態。</span><span class="sxs-lookup"><span data-stu-id="620cb-119">This template generates entity classes that contain logic to track changes made on a disconnected tier as state in the entities themselves.</span></span> <span data-ttu-id="620cb-120">還會產生一組擴充方法，將這些變更套用到內容。</span><span class="sxs-lookup"><span data-stu-id="620cb-120">A set of extension methods is also generated to apply those changes to a context.</span></span>

<span data-ttu-id="620cb-121">這個範本可與使用 EF 設計工具建立的模型搭配使用，但無法與 Code First 模型搭配使用。</span><span class="sxs-lookup"><span data-stu-id="620cb-121">This template can be used with models created using the EF Designer, but can not be used with Code First models.</span></span> <span data-ttu-id="620cb-122">如需詳細資訊，請參閱[自我追蹤實體](self-tracking-entities/index.md)。</span><span class="sxs-lookup"><span data-stu-id="620cb-122">For more information, see [Self-Tracking Entities](self-tracking-entities/index.md).</span></span>  

> [!IMPORTANT]
> <span data-ttu-id="620cb-123">我們不再建議使用自我追蹤實體範本。</span><span class="sxs-lookup"><span data-stu-id="620cb-123">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="620cb-124">繼續提供該範本只是為了支援現有應用程式。</span><span class="sxs-lookup"><span data-stu-id="620cb-124">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="620cb-125">如果您的應用程式需要使用已中斷連線的實體圖形，請考慮使用 [Trackable Entities](http://trackableentities.github.io/) (可追蹤的實體) 之類的其他替代項目，這是一項類似於自我追蹤實體的技術，可由社群更積極地進行開發或使用低層級變更追蹤 API 來撰寫自訂程式碼。</span><span class="sxs-lookup"><span data-stu-id="620cb-125">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](http://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>
