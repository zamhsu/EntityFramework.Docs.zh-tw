---
title: 替代金鑰-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8a5931d4-b480-4298-af36-0e29d74a37c0
uid: core/modeling/alternate-keys
ms.openlocfilehash: e5bb0602adb465435c8e88d045395a9424b2d9a3
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655782"
---
# <a name="alternate-keys"></a>替代索引鍵

除了主鍵以外，其他索引鍵可作為每個實體實例的替代唯一識別碼。 您可以使用替代索引鍵做為關聯性的目標。 當使用關係資料庫時，這會對應至替代索引鍵資料行上唯一索引/條件約束的概念，以及一個或多個參考資料行的 foreign key 條件約束。

> [!TIP]  
> 如果您只想要強制執行資料行的唯一性，則您需要唯一索引，而不是替代索引鍵，請參閱[索引](indexes.md)。 在 EF 中，其他索引鍵提供的功能比唯一索引更高，因為它們可以當做外鍵的目標使用。

您通常會在需要時為您引進替代金鑰，而不需要手動設定。 如需詳細資訊，請參閱[慣例](#conventions)。

## <a name="conventions"></a>慣例

依照慣例，當您將不是主鍵的屬性識別為關聯性的目標時，會為您引進替代索引鍵。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

## <a name="data-annotations"></a>資料註釋

無法使用資料批註來設定替代索引鍵。

## <a name="fluent-api"></a>Fluent API

您可以使用流暢的 API，將單一屬性設定為替代金鑰。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=7,8)]

您也可以使用流暢的 API，將多個屬性設定為另一個索引鍵（也稱為複合的替代索引鍵）。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=7,8)]
