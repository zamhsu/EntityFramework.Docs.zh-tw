---
title: 支援欄位-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526754"
---
# <a name="backing-fields"></a><span data-ttu-id="ac7bd-102">支援欄位</span><span class="sxs-lookup"><span data-stu-id="ac7bd-102">Backing Fields</span></span>

<span data-ttu-id="ac7bd-103">支援欄位可以讓 EF 讀取及/或寫入欄位，而不是屬性。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="ac7bd-104">當使用類別中的封裝來限制和/或透過應用程式代碼存取資料時，這項功能會很有用，但在不使用這些限制/增強功能的情況下，應該從資料庫讀取和/或寫入此值。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="ac7bd-105">基本設定</span><span class="sxs-lookup"><span data-stu-id="ac7bd-105">Basic configuration</span></span>

<span data-ttu-id="ac7bd-106">依照慣例，會將下欄欄位探索為指定屬性的支援欄位（依優先順序列出）。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="ac7bd-107">在下列範例中， `Url` 屬性已設定為， `_url` 其支援欄位如下：</span><span class="sxs-lookup"><span data-stu-id="ac7bd-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="ac7bd-108">請注意，只有模型中包含的屬性才會探索支援欄位。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="ac7bd-109">如需模型中包含哪些屬性的詳細資訊，請參閱[包含 & 排除屬性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="ac7bd-110">您也可以使用資料批註（可在 EFCore 5.0 中取得）或流暢的 API 來設定支援欄位，例如，如果功能變數名稱未對應到上述慣例：</span><span class="sxs-lookup"><span data-stu-id="ac7bd-110">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="ac7bd-111">資料批註</span><span class="sxs-lookup"><span data-stu-id="ac7bd-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="ac7bd-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="ac7bd-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="ac7bd-113">欄位和屬性存取</span><span class="sxs-lookup"><span data-stu-id="ac7bd-113">Field and property access</span></span>

<span data-ttu-id="ac7bd-114">根據預設，EF 一律會讀取和寫入支援欄位（假設其中一個已正確設定），而且永遠不會使用屬性。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="ac7bd-115">不過，EF 也支援其他存取模式。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="ac7bd-116">例如，下列範例會指示 EF 僅在具體化時寫入支援欄位，並在所有其他情況下使用屬性：</span><span class="sxs-lookup"><span data-stu-id="ac7bd-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="ac7bd-117">如需一組完整的支援選項，請參閱[PropertyAccessMode 列舉](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-117">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="ac7bd-118">使用 EF Core 3.0，預設屬性存取模式從變更 `PreferFieldDuringConstruction` 為 `PreferField` 。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="ac7bd-119">僅限欄位屬性</span><span class="sxs-lookup"><span data-stu-id="ac7bd-119">Field-only properties</span></span>

<span data-ttu-id="ac7bd-120">您也可以在模型中建立概念屬性，其在實體類別中沒有對應的 CLR 屬性，而是使用欄位將資料儲存在實體中。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="ac7bd-121">這與[陰影屬性](shadow-properties.md)不同，其中的資料會儲存在變更追蹤程式中，而不是實體的 CLR 型別中。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="ac7bd-122">僅限欄位屬性通常會在實體類別使用方法（而非屬性）來取得/設定值，或在網域模型中不應該公開欄位（例如主鍵）的情況下使用。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="ac7bd-123">您可以在 API 中提供名稱，以設定僅限欄位的屬性 `Property(...)` ：</span><span class="sxs-lookup"><span data-stu-id="ac7bd-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="ac7bd-124">EF 會嘗試尋找具有指定名稱的 CLR 屬性，如果找不到屬性，則為欄位。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="ac7bd-125">如果找不到屬性和欄位，則會改為設定陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="ac7bd-126">您可能需要從 LINQ 查詢參考僅限欄位的屬性，但這類欄位通常是私用的。</span><span class="sxs-lookup"><span data-stu-id="ac7bd-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="ac7bd-127">您可以 `EF.Property(...)` 在 LINQ 查詢中使用方法來參考欄位：</span><span class="sxs-lookup"><span data-stu-id="ac7bd-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
