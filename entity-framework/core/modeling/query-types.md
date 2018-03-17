---
title: "查詢類型-EF 核心"
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: dfd08cd1c30debddc79740bbf05c39c22e973855
ms.sourcegitcommit: 01b5cf3b7c983bcced91e7cc4c78391ced2d2caa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="query-types"></a>查詢類型
> [!NOTE]
> 這項功能的新 EF 核心 2.1

查詢類型都是可以加入至 EF 核心模型的唯讀查詢結果類型。 查詢類型啟用臨機操作查詢 （例如匿名型別），但不更有彈性，因為它們可以指定的對應設定。

在概念上類似於實體類型，包括：

- 它們都是 POCO C# 類型加入至模型中，有兩種```OnModelCreating```使用```ModelBuilder.Query```方法，或透過 DbContext 「 組 」 屬性 (如查詢類型這類屬性型別為```DbQuery<T>```而不是```DbSet<T>```)。
- 它們支援的大部分相同的對應功能，做為一般實體型別。 例如繼承對應、 巡覽 （請參閱下面的 limitiations），然後在關聯式存放區，讓您設定目標資料庫結構描述物件，透過```ToTable```， ```HasColumn``` fluent api 方法 （或資料註解）。

查詢類型會與不同的實體中的類型，它們：

- 不需要定義的索引鍵。
- 永遠不會追蹤變更追蹤程式。
- 永遠不會探索到的慣例。
- 只支援的瀏覽對應功能的子集具體而言，它們可能永遠不會做為關聯性的主要端點。
- 會對應至_定義查詢_-定義查詢是次要的查詢，做為資料來源的查詢類型。

主要的使用方式案例的查詢類型的部分包括：

- 將對應至資料庫檢視。
- 對應至沒有定義主索引鍵的資料表。
- 臨機操作做為傳回型別```FromSql()```查詢。
- 對應至模型中定義的查詢。

> [!TIP]
> 對應到資料庫檢視的查詢類型用來達成```ToTable```fluent 應用程式開發的應用程式開發介面。

## <a name="example"></a>範例

下列範例會示範如何使用查詢型別來查詢資料庫檢視。

> [!TIP]
> 您可以在 GitHub 上檢視此文章的[範例](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) \(英文\)。

首先，我們會定義簡單的部落格和後置模型：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

接下來，我們會定義簡單的資料庫檢視，可讓我們查詢的每個部落格相關聯的文章編號：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

接下來，我們會定義以保存從資料庫檢視結果的類別：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

接下來，我們設定中的查詢類型_OnModelCreating_使用```modelBuilder.Query<T>```應用程式開發介面。
我們可以使用標準 fluent 應用程式開發設定應用程式開發介面來設定查詢類型的對應：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

最後，我們可以查詢資料庫檢視，以標準方式：

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> 請注意，我們也已定義的內容層級查詢屬性 (DbQuery) 做為查詢對這種類型的根。
