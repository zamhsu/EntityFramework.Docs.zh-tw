---
title: EF Core 和 EF6 - 哪一個適合您
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: f0a632902384a65ea3cddf752ad262c7a2e89e2e
ms.sourcegitcommit: 2ef0a4a90b01edd22b9206f8729b8de459ef8cab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
ms.locfileid: "30002817"
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a>EF Core 和 EF6：哪一個適合您

下列資訊將協助您在 Entity Framework Core 和 Entity Framework 6 之間選擇。

## <a name="guidance-for-new-applications"></a>新應用程式的指引

若您希望能利用 EF Core 的所有功能，且您的應用程式不需要尚未在 EF Core 中實作的任何功能，請考慮為新的應用程式使用 EF Core。

EF6 需要 .NET Framework 4.0 (或更新版本) 且只有在 Windows 上才支援 (也就是說，無法在 .NET Core 上執行且不受其他作業系統的支援)，但對於新的應用程式而言，只要這些限制可接受且應用程式不需要 EF6 無法使用的 EF Core 中之新功能，就仍然是可行的選擇。

檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

## <a name="guidance-for-existing-ef6-applications"></a>現有 EF6 應用程式的指引

因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。 如果您想要移至 EF Core 來使用新功能，則在開始之前，請務必確認已了解其限制。 檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

**您應該將從 EF6 移至 EF Core 視為移植，而非升級。** 如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。
