---
title: CSDL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: 642e5977ecbbf0c474cac1ceae19d33a135aa875
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418774"
---
# <a name="csdl-specification"></a><span data-ttu-id="9398d-102">CSDL 規格</span><span class="sxs-lookup"><span data-stu-id="9398d-102">CSDL Specification</span></span>
<span data-ttu-id="9398d-103">概念結構定義語言 (CSDL) 是 XML架構語言，可描述組成資料驅動應用程式之概念模型的實體、關聯性和函式。</span><span class="sxs-lookup"><span data-stu-id="9398d-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="9398d-104">此概念模型可由 Entity Framework 或 WCF Data Services 使用。</span><span class="sxs-lookup"><span data-stu-id="9398d-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="9398d-105">Entity Framework 使用 CSDL 所描述的中繼資料，將概念模型中定義的實體和關聯性對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="9398d-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="9398d-106">如需詳細資訊，請參閱[SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)和[MSL 規格](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="9398d-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="9398d-107">CSDL 是實體資料模型的 Entity Framework 執行。</span><span class="sxs-lookup"><span data-stu-id="9398d-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="9398d-108">在 Entity Framework 應用程式中，概念模型中繼資料會從 csdl 檔案（以 CSDL 撰寫）載入 EdmItemCollection 的實例中，而且可以使用中的方法來存取。System.string. system.string 類別。</span><span class="sxs-lookup"><span data-stu-id="9398d-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="9398d-109">Entity Framework 使用概念模型中繼資料，將針對概念模型的查詢轉譯為數據源特定的命令。</span><span class="sxs-lookup"><span data-stu-id="9398d-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="9398d-110">EF 設計工具會在設計階段將概念模型資訊儲存在 .edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="9398d-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="9398d-111">在組建期間，EF 設計工具會使用 .edmx 檔案中的資訊，建立在執行時間 Entity Framework 所需的 csdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="9398d-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="9398d-112">CSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="9398d-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="9398d-113">CSDL 版本</span><span class="sxs-lookup"><span data-stu-id="9398d-113">CSDL Version</span></span> | <span data-ttu-id="9398d-114">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="9398d-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="9398d-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="9398d-115">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="9398d-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="9398d-116">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="9398d-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="9398d-117">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="9398d-118">Association 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-118">Association Element (CSDL)</span></span>

<span data-ttu-id="9398d-119">Association 元素會定義兩個實體類型之間的**關聯**性。</span><span class="sxs-lookup"><span data-stu-id="9398d-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="9398d-120">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="9398d-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="9398d-121">關聯端點的多重性可以有一（1）、零或一（0 ..1），或多個（\*）的值。</span><span class="sxs-lookup"><span data-stu-id="9398d-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="9398d-122">這項資訊會在兩個 End 子項目中指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="9398d-123">關聯其中一端的實體類型執行個體可透過導覽屬性或外部索引鍵來存取 (若在實體類型上公開)。</span><span class="sxs-lookup"><span data-stu-id="9398d-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="9398d-124">在應用程式中，關聯的執行個體代表實體類型之間的特定關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="9398d-125">關聯執行個體會在邏輯上群組於關聯集中。</span><span class="sxs-lookup"><span data-stu-id="9398d-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="9398d-126">**Association**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-127">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-128">End （正好2個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="9398d-129">ReferentialConstraint （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="9398d-130">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-131">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-131">Applicable Attributes</span></span>

<span data-ttu-id="9398d-132">下表描述可套用至**Association**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="9398d-133">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-133">Attribute Name</span></span> | <span data-ttu-id="9398d-134">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-134">Is Required</span></span> | <span data-ttu-id="9398d-135">值</span><span class="sxs-lookup"><span data-stu-id="9398d-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="9398d-136">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-136">**Name**</span></span>       | <span data-ttu-id="9398d-137">是</span><span class="sxs-lookup"><span data-stu-id="9398d-137">Yes</span></span>         | <span data-ttu-id="9398d-138">關聯的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-139">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="9398d-140">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-141">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-142">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-142">Example</span></span>

<span data-ttu-id="9398d-143">下列範例顯示當外鍵尚未在**Customer**和**Order**實體類型上公開時，定義**CustomerOrders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="9398d-144">關聯的每個**端點**的**多重性**值表示許多**訂單**可以與**客戶**相關聯，但只有一個**客戶**可以與**訂單**產生關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="9398d-145">此外， **OnDelete**元素表示如果**客戶**已被刪除，則會刪除與特定**客戶**相關且已載入 ObjectCoNtext 的所有**訂單**。</span><span class="sxs-lookup"><span data-stu-id="9398d-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="9398d-146">下列範例顯示在**Customer**和**Order**實體類型上公開外鍵時，定義**CustomerOrders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="9398d-147">公開外鍵後，實體之間的關聯性會使用**ReferentialConstraint**元素來管理。</span><span class="sxs-lookup"><span data-stu-id="9398d-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="9398d-148">對應此關聯與資料來源時，不需要對應的 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="9398d-149">AssociationSet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="9398d-150">概念結構定義語言（CSDL）中的**AssociationSet**元素是相同類型之關聯實例的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="9398d-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="9398d-151">關聯集提供群組關聯執行個體的定義，執行個體才能對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="9398d-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="9398d-152">**AssociationSet**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-153">檔（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-154">End （只需要兩個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="9398d-155">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="9398d-156">**Association**屬性會指定關聯集包含的關聯類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="9398d-157">組成關聯集端點的實體集是使用剛好兩**個子項目**來指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-158">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-158">Applicable Attributes</span></span>

<span data-ttu-id="9398d-159">下表描述可套用至**AssociationSet**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="9398d-160">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-160">Attribute Name</span></span>  | <span data-ttu-id="9398d-161">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-161">Is Required</span></span> | <span data-ttu-id="9398d-162">值</span><span class="sxs-lookup"><span data-stu-id="9398d-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-163">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-163">**Name**</span></span>        | <span data-ttu-id="9398d-164">是</span><span class="sxs-lookup"><span data-stu-id="9398d-164">Yes</span></span>         | <span data-ttu-id="9398d-165">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-165">The name of the entity set.</span></span> <span data-ttu-id="9398d-166">**Name**屬性的值不能與**Association**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="9398d-167">**技術協會**</span><span class="sxs-lookup"><span data-stu-id="9398d-167">**Association**</span></span> | <span data-ttu-id="9398d-168">是</span><span class="sxs-lookup"><span data-stu-id="9398d-168">Yes</span></span>         | <span data-ttu-id="9398d-169">關聯執行個體 (由關聯集包含) 之關聯的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="9398d-170">關聯必須存在與關聯集相同的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="9398d-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-171">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**AssociationSet**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="9398d-172">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-173">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-174">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-174">Example</span></span>

<span data-ttu-id="9398d-175">下列範例顯示具有兩個**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="9398d-176">CollectionType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="9398d-177">概念結構定義語言（CSDL）中的**CollectionType**元素會指定函式參數或函式傳回型別為集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="9398d-178">**CollectionType**元素可以是 Parameter 元素或 ReturnType （Function）元素的子系。</span><span class="sxs-lookup"><span data-stu-id="9398d-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="9398d-179">您可以使用**type**屬性或下列其中一個子項目來指定集合的類型：</span><span class="sxs-lookup"><span data-stu-id="9398d-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="9398d-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="9398d-180">**CollectionType**</span></span>
-   <span data-ttu-id="9398d-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9398d-181">ReferenceType</span></span>
-   <span data-ttu-id="9398d-182">RowType</span><span class="sxs-lookup"><span data-stu-id="9398d-182">RowType</span></span>
-   <span data-ttu-id="9398d-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="9398d-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-184">模型不會驗證是否同時使用**type**屬性和子專案來指定集合的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-185">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-185">Applicable Attributes</span></span>

<span data-ttu-id="9398d-186">下表描述可套用至**CollectionType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9398d-187">請注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**和定**序**屬性僅適用于**EDMSimpleTypes**的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="9398d-188">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-188">Attribute Name</span></span>                                                          | <span data-ttu-id="9398d-189">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-189">Is Required</span></span> | <span data-ttu-id="9398d-190">值</span><span class="sxs-lookup"><span data-stu-id="9398d-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-191">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-191">**Type**</span></span>                                                                | <span data-ttu-id="9398d-192">否</span><span class="sxs-lookup"><span data-stu-id="9398d-192">No</span></span>          | <span data-ttu-id="9398d-193">集合的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9398d-194">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-194">**Nullable**</span></span>                                                            | <span data-ttu-id="9398d-195">否</span><span class="sxs-lookup"><span data-stu-id="9398d-195">No</span></span>          | <span data-ttu-id="9398d-196">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="9398d-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="9398d-197">> 在 CSDL v1 中，複雜型別屬性必須具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="9398d-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="9398d-198">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9398d-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="9398d-199">否</span><span class="sxs-lookup"><span data-stu-id="9398d-199">No</span></span>          | <span data-ttu-id="9398d-200">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="9398d-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="9398d-202">否</span><span class="sxs-lookup"><span data-stu-id="9398d-202">No</span></span>          | <span data-ttu-id="9398d-203">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="9398d-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="9398d-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="9398d-205">否</span><span class="sxs-lookup"><span data-stu-id="9398d-205">No</span></span>          | <span data-ttu-id="9398d-206">**True**或**False** ，取決於屬性值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="9398d-207">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-207">**Precision**</span></span>                                                           | <span data-ttu-id="9398d-208">否</span><span class="sxs-lookup"><span data-stu-id="9398d-208">No</span></span>          | <span data-ttu-id="9398d-209">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="9398d-210">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-210">**Scale**</span></span>                                                               | <span data-ttu-id="9398d-211">否</span><span class="sxs-lookup"><span data-stu-id="9398d-211">No</span></span>          | <span data-ttu-id="9398d-212">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-213">**SRID**</span></span>                                                                | <span data-ttu-id="9398d-214">否</span><span class="sxs-lookup"><span data-stu-id="9398d-214">No</span></span>          | <span data-ttu-id="9398d-215">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-216">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-216">Valid only for properties of spatial types.</span></span><span data-ttu-id="9398d-217">   如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="9398d-217">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="9398d-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-218">**Unicode**</span></span>                                                             | <span data-ttu-id="9398d-219">否</span><span class="sxs-lookup"><span data-stu-id="9398d-219">No</span></span>          | <span data-ttu-id="9398d-220">**True**或**False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="9398d-221">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-221">**Collation**</span></span>                                                           | <span data-ttu-id="9398d-222">否</span><span class="sxs-lookup"><span data-stu-id="9398d-222">No</span></span>          | <span data-ttu-id="9398d-223">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="9398d-224">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9398d-225">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-226">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-227">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-227">Example</span></span>

<span data-ttu-id="9398d-228">下列範例示範的模型定義函式會使用**CollectionType**元素，來指定該函數會傳回**Person**實體類型的集合（如同使用**ElementType**屬性所指定）。</span><span class="sxs-lookup"><span data-stu-id="9398d-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="9398d-229">下列範例示範的模型定義函式會使用**CollectionType**專案來指定該函數傳回資料列集合（如**RowType**元素中所指定）。</span><span class="sxs-lookup"><span data-stu-id="9398d-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="9398d-230">下列範例顯示的模型定義函式會使用**CollectionType**元素，指定函式接受**部門**實體類型的集合做為參數。</span><span class="sxs-lookup"><span data-stu-id="9398d-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="9398d-231">ComplexType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="9398d-232">**ComplexType**元素會定義由**EdmSimpleType**屬性或其他複雜類型所組成的資料結構。</span><span class="sxs-lookup"><span data-stu-id="9398d-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="9398d-233">  複雜類型可以是實體類型或其他複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-233">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="9398d-234">複雜類型與實體類型相似之處在於複雜類型會定義資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="9398d-235">不過，複雜型別和實體類型之間還是有些重大的差異：</span><span class="sxs-lookup"><span data-stu-id="9398d-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="9398d-236">複雜型別不具有識別 (或索引鍵)，因此無法獨立存在。</span><span class="sxs-lookup"><span data-stu-id="9398d-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="9398d-237">複雜型別只能以實體類型或其他複雜型別的屬性形式存在。</span><span class="sxs-lookup"><span data-stu-id="9398d-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="9398d-238">複雜類型不可參與關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="9398d-239">關聯的兩個端點皆不可為複雜型別，因此不可為複雜型別定義導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="9398d-240">雖然複雜型別的純量屬性可能每個都設為 null，但複雜型別屬性不可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="9398d-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="9398d-241">**ComplexType**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-242">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-243">Property (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="9398d-244">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="9398d-245">下表描述可套用至**ComplexType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="9398d-246">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="9398d-247">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-247">Is Required</span></span> | <span data-ttu-id="9398d-248">值</span><span class="sxs-lookup"><span data-stu-id="9398d-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-249">名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-249">Name</span></span>                                                                                                           | <span data-ttu-id="9398d-250">是</span><span class="sxs-lookup"><span data-stu-id="9398d-250">Yes</span></span>         | <span data-ttu-id="9398d-251">複雜類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-251">The name of the complex type.</span></span> <span data-ttu-id="9398d-252">複雜類型的名稱不可與其他複雜類型、實體類型或模型範圍內之關聯的名稱相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="9398d-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="9398d-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="9398d-254">否</span><span class="sxs-lookup"><span data-stu-id="9398d-254">No</span></span>          | <span data-ttu-id="9398d-255">其他複雜類型的名稱是即將定義之複雜類型的基底型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="9398d-256">> 這個屬性在 CSDL v1 中不適用。</span><span class="sxs-lookup"><span data-stu-id="9398d-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="9398d-257">該版本不支援複雜類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="9398d-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="9398d-258">摘要</span><span class="sxs-lookup"><span data-stu-id="9398d-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="9398d-259">否</span><span class="sxs-lookup"><span data-stu-id="9398d-259">No</span></span>          | <span data-ttu-id="9398d-260">**True**或**False** （預設值），視複雜型別是否為抽象型別而定。</span><span class="sxs-lookup"><span data-stu-id="9398d-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="9398d-261">> 這個屬性在 CSDL v1 中不適用。</span><span class="sxs-lookup"><span data-stu-id="9398d-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="9398d-262">該版本的複雜類型不可以是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="9398d-263">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ComplexType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="9398d-264">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-265">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-266">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-266">Example</span></span>

