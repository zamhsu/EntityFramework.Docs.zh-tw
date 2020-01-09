---
title: 序列-EF Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 6343e58dd79837cc69308a070c9814030505d7dd
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502478"
---
# <a name="sequences"></a>序列

> [!NOTE]  
> 順序是通常只有關係資料庫才支援的功能。 如果您使用非關係資料庫（例如 Cosmos），請查看您的資料庫檔案，以瞭解如何產生唯一值。

序列會在資料庫中產生唯一的連續數值。 順序不會與特定資料表相關聯，而且可以設定多個資料表，以從相同的順序繪製值。

## <a name="basic-usage"></a>基本使用方式

您可以在模型中設定序列，然後用它來產生屬性的值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

請注意，用來從序列產生值的特定 SQL 是資料庫特定的;上述範例適用于 SQL Server，但在其他資料庫上將會失敗。 如需詳細資訊，請參閱您的特定資料庫檔案。

## <a name="configuring-sequence-settings"></a>進行順序設定

您也可以設定序列的其他層面，例如其架構、開始值、增量等：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
