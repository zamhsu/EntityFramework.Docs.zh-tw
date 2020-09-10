---
title: 序列-EF Core
description: 在 Entity Framework Core 模型中設定順序
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 1a212f3944ca95ebc7675ff29b2687c82fdf7fc7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619084"
---
# <a name="sequences"></a>序列

> [!NOTE]  
> 序列是通常只有關係資料庫才支援的功能。 如果您使用非關係資料庫（例如 Cosmos），請查看您的資料庫檔案，以產生唯一值。

順序會在資料庫中產生唯一的連續數值。 序列不會與特定資料表相關聯，而且可以設定多個資料表，以從相同的序列中繪製值。

## <a name="basic-usage"></a>基本使用方式

您可以在模型中設定序列，然後使用它來產生屬性的值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

請注意，用來從序列產生值的特定 SQL 是資料庫特定的;上述範例適用于 SQL Server，但在其他資料庫上將會失敗。 如需詳細資訊，請參閱您的特定資料庫檔案。

## <a name="configuring-sequence-settings"></a>設定順序設定

您也可以設定順序的其他層面，例如其架構、開始值、增量等：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
