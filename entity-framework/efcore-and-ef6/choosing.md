---
title: EF Core 和 EF6 - 哪一個適合您
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: 17c81e0d6c384c06e45f0cf4f338d4ba402788e1
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949136"
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a><span data-ttu-id="8f736-102">EF Core 和 EF6：哪一個適合您</span><span class="sxs-lookup"><span data-stu-id="8f736-102">EF Core and EF6: Which One Is Right for You</span></span>

<span data-ttu-id="8f736-103">下列資訊將協助您在 Entity Framework Core 和 Entity Framework 6 之間選擇。</span><span class="sxs-lookup"><span data-stu-id="8f736-103">The following information will help you choose between Entity Framework Core and Entity Framework 6.</span></span>

## <a name="guidance-for-new-applications"></a><span data-ttu-id="8f736-104">新應用程式的指引</span><span class="sxs-lookup"><span data-stu-id="8f736-104">Guidance for new applications</span></span>

<span data-ttu-id="8f736-105">若您希望能利用 EF Core 的所有功能，且您的應用程式不需要尚未在 EF Core 中實作的任何功能，請考慮為新的應用程式使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="8f736-105">Consider using EF Core for new applications if you want to take advantage of the all the capabilities of EF Core and your application does not require any features that are not yet implemented in EF Core.</span></span>

<span data-ttu-id="8f736-106">EF6 需要 .NET Framework 4.0 (或更新版本) 且只受 Windows 支援 (亦即其目前無法在 .NET Core 上執行且不受其他作業系統支援)，但對於新的應用程式而言，只要這些限制可接受，且應用程式不需要 EF Core 中 EF6 無法使用的新功能，仍然是可行的選擇。</span><span class="sxs-lookup"><span data-stu-id="8f736-106">EF6 requires .NET Framework 4.0 (or a later version) and is only supported on Windows (that is, EF6 does not currently run on .NET Core and is not supported in other operating systems), but it is still a viable choice for new applications as long those constraints are acceptable and the application does not require new features in EF Core that are not available to EF6.</span></span>

<span data-ttu-id="8f736-107">檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8f736-107">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

## <a name="guidance-for-existing-ef6-applications"></a><span data-ttu-id="8f736-108">現有 EF6 應用程式的指引</span><span class="sxs-lookup"><span data-stu-id="8f736-108">Guidance for existing EF6 applications</span></span>

<span data-ttu-id="8f736-109">因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。</span><span class="sxs-lookup"><span data-stu-id="8f736-109">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span> <span data-ttu-id="8f736-110">如果您想要移至 EF Core 來使用新功能，則在開始之前，請務必確認已了解其限制。</span><span class="sxs-lookup"><span data-stu-id="8f736-110">If you want to move to EF Core to make use of new features, then make sure you are aware of its limitations before you start.</span></span> <span data-ttu-id="8f736-111">檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8f736-111">Review [Feature Comparison](features.md) to see if EF Core may be a suitable choice for your application.</span></span>

<span data-ttu-id="8f736-112">**您應該將從 EF6 移至 EF Core 視為移植，而非升級。**</span><span class="sxs-lookup"><span data-stu-id="8f736-112">**You should view the move from EF6 to EF Core as a port rather than an upgrade.**</span></span> <span data-ttu-id="8f736-113">如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。</span><span class="sxs-lookup"><span data-stu-id="8f736-113">See [Porting from EF6 to EF Core](porting/index.md) for more information.</span></span>