<span data-ttu-id="9398d-267">下列範例顯示具有**EdmSimpleType**屬性**StreetAddress**、 **City**、 **StateOrProvince**、 **Country**和**郵遞區號**的複雜類型**Address**。</span><span class="sxs-lookup"><span data-stu-id="9398d-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="9398d-268">若要將複雜類型**位址**（上方）定義為實體類型的屬性，您必須在實體類型定義中宣告屬性類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="9398d-269">下列範例會將**Address**屬性顯示為實體類型（**發行者**）上的複雜類型：</span><span class="sxs-lookup"><span data-stu-id="9398d-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="9398d-270">DefiningExpression 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="9398d-271">概念結構定義語言（CSDL）中的**DefiningExpression**元素包含定義概念模型中之函式的 Entity SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="9398d-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="9398d-272">基於驗證目的， **DefiningExpression**元素可以包含任意內容。</span><span class="sxs-lookup"><span data-stu-id="9398d-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="9398d-273">不過，如果**DefiningExpression**元素不包含有效的 Entity SQL，Entity Framework 會在執行時間擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9398d-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-274">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-274">Applicable Attributes</span></span>

<span data-ttu-id="9398d-275">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**DefiningExpression**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="9398d-276">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-277">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-278">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-278">Example</span></span>

<span data-ttu-id="9398d-279">下列範例會使用**DefiningExpression**專案來定義函式，該函式會傳回書籍發行後的年數。</span><span class="sxs-lookup"><span data-stu-id="9398d-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="9398d-280">**DefiningExpression**元素的內容是以 Entity SQL 寫入。</span><span class="sxs-lookup"><span data-stu-id="9398d-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="9398d-281">Dependent 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="9398d-282">概念結構定義語言（CSDL）中的**相依**專案是 ReferentialConstraint 專案的子專案，並定義參考條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="9398d-283">**ReferentialConstraint**元素會定義與關係資料庫中的參考完整性條件約束類似的功能。</span><span class="sxs-lookup"><span data-stu-id="9398d-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9398d-284">資料庫資料表中的資料行可以參考另一個資料表的主要索引鍵，同樣地，實體類型的屬性也可以參考另一個實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9398d-285">參考的實體類型稱為條件約束的*主要端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9398d-286">參考主要端點的實體類型稱為條件約束的*相依端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="9398d-287">**PropertyRef**元素是用來指定哪些索引鍵會參考主要端。</span><span class="sxs-lookup"><span data-stu-id="9398d-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="9398d-288">相依元素可以具有下列子專案（**依**列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-289">PropertyRef （一或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9398d-290">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-291">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-291">Applicable Attributes</span></span>

<span data-ttu-id="9398d-292">下表描述可套用至**相依**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="9398d-293">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-293">Attribute Name</span></span> | <span data-ttu-id="9398d-294">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-294">Is Required</span></span> | <span data-ttu-id="9398d-295">值</span><span class="sxs-lookup"><span data-stu-id="9398d-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="9398d-296">**角色**</span><span class="sxs-lookup"><span data-stu-id="9398d-296">**Role**</span></span>       | <span data-ttu-id="9398d-297">是</span><span class="sxs-lookup"><span data-stu-id="9398d-297">Yes</span></span>         | <span data-ttu-id="9398d-298">位於關聯之相依端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-299">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**相依**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="9398d-300">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-301">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-302">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-302">Example</span></span>

