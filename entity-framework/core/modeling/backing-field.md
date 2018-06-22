---
title: EF 核心備份欄位-
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
ms.technology: entity-framework-core
uid: core/modeling/backing-field
ms.openlocfilehash: e95417b3368d09a64f9ec02ae40c38dc770c2e6b
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2017
ms.locfileid: "26053458"
---
# <a name="backing-fields"></a><span data-ttu-id="18d98-102">支援欄位</span><span class="sxs-lookup"><span data-stu-id="18d98-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="18d98-103">這項功能是在 EF 核心 1.1 的新功能。</span><span class="sxs-lookup"><span data-stu-id="18d98-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="18d98-104">支援欄位允許 EF 讀取及/或寫入欄位，而不是屬性。</span><span class="sxs-lookup"><span data-stu-id="18d98-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="18d98-105">這有助於進行應用程式碼，在類別中的封裝 （encapsulation） 用來限制的使用及/或加強周圍資料的存取權的語意，但值應該進行讀取及/或寫入資料庫，而不使用這些限制 /增強功能。</span><span class="sxs-lookup"><span data-stu-id="18d98-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="18d98-106">慣例</span><span class="sxs-lookup"><span data-stu-id="18d98-106">Conventions</span></span>

<span data-ttu-id="18d98-107">依照慣例，會探索下列欄位做為備份給定的屬性 （依照優先順序列出） 的欄位。</span><span class="sxs-lookup"><span data-stu-id="18d98-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="18d98-108">欄位只會探索模型中包含的屬性。</span><span class="sxs-lookup"><span data-stu-id="18d98-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="18d98-109">屬性包含在模型的詳細資訊，請參閱[包括 & 排除內容](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="18d98-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

<span data-ttu-id="18d98-110">設定支援欄位時，EF 會直接寫入該欄位時具體化實體執行個體從資料庫 （而非使用屬性 setter）。</span><span class="sxs-lookup"><span data-stu-id="18d98-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="18d98-111">如果 EF 需要讀取或寫入值，在其他時候，它會盡可能使用屬性。</span><span class="sxs-lookup"><span data-stu-id="18d98-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="18d98-112">比方說，如果 EF 需要更新屬性的值，它會使用屬性 setter，如果有定義。</span><span class="sxs-lookup"><span data-stu-id="18d98-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="18d98-113">如果屬性是唯讀，則會寫入至欄位。</span><span class="sxs-lookup"><span data-stu-id="18d98-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="18d98-114">資料註釋</span><span class="sxs-lookup"><span data-stu-id="18d98-114">Data Annotations</span></span>

<span data-ttu-id="18d98-115">無法設定支援的欄位，使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="18d98-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="18d98-116">關於 fluent 應用程式開發介面</span><span class="sxs-lookup"><span data-stu-id="18d98-116">Fluent API</span></span>

<span data-ttu-id="18d98-117">您可以使用 fluent 應用程式開發的應用程式開發介面來設定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="18d98-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="18d98-118">控制當使用此欄位</span><span class="sxs-lookup"><span data-stu-id="18d98-118">Controlling when the field is used</span></span>

<span data-ttu-id="18d98-119">您可以設定當 EF 使用的欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="18d98-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="18d98-120">請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)解支援的選項。</span><span class="sxs-lookup"><span data-stu-id="18d98-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="18d98-121">欄位沒有屬性</span><span class="sxs-lookup"><span data-stu-id="18d98-121">Fields without a property</span></span>

<span data-ttu-id="18d98-122">您也可以在實體類別中，沒有對應的 CLR 屬性，但改為使用欄位來儲存在實體中的資料在模型中建立概念的屬性。</span><span class="sxs-lookup"><span data-stu-id="18d98-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="18d98-123">這點不同於[遮蔽屬性](shadow-properties.md)、 變更追蹤程式中儲存資料。</span><span class="sxs-lookup"><span data-stu-id="18d98-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="18d98-124">此外，這通常會用如果實體類別會使用方法來取得或設定值。</span><span class="sxs-lookup"><span data-stu-id="18d98-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="18d98-125">您可以提供 EF 中的欄位名稱`Property(...)`應用程式開發介面。</span><span class="sxs-lookup"><span data-stu-id="18d98-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="18d98-126">如果沒有具有指定名稱的屬性，然後 EF 會尋找的欄位。</span><span class="sxs-lookup"><span data-stu-id="18d98-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="18d98-127">您也可以選擇輸入屬性的名稱，而不是欄位名稱。</span><span class="sxs-lookup"><span data-stu-id="18d98-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="18d98-128">建立模型時，會再使用此名稱，最值得注意的是它會使用對應至資料庫中的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="18d98-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="18d98-129">當實體類別中沒有任何屬性時，您可以使用`EF.Property(...)`在 LINQ 查詢中參考的概念模型中的部分屬性的方法。</span><span class="sxs-lookup"><span data-stu-id="18d98-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```
