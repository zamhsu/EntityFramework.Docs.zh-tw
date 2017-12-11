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
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a>EF Core 和 EF6：哪一個適合您

下列資訊將協助您在 Entity Framework Core 和 Entity Framework 6 之間選擇。

## <a name="guidance-for-new-applications"></a>新應用程式的指引

因為 EF Core 是新產品，且仍然缺少一些重要的 O/RM 功能，因此對於許多應用程式而言，EF6 仍將是最適合的選擇。

**以下是我們建議使用 EF Core 的應用程式類型：**

* 不需要尚未在 EF Core 中實作之功能的新應用程式。 檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

* 以 .NET Core 為目標的應用程式，例如通用 Windows 平台 (UWP) 和 ASP.NET Core 應用程式。 這些應用程式不能使用 EF6，因為 EF6 需要 .NET Framework (亦即 .NET Framework 4.5)。

## <a name="guidance-for-existing-ef6-applications"></a>現有 EF6 應用程式的指引

因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。 如果您想要移至 EF Core 來使用新功能，則在開始之前，請務必確認已了解其限制。 檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

**您應該將從 EF6 移至 EF Core 視為移植，而非升級。** 如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。
