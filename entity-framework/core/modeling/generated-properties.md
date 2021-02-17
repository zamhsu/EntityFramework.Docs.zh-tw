---
title: 產生的值-EF Core
description: 如何在使用 Entity Framework Core 時設定屬性的值產生
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: a9e43f3b755bf028bc76581135988e831a42d0d1
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543337"
---
# <a name="generated-values"></a>產生的值

資料庫資料行的值會以各種不同的方式產生：主鍵資料行通常是自動遞增的整數、其他資料行有預設值或計算值等等。此頁面詳細說明使用 EF Core 產生設定值的各種模式。

## <a name="default-values"></a>預設值

在關係資料庫中，可以使用預設值來設定資料行。如果插入的資料列沒有該資料行的值，則會使用預設值。

您可以設定屬性的預設值：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

您也可以指定用來計算預設值的 SQL 片段：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>計算資料行

在大部分的關係資料庫上，您可以將資料行設定成在資料庫中計算其值，通常會有運算式參考其他資料行：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

上述程式會建立 *虛擬* 計算資料行，其值會在每次從資料庫提取時計算。 您也可以指定將計算資料行 *儲存* (有時稱為 *保存*) ，這表示它會在每次更新資料列時計算，並與一般資料行一起儲存在磁片上：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> EF Core 5.0 已新增建立預存計算資料行的支援。

## <a name="primary-keys"></a>主索引鍵

依照慣例，如果應用程式未提供值，就會設定 short、int、long 或 Guid 類型的非複合主鍵，以產生插入的實體值。 您的資料庫提供者通常會處理必要的設定;例如，SQL Server 中的數值主要索引鍵會自動設定為識別資料行。

如需詳細資訊，請 [參閱關於金鑰的檔](xref:core/modeling/keys)。

## <a name="explicitly-configuring-value-generation"></a>明確設定產生值

我們在上面看到 EF Core 會自動設定主鍵的值產生-但我們可能會想要對非索引鍵屬性進行相同的設定。 您可以設定任何屬性，將其值產生給插入的實體，如下所示：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

同樣地，您可以將屬性設定為在新增或更新時產生其值：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> 與預設值或計算資料行不同的是，我們不會指定產生值的 *方式* ;這取決於所使用的資料庫提供者。 資料庫提供者可能會自動為某些屬性類型設定值產生，但其他專案可能會要求您手動設定值的產生方式。
>
> 例如，在 SQL Server 上，當 GUID 屬性設定為 add 時產生的值時，提供者會使用演算法來產生最佳的連續 GUID 值，以自動執行值產生用戶端。 不過， `ValueGeneratedOnAdd()` 在 datetime 屬性上指定將沒有任何作用 ([請參閱下一節中的日期時間值產生](#datetime-value-generation)) 。
>
> 同樣地，在新增或更新時所設定的 byte [] 屬性，以及標示為並行 token 的，都會設定 rowversion 資料類型，以便在資料庫中自動產生值。 不過，指定 `ValueGeneratedOnAddOrUpdate()` 將不再有作用。
>
> [!NOTE]
> 根據所使用的資料庫提供者而定，值可能會由 EF 或資料庫中的用戶端產生。 如果值是由資料庫所產生，則當您將實體新增至內容時，EF 可能會指派暫時值;這個暫存值接著會由資料庫產生的值取代 `SaveChanges()` 。 如需詳細資訊，請 [參閱臨時值上的](xref:core/change-tracking/explicit-tracking#temporary-values)檔。

## <a name="datetime-value-generation"></a>日期/時間值產生

常見的要求是具有資料庫資料行，其中包含第一次將資料行插入的日期/時間， (新增) 上所產生的值，或上次更新的日期/時間 (在新增或更新) 時所產生的值。 由於有各種策略可以達成此目的，EF Core 提供者通常不會自動為日期/時間資料行設定值產生，您必須自行設定。

### <a name="creation-timestamp"></a>建立時間戳記

將日期/時間資料行設定為具有資料列的建立時間戳記，通常只需使用適當的 SQL 函數來設定預設值。 例如，在 SQL Server，您可以使用下列內容：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

請務必選取適當的函式，因為有數個可能存在 (例如 `GETDATE()` `GETUTCDATE()`) 。

### <a name="update-timestamp"></a>更新時間戳記

雖然儲存的計算資料行看起來像是管理上次更新時間戳記的良好解決方案，但資料庫通常不允許 `GETDATE()` 在計算資料行中指定函數。 或者，您可以設定資料庫觸發程式來達成相同的效果：

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

如需建立觸發程式的 [相關資訊，請參閱在遷移中使用原始 SQL 的相關檔](xref:core/managing-schemas/migrations/managing#adding-raw-sql)。

## <a name="overriding-value-generation"></a>覆寫產生值

雖然已針對值產生設定屬性，但在許多情況下，您仍然可以明確地指定值。 這是否會實際運作，取決於已設定的特定值產生機制;雖然您可以指定明確的值，而不是使用資料行的預設值，但是無法使用計算資料行來完成相同的工作。

若要以明確值覆寫值產生，只要將屬性設為任何不是該屬性之類型的 CLR 預設值的值， (for、for `null` `string` `0` `int` 、 `Guid.Empty` for、 `Guid` 等 ) 。

> [!NOTE]
> 依預設，嘗試將明確的值插入 SQL Server 身分識別會失敗; [如需因應措施，請參閱這些](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)檔。

若要針對已設定為在新增或更新時產生之值的屬性提供明確的值，您也必須設定屬性，如下所示：

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>不產生任何值

除了上面所述的特定案例之外，屬性通常不會設定值產生;這表示應用程式一定要提供要儲存至資料庫的值。 必須先將此值指派給新的實體，才能將其新增至內容。

不過，在某些情況下，您可能會想要停用依慣例設定的值產生。 例如，int 類型的主鍵通常會隱含地設定為值產生的 (例如 SQL Server) 上的識別欄位。 您可以透過下列方式停用此功能：

### <a name="data-annotations"></a>[資料批註](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
