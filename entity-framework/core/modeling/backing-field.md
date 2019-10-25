---
title: 支援欄位-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 288440a4494117fe59d27187e24424c4d2fd44ab
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811872"
---
# <a name="backing-fields"></a><span data-ttu-id="8dd16-102">支援欄位</span><span class="sxs-lookup"><span data-stu-id="8dd16-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="8dd16-103">這項功能在 EF Core 1.1 中是新的。</span><span class="sxs-lookup"><span data-stu-id="8dd16-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="8dd16-104">支援欄位可以讓 EF 讀取及/或寫入欄位，而不是屬性。</span><span class="sxs-lookup"><span data-stu-id="8dd16-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="8dd16-105">當使用類別中的封裝來限制和/或透過應用程式代碼存取資料時，這會很有用，但應該在不使用這些限制的情況下，從資料庫讀取和/或寫入值增強.</span><span class="sxs-lookup"><span data-stu-id="8dd16-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="8dd16-106">慣例</span><span class="sxs-lookup"><span data-stu-id="8dd16-106">Conventions</span></span>

<span data-ttu-id="8dd16-107">依照慣例，會將下欄欄位探索為指定屬性的支援欄位（依優先順序列出）。</span><span class="sxs-lookup"><span data-stu-id="8dd16-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="8dd16-108">只有模型中包含的屬性才會探索欄位。</span><span class="sxs-lookup"><span data-stu-id="8dd16-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="8dd16-109">如需模型中包含哪些屬性的詳細資訊，請參閱[包含 & 排除屬性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="8dd16-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="8dd16-110">當支援欄位已設定時，EF 會在從資料庫具體化實體實例時直接寫入該欄位（而不是使用屬性 setter）。</span><span class="sxs-lookup"><span data-stu-id="8dd16-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="8dd16-111">如果 EF 需要在其他時間讀取或寫入值，則會盡可能使用屬性。</span><span class="sxs-lookup"><span data-stu-id="8dd16-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="8dd16-112">例如，如果 EF 需要更新屬性的值，則會使用屬性 setter （如果有定義的話）。</span><span class="sxs-lookup"><span data-stu-id="8dd16-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="8dd16-113">如果屬性是唯讀的，則它會寫入至欄位。</span><span class="sxs-lookup"><span data-stu-id="8dd16-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="8dd16-114">資料註釋</span><span class="sxs-lookup"><span data-stu-id="8dd16-114">Data Annotations</span></span>

<span data-ttu-id="8dd16-115">無法使用資料批註來設定支援欄位。</span><span class="sxs-lookup"><span data-stu-id="8dd16-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="8dd16-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8dd16-116">Fluent API</span></span>

<span data-ttu-id="8dd16-117">您可以使用流暢的 API 來設定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="8dd16-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="8dd16-118">控制使用欄位的時機</span><span class="sxs-lookup"><span data-stu-id="8dd16-118">Controlling when the field is used</span></span>

<span data-ttu-id="8dd16-119">您可以設定 EF 使用欄位或屬性的時機。</span><span class="sxs-lookup"><span data-stu-id="8dd16-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="8dd16-120">如需支援的選項，請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="8dd16-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="8dd16-121">沒有屬性的欄位</span><span class="sxs-lookup"><span data-stu-id="8dd16-121">Fields without a property</span></span>

<span data-ttu-id="8dd16-122">您也可以在模型中建立概念屬性，其在實體類別中沒有對應的 CLR 屬性，而是使用欄位將資料儲存在實體中。</span><span class="sxs-lookup"><span data-stu-id="8dd16-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="8dd16-123">這與[陰影屬性](shadow-properties.md)不同，其中的資料會儲存在變更追蹤器中。</span><span class="sxs-lookup"><span data-stu-id="8dd16-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="8dd16-124">如果實體類別使用方法來取得/設定值，通常會使用此專案。</span><span class="sxs-lookup"><span data-stu-id="8dd16-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="8dd16-125">您可以為 EF 提供 `Property(...)` API 中的功能變數名稱。</span><span class="sxs-lookup"><span data-stu-id="8dd16-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="8dd16-126">如果沒有具有指定名稱的屬性，則 EF 會尋找欄位。</span><span class="sxs-lookup"><span data-stu-id="8dd16-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="8dd16-127">當實體類別中沒有屬性時，您可以在 LINQ 查詢中使用 `EF.Property(...)` 方法，來參考概念上為模型一部分的屬性。</span><span class="sxs-lookup"><span data-stu-id="8dd16-127">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
