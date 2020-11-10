---
title: 繼承-EF Core
description: 如何使用 Entity Framework Core 設定實體類型繼承
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 3ec6e7bd98f9c9716c460d69fc707d95e5e47a05
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429516"
---
# <a name="inheritance"></a>繼承

EF 可將 .NET 型別階層對應到資料庫。 這可讓您照常以程式碼撰寫您的 .NET 實體、使用基底和衍生型別，而且 EF 可順暢地建立適當的資料庫架構、問題查詢等。如何對應類型階層的實際詳細資料與提供者相關;此頁面描述關係資料庫內容中的繼承支援。

## <a name="entity-type-hierarchy-mapping"></a>實體類型階層對應

依照慣例，EF 將不會自動掃描基底或衍生類型;這表示，如果您想要對應階層中的 CLR 型別，就必須在模型上明確指定該型別。 例如，只指定階層的基底類型，並不會造成 EF Core 隱含地包含其所有子類型。

下列範例會公開及其子類別的 DbSet `Blog` `RssBlog` 。 如果 `Blog` 有任何其他子類別，則不會包含在模型中。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> 使用 TPH 對應時，資料庫資料行會自動成為可為 null 的資料行。 例如，資料 `RssUrl` 行可為 null，因為一般 `Blog` 實例沒有該屬性。

如果您不想要針對階層 `DbSet` 中的一或多個實體公開，您也可以使用流暢的 API，以確保它們包含在模型中。

> [!TIP]
> 如果您不依賴慣例，則可以使用明確地指定基底類型 `HasBaseType` 。 您也可以使用 `.HasBaseType((Type)null)` 從階層中移除實體類型。

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>每個階層的資料表和鑒別子設定

根據預設，EF 會使用 *每個* 階層的資料表來對應繼承 (TPH) 模式。 TPH 使用單一資料表來儲存階層中所有類型的資料，而鑒別子資料行會用來識別每個資料列所代表的類型。

上述模型會對應至下列資料庫架構 (請注意隱含建立的資料 `Discriminator` 行，以識別 `Blog` 每個資料列) 中所儲存的型別。

![使用每個階層的資料表模式查詢 Blog 實體階層之結果的螢幕擷取畫面](_static/inheritance-tph-data.png)

您可以設定鑒別子資料行的名稱和類型，以及用來識別階層中每一種類型的值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

在上述範例中，EF 在階層的基底實體上，隱含地將鑒別子新增為 [陰影屬性](xref:core/modeling/shadow-properties) 。 您可以設定此屬性，就像任何其他屬性一樣：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

最後，鑒別子也可以對應至實體中的一般 .NET 屬性：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

當查詢使用 TPH 模式的衍生實體時，EF Core 會在查詢中的鑒別子資料行上新增述詞。 此篩選準則可確保不會針對基底類型或不在結果中的同級類型取得任何額外的資料列。 因為查詢基底實體將會取得階層中所有實體的結果，所以會略過基底實體類型的篩選述詞。 從查詢具體化結果時，如果我們遇到不會對應到模型中任何實體類型的鑒別子值，我們會擲回例外狀況，因為我們不知道如何具體化結果。 只有當您的資料庫包含具有鑒別子值的資料列時，不會在 EF 模型中對應，才會發生此錯誤。 如果您有這類資料，您可以將 EF Core 模型中的鑒別子對應標示為 [不完整]，表示我們應該一律加入篩選述詞，以便查詢階層中的任何類型。 `IsComplete(false)` 在鑒別子設定上呼叫會將對應標示為不完整。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a>共用資料行

依預設，當階層中有兩個同級實體類型的屬性具有相同名稱時，它們會對應至兩個不同的資料行。 但是，如果其型別相同，則可以對應到相同的資料庫資料行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>每一類型的資料表設定

> [!NOTE]
> 每一類型的 (TPT) 資料表是 EF Core 5.0 中的新功能。 EF6 支援每個具體的資料表類型 (TPC) ，但 EF Core 尚未支援。

在 TPT 對應模式中，所有類型都會對應至個別資料表。 單獨屬於基底型別 (Base Type) 或衍生型別 (Derived Type) 的屬性會儲存在對應至該型別的資料表中。 對應至衍生類型的資料表也會儲存外鍵，以聯結衍生資料表與基表。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF 會為上述模型建立下列資料庫架構。

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> 如果重新命名 primary key 條件約束，則新名稱將會套用至對應至階層的所有資料表，未來 EF 版本將允許在 [問題 19970](https://github.com/dotnet/efcore/issues/19970) 修正時，只針對特定資料表重新命名條件約束。

如果您使用大量設定，則可以藉由呼叫來抓取特定資料表的資料行名稱 <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> 。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
