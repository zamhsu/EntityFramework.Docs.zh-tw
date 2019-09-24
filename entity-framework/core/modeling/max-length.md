---
title: 最大長度-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: b6f0594fed0c491b4f79dcda5273cdebe9ecf35f
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197220"
---
# <a name="maximum-length"></a>長度上限

設定最大長度會針對要用於給定屬性的適當資料類型，提供資料存放區的提示。 最大長度僅適用于陣列資料類型，例如`string`和`byte[]`。

> [!NOTE]  
> Entity Framework 在傳遞資料給提供者之前，不會執行任何長度上限的驗證。 由提供者或資料存放區負責驗證是否適當。 例如，當以 SQL Server 為目標時，超過最大長度會導致例外狀況，因為基礎資料行的資料類型將不會允許儲存過量的資料。

## <a name="conventions"></a>慣例

依照慣例，它會留給資料庫提供者選擇適當的屬性資料類型。 對於長度為的屬性，資料庫提供者通常會選擇允許最長資料長度的資料類型。 例如，Microsoft SQL Server 將會使用`nvarchar(max)`做`string`為屬性（ `nvarchar(450)`如果資料行做為索引鍵，則為）。

## <a name="data-annotations"></a>資料註釋

您可以使用資料批註來設定屬性的最大長度。 在此範例中，以 SQL Server 為目標會導致`nvarchar(500)`使用的資料類型。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API 來設定屬性的最大長度。 在此範例中，以 SQL Server 為目標會導致`nvarchar(500)`使用的資料類型。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
