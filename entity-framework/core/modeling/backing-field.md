---
title: EF Core 備份欄位-
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 79221b6f7968675ff10f80d5df181b674b6a20c9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994089"
---
# <a name="backing-fields"></a><span data-ttu-id="aaff6-102">支援欄位</span><span class="sxs-lookup"><span data-stu-id="aaff6-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="aaff6-103">這項功能是在 EF Core 1.1 的新功能。</span><span class="sxs-lookup"><span data-stu-id="aaff6-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="aaff6-104">支援欄位可讓 EF 來讀取和/或寫入的欄位，而不是屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="aaff6-105">這有助於進行應用程式程式碼封裝在類別中的用來限制的使用和/或增強對資料的存取語意，但值應該是從讀取和/或寫入資料庫，而不需使用這些限制 /增強功能。</span><span class="sxs-lookup"><span data-stu-id="aaff6-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="aaff6-106">慣例</span><span class="sxs-lookup"><span data-stu-id="aaff6-106">Conventions</span></span>

<span data-ttu-id="aaff6-107">依照慣例，您會探索下列欄位，為支援給定的屬性 （依照優先順序列出） 的欄位。</span><span class="sxs-lookup"><span data-stu-id="aaff6-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="aaff6-108">欄位只會探索模型中包含的屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="aaff6-109">屬性包含在模型的詳細資訊，請參閱[包含與排除屬性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="aaff6-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/BackingField.cs#Sample)]

<span data-ttu-id="aaff6-110">設定支援欄位時，EF 會直接寫入該欄位時具體化實體執行個體，從資料庫 （而不使用屬性 setter）。</span><span class="sxs-lookup"><span data-stu-id="aaff6-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="aaff6-111">如果 EF 需要讀取或寫入值，在其他時候，它會盡可能使用屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="aaff6-112">比方說，如果 EF 必須更新屬性的值，它會使用屬性 setter，如果有定義。</span><span class="sxs-lookup"><span data-stu-id="aaff6-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="aaff6-113">如果屬性是唯讀，則會寫入至欄位。</span><span class="sxs-lookup"><span data-stu-id="aaff6-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="aaff6-114">資料註釋</span><span class="sxs-lookup"><span data-stu-id="aaff6-114">Data Annotations</span></span>

<span data-ttu-id="aaff6-115">無法設定支援欄位，使用資料註解。</span><span class="sxs-lookup"><span data-stu-id="aaff6-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="aaff6-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="aaff6-116">Fluent API</span></span>

<span data-ttu-id="aaff6-117">您可以使用 Fluent API 來設定屬性的支援欄位。</span><span class="sxs-lookup"><span data-stu-id="aaff6-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="aaff6-118">控制欄位的使用時機</span><span class="sxs-lookup"><span data-stu-id="aaff6-118">Controlling when the field is used</span></span>

<span data-ttu-id="aaff6-119">您可以設定當 EF 所使用的欄位或屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="aaff6-120">請參閱[PropertyAccessMode 列舉](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)解支援的選項。</span><span class="sxs-lookup"><span data-stu-id="aaff6-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="aaff6-121">不含屬性的欄位</span><span class="sxs-lookup"><span data-stu-id="aaff6-121">Fields without a property</span></span>

<span data-ttu-id="aaff6-122">您也可以在模型中的實體類別，沒有對應的 CLR 屬性，但改為使用欄位，來將資料儲存在實體中建立的概念的屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="aaff6-123">這點不同於[遮蔽屬性](shadow-properties.md)、 變更追蹤程式中儲存資料。</span><span class="sxs-lookup"><span data-stu-id="aaff6-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="aaff6-124">此外，這通常會用如果實體類別會使用方法來取得或設定值。</span><span class="sxs-lookup"><span data-stu-id="aaff6-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="aaff6-125">您可以提供 EF 中的欄位名稱`Property(...)`API。</span><span class="sxs-lookup"><span data-stu-id="aaff6-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="aaff6-126">如果沒有具有指定名稱的屬性，然後 EF 會尋找欄位。</span><span class="sxs-lookup"><span data-stu-id="aaff6-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="aaff6-127">您也可以選擇為屬性提供的名稱，而不是欄位名稱。</span><span class="sxs-lookup"><span data-stu-id="aaff6-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="aaff6-128">建立模型時，則會使用此名稱，最值得注意的是它會使用對應至資料庫中的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="aaff6-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="aaff6-129">當這個實體類別中沒有任何屬性時，您可以使用`EF.Property(...)`方法在 LINQ 查詢中參考的概念模型的一部分的屬性。</span><span class="sxs-lookup"><span data-stu-id="aaff6-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```
