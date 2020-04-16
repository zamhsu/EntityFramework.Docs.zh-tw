---
title: 支援欄位 - EF 核心
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434171"
---
# <a name="backing-fields"></a><span data-ttu-id="60288-102">支援欄位</span><span class="sxs-lookup"><span data-stu-id="60288-102">Backing Fields</span></span>

<span data-ttu-id="60288-103">備份欄位允許 EF 讀取和/或寫入欄位而不是屬性。</span><span class="sxs-lookup"><span data-stu-id="60288-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="60288-104">當類中的封裝用於限制和/或增強應用程式代碼訪問數據的語義時,這非常有用,但該值應從資料庫讀取和/或寫入資料庫,而無需使用這些限制/增強功能。</span><span class="sxs-lookup"><span data-stu-id="60288-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="60288-105">基本設定</span><span class="sxs-lookup"><span data-stu-id="60288-105">Basic configuration</span></span>

<span data-ttu-id="60288-106">按照慣例,以下欄位將發現為給定屬性的後備欄位(按優先順序順序順序出)。</span><span class="sxs-lookup"><span data-stu-id="60288-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="60288-107">在下面的範例中,`Url`屬性設定`_url`為 備份欄位:</span><span class="sxs-lookup"><span data-stu-id="60288-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="60288-108">請注意,僅對模型中包含的屬性發現支援欄位。</span><span class="sxs-lookup"><span data-stu-id="60288-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="60288-109">有關模型中包含哪些屬性的詳細資訊,請參閱[包括&排除屬性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="60288-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="60288-110">您還可以使用資料註解或 Fluent API 設定備份欄位,例如,如果欄位名稱與上述約定不對應:</span><span class="sxs-lookup"><span data-stu-id="60288-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="60288-111">資料註解</span><span class="sxs-lookup"><span data-stu-id="60288-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="60288-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="60288-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="60288-113">欄位與屬性存取</span><span class="sxs-lookup"><span data-stu-id="60288-113">Field and property access</span></span>

<span data-ttu-id="60288-114">默認情況下,EF 將始終讀取和寫入備份欄位(假設一個字段已正確配置)並且永遠不會使用該屬性。</span><span class="sxs-lookup"><span data-stu-id="60288-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="60288-115">但是,EF 還支援其他訪問模式。</span><span class="sxs-lookup"><span data-stu-id="60288-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="60288-116">例如,以下範例指示 EF 僅在具體化時寫入備份欄位,並在所有其他情況下使用 該屬性:</span><span class="sxs-lookup"><span data-stu-id="60288-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="60288-117">有關支援的完整選項集,請參考[屬性存取模式的枚舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="60288-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="60288-118">使用 EF Core 3.0`PreferFieldDuringConstruction`時`PreferField`,預設屬性 存取模式從變更為 。</span><span class="sxs-lookup"><span data-stu-id="60288-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="60288-119">只欄位屬性</span><span class="sxs-lookup"><span data-stu-id="60288-119">Field-only properties</span></span>

<span data-ttu-id="60288-120">您還可以在模型中創建一個概念屬性,該屬性在實體類中沒有相應的 CLR 屬性,而是使用欄位將數據存儲在實體中。</span><span class="sxs-lookup"><span data-stu-id="60288-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="60288-121">這與[陰影屬性](shadow-properties.md)不同,其中數據存儲在更改跟蹤器中,而不是存儲在實體的 CLR 類型中。</span><span class="sxs-lookup"><span data-stu-id="60288-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="60288-122">當實體類使用方法而不是屬性來獲取/設置值時,或者在域模型中根本不不應公開欄位(例如主鍵)的情況下,通常使用僅欄位屬性。</span><span class="sxs-lookup"><span data-stu-id="60288-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="60288-123">透過`Property(...)`API 中提供名稱來設定僅欄位屬性:</span><span class="sxs-lookup"><span data-stu-id="60288-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="60288-124">如果找不到屬性,EF 將嘗試查找具有給定名稱的 CLR 屬性,或者查找欄位。</span><span class="sxs-lookup"><span data-stu-id="60288-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="60288-125">如果未找到屬性,也找不到欄位,則將改為設置陰影屬性。</span><span class="sxs-lookup"><span data-stu-id="60288-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="60288-126">您可能需要從 LINQ 查詢中引用僅欄位屬性,但此類欄位通常是私有的。</span><span class="sxs-lookup"><span data-stu-id="60288-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="60288-127">可以使用 LINQ`EF.Property(...)`查詢中的方法參考該欄位:</span><span class="sxs-lookup"><span data-stu-id="60288-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
