---
title: EF Core 和 EF6 - 哪一個適合您
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 288f825b-b3e6-4096-971b-d0a1cb96770e
uid: efcore-and-ef6/choosing
ms.openlocfilehash: 83ae754f899b624d322c48e8de8432b65519546e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993829"
---
# <a name="ef-core-and-ef6-which-one-is-right-for-you"></a>EF Core 和 EF6：哪一個適合您

下列資訊將協助您在 Entity Framework Core 和 Entity Framework 6 之間選擇。

## <a name="guidance-for-new-applications"></a>新應用程式的指引

若您希望能利用 EF Core 的所有功能，且您的應用程式不需要尚未在 EF Core 中實作的任何功能，請考慮為新的應用程式使用 EF Core。

EF6 需要 .NET Framework 4.0 (或更新版本) 且只受 Windows 支援 (亦即其目前無法在 .NET Core 上執行且不受其他作業系統支援)，但對於新的應用程式而言，只要這些限制可接受，且應用程式不需要 EF Core 中 EF6 無法使用的新功能，仍然是可行的選擇。

檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

## <a name="guidance-for-existing-ef6-applications"></a>現有 EF6 應用程式的指引

因為 EF Core 在本質上有所變更，我們不建議嘗試將 EF6 應用程式移至 EF Core，除非您有充足的理由進行該變更。 如果您想要移至 EF Core 來使用新功能，則在開始之前，請務必確認已了解其限制。 檢閱[功能比較](features.md)，以了解 EF Core 是否適合您的應用程式。

**您應該將從 EF6 移至 EF Core 視為移植，而非升級。** 如需詳細資訊，請參閱[從 EF6 移植到 EF Core](porting/index.md)。
