---
title: "EF Core 和 EF6 - 哪一個適合您"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: 9a113e0965fa75a03510199fb75165f6e9be0bbd
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a><span data-ttu-id="cf35c-102">EF Core 和 EF6：哪一個適合您</span><span class="sxs-lookup"><span data-stu-id="cf35c-102">EF Core and EF6: Which One Is Right for You</span></span>

<span data-ttu-id="cf35c-103">下列資訊將協助您在 Entity Framework Core 和 Entity Framework 6 之間選擇。</span><span class="sxs-lookup"><span data-stu-id="cf35c-103">The following information will help you choose between Entity Framework Core and Entity Framework 6.</span></span>

## <a name="guidance-for-new-applications"></a><span data-ttu-id="cf35c-104">新應用程式的指引</span><span class="sxs-lookup"><span data-stu-id="cf35c-104">Guidance for new applications</span></span>

<span data-ttu-id="cf35c-105">因為 EF Core 是新產品，且仍然缺少一些重要的 O/RM 功能，因此對於許多應用程式而言，EF6 仍將是最適合的選擇。</span><span class="sxs-lookup"><span data-stu-id="cf35c-105">Because EF Core is a new product, and still lacks some critical O/RM features, EF6 will still be the most suitable choice for many applications.</span></span>

<span data-ttu-id="cf35c-106">**以下是我們建議使用 EF Core 的應用程式類型：**</span><span class="sxs-lookup"><span data-stu-id="cf35c-106">**These are the types of applications we would recommend using EF Core for:**</span></span>

* <span data-ttu-id="cf35c-107">不需要尚未在 EF Core 中實作之功能的新應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf35c-107">New applications that do not require features that are not yet implemented in EF Core.</span></span> <span data-ttu-id="cf35c-108">檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf35c-108">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

* <span data-ttu-id="cf35c-109">以 .NET Core 為目標的應用程式，例如通用 Windows 平台 (UWP) 和 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf35c-109">Applications that target .NET Core, such as Universal Windows Platform (UWP) and ASP.NET Core applications.</span></span> <span data-ttu-id="cf35c-110">這些應用程式不能使用 EF6，因為 EF6 需要 .NET Framework (亦即 .NET Framework 4.5)。</span><span class="sxs-lookup"><span data-stu-id="cf35c-110">These applications can not use EF6 as it requires the .NET Framework (i.e. .NET Framework 4.5).</span></span>

## <a name="guidance-for-existing-ef6-applications"></a><span data-ttu-id="cf35c-111">現有 EF6 應用程式的指引</span><span class="sxs-lookup"><span data-stu-id="cf35c-111">Guidance for existing EF6 applications</span></span>

<span data-ttu-id="cf35c-112">因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。</span><span class="sxs-lookup"><span data-stu-id="cf35c-112">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span> <span data-ttu-id="cf35c-113">如果您想要移至 EF Core 來使用新功能，則在開始之前，請務必確認已了解其限制。</span><span class="sxs-lookup"><span data-stu-id="cf35c-113">If you want to move to EF Core to make use of new features, then make sure you are aware of its limitations before you start.</span></span> <span data-ttu-id="cf35c-114">檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="cf35c-114">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

<span data-ttu-id="cf35c-115">**您應該將從 EF6 移至 EF Core 視為移植，而非升級。**</span><span class="sxs-lookup"><span data-stu-id="cf35c-115">**You should view the move from EF6 to EF Core as a port rather than an upgrade.**</span></span> <span data-ttu-id="cf35c-116">如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。</span><span class="sxs-lookup"><span data-stu-id="cf35c-116">See [Porting from EF6 to EF Core](porting/index.md) for more information.</span></span>