<span data-ttu-id="9398d-303">下列範例顯示使用**ReferentialConstraint**元素做為**PublishedBy**關聯定義的一部分。</span><span class="sxs-lookup"><span data-stu-id="9398d-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="9398d-304">**Book**實體類型的**PublisherId**屬性構成參考條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="9398d-305">Documentation 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="9398d-306">概念結構定義語言（CSDL）中的**檔**專案可以用來提供父元素中所定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="9398d-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="9398d-307">在 .edmx 檔案中，**當檔專案**是專案的子系，而該專案在 EF 設計工具的設計介面上顯示為物件（例如實體、關聯或屬性）時，**檔**元素的內容會出現在物件的 [Visual Studio **屬性**] 視窗中。</span><span class="sxs-lookup"><span data-stu-id="9398d-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="9398d-308">**檔**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-309">**摘要**：父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="9398d-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="9398d-310">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-310">(zero or one element)</span></span>
-   <span data-ttu-id="9398d-311">**LongDescription**：上層元素的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="9398d-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="9398d-312">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-312">(zero or one element)</span></span>
-   <span data-ttu-id="9398d-313">Annotation 項目.</span><span class="sxs-lookup"><span data-stu-id="9398d-313">Annotation elements.</span></span> <span data-ttu-id="9398d-314">(零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-315">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-315">Applicable Attributes</span></span>

<span data-ttu-id="9398d-316">可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**檔**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="9398d-317">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-318">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-319">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-319">Example</span></span>

<span data-ttu-id="9398d-320">下列範例顯示**檔**元素做為 EntityType 元素的子專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="9398d-321">如果下列程式碼片段位於 .edmx 檔案的 CSDL 內容中，當您按一下 [`Customer`] 實體類型時，[**摘要**] 和 [ **LongDescription** ] 專案的內容就會出現在 [Visual Studio **屬性**] 視窗中。</span><span class="sxs-lookup"><span data-stu-id="9398d-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="9398d-322">End 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-322">End Element (CSDL)</span></span>

<span data-ttu-id="9398d-323">概念結構定義語言（CSDL）中的**End**元素可以是 Association 元素的子系或 AssociationSet 專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="9398d-324">在每個案例中， **End**專案的角色都不同，而適用的屬性則不同。</span><span class="sxs-lookup"><span data-stu-id="9398d-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="9398d-325">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="9398d-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="9398d-326">**End**專案（做為**association**元素的子系）會識別關聯一端的實體類型，以及該關聯結尾的實體類型實例數目。</span><span class="sxs-lookup"><span data-stu-id="9398d-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="9398d-327">關聯 End 會定義為關聯的部分。關聯必須具有兩個關聯 End。</span><span class="sxs-lookup"><span data-stu-id="9398d-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="9398d-328">關聯其中一端的實體型別執行個體可透過導覽屬性或外部索引鍵來存取 (若在實體型別上公開)。</span><span class="sxs-lookup"><span data-stu-id="9398d-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="9398d-329">**End**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-330">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-331">OnDelete （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="9398d-332">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-333">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-333">Applicable Attributes</span></span>

<span data-ttu-id="9398d-334">下表描述當屬性為**Association**元素的子系時，可以套用至**結尾**專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="9398d-335">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-335">Attribute Name</span></span>   | <span data-ttu-id="9398d-336">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-336">Is Required</span></span> | <span data-ttu-id="9398d-337">值</span><span class="sxs-lookup"><span data-stu-id="9398d-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-338">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-338">**Type**</span></span>         | <span data-ttu-id="9398d-339">是</span><span class="sxs-lookup"><span data-stu-id="9398d-339">Yes</span></span>         | <span data-ttu-id="9398d-340">其中一個關聯 End 的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="9398d-341">**角色**</span><span class="sxs-lookup"><span data-stu-id="9398d-341">**Role**</span></span>         | <span data-ttu-id="9398d-342">否</span><span class="sxs-lookup"><span data-stu-id="9398d-342">No</span></span>          | <span data-ttu-id="9398d-343">關聯 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-343">A name for the association end.</span></span> <span data-ttu-id="9398d-344">如果未提供任何名稱，則會使用關聯 End 上之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="9398d-345">**多重性**</span><span class="sxs-lookup"><span data-stu-id="9398d-345">**Multiplicity**</span></span> | <span data-ttu-id="9398d-346">是</span><span class="sxs-lookup"><span data-stu-id="9398d-346">Yes</span></span>         | <span data-ttu-id="9398d-347">**1**、 **0、1**或 **\*** 取決於可以在關聯結尾的實體類型實例數目而定。</span><span class="sxs-lookup"><span data-stu-id="9398d-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="9398d-348">**1**表示關聯端只有一個實體類型實例存在。</span><span class="sxs-lookup"><span data-stu-id="9398d-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="9398d-349">**0 ..1**表示關聯 end 有零或一個實體類型實例。</span><span class="sxs-lookup"><span data-stu-id="9398d-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="9398d-350">**\*** 表示關聯 end 有零個、一個或多個實體類型實例存在。</span><span class="sxs-lookup"><span data-stu-id="9398d-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-351">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="9398d-352">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-353">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-354">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-354">Example</span></span>

<span data-ttu-id="9398d-355">下列範例會顯示定義**CustomerOrders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="9398d-356">關聯的每個**端點**的**多重性**值表示許多**訂單**可以與**客戶**相關聯，但只有一個**客戶**可以與**訂單**產生關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="9398d-357">此外， **OnDelete**元素會指出與特定**客戶**相關且已載入 ObjectCoNtext 的所有**訂單**，會在**客戶**刪除時刪除。</span><span class="sxs-lookup"><span data-stu-id="9398d-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="9398d-358">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="9398d-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="9398d-359">**End**元素會指定關聯集的一端。</span><span class="sxs-lookup"><span data-stu-id="9398d-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="9398d-360">**AssociationSet**元素必須包含兩個**End**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="9398d-361">**End**元素中所包含的資訊是用來將關聯集對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="9398d-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="9398d-362">**End**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-363">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-364">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-365">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="9398d-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="9398d-366">只有在 CSDL v2 和更新版本中才允許 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-367">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-367">Applicable Attributes</span></span>

<span data-ttu-id="9398d-368">下表描述當元素是**AssociationSet**專案的子系時，可以套用至**結尾**專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="9398d-369">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-369">Attribute Name</span></span> | <span data-ttu-id="9398d-370">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-370">Is Required</span></span> | <span data-ttu-id="9398d-371">值</span><span class="sxs-lookup"><span data-stu-id="9398d-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="9398d-372">**EntitySet**</span></span>  | <span data-ttu-id="9398d-373">是</span><span class="sxs-lookup"><span data-stu-id="9398d-373">Yes</span></span>         | <span data-ttu-id="9398d-374">**EntitySet**元素的名稱，定義父**AssociationSet**元素的一端。</span><span class="sxs-lookup"><span data-stu-id="9398d-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="9398d-375">**EntitySet**元素必須在與父系**AssociationSet**元素相同的實體容器中定義。</span><span class="sxs-lookup"><span data-stu-id="9398d-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="9398d-376">**角色**</span><span class="sxs-lookup"><span data-stu-id="9398d-376">**Role**</span></span>       | <span data-ttu-id="9398d-377">否</span><span class="sxs-lookup"><span data-stu-id="9398d-377">No</span></span>          | <span data-ttu-id="9398d-378">關聯集 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-378">The name of the association set end.</span></span> <span data-ttu-id="9398d-379">如果未使用**Role**屬性，則關聯集結尾的名稱將會是實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9398d-380">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="9398d-381">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-382">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-383">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-383">Example</span></span>

<span data-ttu-id="9398d-384">下列範例顯示具有兩個**AssociationSet**元素的**EntityContainer**專案，每個專案都有兩個**End**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="9398d-385">EntityContainer 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="9398d-386">概念結構定義語言（CSDL）中的**EntityContainer**元素是實體集、關聯集和函式匯入的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="9398d-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="9398d-387">概念模型實體容器透過 EntityContainerMapping 項目對應於儲存體模型實體容器。</span><span class="sxs-lookup"><span data-stu-id="9398d-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="9398d-388">儲存體模型實體容器描述資料的結構：實體集描述資料表、關聯集描述外部索引建條件約束，而函式匯入則描述資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="9398d-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="9398d-389">**EntityContainer**元素可以有零個或一個檔元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="9398d-390">如果**檔**元素存在，則必須在所有**EntitySet**、 **AssociationSet**和**FunctionImport**元素的前面。</span><span class="sxs-lookup"><span data-stu-id="9398d-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="9398d-391">**EntityContainer**元素可以有零或多個下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="9398d-392">EntitySet</span></span>
-   <span data-ttu-id="9398d-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="9398d-393">AssociationSet</span></span>
-   <span data-ttu-id="9398d-394">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="9398d-394">FunctionImport</span></span>
-   <span data-ttu-id="9398d-395">Annotation 元素</span><span class="sxs-lookup"><span data-stu-id="9398d-395">Annotation elements</span></span>

<span data-ttu-id="9398d-396">您可以擴充**EntityContainer**元素，以包含相同命名空間內另一個**entitycontainer**的內容。</span><span class="sxs-lookup"><span data-stu-id="9398d-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="9398d-397">若要包含另一個**EntityContainer**的內容，請在參考的**entitycontainer**專案中，將 [**擴充**屬性] 的值設定為您要包含之**EntityContainer**元素的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="9398d-398">包含之**entitycontainer**元素的所有子專案都會被視為參考**entitycontainer**元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-399">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-399">Applicable Attributes</span></span>

<span data-ttu-id="9398d-400">下表描述可套用至**Using**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="9398d-401">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-401">Attribute Name</span></span> | <span data-ttu-id="9398d-402">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-402">Is Required</span></span> | <span data-ttu-id="9398d-403">值</span><span class="sxs-lookup"><span data-stu-id="9398d-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="9398d-404">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-404">**Name**</span></span>       | <span data-ttu-id="9398d-405">是</span><span class="sxs-lookup"><span data-stu-id="9398d-405">Yes</span></span>         | <span data-ttu-id="9398d-406">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="9398d-407">**擴展**</span><span class="sxs-lookup"><span data-stu-id="9398d-407">**Extends**</span></span>    | <span data-ttu-id="9398d-408">否</span><span class="sxs-lookup"><span data-stu-id="9398d-408">No</span></span>          | <span data-ttu-id="9398d-409">相同命名空間中另一個實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-410">可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**EntityContainer**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="9398d-411">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-412">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-413">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-413">Example</span></span>

<span data-ttu-id="9398d-414">下列範例顯示的**EntityContainer**元素會定義三個實體集和兩個關聯集。</span><span class="sxs-lookup"><span data-stu-id="9398d-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="9398d-415">EntitySet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="9398d-416">概念結構定義語言中的**EntitySet**元素是實體類型實例的邏輯容器，以及衍生自該實體類型之任何類型的實例。</span><span class="sxs-lookup"><span data-stu-id="9398d-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="9398d-417">實體類型和實體集之間的關聯性，類似於關聯式資料庫中資料列與資料表的關係。</span><span class="sxs-lookup"><span data-stu-id="9398d-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="9398d-418">實體類型和資料列一樣可以定義相關的資料集，而實體集則和資料表一樣可以包含該定義的執行個體。</span><span class="sxs-lookup"><span data-stu-id="9398d-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="9398d-419">實體集提供群組實體類型執行個體的建構，以便將它們對應至資料來源中的相關資料結構。</span><span class="sxs-lookup"><span data-stu-id="9398d-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="9398d-420">您可以為特定的實體類型定義多個實體集。</span><span class="sxs-lookup"><span data-stu-id="9398d-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-421">EF 設計工具不支援包含每個類型之多個實體集的概念模型。</span><span class="sxs-lookup"><span data-stu-id="9398d-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="9398d-422">**EntitySet**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-423">檔元素（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-424">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-425">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-425">Applicable Attributes</span></span>

<span data-ttu-id="9398d-426">下表描述可套用至**EntitySet**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="9398d-427">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-427">Attribute Name</span></span> | <span data-ttu-id="9398d-428">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-428">Is Required</span></span> | <span data-ttu-id="9398d-429">值</span><span class="sxs-lookup"><span data-stu-id="9398d-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-430">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-430">**Name**</span></span>       | <span data-ttu-id="9398d-431">是</span><span class="sxs-lookup"><span data-stu-id="9398d-431">Yes</span></span>         | <span data-ttu-id="9398d-432">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="9398d-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="9398d-433">**EntityType**</span></span> | <span data-ttu-id="9398d-434">是</span><span class="sxs-lookup"><span data-stu-id="9398d-434">Yes</span></span>         | <span data-ttu-id="9398d-435">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-436">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntitySet**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="9398d-437">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-438">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-439">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-439">Example</span></span>

<span data-ttu-id="9398d-440">下列範例顯示具有三個**EntitySet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

<span data-ttu-id="9398d-441">您可以定義每個類型的多重實體集 (MEST)。</span><span class="sxs-lookup"><span data-stu-id="9398d-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="9398d-442">下列範例會定義具有**Book**實體類型之兩個實體集的實體容器：</span><span class="sxs-lookup"><span data-stu-id="9398d-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="9398d-443">EntityType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="9398d-444">**EntityType**元素代表概念模型中最上層概念的結構，例如客戶或訂單。</span><span class="sxs-lookup"><span data-stu-id="9398d-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="9398d-445">實體類型是應用程式中實體類型之執行個體的範本。</span><span class="sxs-lookup"><span data-stu-id="9398d-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="9398d-446">每個範本包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="9398d-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="9398d-447">唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-447">A unique name.</span></span> <span data-ttu-id="9398d-448">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="9398d-448">(Required.)</span></span>
-   <span data-ttu-id="9398d-449">實體索引鍵是由一個或多個屬性定義。</span><span class="sxs-lookup"><span data-stu-id="9398d-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="9398d-450">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="9398d-450">(Required.)</span></span>
-   <span data-ttu-id="9398d-451">包含資料的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-451">Properties for containing data.</span></span> <span data-ttu-id="9398d-452">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="9398d-452">(Optional.)</span></span>
-   <span data-ttu-id="9398d-453">導覽屬性允許從關聯的一端巡覽至另一端。</span><span class="sxs-lookup"><span data-stu-id="9398d-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="9398d-454">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="9398d-454">(Optional.)</span></span>

<span data-ttu-id="9398d-455">在應用程式中，實體類型的執行個體代表特定的物件 (例如特定的客戶或訂單)。</span><span class="sxs-lookup"><span data-stu-id="9398d-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="9398d-456">實體類型的每一個執行個體都必須在實體集中有唯一的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="9398d-457">如果兩個實體類型執行個體屬於相同類型，而且索引鍵的值也相同，則會將這兩個執行個體視為相等。</span><span class="sxs-lookup"><span data-stu-id="9398d-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="9398d-458">**EntityType**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-459">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-460">Key(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="9398d-461">Property (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="9398d-462">NavigationProperty (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="9398d-463">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-464">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-464">Applicable Attributes</span></span>

<span data-ttu-id="9398d-465">下表描述可套用至**EntityType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="9398d-466">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="9398d-467">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-467">Is Required</span></span> | <span data-ttu-id="9398d-468">值</span><span class="sxs-lookup"><span data-stu-id="9398d-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-469">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="9398d-470">是</span><span class="sxs-lookup"><span data-stu-id="9398d-470">Yes</span></span>         | <span data-ttu-id="9398d-471">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="9398d-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="9398d-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="9398d-473">否</span><span class="sxs-lookup"><span data-stu-id="9398d-473">No</span></span>          | <span data-ttu-id="9398d-474">另一個實體類型的名稱，而此實體類型是要定義之實體類型的基底類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="9398d-475">**概要**</span><span class="sxs-lookup"><span data-stu-id="9398d-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="9398d-476">否</span><span class="sxs-lookup"><span data-stu-id="9398d-476">No</span></span>          | <span data-ttu-id="9398d-477">**True**或**False**，視實體類型是否為抽象類別型而定。</span><span class="sxs-lookup"><span data-stu-id="9398d-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="9398d-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="9398d-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="9398d-479">否</span><span class="sxs-lookup"><span data-stu-id="9398d-479">No</span></span>          | <span data-ttu-id="9398d-480">**True**或**False** ，視實體類型是否為開放式實體類型而定。</span><span class="sxs-lookup"><span data-stu-id="9398d-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="9398d-481">> **OpenType**屬性僅適用于概念模型中定義的實體類型（與 ADO.NET 資料服務搭配使用）。</span><span class="sxs-lookup"><span data-stu-id="9398d-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="9398d-482">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="9398d-483">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-484">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-485">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-485">Example</span></span>

<span data-ttu-id="9398d-486">下列範例顯示具有三個**屬性**專案和兩個**NavigationProperty**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="9398d-487">EnumType 元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="9398d-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="9398d-488">**EnumType**元素代表列舉型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="9398d-489">**EnumType**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-490">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-491">成員（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="9398d-492">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-493">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-493">Applicable Attributes</span></span>

<span data-ttu-id="9398d-494">下表描述可套用至**EnumType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="9398d-495">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-495">Attribute Name</span></span>     | <span data-ttu-id="9398d-496">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-496">Is Required</span></span> | <span data-ttu-id="9398d-497">值</span><span class="sxs-lookup"><span data-stu-id="9398d-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-498">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-498">**Name**</span></span>           | <span data-ttu-id="9398d-499">是</span><span class="sxs-lookup"><span data-stu-id="9398d-499">Yes</span></span>         | <span data-ttu-id="9398d-500">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="9398d-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="9398d-501">**IsFlags**</span></span>        | <span data-ttu-id="9398d-502">否</span><span class="sxs-lookup"><span data-stu-id="9398d-502">No</span></span>          | <span data-ttu-id="9398d-503">**True**或**False**，取決於列舉型別是否可以當做一組旗標使用。</span><span class="sxs-lookup"><span data-stu-id="9398d-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="9398d-504">預設值為**False。**</span><span class="sxs-lookup"><span data-stu-id="9398d-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="9398d-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="9398d-505">**UnderlyingType**</span></span> | <span data-ttu-id="9398d-506">否</span><span class="sxs-lookup"><span data-stu-id="9398d-506">No</span></span>          | <span data-ttu-id="9398d-507">**Edm： Byte**、 **edm**、 **edm**、 **edm**或**edm。 SByte**定義類型的值範圍。</span><span class="sxs-lookup"><span data-stu-id="9398d-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="9398d-508">  列舉元素的預設基礎類型是**Edm. Int32.** 。</span><span class="sxs-lookup"><span data-stu-id="9398d-508">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-509">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EnumType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="9398d-510">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-511">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-512">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-512">Example</span></span>

<span data-ttu-id="9398d-513">下列範例顯示具有三個**成員**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="9398d-514">Function 屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-514">Function Element (CSDL)</span></span>

<span data-ttu-id="9398d-515">概念結構定義語言（CSDL）中的**Function**元素是用來定義或宣告概念模型中的函式。</span><span class="sxs-lookup"><span data-stu-id="9398d-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="9398d-516">函式是使用 DefiningExpression 項目 來定義的。</span><span class="sxs-lookup"><span data-stu-id="9398d-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="9398d-517">**Function**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-518">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-519">Parameter (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="9398d-520">DefiningExpression (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="9398d-521">ReturnType （函數）（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="9398d-522">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="9398d-523">函式的傳回型別必須使用**returntype** （function）元素或**returntype**屬性（請參閱下文）來指定，但不能兩者同時指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="9398d-524">可能的傳回型別包括任何 EdmSimpleType、實體類型、複雜類型、資料列型別或 ref 型別 (或這些類型其中之一的集合)。</span><span class="sxs-lookup"><span data-stu-id="9398d-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-525">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-525">Applicable Attributes</span></span>

<span data-ttu-id="9398d-526">下表描述可套用至**Function**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="9398d-527">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-527">Attribute Name</span></span> | <span data-ttu-id="9398d-528">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-528">Is Required</span></span> | <span data-ttu-id="9398d-529">值</span><span class="sxs-lookup"><span data-stu-id="9398d-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="9398d-530">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-530">**Name**</span></span>       | <span data-ttu-id="9398d-531">是</span><span class="sxs-lookup"><span data-stu-id="9398d-531">Yes</span></span>         | <span data-ttu-id="9398d-532">函數的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-532">The name of the function.</span></span>          |
| <span data-ttu-id="9398d-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9398d-533">**ReturnType**</span></span> | <span data-ttu-id="9398d-534">否</span><span class="sxs-lookup"><span data-stu-id="9398d-534">No</span></span>          | <span data-ttu-id="9398d-535">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-536">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="9398d-537">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-538">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-539">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-539">Example</span></span>

<span data-ttu-id="9398d-540">下列範例會使用**function**元素來定義函式，該函數會傳回講師雇用後的年數。</span><span class="sxs-lookup"><span data-stu-id="9398d-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="9398d-541">FunctionImport 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="9398d-542">概念結構定義語言（CSDL）中的**FunctionImport**元素代表在資料來源中定義，但可透過概念模型提供給物件的函數。</span><span class="sxs-lookup"><span data-stu-id="9398d-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="9398d-543">例如，儲存體模型中的 Function 項目 可以用來代表資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="9398d-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="9398d-544">概念模型中的**FunctionImport**元素代表 Entity Framework 應用程式中的對應函式，而且會使用 FunctionImportMapping 元素來對應至儲存模型函式。</span><span class="sxs-lookup"><span data-stu-id="9398d-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="9398d-545">在應用程式中呼叫函式時，對應的預存程序會在資料庫中執行。</span><span class="sxs-lookup"><span data-stu-id="9398d-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="9398d-546">**FunctionImport**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-547">檔（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-548">Parameter (可允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="9398d-549">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="9398d-550">ReturnType （FunctionImport）（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="9398d-551">針對函式可接受的每個參數，應定義一個**參數**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="9398d-552">函式的傳回型別必須使用**returntype** （FunctionImport）專案或**returntype**屬性（請參閱下文）來指定，但不能同時指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="9398d-553">傳回型別值必須是 EdmSimpleType、EntityType 或 ComplexType 的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-554">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-554">Applicable Attributes</span></span>

<span data-ttu-id="9398d-555">下表描述可套用至**FunctionImport**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="9398d-556">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-556">Attribute Name</span></span>   | <span data-ttu-id="9398d-557">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-557">Is Required</span></span> | <span data-ttu-id="9398d-558">值</span><span class="sxs-lookup"><span data-stu-id="9398d-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-559">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-559">**Name**</span></span>         | <span data-ttu-id="9398d-560">是</span><span class="sxs-lookup"><span data-stu-id="9398d-560">Yes</span></span>         | <span data-ttu-id="9398d-561">匯入函式的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="9398d-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9398d-562">**ReturnType**</span></span>   | <span data-ttu-id="9398d-563">否</span><span class="sxs-lookup"><span data-stu-id="9398d-563">No</span></span>          | <span data-ttu-id="9398d-564">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-564">The type that the function returns.</span></span> <span data-ttu-id="9398d-565">如果函數沒有傳回值，請勿使用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="9398d-566">否則，此值必須是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="9398d-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="9398d-567">**EntitySet**</span></span>    | <span data-ttu-id="9398d-568">否</span><span class="sxs-lookup"><span data-stu-id="9398d-568">No</span></span>          | <span data-ttu-id="9398d-569">如果函數傳回實體類型的集合，則**EntitySet**的值必須是集合所屬的實體集。</span><span class="sxs-lookup"><span data-stu-id="9398d-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="9398d-570">否則，不得使用 **EntitySet** 屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="9398d-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="9398d-571">**IsComposable**</span></span> | <span data-ttu-id="9398d-572">否</span><span class="sxs-lookup"><span data-stu-id="9398d-572">No</span></span>          | <span data-ttu-id="9398d-573">如果值設定為 true，則函式可組合（資料表值函式），而且可以在 LINQ 查詢中使用。</span><span class="sxs-lookup"><span data-stu-id="9398d-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="9398d-574">  預設值為**false**。</span><span class="sxs-lookup"><span data-stu-id="9398d-574">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="9398d-575">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="9398d-576">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-577">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-578">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-578">Example</span></span>

<span data-ttu-id="9398d-579">下列範例顯示的**FunctionImport**元素會接受一個參數，並傳回實體類型的集合：</span><span class="sxs-lookup"><span data-stu-id="9398d-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="9398d-580">Key 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-580">Key Element (CSDL)</span></span>

<span data-ttu-id="9398d-581">**Key**元素是 EntityType 元素的子專案，並定義*實體索引鍵*（屬性或實體類型的一組屬性，以決定識別）。</span><span class="sxs-lookup"><span data-stu-id="9398d-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="9398d-582">構成實體索引鍵的屬性是在設計階段選取的。</span><span class="sxs-lookup"><span data-stu-id="9398d-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="9398d-583">實體索引鍵屬性的值必須在執行階段的實體集中，單獨識別實體類型執行個體。</span><span class="sxs-lookup"><span data-stu-id="9398d-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="9398d-584">您應選取構成實體索引鍵的屬性，以保證執行個體在實體集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="9398d-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="9398d-585">**Key**元素會藉由參考實體類型的一或多個屬性來定義實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="9398d-586">**Key**元素可以具有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="9398d-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="9398d-587">PropertyRef （一或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9398d-588">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-589">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-589">Applicable Attributes</span></span>

<span data-ttu-id="9398d-590">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至索引**鍵**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="9398d-591">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-592">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-593">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-593">Example</span></span>

<span data-ttu-id="9398d-594">下列範例會定義名為**Book**的實體類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="9398d-595">實體索引鍵是藉由參考實體類型的**ISBN**屬性來定義。</span><span class="sxs-lookup"><span data-stu-id="9398d-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="9398d-596">**ISBN**屬性是實體索引鍵的理想選擇，因為國際標準書籍號碼（ISBN）可唯一識別書籍。</span><span class="sxs-lookup"><span data-stu-id="9398d-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="9398d-597">下列範例顯示實體類型（**作者**），其實體索引鍵包含兩個屬性：**名稱**和**位址**。</span><span class="sxs-lookup"><span data-stu-id="9398d-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

<span data-ttu-id="9398d-598">使用實體索引鍵的**名稱**和**位址**是合理的選擇，因為相同名稱的兩位作者不太可能會存留在相同的位址。</span><span class="sxs-lookup"><span data-stu-id="9398d-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="9398d-599">不過，針對實體索引鍵所做的這個選擇不能絕對保證實體集中的唯一實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="9398d-600">在此情況下，建議您加入可用於唯一識別作者的屬性 **（例如作者）。**</span><span class="sxs-lookup"><span data-stu-id="9398d-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="9398d-601">Member 元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="9398d-601">Member Element (CSDL)</span></span>

<span data-ttu-id="9398d-602">**Member**元素是 EnumType 專案的子專案，並定義列舉類型的成員。</span><span class="sxs-lookup"><span data-stu-id="9398d-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-603">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-603">Applicable Attributes</span></span>

<span data-ttu-id="9398d-604">下表描述可套用至**FunctionImport**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="9398d-605">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-605">Attribute Name</span></span> | <span data-ttu-id="9398d-606">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-606">Is Required</span></span> | <span data-ttu-id="9398d-607">值</span><span class="sxs-lookup"><span data-stu-id="9398d-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-608">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-608">**Name**</span></span>       | <span data-ttu-id="9398d-609">是</span><span class="sxs-lookup"><span data-stu-id="9398d-609">Yes</span></span>         | <span data-ttu-id="9398d-610">成員的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="9398d-611">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="9398d-611">**Value**</span></span>      | <span data-ttu-id="9398d-612">否</span><span class="sxs-lookup"><span data-stu-id="9398d-612">No</span></span>          | <span data-ttu-id="9398d-613">成員的值。</span><span class="sxs-lookup"><span data-stu-id="9398d-613">The value of the member.</span></span> <span data-ttu-id="9398d-614">根據預設，第一個成員的值為0，而每個後續列舉值的值會遞增1。</span><span class="sxs-lookup"><span data-stu-id="9398d-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="9398d-615">有多個具有相同值的成員可能存在。</span><span class="sxs-lookup"><span data-stu-id="9398d-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-616">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="9398d-617">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-618">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-619">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-619">Example</span></span>

<span data-ttu-id="9398d-620">下列範例顯示具有三個**成員**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="9398d-621">NavigationProperty 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="9398d-622">**NavigationProperty**元素會定義導覽屬性，以提供關聯另一端的參考。</span><span class="sxs-lookup"><span data-stu-id="9398d-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="9398d-623">與使用 Property 項目 定義的屬性不同，導覽屬性不會定義資料的圖案和特性。</span><span class="sxs-lookup"><span data-stu-id="9398d-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="9398d-624">他們提供巡覽兩個實體類型間之關聯的方式。</span><span class="sxs-lookup"><span data-stu-id="9398d-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="9398d-625">請注意，在關聯各端點的實體類型上，導覽屬性是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="9398d-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="9398d-626">如果您在關聯其中一個端點的實體類型上定義導覽屬性，就不必在關聯另一個端點的實體類型上定義導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="9398d-627">導覽屬性傳回的資料類型是由其遠端關聯端點的多重性所判斷。</span><span class="sxs-lookup"><span data-stu-id="9398d-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="9398d-628">例如，假設有一個導覽屬性**OrdersNavProp**存在於**customer**實體型別上，並且在**customer**與**Order**之間導覽一對多關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="9398d-629">因為導覽屬性的遠端關聯 end 具有多重性 many （\*），所以其資料類型為集合（**順序**）。</span><span class="sxs-lookup"><span data-stu-id="9398d-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="9398d-630">同樣地，如果 [**順序**] 實體類型上有導覽屬性**CustomerNavProp**，其資料類型會是 [**客戶**]，因為遠端端點的多重性是一（1）。</span><span class="sxs-lookup"><span data-stu-id="9398d-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="9398d-631">**NavigationProperty**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-632">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-633">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-634">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-634">Applicable Attributes</span></span>

<span data-ttu-id="9398d-635">下表描述可套用至**NavigationProperty**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="9398d-636">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-636">Attribute Name</span></span>   | <span data-ttu-id="9398d-637">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-637">Is Required</span></span> | <span data-ttu-id="9398d-638">值</span><span class="sxs-lookup"><span data-stu-id="9398d-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-639">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-639">**Name**</span></span>         | <span data-ttu-id="9398d-640">是</span><span class="sxs-lookup"><span data-stu-id="9398d-640">Yes</span></span>         | <span data-ttu-id="9398d-641">導覽屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="9398d-642">**相對**</span><span class="sxs-lookup"><span data-stu-id="9398d-642">**Relationship**</span></span> | <span data-ttu-id="9398d-643">是</span><span class="sxs-lookup"><span data-stu-id="9398d-643">Yes</span></span>         | <span data-ttu-id="9398d-644">關聯的名稱在模型的範圍之內。</span><span class="sxs-lookup"><span data-stu-id="9398d-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="9398d-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="9398d-645">**ToRole**</span></span>       | <span data-ttu-id="9398d-646">是</span><span class="sxs-lookup"><span data-stu-id="9398d-646">Yes</span></span>         | <span data-ttu-id="9398d-647">位於導覽端點的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="9398d-648">**ToRole**屬性的值必須與其中一個關聯 end （定義于 AssociationEnd 元素）中所定義的其中一個**角色**屬性值相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="9398d-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="9398d-649">**FromRole**</span></span>     | <span data-ttu-id="9398d-650">是</span><span class="sxs-lookup"><span data-stu-id="9398d-650">Yes</span></span>         | <span data-ttu-id="9398d-651">開始導覽的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="9398d-652">**FromRole**屬性的值必須與其中一個關聯 end （定義于 AssociationEnd 元素）中所定義的其中一個**角色**屬性值相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-653">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**NavigationProperty**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="9398d-654">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-655">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-656">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-656">Example</span></span>

<span data-ttu-id="9398d-657">下列範例會定義具有兩個導覽屬性（**PublishedBy**和**WrittenBy**）的實體類型（**Book**）：</span><span class="sxs-lookup"><span data-stu-id="9398d-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="9398d-658">OnDelete 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="9398d-659">概念結構定義語言（CSDL）中的**OnDelete**元素會定義與關聯連接的行為。</span><span class="sxs-lookup"><span data-stu-id="9398d-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="9398d-660">如果在關聯的一端上將**Action**屬性設為**Cascade** ，當第一端的實體類型刪除時，會刪除關聯另一端上的相關實體類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="9398d-661">如果兩個實體類型之間的關聯是主要索引鍵對主鍵的關係，則不論**OnDelete**規格為何，當關聯的另一端上的主體物件遭到刪除時，會刪除已載入的相依物件。</span><span class="sxs-lookup"><span data-stu-id="9398d-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="9398d-662">**OnDelete**元素只會影響應用程式的執行時間行為。它不會影響資料來源中的行為。</span><span class="sxs-lookup"><span data-stu-id="9398d-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="9398d-663">資料來源中定義的行為應與應用程式中定義的行為相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="9398d-664">**OnDelete**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-665">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-666">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-667">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-667">Applicable Attributes</span></span>

<span data-ttu-id="9398d-668">下表描述可套用至**OnDelete**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="9398d-669">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-669">Attribute Name</span></span> | <span data-ttu-id="9398d-670">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-670">Is Required</span></span> | <span data-ttu-id="9398d-671">值</span><span class="sxs-lookup"><span data-stu-id="9398d-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-672">**動作**</span><span class="sxs-lookup"><span data-stu-id="9398d-672">**Action**</span></span>     | <span data-ttu-id="9398d-673">是</span><span class="sxs-lookup"><span data-stu-id="9398d-673">Yes</span></span>         | <span data-ttu-id="9398d-674">**Cascade**或**None**。</span><span class="sxs-lookup"><span data-stu-id="9398d-674">**Cascade** or **None**.</span></span> <span data-ttu-id="9398d-675">如果**Cascade**，則刪除主體實體類型時，將會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="9398d-676">如果**沒有**，則在刪除主體實體類型時，不會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-677">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="9398d-678">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-679">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-680">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-680">Example</span></span>

<span data-ttu-id="9398d-681">下列範例會顯示定義**CustomerOrders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="9398d-682">**OnDelete**元素表示當**客戶**刪除時，與特定**客戶**相關且已載入 ObjectCoNtext 的所有**訂單**將會刪除。</span><span class="sxs-lookup"><span data-stu-id="9398d-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="9398d-683">Parameter 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="9398d-684">概念結構定義語言（CSDL）中的**Parameter**元素可以是 FunctionImport 元素或 Function 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="9398d-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="9398d-685">FunctionImport 項目的應用</span><span class="sxs-lookup"><span data-stu-id="9398d-685">FunctionImport Element Application</span></span>

<span data-ttu-id="9398d-686">**參數**專案（做為**FunctionImport**元素的子系）是用來定義在 CSDL 中宣告之函式匯入的輸入和輸出參數。</span><span class="sxs-lookup"><span data-stu-id="9398d-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="9398d-687">**Parameter**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-688">檔（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-689">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-690">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-690">Applicable Attributes</span></span>

<span data-ttu-id="9398d-691">下表描述可套用至**參數**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="9398d-692">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-692">Attribute Name</span></span> | <span data-ttu-id="9398d-693">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-693">Is Required</span></span> | <span data-ttu-id="9398d-694">值</span><span class="sxs-lookup"><span data-stu-id="9398d-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-695">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-695">**Name**</span></span>       | <span data-ttu-id="9398d-696">是</span><span class="sxs-lookup"><span data-stu-id="9398d-696">Yes</span></span>         | <span data-ttu-id="9398d-697">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9398d-698">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-698">**Type**</span></span>       | <span data-ttu-id="9398d-699">是</span><span class="sxs-lookup"><span data-stu-id="9398d-699">Yes</span></span>         | <span data-ttu-id="9398d-700">參數類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-700">The parameter type.</span></span> <span data-ttu-id="9398d-701">此值必須是 **EDMSimpleType** 或是模型範圍內的複雜類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="9398d-702">**模式**</span><span class="sxs-lookup"><span data-stu-id="9398d-702">**Mode**</span></span>       | <span data-ttu-id="9398d-703">否</span><span class="sxs-lookup"><span data-stu-id="9398d-703">No</span></span>          | <span data-ttu-id="9398d-704">**In**、 **Out**或**InOut** ，取決於參數是輸入、輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="9398d-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="9398d-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-705">**MaxLength**</span></span>  | <span data-ttu-id="9398d-706">否</span><span class="sxs-lookup"><span data-stu-id="9398d-706">No</span></span>          | <span data-ttu-id="9398d-707">允許的參數長度上限。</span><span class="sxs-lookup"><span data-stu-id="9398d-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="9398d-708">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-708">**Precision**</span></span>  | <span data-ttu-id="9398d-709">否</span><span class="sxs-lookup"><span data-stu-id="9398d-709">No</span></span>          | <span data-ttu-id="9398d-710">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="9398d-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-711">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-711">**Scale**</span></span>      | <span data-ttu-id="9398d-712">否</span><span class="sxs-lookup"><span data-stu-id="9398d-712">No</span></span>          | <span data-ttu-id="9398d-713">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="9398d-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-714">**SRID**</span></span>       | <span data-ttu-id="9398d-715">否</span><span class="sxs-lookup"><span data-stu-id="9398d-715">No</span></span>          | <span data-ttu-id="9398d-716">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-717">僅對空間類型的參數有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="9398d-718">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-718">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-719">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Parameter**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="9398d-720">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-721">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-722">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-722">Example</span></span>

<span data-ttu-id="9398d-723">下列範例顯示具有一個**參數**子項目的**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="9398d-724">函式接受一個輸入參數，然後傳回實體類型的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="9398d-725">Function 項目的應用</span><span class="sxs-lookup"><span data-stu-id="9398d-725">Function Element Application</span></span>

<span data-ttu-id="9398d-726">**Parameter**專案（當做**Function**元素的子系）會定義在概念模型中定義或宣告之函式的參數。</span><span class="sxs-lookup"><span data-stu-id="9398d-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="9398d-727">**Parameter**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-728">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="9398d-729">CollectionType （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="9398d-730">ReferenceType （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="9398d-731">RowType （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-732">只有其中一個**CollectionType**、 **ReferenceType**或**RowType**元素可以是**Property**專案的子項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="9398d-733">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-734">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="9398d-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="9398d-735">只有在 CSDL v2 和更新版本中才允許 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-736">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-736">Applicable Attributes</span></span>

<span data-ttu-id="9398d-737">下表描述可套用至**參數**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="9398d-738">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-738">Attribute Name</span></span>   | <span data-ttu-id="9398d-739">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-739">Is Required</span></span> | <span data-ttu-id="9398d-740">值</span><span class="sxs-lookup"><span data-stu-id="9398d-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-741">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-741">**Name**</span></span>         | <span data-ttu-id="9398d-742">是</span><span class="sxs-lookup"><span data-stu-id="9398d-742">Yes</span></span>         | <span data-ttu-id="9398d-743">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="9398d-744">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-744">**Type**</span></span>         | <span data-ttu-id="9398d-745">否</span><span class="sxs-lookup"><span data-stu-id="9398d-745">No</span></span>          | <span data-ttu-id="9398d-746">參數類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-746">The parameter type.</span></span> <span data-ttu-id="9398d-747">參數可以是下列任何一種型別 (或這些型別的集合)：</span><span class="sxs-lookup"><span data-stu-id="9398d-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="9398d-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="9398d-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="9398d-749">Entity Type - 實體類型</span><span class="sxs-lookup"><span data-stu-id="9398d-749">entity type</span></span> <br/> <span data-ttu-id="9398d-750">複雜類型</span><span class="sxs-lookup"><span data-stu-id="9398d-750">complex type</span></span> <br/> <span data-ttu-id="9398d-751">資料列型別</span><span class="sxs-lookup"><span data-stu-id="9398d-751">row type</span></span> <br/> <span data-ttu-id="9398d-752">參考類型</span><span class="sxs-lookup"><span data-stu-id="9398d-752">reference type</span></span>                             |
| <span data-ttu-id="9398d-753">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-753">**Nullable**</span></span>     | <span data-ttu-id="9398d-754">否</span><span class="sxs-lookup"><span data-stu-id="9398d-754">No</span></span>          | <span data-ttu-id="9398d-755">**True** （預設值）或**False** ，取決於屬性是否可以有**null**值。</span><span class="sxs-lookup"><span data-stu-id="9398d-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="9398d-756">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9398d-756">**DefaultValue**</span></span> | <span data-ttu-id="9398d-757">否</span><span class="sxs-lookup"><span data-stu-id="9398d-757">No</span></span>          | <span data-ttu-id="9398d-758">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9398d-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-759">**MaxLength**</span></span>    | <span data-ttu-id="9398d-760">否</span><span class="sxs-lookup"><span data-stu-id="9398d-760">No</span></span>          | <span data-ttu-id="9398d-761">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="9398d-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9398d-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-762">**FixedLength**</span></span>  | <span data-ttu-id="9398d-763">否</span><span class="sxs-lookup"><span data-stu-id="9398d-763">No</span></span>          | <span data-ttu-id="9398d-764">**True**或**False** ，取決於屬性值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9398d-765">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-765">**Precision**</span></span>    | <span data-ttu-id="9398d-766">否</span><span class="sxs-lookup"><span data-stu-id="9398d-766">No</span></span>          | <span data-ttu-id="9398d-767">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9398d-768">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-768">**Scale**</span></span>        | <span data-ttu-id="9398d-769">否</span><span class="sxs-lookup"><span data-stu-id="9398d-769">No</span></span>          | <span data-ttu-id="9398d-770">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9398d-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-771">**SRID**</span></span>         | <span data-ttu-id="9398d-772">否</span><span class="sxs-lookup"><span data-stu-id="9398d-772">No</span></span>          | <span data-ttu-id="9398d-773">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-774">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9398d-775">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-775">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9398d-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-776">**Unicode**</span></span>      | <span data-ttu-id="9398d-777">否</span><span class="sxs-lookup"><span data-stu-id="9398d-777">No</span></span>          | <span data-ttu-id="9398d-778">**True**或**False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9398d-779">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-779">**Collation**</span></span>    | <span data-ttu-id="9398d-780">否</span><span class="sxs-lookup"><span data-stu-id="9398d-780">No</span></span>          | <span data-ttu-id="9398d-781">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9398d-782">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Parameter**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="9398d-783">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-784">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-785">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-785">Example</span></span>

<span data-ttu-id="9398d-786">下列範例**顯示的函**式專案會使用一個**參數**子專案來定義函式參數。</span><span class="sxs-lookup"><span data-stu-id="9398d-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="9398d-787">Principal 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="9398d-788">概念結構定義語言（CSDL）中的**Principal**元素是 ReferentialConstraint 元素的子專案，可定義參考條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="9398d-789">**ReferentialConstraint**元素會定義與關係資料庫中的參考完整性條件約束類似的功能。</span><span class="sxs-lookup"><span data-stu-id="9398d-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9398d-790">資料庫資料表中的資料行可以參考另一個資料表的主要索引鍵，同樣地，實體類型的屬性也可以參考另一個實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9398d-791">參考的實體類型稱為條件約束的*主要端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9398d-792">參考主要端點的實體類型稱為條件約束的*相依端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="9398d-793">**PropertyRef**元素是用來指定相依端所參考的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="9398d-794">**Principal**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-795">PropertyRef （一或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="9398d-796">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-797">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-797">Applicable Attributes</span></span>

<span data-ttu-id="9398d-798">下表描述可套用至**主體**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="9398d-799">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-799">Attribute Name</span></span> | <span data-ttu-id="9398d-800">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-800">Is Required</span></span> | <span data-ttu-id="9398d-801">值</span><span class="sxs-lookup"><span data-stu-id="9398d-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="9398d-802">**角色**</span><span class="sxs-lookup"><span data-stu-id="9398d-802">**Role**</span></span>       | <span data-ttu-id="9398d-803">是</span><span class="sxs-lookup"><span data-stu-id="9398d-803">Yes</span></span>         | <span data-ttu-id="9398d-804">位於關聯之主要端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-805">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**主體**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="9398d-806">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-807">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-808">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-808">Example</span></span>

<span data-ttu-id="9398d-809">下列範例顯示屬於**PublishedBy**關聯定義一部分的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="9398d-810">**發行者**實體類型的**Id**屬性構成參考條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="9398d-811">Property 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-811">Property Element (CSDL)</span></span>

<span data-ttu-id="9398d-812">概念結構定義語言（CSDL）中的**Property**元素可以是 EntityType 元素的子系、ComplexType 元素或 RowType 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="9398d-813">EntityType 和 ComplexType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="9398d-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="9398d-814">**屬性**專案（做為**EntityType**或**ComplexType**專案的子系）定義實體類型實例或複雜類型實例將包含之資料的形狀和特性。</span><span class="sxs-lookup"><span data-stu-id="9398d-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="9398d-815">概念模型中的屬性類似類別中定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="9398d-816">如同類別上的屬性可定義類別的圖形並包含關於物件的資訊，概念模型的屬性可定義實體類別的圖形，並包含關於實體類型執行個體的資訊。</span><span class="sxs-lookup"><span data-stu-id="9398d-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="9398d-817">**Property**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-818">檔元素（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-819">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="9398d-820">下列 facet 可以套用至**Property**元素： **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、定**序**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="9398d-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="9398d-821">Facet 是 XML 屬性 (attribute)，提供關於屬性 (property) 值如何儲存在資料存放區資訊。</span><span class="sxs-lookup"><span data-stu-id="9398d-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-822">Facet 只能套用至**EDMSimpleType**類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-823">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-823">Applicable Attributes</span></span>

<span data-ttu-id="9398d-824">下表描述可套用至**Property**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="9398d-825">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-825">Attribute Name</span></span>                                                         | <span data-ttu-id="9398d-826">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-826">Is Required</span></span> | <span data-ttu-id="9398d-827">值</span><span class="sxs-lookup"><span data-stu-id="9398d-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-828">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-828">**Name**</span></span>                                                               | <span data-ttu-id="9398d-829">是</span><span class="sxs-lookup"><span data-stu-id="9398d-829">Yes</span></span>         | <span data-ttu-id="9398d-830">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="9398d-831">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-831">**Type**</span></span>                                                               | <span data-ttu-id="9398d-832">是</span><span class="sxs-lookup"><span data-stu-id="9398d-832">Yes</span></span>         | <span data-ttu-id="9398d-833">屬性值的類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-833">The type of the property value.</span></span> <span data-ttu-id="9398d-834">屬性值類型必須是 **EDMSimpleType** ，或是模型範圍內的複雜類型 (以完整名稱表示)。</span><span class="sxs-lookup"><span data-stu-id="9398d-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="9398d-835">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-835">**Nullable**</span></span>                                                           | <span data-ttu-id="9398d-836">否</span><span class="sxs-lookup"><span data-stu-id="9398d-836">No</span></span>          | <span data-ttu-id="9398d-837">**True** (預設值) 或 <strong>False</strong>，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="9398d-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="9398d-838">CSDL v1 中的 > 複雜型別屬性必須具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="9398d-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-839">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9398d-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="9398d-840">否</span><span class="sxs-lookup"><span data-stu-id="9398d-840">No</span></span>          | <span data-ttu-id="9398d-841">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9398d-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="9398d-843">否</span><span class="sxs-lookup"><span data-stu-id="9398d-843">No</span></span>          | <span data-ttu-id="9398d-844">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="9398d-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9398d-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="9398d-846">否</span><span class="sxs-lookup"><span data-stu-id="9398d-846">No</span></span>          | <span data-ttu-id="9398d-847">**True**或**False** ，取決於屬性值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9398d-848">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-848">**Precision**</span></span>                                                          | <span data-ttu-id="9398d-849">否</span><span class="sxs-lookup"><span data-stu-id="9398d-849">No</span></span>          | <span data-ttu-id="9398d-850">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9398d-851">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-851">**Scale**</span></span>                                                              | <span data-ttu-id="9398d-852">否</span><span class="sxs-lookup"><span data-stu-id="9398d-852">No</span></span>          | <span data-ttu-id="9398d-853">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9398d-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-854">**SRID**</span></span>                                                               | <span data-ttu-id="9398d-855">否</span><span class="sxs-lookup"><span data-stu-id="9398d-855">No</span></span>          | <span data-ttu-id="9398d-856">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-857">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9398d-858">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-858">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9398d-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-859">**Unicode**</span></span>                                                            | <span data-ttu-id="9398d-860">否</span><span class="sxs-lookup"><span data-stu-id="9398d-860">No</span></span>          | <span data-ttu-id="9398d-861">**True**或**False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9398d-862">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-862">**Collation**</span></span>                                                          | <span data-ttu-id="9398d-863">否</span><span class="sxs-lookup"><span data-stu-id="9398d-863">No</span></span>          | <span data-ttu-id="9398d-864">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="9398d-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="9398d-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="9398d-866">否</span><span class="sxs-lookup"><span data-stu-id="9398d-866">No</span></span>          | <span data-ttu-id="9398d-867">**None** (預設值) 或 **Fixed**。</span><span class="sxs-lookup"><span data-stu-id="9398d-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="9398d-868">如果值設為 **Fixed**，則屬性值將用於開放式並行存取檢查中。</span><span class="sxs-lookup"><span data-stu-id="9398d-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="9398d-869">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**屬性**專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="9398d-870">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-871">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-872">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-872">Example</span></span>

<span data-ttu-id="9398d-873">下列範例顯示具有三個**屬性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="9398d-874">下列範例顯示具有五個**屬性**元素的**ComplexType**元素：</span><span class="sxs-lookup"><span data-stu-id="9398d-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="9398d-875">RowType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="9398d-875">RowType Element Application</span></span>

<span data-ttu-id="9398d-876">**屬性**專案（做為**RowType**專案的子系）可定義資料的圖形和特性，而這些資料可以傳遞給模型定義的函式，或從該函數傳回。</span><span class="sxs-lookup"><span data-stu-id="9398d-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="9398d-877">**Property**元素可以只有下列其中一個子專案：</span><span class="sxs-lookup"><span data-stu-id="9398d-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="9398d-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="9398d-878">CollectionType</span></span>
-   <span data-ttu-id="9398d-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9398d-879">ReferenceType</span></span>
-   <span data-ttu-id="9398d-880">RowType</span><span class="sxs-lookup"><span data-stu-id="9398d-880">RowType</span></span>

<span data-ttu-id="9398d-881">**Property**元素可以有任意數目的子批註元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-882">只有在 CSDL v2 和更新版本中才允許 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="9398d-883">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-883">Applicable Attributes</span></span>

<span data-ttu-id="9398d-884">下表描述可套用至**Property**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="9398d-885">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-885">Attribute Name</span></span>                                                     | <span data-ttu-id="9398d-886">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-886">Is Required</span></span> | <span data-ttu-id="9398d-887">值</span><span class="sxs-lookup"><span data-stu-id="9398d-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-888">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-888">**Name**</span></span>                                                           | <span data-ttu-id="9398d-889">是</span><span class="sxs-lookup"><span data-stu-id="9398d-889">Yes</span></span>         | <span data-ttu-id="9398d-890">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="9398d-891">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-891">**Type**</span></span>                                                           | <span data-ttu-id="9398d-892">是</span><span class="sxs-lookup"><span data-stu-id="9398d-892">Yes</span></span>         | <span data-ttu-id="9398d-893">屬性值的類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-894">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-894">**Nullable**</span></span>                                                       | <span data-ttu-id="9398d-895">否</span><span class="sxs-lookup"><span data-stu-id="9398d-895">No</span></span>          | <span data-ttu-id="9398d-896">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="9398d-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="9398d-897">CSDL v1 中的 > 複雜型別屬性必須具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="9398d-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-898">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9398d-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="9398d-899">否</span><span class="sxs-lookup"><span data-stu-id="9398d-899">No</span></span>          | <span data-ttu-id="9398d-900">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9398d-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="9398d-902">否</span><span class="sxs-lookup"><span data-stu-id="9398d-902">No</span></span>          | <span data-ttu-id="9398d-903">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="9398d-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9398d-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="9398d-905">否</span><span class="sxs-lookup"><span data-stu-id="9398d-905">No</span></span>          | <span data-ttu-id="9398d-906">**True**或**False** ，取決於屬性值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9398d-907">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-907">**Precision**</span></span>                                                      | <span data-ttu-id="9398d-908">否</span><span class="sxs-lookup"><span data-stu-id="9398d-908">No</span></span>          | <span data-ttu-id="9398d-909">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9398d-910">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-910">**Scale**</span></span>                                                          | <span data-ttu-id="9398d-911">否</span><span class="sxs-lookup"><span data-stu-id="9398d-911">No</span></span>          | <span data-ttu-id="9398d-912">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9398d-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-913">**SRID**</span></span>                                                           | <span data-ttu-id="9398d-914">否</span><span class="sxs-lookup"><span data-stu-id="9398d-914">No</span></span>          | <span data-ttu-id="9398d-915">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-916">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9398d-917">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-917">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9398d-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-918">**Unicode**</span></span>                                                        | <span data-ttu-id="9398d-919">否</span><span class="sxs-lookup"><span data-stu-id="9398d-919">No</span></span>          | <span data-ttu-id="9398d-920">**True**或**False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9398d-921">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-921">**Collation**</span></span>                                                      | <span data-ttu-id="9398d-922">否</span><span class="sxs-lookup"><span data-stu-id="9398d-922">No</span></span>          | <span data-ttu-id="9398d-923">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9398d-924">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**屬性**專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="9398d-925">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-926">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="9398d-927">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-927">Example</span></span>

<span data-ttu-id="9398d-928">下列範例顯示用來定義模型定義函式之傳回型別的圖形的**屬性**專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="9398d-929">PropertyRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="9398d-930">概念結構定義語言（CSDL）中的**PropertyRef**元素會參考實體類型的屬性，以指出屬性將會執行下列其中一個角色：</span><span class="sxs-lookup"><span data-stu-id="9398d-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="9398d-931">實體索引鍵的一部分 (可判斷識別之實體類型的屬性或屬性集)。</span><span class="sxs-lookup"><span data-stu-id="9398d-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="9398d-932">一或多個**PropertyRef**元素可以用來定義實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="9398d-933">參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="9398d-934">**PropertyRef**元素只能有 annotation 元素（零或多個）做為子項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-935">只有在 CSDL v2 和更新版本中才允許 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-936">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-936">Applicable Attributes</span></span>

<span data-ttu-id="9398d-937">下表描述可套用至**PropertyRef**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="9398d-938">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-938">Attribute Name</span></span> | <span data-ttu-id="9398d-939">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-939">Is Required</span></span> | <span data-ttu-id="9398d-940">值</span><span class="sxs-lookup"><span data-stu-id="9398d-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="9398d-941">**名稱**</span><span class="sxs-lookup"><span data-stu-id="9398d-941">**Name**</span></span>       | <span data-ttu-id="9398d-942">是</span><span class="sxs-lookup"><span data-stu-id="9398d-942">Yes</span></span>         | <span data-ttu-id="9398d-943">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-944">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**PropertyRef**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="9398d-945">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-946">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-947">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-947">Example</span></span>

<span data-ttu-id="9398d-948">下列範例定義實體類型（**Book**）。</span><span class="sxs-lookup"><span data-stu-id="9398d-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="9398d-949">實體索引鍵是藉由參考實體類型的**ISBN**屬性來定義。</span><span class="sxs-lookup"><span data-stu-id="9398d-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="9398d-950">在下一個範例中，會使用兩個**PropertyRef**元素來表示兩個屬性（**Id**和**PublisherId**）是參考條件約束的主體和相依端點。</span><span class="sxs-lookup"><span data-stu-id="9398d-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="9398d-951">ReferenceType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="9398d-952">概念結構定義語言（CSDL）中的**ReferenceType**元素會指定實體類型的參考。</span><span class="sxs-lookup"><span data-stu-id="9398d-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="9398d-953">**ReferenceType**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="9398d-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="9398d-954">ReturnType （函數）</span><span class="sxs-lookup"><span data-stu-id="9398d-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="9398d-955">參數</span><span class="sxs-lookup"><span data-stu-id="9398d-955">Parameter</span></span>
-   <span data-ttu-id="9398d-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="9398d-956">CollectionType</span></span>

<span data-ttu-id="9398d-957">定義函數的參數或傳回型別時，會使用**ReferenceType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="9398d-958">**ReferenceType**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-959">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-960">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-961">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-961">Applicable Attributes</span></span>

<span data-ttu-id="9398d-962">下表描述可套用至**ReferenceType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="9398d-963">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-963">Attribute Name</span></span> | <span data-ttu-id="9398d-964">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-964">Is Required</span></span> | <span data-ttu-id="9398d-965">值</span><span class="sxs-lookup"><span data-stu-id="9398d-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="9398d-966">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-966">**Type**</span></span>       | <span data-ttu-id="9398d-967">是</span><span class="sxs-lookup"><span data-stu-id="9398d-967">Yes</span></span>         | <span data-ttu-id="9398d-968">參考之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-969">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReferenceType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="9398d-970">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-971">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-972">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-972">Example</span></span>

<span data-ttu-id="9398d-973">下列範例顯示在模型定義函式中，用來做為**參數**專案子系的**ReferenceType**專案，該函數接受**Person**實體類型的參考：</span><span class="sxs-lookup"><span data-stu-id="9398d-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="9398d-974">下列範例顯示在模型定義函式中，做為**ReturnType** （Function）專案子系的**ReferenceType**元素，該函數會傳回**Person**實體類型的參考：</span><span class="sxs-lookup"><span data-stu-id="9398d-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="9398d-975">ReferentialConstraint 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="9398d-976">概念結構定義語言（CSDL）中的**ReferentialConstraint**專案會定義與關係資料庫中的參考完整性條件約束類似的功能。</span><span class="sxs-lookup"><span data-stu-id="9398d-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="9398d-977">資料庫資料表中的資料行可以參考另一個資料表的主要索引鍵，同樣地，實體類型的屬性也可以參考另一個實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="9398d-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="9398d-978">參考的實體類型稱為條件約束的*主要端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="9398d-979">參考主要端點的實體類型稱為條件約束的*相依端點*。</span><span class="sxs-lookup"><span data-stu-id="9398d-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="9398d-980">如果在某個實體類型上公開的外鍵參考另一個實體類型上的屬性，則**ReferentialConstraint**元素會定義兩個實體類型之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="9398d-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="9398d-981">由於**ReferentialConstraint**元素會提供與兩個實體類型相關的資訊，因此對應規格語言（MSL）中不需要有對應的 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="9398d-982">沒有公開外鍵的兩個實體類型之間的關聯，必須具有對應的**AssociationSetMapping**元素，才能將關聯資訊對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="9398d-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="9398d-983">如果外鍵未在實體類型上公開，則**ReferentialConstraint**元素只能定義實體類型與另一個實體類型之間的 primary key to primary key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="9398d-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="9398d-984">**ReferentialConstraint**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-985">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-986">Principal （只有一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="9398d-987">Dependent (只有一個項目)</span><span class="sxs-lookup"><span data-stu-id="9398d-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="9398d-988">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-989">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-989">Applicable Attributes</span></span>

<span data-ttu-id="9398d-990">**ReferentialConstraint**元素可以有任意數目的注釋屬性（自訂 XML 屬性）。</span><span class="sxs-lookup"><span data-stu-id="9398d-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="9398d-991">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-992">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-993">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-993">Example</span></span>

<span data-ttu-id="9398d-994">下列範例顯示使用**ReferentialConstraint**元素做為**PublishedBy**關聯定義的一部分。</span><span class="sxs-lookup"><span data-stu-id="9398d-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="9398d-995">ReturnType （函數）元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="9398d-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="9398d-996">概念結構定義語言（CSDL）中的**ReturnType** （函式）專案會指定在 function 元素中定義之函式的傳回類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="9398d-997">函式傳回型別也可以使用**ReturnType**屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="9398d-998">傳回類型可以是任何**EdmSimpleType**、實體類型、複雜類型、資料列類型、ref 類型或其中一種類型的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="9398d-999">函式的傳回型別可以使用**ReturnType** （function）元素的**type**屬性或下列其中一個子專案來指定：</span><span class="sxs-lookup"><span data-stu-id="9398d-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="9398d-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="9398d-1000">CollectionType</span></span>
-   <span data-ttu-id="9398d-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="9398d-1001">ReferenceType</span></span>
-   <span data-ttu-id="9398d-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="9398d-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-1003">如果您使用**ReturnType** （函式）專案的**type**屬性和其中一個子專案來指定函式傳回型別，則模型將不會驗證。</span><span class="sxs-lookup"><span data-stu-id="9398d-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1004">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1004">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1005">下表描述可套用至**ReturnType** （Function）元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="9398d-1006">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-1006">Attribute Name</span></span> | <span data-ttu-id="9398d-1007">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-1007">Is Required</span></span> | <span data-ttu-id="9398d-1008">值</span><span class="sxs-lookup"><span data-stu-id="9398d-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="9398d-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="9398d-1009">**ReturnType**</span></span> | <span data-ttu-id="9398d-1010">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1010">No</span></span>          | <span data-ttu-id="9398d-1011">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-1012">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReturnType** （Function）元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="9398d-1013">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1014">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-1015">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1015">Example</span></span>

<span data-ttu-id="9398d-1016">下列範例會使用**function**專案來定義函式，該函式會傳回書籍已列印的年數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="9398d-1017">請注意，傳回類型是由**ReturnType** （函式）元素的**type**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="9398d-1018">ReturnType （FunctionImport）元素（CSDL）</span><span class="sxs-lookup"><span data-stu-id="9398d-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="9398d-1019">概念結構定義語言（CSDL）中的**ReturnType** （FunctionImport）專案會指定 FunctionImport 元素中定義之函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="9398d-1020">函式傳回型別也可以使用**ReturnType**屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="9398d-1021">傳回類型可以是實體類型、複雜類型或**EdmSimpleType**的任何集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="9398d-1022">函式的傳回型別是以**ReturnType** （FunctionImport）元素的**type**屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="9398d-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1023">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1023">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1024">下表描述可套用至**ReturnType** （FunctionImport）元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="9398d-1025">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-1025">Attribute Name</span></span> | <span data-ttu-id="9398d-1026">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-1026">Is Required</span></span> | <span data-ttu-id="9398d-1027">值</span><span class="sxs-lookup"><span data-stu-id="9398d-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-1028">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-1028">**Type**</span></span>       | <span data-ttu-id="9398d-1029">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1029">No</span></span>          | <span data-ttu-id="9398d-1030">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1030">The type that the function returns.</span></span> <span data-ttu-id="9398d-1031">此值必須是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="9398d-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="9398d-1032">**EntitySet**</span></span>  | <span data-ttu-id="9398d-1033">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1033">No</span></span>          | <span data-ttu-id="9398d-1034">如果函數傳回實體類型的集合，則**EntitySet**的值必須是集合所屬的實體集。</span><span class="sxs-lookup"><span data-stu-id="9398d-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="9398d-1035">否則，不得使用 **EntitySet** 屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-1036">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReturnType** （FunctionImport）元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="9398d-1037">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1038">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-1039">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1039">Example</span></span>

<span data-ttu-id="9398d-1040">下列範例會使用會傳回書籍和發行者的**FunctionImport** 。</span><span class="sxs-lookup"><span data-stu-id="9398d-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="9398d-1041">請注意，此函數會傳回兩個結果集，因此會指定兩個**ReturnType** （FunctionImport）元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="9398d-1042">RowType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="9398d-1043">概念結構定義語言（CSDL）中的**RowType**專案會將未命名的結構定義為概念模型中所定義之函式的參數或傳回型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="9398d-1044">**RowType**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="9398d-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="9398d-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="9398d-1045">CollectionType</span></span>
-   <span data-ttu-id="9398d-1046">參數</span><span class="sxs-lookup"><span data-stu-id="9398d-1046">Parameter</span></span>
-   <span data-ttu-id="9398d-1047">ReturnType （函數）</span><span class="sxs-lookup"><span data-stu-id="9398d-1047">ReturnType (Function)</span></span>

<span data-ttu-id="9398d-1048">**RowType**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-1049">屬性 (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="9398d-1049">Property (one or more)</span></span>
-   <span data-ttu-id="9398d-1050">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="9398d-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1051">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1051">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1052">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**RowType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="9398d-1053">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1054">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-1055">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1055">Example</span></span>

<span data-ttu-id="9398d-1056">下列範例示範的模型定義函式會使用**CollectionType**專案來指定該函數傳回資料列集合（如**RowType**元素中所指定）。</span><span class="sxs-lookup"><span data-stu-id="9398d-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a><span data-ttu-id="9398d-1057">Schema 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="9398d-1058">**Schema**元素是概念模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="9398d-1059">其中包含組成概念模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="9398d-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="9398d-1060">**Schema**元素可以包含零個或多個下列子項目：</span><span class="sxs-lookup"><span data-stu-id="9398d-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="9398d-1061">使用</span><span class="sxs-lookup"><span data-stu-id="9398d-1061">Using</span></span>
-   <span data-ttu-id="9398d-1062">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="9398d-1062">EntityContainer</span></span>
-   <span data-ttu-id="9398d-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="9398d-1063">EntityType</span></span>
-   <span data-ttu-id="9398d-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="9398d-1064">EnumType</span></span>
-   <span data-ttu-id="9398d-1065">關聯</span><span class="sxs-lookup"><span data-stu-id="9398d-1065">Association</span></span>
-   <span data-ttu-id="9398d-1066">ComplexType</span><span class="sxs-lookup"><span data-stu-id="9398d-1066">ComplexType</span></span>
-   <span data-ttu-id="9398d-1067">函式</span><span class="sxs-lookup"><span data-stu-id="9398d-1067">Function</span></span>

<span data-ttu-id="9398d-1068">**Schema**元素可以包含零個或一個 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-1069">只有在 CSDL v2 和更新版本中才允許**Function**元素和 annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="9398d-1070">**Schema**元素會使用**namespace**屬性來定義概念模型中的實體類型、複雜類型和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="9398d-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="9398d-1071">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="9398d-1072">命名空間可以跨越多個**架構**元素和多個 csdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="9398d-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="9398d-1073">概念模型命名空間與**架構**元素的 XML 命名空間不同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="9398d-1074">概念模型命名空間（如**命名空間**屬性所定義）是實體類型、複雜類型和關聯類型的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="9398d-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="9398d-1075">**Schema**元素的 XML 命名空間（由**xmlns**屬性所表示）是**schema**專案之子專案和屬性的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="9398d-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="9398d-1076">https://schemas.microsoft.com/ado/YYYY/MM/edm 格式的 XML 命名空間（其中 YYYY 和 MM 分別表示年和月）是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1076">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="9398d-1077">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="9398d-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1078">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1078">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1079">下表描述可套用至**架構**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="9398d-1080">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-1080">Attribute Name</span></span> | <span data-ttu-id="9398d-1081">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-1081">Is Required</span></span> | <span data-ttu-id="9398d-1082">值</span><span class="sxs-lookup"><span data-stu-id="9398d-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-1083">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="9398d-1083">**Namespace**</span></span>  | <span data-ttu-id="9398d-1084">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1084">Yes</span></span>         | <span data-ttu-id="9398d-1085">概念模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="9398d-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="9398d-1086">**Namespace**屬性的值是用來形成類型的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="9398d-1087">例如，如果名為*Customer*的**EntityType**在 Simple. Model 命名空間中，則**entitytype**的完整限定名稱為 simpleexamplemodel.customer。</span><span class="sxs-lookup"><span data-stu-id="9398d-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="9398d-1088">下列字串不能當做**Namespace**屬性的值使用： **System**、**暫時性**或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="9398d-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="9398d-1089">**Namespace**屬性的值不能與 SSDL Schema 元素中**namespace**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="9398d-1090">**別名**</span><span class="sxs-lookup"><span data-stu-id="9398d-1090">**Alias**</span></span>      | <span data-ttu-id="9398d-1091">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1091">No</span></span>          | <span data-ttu-id="9398d-1092">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="9398d-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="9398d-1093">例如，如果名為*Customer*的**EntityType**在 Simple. model 命名空間中，而**Alias**屬性的值是*model*，則您可以使用 model. Customer 當做 EntityType 的完整名稱 **。**</span><span class="sxs-lookup"><span data-stu-id="9398d-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="9398d-1094">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**架構**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="9398d-1095">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1096">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-1097">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1097">Example</span></span>

<span data-ttu-id="9398d-1098">下列範例顯示包含**EntityContainer**元素、兩個**EntityType**專案和一個**Association**元素的**Schema**專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="9398d-1099">TypeRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="9398d-1100">概念結構定義語言（CSDL）中的**TypeRef**元素會提供現有已命名類型的參考。</span><span class="sxs-lookup"><span data-stu-id="9398d-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="9398d-1101">**TypeRef**元素可以是 CollectionType 元素的子系，用來指定函式具有集合做為參數或傳回型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="9398d-1102">**TypeRef**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="9398d-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9398d-1103">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="9398d-1104">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1105">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1105">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1106">下表描述可套用至**TypeRef**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="9398d-1107">請注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**和定**序**屬性僅適用于**EDMSimpleTypes**。</span><span class="sxs-lookup"><span data-stu-id="9398d-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="9398d-1108">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="9398d-1109">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-1109">Is Required</span></span> | <span data-ttu-id="9398d-1110">值</span><span class="sxs-lookup"><span data-stu-id="9398d-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-1111">**型別**</span><span class="sxs-lookup"><span data-stu-id="9398d-1111">**Type**</span></span>                                                           | <span data-ttu-id="9398d-1112">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1112">No</span></span>          | <span data-ttu-id="9398d-1113">所參考的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="9398d-1114">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="9398d-1115">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1115">No</span></span>          | <span data-ttu-id="9398d-1116">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="9398d-1117">CSDL v1 中的 > 複雜型別屬性必須具有 `Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="9398d-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="9398d-1118">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="9398d-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="9398d-1119">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1119">No</span></span>          | <span data-ttu-id="9398d-1120">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="9398d-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="9398d-1122">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1122">No</span></span>          | <span data-ttu-id="9398d-1123">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="9398d-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="9398d-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="9398d-1125">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1125">No</span></span>          | <span data-ttu-id="9398d-1126">**True**或**False** ，取決於屬性值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="9398d-1127">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-1127">**Precision**</span></span>                                                      | <span data-ttu-id="9398d-1128">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1128">No</span></span>          | <span data-ttu-id="9398d-1129">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="9398d-1130">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-1130">**Scale**</span></span>                                                          | <span data-ttu-id="9398d-1131">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1131">No</span></span>          | <span data-ttu-id="9398d-1132">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="9398d-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-1133">**SRID**</span></span>                                                           | <span data-ttu-id="9398d-1134">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1134">No</span></span>          | <span data-ttu-id="9398d-1135">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="9398d-1136">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="9398d-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="9398d-1137">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-1137">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="9398d-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="9398d-1139">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1139">No</span></span>          | <span data-ttu-id="9398d-1140">**True**或**False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="9398d-1141">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-1141">**Collation**</span></span>                                                      | <span data-ttu-id="9398d-1142">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1142">No</span></span>          | <span data-ttu-id="9398d-1143">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="9398d-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="9398d-1144">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="9398d-1145">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1146">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-1147">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1147">Example</span></span>

<span data-ttu-id="9398d-1148">下列範例顯示使用**TypeRef**專案（做為**CollectionType**元素的子系）的模型定義函式，以指定函式接受**部門**實體類型的集合。</span><span class="sxs-lookup"><span data-stu-id="9398d-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="9398d-1149">Using 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="9398d-1150">概念結構定義語言（CSDL）中的**Using**元素會匯入存在於不同命名空間中之概念模型的內容。</span><span class="sxs-lookup"><span data-stu-id="9398d-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="9398d-1151">藉由設定**Namespace**屬性的值，您可以參考在另一個概念模型中定義的實體類型、複雜類型和關聯類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="9398d-1152">一個以上的**Using**元素可以是**Schema**元素的子系。</span><span class="sxs-lookup"><span data-stu-id="9398d-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-1153">CSDL 中的**using**元素無法與程式設計語言中的**using**語句一樣運作。</span><span class="sxs-lookup"><span data-stu-id="9398d-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="9398d-1154">藉由使用程式設計語言中的**using**語句匯入命名空間，您不會影響原始命名空間中的物件。</span><span class="sxs-lookup"><span data-stu-id="9398d-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="9398d-1155">在 CSDL 中，匯入的命名空間可以包含實體類型，該實體類型是衍生自原始命名空間中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="9398d-1156">這會影響在原始命名空間中宣告的實體集。</span><span class="sxs-lookup"><span data-stu-id="9398d-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="9398d-1157">**Using**元素可以具有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="9398d-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="9398d-1158">檔（允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="9398d-1159">Annotation 元素（允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="9398d-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9398d-1160">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1160">Applicable Attributes</span></span>

<span data-ttu-id="9398d-1161">下表描述可套用至**Using**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="9398d-1162">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="9398d-1162">Attribute Name</span></span> | <span data-ttu-id="9398d-1163">必要</span><span class="sxs-lookup"><span data-stu-id="9398d-1163">Is Required</span></span> | <span data-ttu-id="9398d-1164">值</span><span class="sxs-lookup"><span data-stu-id="9398d-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9398d-1165">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="9398d-1165">**Namespace**</span></span>  | <span data-ttu-id="9398d-1166">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1166">Yes</span></span>         | <span data-ttu-id="9398d-1167">匯入的命名空間名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="9398d-1168">**別名**</span><span class="sxs-lookup"><span data-stu-id="9398d-1168">**Alias**</span></span>      | <span data-ttu-id="9398d-1169">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1169">Yes</span></span>         | <span data-ttu-id="9398d-1170">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="9398d-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="9398d-1171">雖然這個屬性是必要的，但是不必使用此屬性取代命名空間名稱，來限定物件名稱。</span><span class="sxs-lookup"><span data-stu-id="9398d-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="9398d-1172">可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**Using**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="9398d-1173">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="9398d-1174">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="9398d-1175">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1175">Example</span></span>

<span data-ttu-id="9398d-1176">下列範例示範用來匯入在其他地方定義之命名空間的**Using**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="9398d-1177">請注意，顯示的**架構**元素的命名空間是 `BooksModel`。</span><span class="sxs-lookup"><span data-stu-id="9398d-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="9398d-1178">`Publisher`**EntityType**上的 `Address` 屬性是在 `ExtendedBooksModel` 命名空間（**使用 Using**元素匯入）中定義的複雜類型。</span><span class="sxs-lookup"><span data-stu-id="9398d-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="9398d-1179">註釋屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="9398d-1180">概念結構描述定義語言 (CSDL) 中的附註屬性是概念模型中自訂的 XML 屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="9398d-1181">除了擁有有效的 XML 結構外，下列附註屬性的條件必須成立：</span><span class="sxs-lookup"><span data-stu-id="9398d-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="9398d-1182">附註屬性不能在任何 XML 命名空間之中，這是保留供 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="9398d-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="9398d-1183">超過一個以上的附註屬性可以套用至給定的 CSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="9398d-1184">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="9398d-1185">附註屬性可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="9398d-1186">注釋專案中包含的中繼資料可在執行時間使用 system.string. 中繼資料命名空間中的類別來存取。</span><span class="sxs-lookup"><span data-stu-id="9398d-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-1187">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1187">Example</span></span>

<span data-ttu-id="9398d-1188">下列範例顯示具有 annotation 屬性（**system.reflection.customattribute.isdefined**）的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="9398d-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="9398d-1189">下列範例也顯示套用至實體類型項目的 Annotation 項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-1189">The example also shows an annotation element applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="9398d-1190">下列程式碼會擷取附註屬性中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="9398d-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="9398d-1191">以上程式碼假設 `School.csdl` 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="9398d-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="9398d-1192">註釋項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="9398d-1193">概念結構定義語言 (CSDL) 中的 Annotation 項目是概念模型中的自訂 XML 項目。</span><span class="sxs-lookup"><span data-stu-id="9398d-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="9398d-1194">除了擁有有效的 XML 結構外，下列 Annotation 項目的條件也必須成立：</span><span class="sxs-lookup"><span data-stu-id="9398d-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="9398d-1195">Annotation 項目不能存在於保留供 CSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="9398d-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="9398d-1196">多個 Annotation 項目可以同時為指定 CSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="9398d-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="9398d-1197">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="9398d-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="9398d-1198">Annotation 項目必須出現在指定 CSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="9398d-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="9398d-1199">Annotation 項目可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="9398d-1200">從 .NET Framework 第4版開始，您可以在執行時間使用 system.servicemodel 命名空間中的類別來存取 annotation 元素中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-1201">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1201">Example</span></span>

<span data-ttu-id="9398d-1202">下列範例顯示具有 annotation 元素（**CustomElement**）的**EntityType**專案。</span><span class="sxs-lookup"><span data-stu-id="9398d-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="9398d-1203">該範例也顯示套用至實體類型項目的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="9398d-1204">下列程式碼會擷取 annotation 項目中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="9398d-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="9398d-1205">以上程式碼假設 School.csdl 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="9398d-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="9398d-1206">概念模型類型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="9398d-1207">概念結構定義語言（CSDL）支援一組抽象的基本資料類型（稱為**EDMSimpleTypes**），可定義概念模型中的屬性。</span><span class="sxs-lookup"><span data-stu-id="9398d-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="9398d-1208">**EDMSimpleTypes**是儲存或裝載環境中支援之基本資料類型的 proxy。</span><span class="sxs-lookup"><span data-stu-id="9398d-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="9398d-1209">下表列出 CSDL 所支援的基本資料型別。</span><span class="sxs-lookup"><span data-stu-id="9398d-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="9398d-1210">資料表也會列出可以套用至每個**EDMSimpleType**的 facet。</span><span class="sxs-lookup"><span data-stu-id="9398d-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="9398d-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="9398d-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="9398d-1212">描述</span><span class="sxs-lookup"><span data-stu-id="9398d-1212">Description</span></span>                                                | <span data-ttu-id="9398d-1213">適用的 Facet</span><span class="sxs-lookup"><span data-stu-id="9398d-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="9398d-1214">**Edm. Binary**</span><span class="sxs-lookup"><span data-stu-id="9398d-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="9398d-1215">包含二進位資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="9398d-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="9398d-1217">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="9398d-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="9398d-1218">包含**true**或**false**值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="9398d-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="9398d-1220">**Edm. Byte**</span><span class="sxs-lookup"><span data-stu-id="9398d-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="9398d-1221">包含不帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="9398d-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1223">**Edm. DateTime**</span><span class="sxs-lookup"><span data-stu-id="9398d-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="9398d-1224">表示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="9398d-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="9398d-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1226">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="9398d-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="9398d-1227">包含日期和時間以表示與 GMT 的時差 (以分鐘為單位)。</span><span class="sxs-lookup"><span data-stu-id="9398d-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="9398d-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1229">**Edm Decimal**</span><span class="sxs-lookup"><span data-stu-id="9398d-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="9398d-1230">包含固定有效位數和小數位數的數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="9398d-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1232">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="9398d-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="9398d-1233">包含具有15位數精確度的浮點數</span><span class="sxs-lookup"><span data-stu-id="9398d-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="9398d-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1235">**Edm. Float**</span><span class="sxs-lookup"><span data-stu-id="9398d-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="9398d-1236">包含具有 7 位數精確度的浮點數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="9398d-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1238">**Edm. Guid**</span><span class="sxs-lookup"><span data-stu-id="9398d-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="9398d-1239">包含 16 位元組的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="9398d-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1241">**Edm. Int16**</span><span class="sxs-lookup"><span data-stu-id="9398d-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="9398d-1242">包含帶正負號的 16 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="9398d-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1244">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="9398d-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="9398d-1245">包含帶正負號的 32 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="9398d-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1247">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="9398d-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="9398d-1248">包含帶正負號的 64 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="9398d-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1250">**Edm. SByte**</span><span class="sxs-lookup"><span data-stu-id="9398d-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="9398d-1251">包含帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="9398d-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1253">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9398d-1253">**Edm.String**</span></span>                   | <span data-ttu-id="9398d-1254">包含字元資料。</span><span class="sxs-lookup"><span data-stu-id="9398d-1254">Contains character data.</span></span>                                   | <span data-ttu-id="9398d-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="9398d-1256">**Edm。時間**</span><span class="sxs-lookup"><span data-stu-id="9398d-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="9398d-1257">包含一天的時間。</span><span class="sxs-lookup"><span data-stu-id="9398d-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="9398d-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="9398d-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="9398d-1259">**Edm. Geography**</span><span class="sxs-lookup"><span data-stu-id="9398d-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="9398d-1260">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1261">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="9398d-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="9398d-1262">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1263">**Edm. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="9398d-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="9398d-1264">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1265">**Edm. 之 geographypolygon**</span><span class="sxs-lookup"><span data-stu-id="9398d-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="9398d-1266">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1267">**Edm. 之 geographymultipoint**</span><span class="sxs-lookup"><span data-stu-id="9398d-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="9398d-1268">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1269">**Edm. 之 geographymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="9398d-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="9398d-1270">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1271">**Edm. 之 geographymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="9398d-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="9398d-1272">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1273">**Edm. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="9398d-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="9398d-1274">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1275">**Edm Geometry**</span><span class="sxs-lookup"><span data-stu-id="9398d-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="9398d-1276">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1277">**Edm. 之 geometrypoint**</span><span class="sxs-lookup"><span data-stu-id="9398d-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="9398d-1278">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1279">**Edm. 之 geometrylinestring**</span><span class="sxs-lookup"><span data-stu-id="9398d-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="9398d-1280">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1281">**Edm. 之 geometrypolygon**</span><span class="sxs-lookup"><span data-stu-id="9398d-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="9398d-1282">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1283">**Edm. 之 geometrymultipoint**</span><span class="sxs-lookup"><span data-stu-id="9398d-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="9398d-1284">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1285">**Edm. 之 geometrymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="9398d-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="9398d-1286">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1287">**Edm. 之 geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="9398d-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="9398d-1288">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="9398d-1289">**Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="9398d-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="9398d-1290">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="9398d-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="9398d-1291">Facet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="9398d-1291">Facets (CSDL)</span></span>

<span data-ttu-id="9398d-1292">概念結構定義語言 (CSDL) 中的 Facet 表示實體型別和複雜型別屬性上的條件約束。</span><span class="sxs-lookup"><span data-stu-id="9398d-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="9398d-1293">Facet 在下列 CSDL 元素上會以 XML 屬性的形式出現：</span><span class="sxs-lookup"><span data-stu-id="9398d-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="9398d-1294">屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1294">Property</span></span>
-   <span data-ttu-id="9398d-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="9398d-1295">TypeRef</span></span>
-   <span data-ttu-id="9398d-1296">參數</span><span class="sxs-lookup"><span data-stu-id="9398d-1296">Parameter</span></span>

<span data-ttu-id="9398d-1297">下表說明 CSDL 中支援的 Facet。</span><span class="sxs-lookup"><span data-stu-id="9398d-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="9398d-1298">所有的 Facet 都是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="9398d-1298">All facets are optional.</span></span> <span data-ttu-id="9398d-1299">從概念模型產生資料庫時，Entity Framework 會使用下面列出的某些 facet。</span><span class="sxs-lookup"><span data-stu-id="9398d-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="9398d-1300">如需概念模型中資料類型的詳細資訊，請參閱概念模型類型（CSDL）。</span><span class="sxs-lookup"><span data-stu-id="9398d-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="9398d-1301">Facet</span><span class="sxs-lookup"><span data-stu-id="9398d-1301">Facet</span></span>               | <span data-ttu-id="9398d-1302">描述</span><span class="sxs-lookup"><span data-stu-id="9398d-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="9398d-1303">適用於</span><span class="sxs-lookup"><span data-stu-id="9398d-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="9398d-1304">用於產生資料庫</span><span class="sxs-lookup"><span data-stu-id="9398d-1304">Used for the database generation</span></span> | <span data-ttu-id="9398d-1305">由執行階段所使用</span><span class="sxs-lookup"><span data-stu-id="9398d-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="9398d-1306">**定序**</span><span class="sxs-lookup"><span data-stu-id="9398d-1306">**Collation**</span></span>       | <span data-ttu-id="9398d-1307">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="9398d-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="9398d-1308">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9398d-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9398d-1309">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1309">Yes</span></span>                              | <span data-ttu-id="9398d-1310">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1310">No</span></span>                  |
| <span data-ttu-id="9398d-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="9398d-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="9398d-1312">表示屬性值應用於開放式並行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="9398d-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="9398d-1313">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9398d-1314">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1314">No</span></span>                               | <span data-ttu-id="9398d-1315">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1315">Yes</span></span>                 |
| <span data-ttu-id="9398d-1316">**預設值**</span><span class="sxs-lookup"><span data-stu-id="9398d-1316">**Default**</span></span>         | <span data-ttu-id="9398d-1317">執行個體化時如果沒有提供值，請指定屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="9398d-1318">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9398d-1319">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1319">Yes</span></span>                              | <span data-ttu-id="9398d-1320">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1320">Yes</span></span>                 |
| <span data-ttu-id="9398d-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-1321">**FixedLength**</span></span>     | <span data-ttu-id="9398d-1322">指定屬性值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="9398d-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="9398d-1323">**Edm. Binary**、 **Edm 字串**</span><span class="sxs-lookup"><span data-stu-id="9398d-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9398d-1324">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1324">Yes</span></span>                              | <span data-ttu-id="9398d-1325">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1325">No</span></span>                  |
| <span data-ttu-id="9398d-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="9398d-1326">**MaxLength**</span></span>       | <span data-ttu-id="9398d-1327">指定屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="9398d-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="9398d-1328">**Edm. Binary**、 **Edm 字串**</span><span class="sxs-lookup"><span data-stu-id="9398d-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9398d-1329">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1329">Yes</span></span>                              | <span data-ttu-id="9398d-1330">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1330">No</span></span>                  |
| <span data-ttu-id="9398d-1331">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="9398d-1331">**Nullable**</span></span>        | <span data-ttu-id="9398d-1332">指定屬性是否可以有**null**值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="9398d-1333">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="9398d-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="9398d-1334">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1334">Yes</span></span>                              | <span data-ttu-id="9398d-1335">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1335">Yes</span></span>                 |
| <span data-ttu-id="9398d-1336">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="9398d-1336">**Precision**</span></span>       | <span data-ttu-id="9398d-1337">針對**Decimal**類型的屬性，指定屬性值可以擁有的位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="9398d-1338">針對**Time**、 **DateTime**和**DateTimeOffset**類型的屬性，指定屬性值秒數小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="9398d-1339">**Edm. DateTime**， **edm**，edm. **Decimal**， **edm。 Time**</span><span class="sxs-lookup"><span data-stu-id="9398d-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="9398d-1340">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1340">Yes</span></span>                              | <span data-ttu-id="9398d-1341">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1341">No</span></span>                  |
| <span data-ttu-id="9398d-1342">**調整**</span><span class="sxs-lookup"><span data-stu-id="9398d-1342">**Scale**</span></span>           | <span data-ttu-id="9398d-1343">指定屬性值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="9398d-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="9398d-1344">**Edm Decimal**</span><span class="sxs-lookup"><span data-stu-id="9398d-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="9398d-1345">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1345">Yes</span></span>                              | <span data-ttu-id="9398d-1346">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1346">No</span></span>                  |
| <span data-ttu-id="9398d-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="9398d-1347">**SRID**</span></span>            | <span data-ttu-id="9398d-1348">指定空間系統參考系統識別碼。</span><span class="sxs-lookup"><span data-stu-id="9398d-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="9398d-1349">如需詳細資訊，請參閱[SRID](https://en.wikipedia.org/wiki/SRID) and [SRID （SQL Server）](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="9398d-1349">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="9398d-1350">**GeographyPoint，Edm，GeographyLineString，Edm. 之 geographypolygon，Edm. 之 geographymultipoint，Edm. 之 geographymultilinestring，Edm. 之 geographymultipolygon，edm. GeographyCollection，Edm，Edm. 之 geometrypoint，之 geometrylinestring，Edm 之 geometrypolygon，Edm. 之 geometrymultipoint，Edm. 之 geometrymultilinestring，Edm. 之 geometrymultipolygon，Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="9398d-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="9398d-1351">否</span><span class="sxs-lookup"><span data-stu-id="9398d-1351">No</span></span>                               | <span data-ttu-id="9398d-1352">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1352">Yes</span></span>                 |
| <span data-ttu-id="9398d-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="9398d-1353">**Unicode**</span></span>         | <span data-ttu-id="9398d-1354">指出屬性值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="9398d-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="9398d-1355">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="9398d-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="9398d-1356">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1356">Yes</span></span>                              | <span data-ttu-id="9398d-1357">是</span><span class="sxs-lookup"><span data-stu-id="9398d-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="9398d-1358">從概念模型產生資料庫時，如果**屬性**專案位於下列命名空間中，則「產生資料庫」 Wizard 會辨識其**StoreGeneratedPattern**屬性的值： https://schemas.microsoft.com/ado/2009/02/edm/annotation。</span><span class="sxs-lookup"><span data-stu-id="9398d-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="9398d-1359">屬性的支援值為**Identity**和**計算**。</span><span class="sxs-lookup"><span data-stu-id="9398d-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="9398d-1360">**Identity**的值將會產生資料庫資料行，其中具有在資料庫中產生的識別值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="9398d-1361">**計算**的值將會產生資料行，其中包含在資料庫中計算的值。</span><span class="sxs-lookup"><span data-stu-id="9398d-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="9398d-1362">範例</span><span class="sxs-lookup"><span data-stu-id="9398d-1362">Example</span></span>

<span data-ttu-id="9398d-1363">下列範例顯示 Facet 套用至實體類型的屬性：</span><span class="sxs-lookup"><span data-stu-id="9398d-1363">The following example shows facets applied to the properties of an entity type:</span></span>

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
