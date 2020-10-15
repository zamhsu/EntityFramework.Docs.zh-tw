---
title: CSDL 規格-EF6
description: Entity Framework 6 中的 CSDL 規格
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066352"
---
# <a name="csdl-specification"></a><span data-ttu-id="62ba9-103">CSDL 規格</span><span class="sxs-lookup"><span data-stu-id="62ba9-103">CSDL Specification</span></span>
<span data-ttu-id="62ba9-104">概念結構定義語言 (CSDL) 是 XML架構語言，可描述組成資料驅動應用程式之概念模型的實體、關聯性和函式。</span><span class="sxs-lookup"><span data-stu-id="62ba9-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="62ba9-105">Entity Framework 或 WCF Data Services 可以使用這個概念模型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="62ba9-106">Entity Framework 使用 CSDL 描述的中繼資料，將概念模型中定義的實體和關聯性對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="62ba9-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="62ba9-107">如需詳細資訊，請參閱 [SSDL 規格](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) 和 [MSL 規格](xref:ef6/modeling/designer/advanced/edmx/msl-spec)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="62ba9-108">CSDL 是實體資料模型的 Entity Framework 執行。</span><span class="sxs-lookup"><span data-stu-id="62ba9-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="62ba9-109">在 Entity Framework 應用程式中，概念模型中繼資料是從 csdl 檔案載入， (以 CSDL) 寫入至 EdmItemCollection 的實例中，而且可以使用 system.string 類別中的方法來加以存取。</span><span class="sxs-lookup"><span data-stu-id="62ba9-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="62ba9-110">Entity Framework 使用概念模型中繼資料，將針對概念模型的查詢轉譯成資料來源特有的命令。</span><span class="sxs-lookup"><span data-stu-id="62ba9-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="62ba9-111">EF 設計工具會在設計階段將概念模型資訊儲存在 .edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="62ba9-112">在組建階段，EF 設計工具會使用 .edmx 檔案中的資訊，建立在執行時間 Entity Framework 所需的 csdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="62ba9-113">CSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="62ba9-114">CSDL 版本</span><span class="sxs-lookup"><span data-stu-id="62ba9-114">CSDL Version</span></span> | <span data-ttu-id="62ba9-115">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="62ba9-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="62ba9-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="62ba9-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="62ba9-117">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="62ba9-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="62ba9-118">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="62ba9-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="62ba9-119">Association 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-119">Association Element (CSDL)</span></span>

<span data-ttu-id="62ba9-120">Association 元素會定義兩個實體類型之間的 **關聯** 性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="62ba9-121">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="62ba9-122">關聯 end 的多重性可以有一個 (1) 、零個或一個 (0 ..1) 或許多 () 中的值。 \*</span><span class="sxs-lookup"><span data-stu-id="62ba9-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="62ba9-123">這項資訊會在兩個 End 子項目中指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="62ba9-124">關聯其中一端的實體類型執行個體可透過導覽屬性或外部索引鍵來存取 (若在實體類型上公開)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="62ba9-125">在應用程式中，關聯的執行個體代表實體類型之間的特定關聯。</span><span class="sxs-lookup"><span data-stu-id="62ba9-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="62ba9-126">關聯執行個體會在邏輯上群組於關聯集中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="62ba9-127">**Association**元素可以依照所列的順序， (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-128">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-129">End (兩個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="62ba9-130">ReferentialConstraint (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-131">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-132">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-132">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-133">下表說明可套用至 **Association** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="62ba9-134">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-134">Attribute Name</span></span> | <span data-ttu-id="62ba9-135">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-135">Is Required</span></span> | <span data-ttu-id="62ba9-136">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="62ba9-137">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-137">**Name**</span></span>       | <span data-ttu-id="62ba9-138">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-138">Yes</span></span>         | <span data-ttu-id="62ba9-139">關聯的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-140">您可以將任何數目的注釋屬性 (自訂 XML 屬性，) 可以套用至 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="62ba9-141">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-142">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-143">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-143">Example</span></span>

<span data-ttu-id="62ba9-144">下列範例顯示在**Customer**和**Order**實體類型上未公開外鍵時，定義 **>customerorders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="62ba9-145">關聯的每個**端點**的**多重性**值表示許多**訂單**可以與**客戶**相關聯，但只有一個**客戶**可以與**訂單**相關聯。</span><span class="sxs-lookup"><span data-stu-id="62ba9-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="62ba9-146">此外， **OnDelete**元素表示如果刪除**客戶**，則會刪除所有與特定**客戶**相關的**訂單**，並將其載入 ObjectCoNtext。</span><span class="sxs-lookup"><span data-stu-id="62ba9-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="62ba9-147">下列範例顯示在**Customer**和**Order**實體類型上公開外鍵時，定義 **>customerorders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="62ba9-148">公開外鍵之後，實體之間的關聯性就會以 **ReferentialConstraint** 元素來管理。</span><span class="sxs-lookup"><span data-stu-id="62ba9-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="62ba9-149">對應此關聯與資料來源時，不需要對應的 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="62ba9-150">AssociationSet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="62ba9-151">概念結構定義語言 (CSDL) 中的 **AssociationSet** 元素是相同類型之關聯實例的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="62ba9-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="62ba9-152">關聯集提供群組關聯執行個體的定義，執行個體才能對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="62ba9-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="62ba9-153">**AssociationSet**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-154">Documentation (可允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-155">End (只需兩個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="62ba9-156">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="62ba9-157">**Association**屬性會指定關聯集包含的關聯類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="62ba9-158">組成關聯集端點的實體集會以剛好兩 **個子項目** 來指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-159">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-159">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-160">下表說明可套用至 **AssociationSet** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="62ba9-161">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-161">Attribute Name</span></span>  | <span data-ttu-id="62ba9-162">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-162">Is Required</span></span> | <span data-ttu-id="62ba9-163">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-164">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-164">**Name**</span></span>        | <span data-ttu-id="62ba9-165">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-165">Yes</span></span>         | <span data-ttu-id="62ba9-166">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-166">The name of the entity set.</span></span> <span data-ttu-id="62ba9-167">**Name**屬性的值不能與**Association**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="62ba9-168">**協會**</span><span class="sxs-lookup"><span data-stu-id="62ba9-168">**Association**</span></span> | <span data-ttu-id="62ba9-169">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-169">Yes</span></span>         | <span data-ttu-id="62ba9-170">關聯執行個體 (由關聯集包含) 之關聯的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="62ba9-171">關聯必須存在與關聯集相同的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-172">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **AssociationSet** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="62ba9-173">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-174">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-175">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-175">Example</span></span>

<span data-ttu-id="62ba9-176">下列範例顯示具有兩個**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="62ba9-177">CollectionType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-178">概念結構定義語言 (CSDL 中的 **CollectionType** 元素) 指定函式參數或函式傳回型別為集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="62ba9-179">**CollectionType**元素可以是 Parameter 專案的子系，或 ReturnType (函式) 專案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="62ba9-180">您可以使用 **type** 屬性或下列其中一個子項目來指定集合的類型：</span><span class="sxs-lookup"><span data-stu-id="62ba9-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="62ba9-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-181">**CollectionType**</span></span>
-   <span data-ttu-id="62ba9-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="62ba9-182">ReferenceType</span></span>
-   <span data-ttu-id="62ba9-183">RowType</span><span class="sxs-lookup"><span data-stu-id="62ba9-183">RowType</span></span>
-   <span data-ttu-id="62ba9-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="62ba9-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-185">如果集合的型別同時以 **型** 別屬性和子專案指定，模型就不會驗證。</span><span class="sxs-lookup"><span data-stu-id="62ba9-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-186">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-186">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-187">下表說明可套用至 **CollectionType** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="62ba9-188">請注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**和定 **序** 屬性只適用于 **EDMSimpleTypes**的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="62ba9-189">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-189">Attribute Name</span></span>                                                          | <span data-ttu-id="62ba9-190">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-190">Is Required</span></span> | <span data-ttu-id="62ba9-191">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-192">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-192">**Type**</span></span>                                                                | <span data-ttu-id="62ba9-193">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-193">No</span></span>          | <span data-ttu-id="62ba9-194">集合的型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="62ba9-195">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-195">**Nullable**</span></span>                                                            | <span data-ttu-id="62ba9-196">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-196">No</span></span>          | <span data-ttu-id="62ba9-197">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="62ba9-198">> 在 CSDL v1 中，複雜型別屬性必須具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="62ba9-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="62ba9-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="62ba9-200">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-200">No</span></span>          | <span data-ttu-id="62ba9-201">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="62ba9-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="62ba9-203">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-203">No</span></span>          | <span data-ttu-id="62ba9-204">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="62ba9-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="62ba9-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="62ba9-206">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-206">No</span></span>          | <span data-ttu-id="62ba9-207">**True** 或 **False** ，取決於屬性值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="62ba9-208">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-208">**Precision**</span></span>                                                           | <span data-ttu-id="62ba9-209">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-209">No</span></span>          | <span data-ttu-id="62ba9-210">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="62ba9-211">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-211">**Scale**</span></span>                                                               | <span data-ttu-id="62ba9-212">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-212">No</span></span>          | <span data-ttu-id="62ba9-213">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-214">**SRID**</span></span>                                                                | <span data-ttu-id="62ba9-215">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-215">No</span></span>          | <span data-ttu-id="62ba9-216">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-217">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="62ba9-218">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="62ba9-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="62ba9-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-219">**Unicode**</span></span>                                                             | <span data-ttu-id="62ba9-220">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-220">No</span></span>          | <span data-ttu-id="62ba9-221">**True** 或 **False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="62ba9-222">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-222">**Collation**</span></span>                                                           | <span data-ttu-id="62ba9-223">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-223">No</span></span>          | <span data-ttu-id="62ba9-224">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="62ba9-225">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **CollectionType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="62ba9-226">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-227">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-228">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-228">Example</span></span>

<span data-ttu-id="62ba9-229">下列範例示範模型定義的函式，該函式會使用 **CollectionType** 專案來指定函式傳回 **Person** 實體類型的集合 (如) **ElementType** 屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="62ba9-230">下列範例示範模型定義函式，該函式會使用 **CollectionType** 專案來指定函式傳回資料列集合 (如 **RowType** 元素) 中所指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="62ba9-231">下列範例示範模型定義的函式，該函式會使用 **CollectionType** 專案來指定函式接受做為參數的 **部門** 實體類型集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="62ba9-232">ComplexType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-233">**ComplexType**元素定義由**EdmSimpleType**屬性或其他複雜類型組成的資料結構。</span><span class="sxs-lookup"><span data-stu-id="62ba9-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="62ba9-234">複雜類型可以是實體類型的屬性或另一個複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="62ba9-235">複雜類型與實體類型相似之處在於複雜類型會定義資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="62ba9-236">不過，複雜型別和實體類型之間還是有些重大的差異：</span><span class="sxs-lookup"><span data-stu-id="62ba9-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="62ba9-237">複雜型別不具有識別 (或索引鍵)，因此無法獨立存在。</span><span class="sxs-lookup"><span data-stu-id="62ba9-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="62ba9-238">複雜型別只能以實體類型或其他複雜型別的屬性形式存在。</span><span class="sxs-lookup"><span data-stu-id="62ba9-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="62ba9-239">複雜類型不可參與關聯。</span><span class="sxs-lookup"><span data-stu-id="62ba9-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="62ba9-240">關聯的兩個端點皆不可為複雜型別，因此不可為複雜型別定義導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="62ba9-241">雖然複雜型別的純量屬性可能每個都設為 null，但複雜型別屬性不可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="62ba9-242">**ComplexType**元素可以依照所列的順序)  (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-243">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-244">Property (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="62ba9-245">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="62ba9-246">下表說明可套用至 **ComplexType** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="62ba9-247">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="62ba9-248">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-248">Is Required</span></span> | <span data-ttu-id="62ba9-249">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-250">名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-250">Name</span></span>                                                                                                           | <span data-ttu-id="62ba9-251">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-251">Yes</span></span>         | <span data-ttu-id="62ba9-252">複雜類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-252">The name of the complex type.</span></span> <span data-ttu-id="62ba9-253">複雜類型的名稱不可與其他複雜類型、實體類型或模型範圍內之關聯的名稱相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="62ba9-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="62ba9-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="62ba9-255">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-255">No</span></span>          | <span data-ttu-id="62ba9-256">其他複雜類型的名稱是即將定義之複雜類型的基底型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="62ba9-257">> 這個屬性在 CSDL v1 中不適用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="62ba9-258">該版本不支援複雜類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="62ba9-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="62ba9-259">摘要</span><span class="sxs-lookup"><span data-stu-id="62ba9-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="62ba9-260">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-260">No</span></span>          | <span data-ttu-id="62ba9-261">**True** 或 **False** (預設值) ，視複雜型別是否為抽象型別而定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="62ba9-262">> 這個屬性在 CSDL v1 中不適用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="62ba9-263">該版本的複雜類型不可以是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="62ba9-264">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **ComplexType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="62ba9-265">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-266">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-267">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-267">Example</span></span>

<span data-ttu-id="62ba9-268">下列範例顯示具有**EdmSimpleType**屬性**StreetAddress**、 **City**、 **StateOrProvince**、 **Country**和**郵遞區號**的複雜類型**Address**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="62ba9-269">若要定義上述的複雜類型 **位址** () 為實體類型的屬性，您必須在實體類型定義中宣告屬性型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="62ba9-270">下列範例會在 (**發行者**) 的實體類型上，將**Address**屬性顯示為複雜類型：</span><span class="sxs-lookup"><span data-stu-id="62ba9-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="62ba9-271">DefiningExpression 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="62ba9-272">概念結構定義語言 (CSDL) 中的 **DefiningExpression** 元素包含定義概念模型中之函式的 Entity SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="62ba9-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="62ba9-273">基於驗證目的， **DefiningExpression** 元素可以包含任意內容。</span><span class="sxs-lookup"><span data-stu-id="62ba9-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="62ba9-274">但是，如果 **DefiningExpression** 的元素不包含有效的 Entity SQL，Entity Framework 會在執行時間擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="62ba9-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-275">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-275">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-276">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **DefiningExpression** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="62ba9-277">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-278">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-279">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-279">Example</span></span>

<span data-ttu-id="62ba9-280">下列範例會使用 **DefiningExpression** 專案來定義函式，該函式會傳回發行書籍之後的年數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="62ba9-281">**DefiningExpression**元素的內容是以 Entity SQL 撰寫。</span><span class="sxs-lookup"><span data-stu-id="62ba9-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="62ba9-282">Dependent 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="62ba9-283">概念結構定義語言 (CSDL) 中的 **相依** 元素是 ReferentialConstraint 專案的子專案，並定義參考條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="62ba9-284">**ReferentialConstraint**元素會定義類似于關係資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="62ba9-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="62ba9-285">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="62ba9-286">參考的實體類型稱為條件約束的 *主要端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="62ba9-287">參考主體端的實體類型稱為條件約束的 *相依端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="62ba9-288">**PropertyRef** 元素可用來指定參考主體端的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="62ba9-289">相依專案可以有下列子項目， (**依** 列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-290">PropertyRef (一或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="62ba9-291">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-292">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-292">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-293">下表說明可套用至 **相依** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="62ba9-294">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-294">Attribute Name</span></span> | <span data-ttu-id="62ba9-295">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-295">Is Required</span></span> | <span data-ttu-id="62ba9-296">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="62ba9-297">**角色**</span><span class="sxs-lookup"><span data-stu-id="62ba9-297">**Role**</span></span>       | <span data-ttu-id="62ba9-298">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-298">Yes</span></span>         | <span data-ttu-id="62ba9-299">位於關聯之相依端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-300">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **相依** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="62ba9-301">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-302">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-303">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-303">Example</span></span>

<span data-ttu-id="62ba9-304">下列範例顯示在**PublishedBy**關聯定義中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="62ba9-305">**Book**實體類型的**PublisherId**屬性組成參考條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="62ba9-306">Documentation 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="62ba9-307">概念結構定義語言 (CSDL) 中的 **檔** 元素可用來提供在父元素中定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="62ba9-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="62ba9-308">在 .edmx 檔案中， **當檔專案** 是專案的子系時，在 EF 設計工具的設計介面上會顯示為物件 (例如實體、關聯或屬性) 時， **檔** 元素的內容會出現在物件的 [Visual Studio **屬性** ] 視窗中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="62ba9-309">**檔**元素可以依照列出的順序， (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-310">**摘要**：父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="62ba9-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="62ba9-311">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-311">(zero or one element)</span></span>
-   <span data-ttu-id="62ba9-312">**LongDescription**：父元素的廣泛描述。</span><span class="sxs-lookup"><span data-stu-id="62ba9-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="62ba9-313">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-313">(zero or one element)</span></span>
-   <span data-ttu-id="62ba9-314">Annotation 項目.</span><span class="sxs-lookup"><span data-stu-id="62ba9-314">Annotation elements.</span></span> <span data-ttu-id="62ba9-315">(零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-316">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-316">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-317">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **檔** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="62ba9-318">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-319">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-320">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-320">Example</span></span>

<span data-ttu-id="62ba9-321">下列範例會將 **檔** 專案顯示為 EntityType 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="62ba9-322">如果下面的程式碼片段位於 .edmx 檔的 CSDL 內容中，則當您按一下實體類型時， **摘要** 和 **LongDescription** 專案的內容就會出現在 Visual Studio 的 [ **屬性** ] 視窗中 `Customer` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="62ba9-323">End 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-323">End Element (CSDL)</span></span>

<span data-ttu-id="62ba9-324">概念結構定義語言 (CSDL) 的 **End** 元素可以是 Association 元素或 AssociationSet 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="62ba9-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="62ba9-325">在每個案例中， **End** 元素的角色都不同，而且適用的屬性會不同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="62ba9-326">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="62ba9-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="62ba9-327">**End**專案 (為**association**專案的子系) 識別關聯一端的實體類型，以及可存在於關聯之一端的實體類型實例數目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="62ba9-328">關聯 End 會定義為關聯的部分。關聯必須具有兩個關聯 End。</span><span class="sxs-lookup"><span data-stu-id="62ba9-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="62ba9-329">關聯其中一端的實體型別執行個體可透過巡覽屬性或外部索引鍵來存取 (若在實體型別上公開)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="62ba9-330">**End**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-331">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-332">OnDelete (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-333">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-334">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-334">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-335">下表描述可套用至 **End** 專案的屬性（當它是 **Association** 元素的子系時）。</span><span class="sxs-lookup"><span data-stu-id="62ba9-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="62ba9-336">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-336">Attribute Name</span></span>   | <span data-ttu-id="62ba9-337">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-337">Is Required</span></span> | <span data-ttu-id="62ba9-338">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-339">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-339">**Type**</span></span>         | <span data-ttu-id="62ba9-340">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-340">Yes</span></span>         | <span data-ttu-id="62ba9-341">其中一個關聯 End 的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="62ba9-342">**角色**</span><span class="sxs-lookup"><span data-stu-id="62ba9-342">**Role**</span></span>         | <span data-ttu-id="62ba9-343">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-343">No</span></span>          | <span data-ttu-id="62ba9-344">關聯 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-344">A name for the association end.</span></span> <span data-ttu-id="62ba9-345">如果未提供任何名稱，則會使用關聯 End 上之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="62ba9-346">**多重性**</span><span class="sxs-lookup"><span data-stu-id="62ba9-346">**Multiplicity**</span></span> | <span data-ttu-id="62ba9-347">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-347">Yes</span></span>         | <span data-ttu-id="62ba9-348">**1**、 **0 或 1**，或 **\*** 視關聯的結尾可以是實體類型實例的數目而定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="62ba9-349">**1** 表示關聯 end 只存在一個實體類型實例。</span><span class="sxs-lookup"><span data-stu-id="62ba9-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="62ba9-350">**0 ..1** 表示關聯 end 有零個或一個實體類型實例。</span><span class="sxs-lookup"><span data-stu-id="62ba9-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="62ba9-351">**\*** 指出關聯 end 存在零個、一個或多個實體類型實例。</span><span class="sxs-lookup"><span data-stu-id="62ba9-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-352">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **End** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="62ba9-353">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-354">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-355">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-355">Example</span></span>

<span data-ttu-id="62ba9-356">下列範例會顯示定義 **>customerorders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="62ba9-357">關聯的每個**端點**的**多重性**值表示許多**訂單**可以與**客戶**相關聯，但只有一個**客戶**可以與**訂單**相關聯。</span><span class="sxs-lookup"><span data-stu-id="62ba9-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="62ba9-358">此外， **OnDelete**元素表示如果刪除**客戶**，則會刪除所有與特定**客戶**相關的訂單，以及已載入 ObjectCoNtext 的所有**訂單**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="62ba9-359">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="62ba9-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="62ba9-360">**End**元素會指定關聯集的一端。</span><span class="sxs-lookup"><span data-stu-id="62ba9-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="62ba9-361">**AssociationSet**元素必須包含兩個**End**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="62ba9-362">**End**元素中包含的資訊會用於將關聯集對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="62ba9-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="62ba9-363">**End**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-364">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-365">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-366">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="62ba9-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="62ba9-367">只有 CSDL v2 和更新版本中才允許注釋元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-368">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-368">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-369">下表描述可套用至 **End** 專案的屬性（當它是 **AssociationSet** 專案的子專案時）。</span><span class="sxs-lookup"><span data-stu-id="62ba9-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="62ba9-370">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-370">Attribute Name</span></span> | <span data-ttu-id="62ba9-371">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-371">Is Required</span></span> | <span data-ttu-id="62ba9-372">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="62ba9-373">**EntitySet**</span></span>  | <span data-ttu-id="62ba9-374">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-374">Yes</span></span>         | <span data-ttu-id="62ba9-375">定義父**AssociationSet**專案一端的**EntitySet**元素名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="62ba9-376">**EntitySet**元素必須在與父**AssociationSet**元素相同的實體容器中定義。</span><span class="sxs-lookup"><span data-stu-id="62ba9-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="62ba9-377">**角色**</span><span class="sxs-lookup"><span data-stu-id="62ba9-377">**Role**</span></span>       | <span data-ttu-id="62ba9-378">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-378">No</span></span>          | <span data-ttu-id="62ba9-379">關聯集 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-379">The name of the association set end.</span></span> <span data-ttu-id="62ba9-380">如果未使用 **Role** 屬性，關聯集的名稱 end 將會是實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="62ba9-381">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **End** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="62ba9-382">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-383">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-384">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-384">Example</span></span>

<span data-ttu-id="62ba9-385">下列範例顯示具有兩個**AssociationSet**專案的**EntityContainer**元素，每個元素都有兩個**End**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="62ba9-386">EntityContainer 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="62ba9-387">概念結構定義語言 (CSDL) 中的 **EntityContainer** 元素是實體集、關聯集和函式匯入的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="62ba9-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="62ba9-388">概念模型實體容器透過 EntityContainerMapping 項目對應於儲存體模型實體容器。</span><span class="sxs-lookup"><span data-stu-id="62ba9-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="62ba9-389">儲存體模型實體容器描述資料的結構：實體集描述資料表、關聯集描述外部索引建條件約束，而函式匯入則描述資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="62ba9-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="62ba9-390">**EntityContainer**元素可以有零個或一個檔元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="62ba9-391">如果有 **檔** 元素存在，它必須在所有 **EntitySet**、 **AssociationSet**和 **FunctionImport** 元素之前。</span><span class="sxs-lookup"><span data-stu-id="62ba9-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="62ba9-392">**EntityContainer**元素可以有零或多個下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="62ba9-393">EntitySet</span></span>
-   <span data-ttu-id="62ba9-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="62ba9-394">AssociationSet</span></span>
-   <span data-ttu-id="62ba9-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="62ba9-395">FunctionImport</span></span>
-   <span data-ttu-id="62ba9-396">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="62ba9-396">Annotation elements</span></span>

<span data-ttu-id="62ba9-397">您可以擴充 **EntityContainer** 元素，以包含相同命名空間內另一個 **EntityContainer** 的內容。</span><span class="sxs-lookup"><span data-stu-id="62ba9-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="62ba9-398">若要加入另一個 **entitycontainer**的內容，請在參考的 **entitycontainer** 專案中，將 [ **擴充** 屬性] 的值設定為您想要包含之 **EntityContainer** 元素的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="62ba9-399">包含之 **entitycontainer** 專案的所有子項目都會被視為參考 **EntityContainer** 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-400">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-400">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-401">下表說明可套用至 **Using** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="62ba9-402">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-402">Attribute Name</span></span> | <span data-ttu-id="62ba9-403">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-403">Is Required</span></span> | <span data-ttu-id="62ba9-404">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="62ba9-405">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-405">**Name**</span></span>       | <span data-ttu-id="62ba9-406">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-406">Yes</span></span>         | <span data-ttu-id="62ba9-407">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="62ba9-408">**延伸**</span><span class="sxs-lookup"><span data-stu-id="62ba9-408">**Extends**</span></span>    | <span data-ttu-id="62ba9-409">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-409">No</span></span>          | <span data-ttu-id="62ba9-410">相同命名空間中另一個實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-411">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntityContainer** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="62ba9-412">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-413">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-414">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-414">Example</span></span>

<span data-ttu-id="62ba9-415">下列範例顯示定義三個實體集和兩個關聯集的 **EntityContainer** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="62ba9-416">EntitySet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="62ba9-417">概念結構定義語言中的 **EntitySet** 元素是實體類型實例的邏輯容器，以及衍生自該實體類型之任何類型的實例。</span><span class="sxs-lookup"><span data-stu-id="62ba9-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="62ba9-418">實體類型和實體集之間的關聯性，類似於關聯式資料庫中資料列與資料表的關係。</span><span class="sxs-lookup"><span data-stu-id="62ba9-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="62ba9-419">實體類型和資料列一樣可以定義相關的資料集，而實體集則和資料表一樣可以包含該定義的執行個體。</span><span class="sxs-lookup"><span data-stu-id="62ba9-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="62ba9-420">實體集提供群組實體類型執行個體的建構，以便將它們對應至資料來源中的相關資料結構。</span><span class="sxs-lookup"><span data-stu-id="62ba9-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="62ba9-421">您可以為特定的實體類型定義多個實體集。</span><span class="sxs-lookup"><span data-stu-id="62ba9-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-422">EF 設計工具不支援包含每個類型之多個實體集的概念模型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="62ba9-423">**EntitySet**元素可依所列的順序)  (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-424">Documentation 項目 (允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-425">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-426">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-426">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-427">下表說明可套用至 **EntitySet** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="62ba9-428">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-428">Attribute Name</span></span> | <span data-ttu-id="62ba9-429">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-429">Is Required</span></span> | <span data-ttu-id="62ba9-430">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-431">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-431">**Name**</span></span>       | <span data-ttu-id="62ba9-432">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-432">Yes</span></span>         | <span data-ttu-id="62ba9-433">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="62ba9-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-434">**EntityType**</span></span> | <span data-ttu-id="62ba9-435">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-435">Yes</span></span>         | <span data-ttu-id="62ba9-436">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-437">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntitySet** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="62ba9-438">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-439">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-440">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-440">Example</span></span>

<span data-ttu-id="62ba9-441">下列範例顯示具有三個**EntitySet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="62ba9-442">您可以定義每個類型的多重實體集 (MEST)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="62ba9-443">下列範例會針對 **Book** 實體類型定義具有兩個實體集的實體容器：</span><span class="sxs-lookup"><span data-stu-id="62ba9-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="62ba9-444">EntityType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-445">**EntityType**元素代表概念模型中最上層概念的結構，例如客戶或訂單。</span><span class="sxs-lookup"><span data-stu-id="62ba9-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="62ba9-446">實體類型是應用程式中實體類型之執行個體的範本。</span><span class="sxs-lookup"><span data-stu-id="62ba9-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="62ba9-447">每個範本包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="62ba9-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="62ba9-448">唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-448">A unique name.</span></span> <span data-ttu-id="62ba9-449">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="62ba9-449">(Required.)</span></span>
-   <span data-ttu-id="62ba9-450">實體索引鍵是由一個或多個屬性定義。</span><span class="sxs-lookup"><span data-stu-id="62ba9-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="62ba9-451">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="62ba9-451">(Required.)</span></span>
-   <span data-ttu-id="62ba9-452">包含資料的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-452">Properties for containing data.</span></span> <span data-ttu-id="62ba9-453">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="62ba9-453">(Optional.)</span></span>
-   <span data-ttu-id="62ba9-454">導覽屬性允許從關聯的一端巡覽至另一端。</span><span class="sxs-lookup"><span data-stu-id="62ba9-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="62ba9-455">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="62ba9-455">(Optional.)</span></span>

<span data-ttu-id="62ba9-456">在應用程式中，實體類型的執行個體代表特定的物件 (例如特定的客戶或訂單)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="62ba9-457">實體類型的每一個執行個體都必須在實體集中有唯一的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="62ba9-458">如果兩個實體類型執行個體屬於相同類型，而且索引鍵的值也相同，則會將這兩個執行個體視為相等。</span><span class="sxs-lookup"><span data-stu-id="62ba9-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="62ba9-459">**EntityType**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-460">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-461">Key(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-462">Property (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="62ba9-463">NavigationProperty (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="62ba9-464">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-465">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-465">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-466">下表說明可套用至 **EntityType** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="62ba9-467">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="62ba9-468">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-468">Is Required</span></span> | <span data-ttu-id="62ba9-469">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-470">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="62ba9-471">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-471">Yes</span></span>         | <span data-ttu-id="62ba9-472">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="62ba9-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="62ba9-474">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-474">No</span></span>          | <span data-ttu-id="62ba9-475">另一個實體類型的名稱，而此實體類型是要定義之實體類型的基底類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="62ba9-476">**抽象**</span><span class="sxs-lookup"><span data-stu-id="62ba9-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="62ba9-477">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-477">No</span></span>          | <span data-ttu-id="62ba9-478">**True** 或 **False**，取決於實體型別是否為抽象型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="62ba9-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="62ba9-480">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-480">No</span></span>          | <span data-ttu-id="62ba9-481">**True** 或 **False** ，取決於實體型別是否為開放式實體型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="62ba9-482">> **OpenType** 屬性只適用于概念模型中定義的實體類型，這些模型會與 ADO.NET 資料服務搭配使用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="62ba9-483">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntityType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="62ba9-484">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-485">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-486">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-486">Example</span></span>

<span data-ttu-id="62ba9-487">下列範例顯示具有三個**Property**元素和兩個**NavigationProperty**元素的**EntityType**專案：</span><span class="sxs-lookup"><span data-stu-id="62ba9-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="62ba9-488">CSDL)  (EnumType 元素</span><span class="sxs-lookup"><span data-stu-id="62ba9-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-489">**EnumType**元素代表列舉型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="62ba9-490">**EnumType**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-491">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-492">成員 (零或多個元素) </span><span class="sxs-lookup"><span data-stu-id="62ba9-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="62ba9-493">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-494">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-494">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-495">下表說明可套用至 **EnumType** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="62ba9-496">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-496">Attribute Name</span></span>     | <span data-ttu-id="62ba9-497">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-497">Is Required</span></span> | <span data-ttu-id="62ba9-498">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-499">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-499">**Name**</span></span>           | <span data-ttu-id="62ba9-500">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-500">Yes</span></span>         | <span data-ttu-id="62ba9-501">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="62ba9-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="62ba9-502">**IsFlags**</span></span>        | <span data-ttu-id="62ba9-503">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-503">No</span></span>          | <span data-ttu-id="62ba9-504">**True** 或 **False**，取決於列舉型別是否可以當做一組旗標使用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="62ba9-505">預設值為**False。**</span><span class="sxs-lookup"><span data-stu-id="62ba9-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="62ba9-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-506">**UnderlyingType**</span></span> | <span data-ttu-id="62ba9-507">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-507">No</span></span>          | <span data-ttu-id="62ba9-508">**Edm**、Edm、 **Int16**、 **edm**、 **edm** 或 **edm。 SByte** 定義型別的值範圍。</span><span class="sxs-lookup"><span data-stu-id="62ba9-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="62ba9-509">列舉元素的預設基礎類型為 **Edm**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-510">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EnumType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="62ba9-511">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-512">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-513">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-513">Example</span></span>

<span data-ttu-id="62ba9-514">下列範例顯示具有三個**成員**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="62ba9-515">Function 屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-515">Function Element (CSDL)</span></span>

<span data-ttu-id="62ba9-516">概念結構定義語言 (CSDL) 中的 **Function** 元素用來定義或宣告概念模型中的函式。</span><span class="sxs-lookup"><span data-stu-id="62ba9-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="62ba9-517">函式是使用 DefiningExpression 項目 來定義的。</span><span class="sxs-lookup"><span data-stu-id="62ba9-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="62ba9-518">函式專案可以有下列 **子項目，** (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-519">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-520">Parameter (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="62ba9-521">DefiningExpression (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-522">ReturnType (函式)  (零或一個元素) </span><span class="sxs-lookup"><span data-stu-id="62ba9-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-523">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="62ba9-524">函式的傳回型別必須以 **returntype** (函式) 元素或 **returntype** 屬性指定 (請參閱下文) ，但不能同時使用這兩者。</span><span class="sxs-lookup"><span data-stu-id="62ba9-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="62ba9-525">可能的傳回型別包括任何 EdmSimpleType、實體類型、複雜類型、資料列型別或 ref 型別 (或這些類型其中之一的集合)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-526">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-526">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-527">下表說明可套用至 **Function** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="62ba9-528">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-528">Attribute Name</span></span> | <span data-ttu-id="62ba9-529">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-529">Is Required</span></span> | <span data-ttu-id="62ba9-530">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="62ba9-531">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-531">**Name**</span></span>       | <span data-ttu-id="62ba9-532">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-532">Yes</span></span>         | <span data-ttu-id="62ba9-533">函式的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-533">The name of the function.</span></span>          |
| <span data-ttu-id="62ba9-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-534">**ReturnType**</span></span> | <span data-ttu-id="62ba9-535">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-535">No</span></span>          | <span data-ttu-id="62ba9-536">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-537">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Function** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="62ba9-538">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-539">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-540">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-540">Example</span></span>

<span data-ttu-id="62ba9-541">下列範例會使用 **function** 元素來定義函式，該函式會傳回在講師雇用之後的年數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="62ba9-542">FunctionImport 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="62ba9-543">概念結構定義語言中的 **FunctionImport** 元素 (CSDL) 代表資料來源中定義的函式，但可透過概念模型取得物件。</span><span class="sxs-lookup"><span data-stu-id="62ba9-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="62ba9-544">例如，儲存體模型中的 Function 項目 可以用來代表資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="62ba9-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="62ba9-545">概念模型中的 **FunctionImport** 元素代表 Entity Framework 應用程式中對應的函式，並使用 FunctionImportMapping 專案對應至儲存體模型函數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="62ba9-546">在應用程式中呼叫函式時，對應的預存程序會在資料庫中執行。</span><span class="sxs-lookup"><span data-stu-id="62ba9-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="62ba9-547">**FunctionImport**元素可依所列的順序)  (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-548">Documentation (可允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-549">Parameter (可允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="62ba9-550">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="62ba9-551">ReturnType (FunctionImport)  (零或多個允許的元素) </span><span class="sxs-lookup"><span data-stu-id="62ba9-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="62ba9-552">應針對函式所接受的每個參數定義一個 **參數** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="62ba9-553">函式的傳回型別必須以 **returntype** (FunctionImport) 元素或 **returntype** 屬性指定 (請參閱下文) ，但不能同時使用這兩者。</span><span class="sxs-lookup"><span data-stu-id="62ba9-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="62ba9-554">傳回型別值必須是 EdmSimpleType、EntityType 或 ComplexType 的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-555">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-555">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-556">下表說明可套用至 **FunctionImport** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="62ba9-557">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-557">Attribute Name</span></span>   | <span data-ttu-id="62ba9-558">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-558">Is Required</span></span> | <span data-ttu-id="62ba9-559">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-560">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-560">**Name**</span></span>         | <span data-ttu-id="62ba9-561">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-561">Yes</span></span>         | <span data-ttu-id="62ba9-562">匯入函式的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="62ba9-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-563">**ReturnType**</span></span>   | <span data-ttu-id="62ba9-564">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-564">No</span></span>          | <span data-ttu-id="62ba9-565">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-565">The type that the function returns.</span></span> <span data-ttu-id="62ba9-566">如果函數沒有傳回值，請勿使用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="62ba9-567">否則，此值必須是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="62ba9-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="62ba9-568">**EntitySet**</span></span>    | <span data-ttu-id="62ba9-569">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-569">No</span></span>          | <span data-ttu-id="62ba9-570">如果函式傳回實體類型的集合，則 **EntitySet** 的值必須是集合所屬的實體集。</span><span class="sxs-lookup"><span data-stu-id="62ba9-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="62ba9-571">否則，不得使用 **EntitySet** 屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="62ba9-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="62ba9-572">**IsComposable**</span></span> | <span data-ttu-id="62ba9-573">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-573">No</span></span>          | <span data-ttu-id="62ba9-574">如果值設定為 true，則函數可組合 (資料表值函式) ，而且可以在 LINQ 查詢中使用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="62ba9-575">預設值為 **false**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="62ba9-576">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **FunctionImport** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="62ba9-577">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-578">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-579">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-579">Example</span></span>

<span data-ttu-id="62ba9-580">下列範例會顯示一個 **FunctionImport** 元素，此專案會接受一個參數並傳回實體類型的集合：</span><span class="sxs-lookup"><span data-stu-id="62ba9-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="62ba9-581">Key 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-581">Key Element (CSDL)</span></span>

<span data-ttu-id="62ba9-582">**Key**專案是 EntityType 專案的子專案，可定義*實體索引鍵* (屬性或實體類型的一組屬性，以決定身分識別) 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="62ba9-583">構成實體索引鍵的屬性是在設計階段選取的。</span><span class="sxs-lookup"><span data-stu-id="62ba9-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="62ba9-584">實體索引鍵屬性的值必須在執行階段的實體集中，單獨識別實體類型執行個體。</span><span class="sxs-lookup"><span data-stu-id="62ba9-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="62ba9-585">您應選取構成實體索引鍵的屬性，以保證執行個體在實體集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="62ba9-586">**Key**元素會藉由參考實體類型的一或多個屬性來定義實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="62ba9-587">**Key**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="62ba9-588">PropertyRef (一或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="62ba9-589">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-590">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-590">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-591">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至索引 **鍵** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="62ba9-592">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-593">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-594">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-594">Example</span></span>

<span data-ttu-id="62ba9-595">下列範例會定義名為 **Book**的實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="62ba9-596">實體索引鍵是透過參考實體類型的 **ISBN** 屬性來定義。</span><span class="sxs-lookup"><span data-stu-id="62ba9-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="62ba9-597">**Isbn**屬性是實體索引鍵的理想選擇，因為國際標準書籍號碼 (ISBN) 可唯一識別書籍。</span><span class="sxs-lookup"><span data-stu-id="62ba9-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="62ba9-598">下列範例顯示 (**作者**) 的實體類型，此實體索引鍵包含兩個屬性： **名稱** 和 **位址**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="62ba9-599">使用實體索引鍵的 **名稱** 和 **位址** 是合理的選擇，因為兩個相同名稱的作者不太可能存留在相同的位址。</span><span class="sxs-lookup"><span data-stu-id="62ba9-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="62ba9-600">不過，針對實體索引鍵所做的這個選擇不能絕對保證實體集中的唯一實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="62ba9-601">在此情況下，建議您加入可用於唯一識別作者的屬性，**例如作者。**</span><span class="sxs-lookup"><span data-stu-id="62ba9-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="62ba9-602">CSDL)  (成員元素</span><span class="sxs-lookup"><span data-stu-id="62ba9-602">Member Element (CSDL)</span></span>

<span data-ttu-id="62ba9-603">**Member**元素是 EnumType 專案的子專案，並定義列舉型別的成員。</span><span class="sxs-lookup"><span data-stu-id="62ba9-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-604">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-604">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-605">下表說明可套用至 **FunctionImport** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="62ba9-606">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-606">Attribute Name</span></span> | <span data-ttu-id="62ba9-607">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-607">Is Required</span></span> | <span data-ttu-id="62ba9-608">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-609">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-609">**Name**</span></span>       | <span data-ttu-id="62ba9-610">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-610">Yes</span></span>         | <span data-ttu-id="62ba9-611">成員的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="62ba9-612">**ReplTest1**</span><span class="sxs-lookup"><span data-stu-id="62ba9-612">**Value**</span></span>      | <span data-ttu-id="62ba9-613">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-613">No</span></span>          | <span data-ttu-id="62ba9-614">成員的值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-614">The value of the member.</span></span> <span data-ttu-id="62ba9-615">依預設，第一個成員的值為0，而每個後續列舉值的值會遞增1。</span><span class="sxs-lookup"><span data-stu-id="62ba9-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="62ba9-616">有多個具有相同值的成員可以存在。</span><span class="sxs-lookup"><span data-stu-id="62ba9-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-617">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **FunctionImport** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="62ba9-618">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-619">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-620">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-620">Example</span></span>

<span data-ttu-id="62ba9-621">下列範例顯示具有三個**成員**元素的**EnumType**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="62ba9-622">NavigationProperty 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="62ba9-623">**NavigationProperty**元素會定義導覽屬性，以提供關聯另一端的參考。</span><span class="sxs-lookup"><span data-stu-id="62ba9-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="62ba9-624">與使用 Property 項目 定義的屬性不同，導覽屬性不會定義資料的圖案和特性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="62ba9-625">他們提供巡覽兩個實體類型間之關聯的方式。</span><span class="sxs-lookup"><span data-stu-id="62ba9-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="62ba9-626">請注意，在關聯各端點的實體類型上，導覽屬性是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="62ba9-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="62ba9-627">如果您在關聯其中一個端點的實體類型上定義導覽屬性，就不必在關聯另一個端點的實體類型上定義導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="62ba9-628">導覽屬性傳回的資料類型是由其遠端關聯端點的多重性所判斷。</span><span class="sxs-lookup"><span data-stu-id="62ba9-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="62ba9-629">例如，假設有一個導覽屬性 **OrdersNavProp**存在於 **customer** 實體型別上，並且導覽 **customer** 和 **Order**之間的一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="62ba9-630">因為導覽屬性的遠端關聯 end 具有多重性許多 (\*) ，所以其資料類型是 **Order**)  (的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="62ba9-631">同樣地，如果 **CustomerNavProp**的導覽屬性存在於 **Order** 實體型別上，其資料類型就會是 **Customer** ，因為遠端 end 的多重性是一個 (1) 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="62ba9-632">**NavigationProperty**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-633">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-634">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-635">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-635">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-636">下表說明可套用至 **NavigationProperty** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="62ba9-637">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-637">Attribute Name</span></span>   | <span data-ttu-id="62ba9-638">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-638">Is Required</span></span> | <span data-ttu-id="62ba9-639">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-640">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-640">**Name**</span></span>         | <span data-ttu-id="62ba9-641">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-641">Yes</span></span>         | <span data-ttu-id="62ba9-642">導覽屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="62ba9-643">**關聯性**</span><span class="sxs-lookup"><span data-stu-id="62ba9-643">**Relationship**</span></span> | <span data-ttu-id="62ba9-644">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-644">Yes</span></span>         | <span data-ttu-id="62ba9-645">關聯的名稱在模型的範圍之內。</span><span class="sxs-lookup"><span data-stu-id="62ba9-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="62ba9-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="62ba9-646">**ToRole**</span></span>       | <span data-ttu-id="62ba9-647">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-647">Yes</span></span>         | <span data-ttu-id="62ba9-648">位於導覽端點的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="62ba9-649">**ToRole**屬性的值必須與 AssociationEnd 元素) 中定義的其中一個關聯 end (定義的其中一個**角色**屬性值相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="62ba9-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="62ba9-650">**FromRole**</span></span>     | <span data-ttu-id="62ba9-651">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-651">Yes</span></span>         | <span data-ttu-id="62ba9-652">開始導覽的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="62ba9-653">**FromRole**屬性的值必須與 AssociationEnd 元素) 中定義的其中一個關聯 end (定義的其中一個**角色**屬性值相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-654">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **NavigationProperty** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="62ba9-655">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-656">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-657">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-657">Example</span></span>

<span data-ttu-id="62ba9-658">下列範例會使用兩個導覽屬性（ (**PublishedBy**和**WrittenBy**) ）來定義實體類型 (**Book**) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="62ba9-659">OnDelete 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="62ba9-660">概念結構定義語言 (CSDL) 中的 **OnDelete** 元素會定義與關聯連接的行為。</span><span class="sxs-lookup"><span data-stu-id="62ba9-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="62ba9-661">如果 **Action** 屬性在關聯的一端設定為 **Cascade** ，則在刪除第一端的實體類型時，會刪除關聯另一端上的相關實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="62ba9-662">如果兩個實體類型之間的關聯是主鍵與主鍵之間的關聯性，則不論 **OnDelete** 規格為何，當關聯的另一端上的主體物件都被刪除時，都會刪除載入的相依物件。</span><span class="sxs-lookup"><span data-stu-id="62ba9-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="62ba9-663">**OnDelete**元素只會影響應用程式的執行時間行為;它不會影響資料來源中的行為。</span><span class="sxs-lookup"><span data-stu-id="62ba9-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="62ba9-664">資料來源中定義的行為應與應用程式中定義的行為相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="62ba9-665">**OnDelete**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-666">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-667">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-668">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-668">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-669">下表說明可套用至 **OnDelete** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="62ba9-670">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-670">Attribute Name</span></span> | <span data-ttu-id="62ba9-671">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-671">Is Required</span></span> | <span data-ttu-id="62ba9-672">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-673">**動作**</span><span class="sxs-lookup"><span data-stu-id="62ba9-673">**Action**</span></span>     | <span data-ttu-id="62ba9-674">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-674">Yes</span></span>         | <span data-ttu-id="62ba9-675">**Cascade**串聯或**無**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-675">**Cascade** or **None**.</span></span> <span data-ttu-id="62ba9-676">如果 **Cascade**，則在刪除主體實體類型時，將會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="62ba9-677">如果 **沒有**，當刪除主體實體類型時，不會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-678">您可以將任何數目的注釋屬性 (自訂 XML 屬性，) 可以套用至 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="62ba9-679">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-680">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-681">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-681">Example</span></span>

<span data-ttu-id="62ba9-682">下列範例會顯示定義 **>customerorders**關聯的**association**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="62ba9-683">**OnDelete**元素指出當刪除**客戶**時，會刪除與特定**客戶**相關且已載入 ObjectCoNtext 的所有**訂單**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="62ba9-684">Parameter 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="62ba9-685">概念結構定義語言 (CSDL) 中的 **參數** 專案可以是 FunctionImport 元素或 Function 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="62ba9-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="62ba9-686">FunctionImport 項目的應用</span><span class="sxs-lookup"><span data-stu-id="62ba9-686">FunctionImport Element Application</span></span>

<span data-ttu-id="62ba9-687">**參數**專案 (為**FunctionImport**元素的子系) 用來定義 CSDL 中宣告之函式匯入的輸入和輸出參數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="62ba9-688">**Parameter**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-689">Documentation (可允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-690">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-691">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-691">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-692">下表描述可套用至 **Parameter** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="62ba9-693">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-693">Attribute Name</span></span> | <span data-ttu-id="62ba9-694">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-694">Is Required</span></span> | <span data-ttu-id="62ba9-695">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-696">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-696">**Name**</span></span>       | <span data-ttu-id="62ba9-697">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-697">Yes</span></span>         | <span data-ttu-id="62ba9-698">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="62ba9-699">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-699">**Type**</span></span>       | <span data-ttu-id="62ba9-700">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-700">Yes</span></span>         | <span data-ttu-id="62ba9-701">參數類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-701">The parameter type.</span></span> <span data-ttu-id="62ba9-702">此值必須是 **EDMSimpleType** 或是模型範圍內的複雜類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="62ba9-703">**Mode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-703">**Mode**</span></span>       | <span data-ttu-id="62ba9-704">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-704">No</span></span>          | <span data-ttu-id="62ba9-705">**In**、 **Out**或 **InOut** ，取決於參數是輸入、輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="62ba9-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-706">**MaxLength**</span></span>  | <span data-ttu-id="62ba9-707">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-707">No</span></span>          | <span data-ttu-id="62ba9-708">允許的參數長度上限。</span><span class="sxs-lookup"><span data-stu-id="62ba9-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="62ba9-709">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-709">**Precision**</span></span>  | <span data-ttu-id="62ba9-710">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-710">No</span></span>          | <span data-ttu-id="62ba9-711">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="62ba9-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-712">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-712">**Scale**</span></span>      | <span data-ttu-id="62ba9-713">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-713">No</span></span>          | <span data-ttu-id="62ba9-714">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="62ba9-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-715">**SRID**</span></span>       | <span data-ttu-id="62ba9-716">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-716">No</span></span>          | <span data-ttu-id="62ba9-717">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-718">只對空間類型的參數有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="62ba9-719">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-720">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Parameter** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="62ba9-721">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-722">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-723">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-723">Example</span></span>

<span data-ttu-id="62ba9-724">下列範例顯示具有一個**參數**子項目的**FunctionImport**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="62ba9-725">函式接受一個輸入參數，然後傳回實體類型的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="62ba9-726">Function 項目的應用</span><span class="sxs-lookup"><span data-stu-id="62ba9-726">Function Element Application</span></span>

<span data-ttu-id="62ba9-727">**參數**專案 (為函式**專案的子**系) 定義在概念模型中定義或宣告之函式的參數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="62ba9-728">**Parameter**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-729">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="62ba9-730">CollectionType (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="62ba9-731">ReferenceType (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="62ba9-732">RowType (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-733">只有其中一個 **CollectionType**、 **ReferenceType**或 **RowType** 專案可以是 **Property** 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="62ba9-734">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-735">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="62ba9-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="62ba9-736">只有 CSDL v2 和更新版本中才允許注釋元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-737">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-737">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-738">下表描述可套用至 **Parameter** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="62ba9-739">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-739">Attribute Name</span></span>   | <span data-ttu-id="62ba9-740">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-740">Is Required</span></span> | <span data-ttu-id="62ba9-741">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-742">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-742">**Name**</span></span>         | <span data-ttu-id="62ba9-743">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-743">Yes</span></span>         | <span data-ttu-id="62ba9-744">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="62ba9-745">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-745">**Type**</span></span>         | <span data-ttu-id="62ba9-746">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-746">No</span></span>          | <span data-ttu-id="62ba9-747">參數類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-747">The parameter type.</span></span> <span data-ttu-id="62ba9-748">參數可以是下列任何一種型別 (或這些型別的集合)：</span><span class="sxs-lookup"><span data-stu-id="62ba9-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="62ba9-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="62ba9-750">Entity Type - 實體類型</span><span class="sxs-lookup"><span data-stu-id="62ba9-750">entity type</span></span> <br/> <span data-ttu-id="62ba9-751">複雜類型</span><span class="sxs-lookup"><span data-stu-id="62ba9-751">complex type</span></span> <br/> <span data-ttu-id="62ba9-752">資料列型別</span><span class="sxs-lookup"><span data-stu-id="62ba9-752">row type</span></span> <br/> <span data-ttu-id="62ba9-753">參考類型</span><span class="sxs-lookup"><span data-stu-id="62ba9-753">reference type</span></span>                             |
| <span data-ttu-id="62ba9-754">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-754">**Nullable**</span></span>     | <span data-ttu-id="62ba9-755">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-755">No</span></span>          | <span data-ttu-id="62ba9-756">**True** (預設值) 或 **False** ，取決於屬性是否可以有 **null** 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="62ba9-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="62ba9-757">**DefaultValue**</span></span> | <span data-ttu-id="62ba9-758">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-758">No</span></span>          | <span data-ttu-id="62ba9-759">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="62ba9-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-760">**MaxLength**</span></span>    | <span data-ttu-id="62ba9-761">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-761">No</span></span>          | <span data-ttu-id="62ba9-762">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="62ba9-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-763">**FixedLength**</span></span>  | <span data-ttu-id="62ba9-764">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-764">No</span></span>          | <span data-ttu-id="62ba9-765">**True** 或 **False** ，取決於屬性值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="62ba9-766">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-766">**Precision**</span></span>    | <span data-ttu-id="62ba9-767">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-767">No</span></span>          | <span data-ttu-id="62ba9-768">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="62ba9-769">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-769">**Scale**</span></span>        | <span data-ttu-id="62ba9-770">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-770">No</span></span>          | <span data-ttu-id="62ba9-771">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="62ba9-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-772">**SRID**</span></span>         | <span data-ttu-id="62ba9-773">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-773">No</span></span>          | <span data-ttu-id="62ba9-774">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-775">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="62ba9-776">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="62ba9-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-777">**Unicode**</span></span>      | <span data-ttu-id="62ba9-778">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-778">No</span></span>          | <span data-ttu-id="62ba9-779">**True** 或 **False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="62ba9-780">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-780">**Collation**</span></span>    | <span data-ttu-id="62ba9-781">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-781">No</span></span>          | <span data-ttu-id="62ba9-782">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="62ba9-783">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Parameter** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="62ba9-784">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-785">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-786">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-786">Example</span></span>

<span data-ttu-id="62ba9-787">下列範例 **顯示的函** 式專案會使用一個 **參數** 子項目來定義函數參數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="62ba9-788">Principal 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="62ba9-789">概念結構定義語言 (CSDL) 中的 **主體** 專案是 ReferentialConstraint 專案的子專案，可定義參考條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="62ba9-790">**ReferentialConstraint**元素會定義類似于關係資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="62ba9-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="62ba9-791">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="62ba9-792">參考的實體類型稱為條件約束的 *主要端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="62ba9-793">參考主體端的實體類型稱為條件約束的 *相依端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="62ba9-794">**PropertyRef** 元素可用來指定相依端所參考的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="62ba9-795">**主體**專案可以依) 列出的順序 (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-796">PropertyRef (一或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="62ba9-797">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-798">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-798">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-799">下表說明可套用至 **主體** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="62ba9-800">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-800">Attribute Name</span></span> | <span data-ttu-id="62ba9-801">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-801">Is Required</span></span> | <span data-ttu-id="62ba9-802">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="62ba9-803">**角色**</span><span class="sxs-lookup"><span data-stu-id="62ba9-803">**Role**</span></span>       | <span data-ttu-id="62ba9-804">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-804">Yes</span></span>         | <span data-ttu-id="62ba9-805">位於關聯之主要端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-806">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **主體** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="62ba9-807">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-808">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-809">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-809">Example</span></span>

<span data-ttu-id="62ba9-810">下列範例顯示 **ReferentialConstraint** 元素，此專案屬於 **PublishedBy** 關聯的定義一部分。</span><span class="sxs-lookup"><span data-stu-id="62ba9-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="62ba9-811">**發行者**實體類型的**Id**屬性組成參考條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="62ba9-812">Property 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-812">Property Element (CSDL)</span></span>

<span data-ttu-id="62ba9-813">概念結構定義語言 (CSDL) 中的 **Property** 元素可以是 EntityType 元素、ComplexType 專案或 RowType 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="62ba9-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="62ba9-814">EntityType 和 ComplexType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="62ba9-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="62ba9-815">**屬性** 元素 (為 **EntityType** 或 **ComplexType** 專案的子系) 定義實體類型實例或複雜類型實例將包含之資料的形狀和特性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="62ba9-816">概念模型中的屬性類似類別中定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="62ba9-817">如同類別上的屬性可定義類別的圖形並包含關於物件的資訊，概念模型的屬性可定義實體類別的圖形，並包含關於實體類型執行個體的資訊。</span><span class="sxs-lookup"><span data-stu-id="62ba9-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="62ba9-818">**Property**專案可以有下列子項目， (依照列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-819">Documentation 項目 (允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-820">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="62ba9-821">下列 facet 可以套用至 **Property** 元素： **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、定 **序**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="62ba9-822">Facet 是 XML 屬性 (attribute)，提供關於屬性 (property) 值如何儲存在資料存放區資訊。</span><span class="sxs-lookup"><span data-stu-id="62ba9-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-823">Facet 只能套用至 **EDMSimpleType**類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-824">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-824">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-825">下表描述可套用至 **Property** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="62ba9-826">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-826">Attribute Name</span></span>                                                         | <span data-ttu-id="62ba9-827">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-827">Is Required</span></span> | <span data-ttu-id="62ba9-828">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-829">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-829">**Name**</span></span>                                                               | <span data-ttu-id="62ba9-830">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-830">Yes</span></span>         | <span data-ttu-id="62ba9-831">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-832">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-832">**Type**</span></span>                                                               | <span data-ttu-id="62ba9-833">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-833">Yes</span></span>         | <span data-ttu-id="62ba9-834">屬性值的類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-834">The type of the property value.</span></span> <span data-ttu-id="62ba9-835">屬性值類型必須是 **EDMSimpleType** ，或是模型範圍內的複雜類型 (以完整名稱表示)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="62ba9-836">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-836">**Nullable**</span></span>                                                           | <span data-ttu-id="62ba9-837">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-837">No</span></span>          | <span data-ttu-id="62ba9-838">**True** (預設值) 或 <strong>False</strong>，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="62ba9-839">CSDL v1 中的 >，複雜型別屬性必須具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="62ba9-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="62ba9-841">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-841">No</span></span>          | <span data-ttu-id="62ba9-842">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="62ba9-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="62ba9-844">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-844">No</span></span>          | <span data-ttu-id="62ba9-845">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="62ba9-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="62ba9-847">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-847">No</span></span>          | <span data-ttu-id="62ba9-848">**True** 或 **False** ，取決於屬性值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="62ba9-849">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-849">**Precision**</span></span>                                                          | <span data-ttu-id="62ba9-850">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-850">No</span></span>          | <span data-ttu-id="62ba9-851">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="62ba9-852">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-852">**Scale**</span></span>                                                              | <span data-ttu-id="62ba9-853">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-853">No</span></span>          | <span data-ttu-id="62ba9-854">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="62ba9-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-855">**SRID**</span></span>                                                               | <span data-ttu-id="62ba9-856">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-856">No</span></span>          | <span data-ttu-id="62ba9-857">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-858">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="62ba9-859">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="62ba9-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-860">**Unicode**</span></span>                                                            | <span data-ttu-id="62ba9-861">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-861">No</span></span>          | <span data-ttu-id="62ba9-862">**True** 或 **False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="62ba9-863">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-863">**Collation**</span></span>                                                          | <span data-ttu-id="62ba9-864">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-864">No</span></span>          | <span data-ttu-id="62ba9-865">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="62ba9-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="62ba9-867">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-867">No</span></span>          | <span data-ttu-id="62ba9-868">**None** (預設值) 或 **Fixed**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="62ba9-869">如果值設為 **Fixed**，則屬性值將用於開放式並行存取檢查中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="62ba9-870">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Property** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="62ba9-871">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-872">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-873">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-873">Example</span></span>

<span data-ttu-id="62ba9-874">下列範例顯示具有三個**Property**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="62ba9-875">下列範例顯示具有五個**屬性**元素的**ComplexType**元素：</span><span class="sxs-lookup"><span data-stu-id="62ba9-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="62ba9-876">RowType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="62ba9-876">RowType Element Application</span></span>

<span data-ttu-id="62ba9-877">**屬性** 專案 (為 **RowType** 專案的子系) 定義可傳遞至模型定義函數或從模型定義函數傳回之資料的圖形和特性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="62ba9-878">**Property**元素只能有下列其中一個子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="62ba9-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="62ba9-879">CollectionType</span></span>
-   <span data-ttu-id="62ba9-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="62ba9-880">ReferenceType</span></span>
-   <span data-ttu-id="62ba9-881">RowType</span><span class="sxs-lookup"><span data-stu-id="62ba9-881">RowType</span></span>

<span data-ttu-id="62ba9-882">**屬性**元素可以有任意數目的子批註元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-883">只有 CSDL v2 和更新版本中才允許注釋元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-884">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-884">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-885">下表描述可套用至 **Property** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="62ba9-886">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-886">Attribute Name</span></span>                                                     | <span data-ttu-id="62ba9-887">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-887">Is Required</span></span> | <span data-ttu-id="62ba9-888">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-889">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-889">**Name**</span></span>                                                           | <span data-ttu-id="62ba9-890">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-890">Yes</span></span>         | <span data-ttu-id="62ba9-891">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-892">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-892">**Type**</span></span>                                                           | <span data-ttu-id="62ba9-893">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-893">Yes</span></span>         | <span data-ttu-id="62ba9-894">屬性值的類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-895">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-895">**Nullable**</span></span>                                                       | <span data-ttu-id="62ba9-896">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-896">No</span></span>          | <span data-ttu-id="62ba9-897">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="62ba9-898">CSDL v1 中的 >，複雜型別屬性必須具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="62ba9-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="62ba9-900">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-900">No</span></span>          | <span data-ttu-id="62ba9-901">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="62ba9-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="62ba9-903">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-903">No</span></span>          | <span data-ttu-id="62ba9-904">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="62ba9-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="62ba9-906">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-906">No</span></span>          | <span data-ttu-id="62ba9-907">**True** 或 **False** ，取決於屬性值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="62ba9-908">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-908">**Precision**</span></span>                                                      | <span data-ttu-id="62ba9-909">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-909">No</span></span>          | <span data-ttu-id="62ba9-910">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="62ba9-911">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-911">**Scale**</span></span>                                                          | <span data-ttu-id="62ba9-912">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-912">No</span></span>          | <span data-ttu-id="62ba9-913">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="62ba9-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-914">**SRID**</span></span>                                                           | <span data-ttu-id="62ba9-915">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-915">No</span></span>          | <span data-ttu-id="62ba9-916">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-917">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="62ba9-918">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="62ba9-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-919">**Unicode**</span></span>                                                        | <span data-ttu-id="62ba9-920">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-920">No</span></span>          | <span data-ttu-id="62ba9-921">**True** 或 **False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="62ba9-922">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-922">**Collation**</span></span>                                                      | <span data-ttu-id="62ba9-923">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-923">No</span></span>          | <span data-ttu-id="62ba9-924">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="62ba9-925">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Property** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="62ba9-926">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-927">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="62ba9-928">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-928">Example</span></span>

<span data-ttu-id="62ba9-929">下列範例顯示用來定義模型定義函式之傳回型別圖形的 **屬性** 專案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="62ba9-930">PropertyRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="62ba9-931">概念結構定義語言 (CSDL 中的 **PropertyRef** 專案) 參考實體類型的屬性，以表示屬性將執行下列其中一個角色：</span><span class="sxs-lookup"><span data-stu-id="62ba9-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="62ba9-932">實體索引鍵的一部分 (可判斷識別之實體類型的屬性或屬性集)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="62ba9-933">您可以使用一或多個 **PropertyRef** 元素來定義實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="62ba9-934">參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="62ba9-935">**PropertyRef**元素只能將 annotation 元素 (零或多個) 做為子項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-936">只有 CSDL v2 和更新版本中才允許注釋元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-937">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-937">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-938">下表說明可套用至 **PropertyRef** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="62ba9-939">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-939">Attribute Name</span></span> | <span data-ttu-id="62ba9-940">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-940">Is Required</span></span> | <span data-ttu-id="62ba9-941">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="62ba9-942">**名稱**</span><span class="sxs-lookup"><span data-stu-id="62ba9-942">**Name**</span></span>       | <span data-ttu-id="62ba9-943">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-943">Yes</span></span>         | <span data-ttu-id="62ba9-944">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-945">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **PropertyRef** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="62ba9-946">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-947">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-948">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-948">Example</span></span>

<span data-ttu-id="62ba9-949">下列範例會定義 (**Book**) 的實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="62ba9-950">實體索引鍵是透過參考實體類型的 **ISBN** 屬性來定義。</span><span class="sxs-lookup"><span data-stu-id="62ba9-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="62ba9-951">在下一個範例中，會使用兩個 **PropertyRef** 元素來指出 (**識別碼** 和 **PublisherId**) 的兩個屬性是參考條件約束的主體和相依端點。</span><span class="sxs-lookup"><span data-stu-id="62ba9-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="62ba9-952">ReferenceType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-953">概念結構定義語言 (CSDL) 中的 **ReferenceType** 元素會指定實體類型的參考。</span><span class="sxs-lookup"><span data-stu-id="62ba9-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="62ba9-954">**ReferenceType**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="62ba9-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="62ba9-955">ReturnType (函式) </span><span class="sxs-lookup"><span data-stu-id="62ba9-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="62ba9-956">參數</span><span class="sxs-lookup"><span data-stu-id="62ba9-956">Parameter</span></span>
-   <span data-ttu-id="62ba9-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="62ba9-957">CollectionType</span></span>

<span data-ttu-id="62ba9-958">定義函數的參數或傳回類型時，會使用 **ReferenceType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="62ba9-959">**ReferenceType**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-960">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-961">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-962">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-962">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-963">下表說明可套用至 **ReferenceType** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="62ba9-964">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-964">Attribute Name</span></span> | <span data-ttu-id="62ba9-965">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-965">Is Required</span></span> | <span data-ttu-id="62ba9-966">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="62ba9-967">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-967">**Type**</span></span>       | <span data-ttu-id="62ba9-968">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-968">Yes</span></span>         | <span data-ttu-id="62ba9-969">參考之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-970">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **ReferenceType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="62ba9-971">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-972">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-973">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-973">Example</span></span>

<span data-ttu-id="62ba9-974">下列範例顯示在模型定義函式中，用來做為**Parameter**元素子系的**ReferenceType**專案，該函式會接受**Person**實體類型的參考：</span><span class="sxs-lookup"><span data-stu-id="62ba9-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="62ba9-975">下列範例顯示在模型定義函式中，用來做為**ReturnType**)  (函式之子系的**ReferenceType**專案，該函數會傳回**Person**實體類型的參考：</span><span class="sxs-lookup"><span data-stu-id="62ba9-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="62ba9-976">ReferentialConstraint 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="62ba9-977">概念結構定義語言 (CSDL) 中的 **ReferentialConstraint** 專案會定義類似于關係資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="62ba9-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="62ba9-978">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="62ba9-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="62ba9-979">參考的實體類型稱為條件約束的 *主要端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="62ba9-980">參考主體端的實體類型稱為條件約束的 *相依端點* 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="62ba9-981">如果在某個實體類型上公開的外鍵參考另一個實體類型上的屬性，則 **ReferentialConstraint** 元素會定義這兩個實體類型之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="62ba9-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="62ba9-982">由於 **ReferentialConstraint** 元素會提供兩個實體型別的相關資訊，對應規格語言 (MSL) 中不需要對應的 AssociationSetMapping 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="62ba9-983">未公開外鍵的兩個實體類型之間的關聯，必須有對應的 **AssociationSetMapping** 專案，才能將關聯資訊對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="62ba9-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="62ba9-984">如果未在實體類型上公開外鍵，則 **ReferentialConstraint** 元素只能定義實體類型與另一個實體類型之間的 primary key to primary key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="62ba9-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="62ba9-985">**ReferentialConstraint**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-986">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-987">Principal (只有一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="62ba9-988">Dependent (只有一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="62ba9-989">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-990">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-990">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-991">**ReferentialConstraint**元素可以有任意數目的注釋屬性 (自訂 XML 屬性) 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="62ba9-992">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-993">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-994">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-994">Example</span></span>

<span data-ttu-id="62ba9-995">下列範例顯示在**PublishedBy**關聯定義中使用的**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="62ba9-996">ReturnType (函式) 元素 (CSDL) </span><span class="sxs-lookup"><span data-stu-id="62ba9-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="62ba9-997">在概念結構定義語言 (CSDL 中， **ReturnType** (函式) 元素) 指定函式專案中定義之函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="62ba9-998">函數傳回型別也可以使用 **ReturnType** 屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="62ba9-999">傳回型別可以是任何 **EdmSimpleType**、實體類型、複雜型別、資料列類型、ref 型別或其中一個類型的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="62ba9-1000">您可以使用**ReturnType** (函式) 專案的**型**別屬性，或使用下列其中一個子項目來指定函式的傳回型別：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="62ba9-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1001">CollectionType</span></span>
-   <span data-ttu-id="62ba9-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1002">ReferenceType</span></span>
-   <span data-ttu-id="62ba9-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-1004">如果您指定的函式傳回型別同時具有**ReturnType** (函式的**類型**屬性) 專案和其中一個子項目，則模型將不會驗證。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1005">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1005">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1006">下表描述可套用至 **ReturnType** (函式) 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="62ba9-1007">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-1007">Attribute Name</span></span> | <span data-ttu-id="62ba9-1008">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-1008">Is Required</span></span> | <span data-ttu-id="62ba9-1009">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="62ba9-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1010">**ReturnType**</span></span> | <span data-ttu-id="62ba9-1011">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1011">No</span></span>          | <span data-ttu-id="62ba9-1012">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-1013">任何數目的注釋屬性 (自訂 XML 屬性) 可以套用至 **ReturnType** (函數) 專案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="62ba9-1014">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1015">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-1016">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1016">Example</span></span>

<span data-ttu-id="62ba9-1017">下列範例會使用 **function** 專案來定義函式，該函式會傳回書籍列印的年數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="62ba9-1018">請注意，傳回型別是由**ReturnType** (函式) 元素的**type**屬性所指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="62ba9-1019">ReturnType (FunctionImport) 元素 (CSDL) </span><span class="sxs-lookup"><span data-stu-id="62ba9-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="62ba9-1020">在概念結構定義語言 (CSDL 中， **ReturnType** (FunctionImport) 元素) 指定 FunctionImport 元素中定義之函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="62ba9-1021">函數傳回型別也可以使用 **ReturnType** 屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="62ba9-1022">傳回類型可以是任何實體類型、複雜類型或 **EdmSimpleType**的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="62ba9-1023">函式的傳回型別是以**ReturnType** (FunctionImport) 專案的**型**別屬性（attribute）所指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1024">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1024">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1025">下表描述可套用至 **ReturnType** (FunctionImport) 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="62ba9-1026">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-1026">Attribute Name</span></span> | <span data-ttu-id="62ba9-1027">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-1027">Is Required</span></span> | <span data-ttu-id="62ba9-1028">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-1029">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1029">**Type**</span></span>       | <span data-ttu-id="62ba9-1030">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1030">No</span></span>          | <span data-ttu-id="62ba9-1031">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1031">The type that the function returns.</span></span> <span data-ttu-id="62ba9-1032">值必須是 ComplexType、EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="62ba9-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1033">**EntitySet**</span></span>  | <span data-ttu-id="62ba9-1034">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1034">No</span></span>          | <span data-ttu-id="62ba9-1035">如果函式傳回實體類型的集合，則 **EntitySet** 的值必須是集合所屬的實體集。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="62ba9-1036">否則，不得使用 **EntitySet** 屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-1037">任何數目的注釋屬性 (自訂 XML 屬性) 可能會套用至 **ReturnType** (FunctionImport) 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="62ba9-1038">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1039">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-1040">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1040">Example</span></span>

<span data-ttu-id="62ba9-1041">下列範例會使用會傳回書籍和發行者的 **FunctionImport** 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="62ba9-1042">請注意，此函數會傳回兩個結果集，因此會指定兩個 **ReturnType** (FunctionImport) 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="62ba9-1043">RowType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="62ba9-1044">概念結構定義語言 (CSDL 中的 **RowType** 專案) 會將未命名的結構定義為概念模型中定義之函式的參數或傳回型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="62ba9-1045">**RowType**元素可以是下列元素的子系：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="62ba9-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1046">CollectionType</span></span>
-   <span data-ttu-id="62ba9-1047">參數</span><span class="sxs-lookup"><span data-stu-id="62ba9-1047">Parameter</span></span>
-   <span data-ttu-id="62ba9-1048">ReturnType (函式) </span><span class="sxs-lookup"><span data-stu-id="62ba9-1048">ReturnType (Function)</span></span>

<span data-ttu-id="62ba9-1049">**RowType**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-1050">屬性 (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1050">Property (one or more)</span></span>
-   <span data-ttu-id="62ba9-1051">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1052">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1052">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1053">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **RowType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="62ba9-1054">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1055">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-1056">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1056">Example</span></span>

<span data-ttu-id="62ba9-1057">下列範例示範模型定義函式，該函式會使用 **CollectionType** 專案來指定函式傳回資料列集合 (如 **RowType** 元素) 中所指定。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="62ba9-1058">Schema 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="62ba9-1059">**Schema**元素是概念模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="62ba9-1060">其中包含組成概念模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="62ba9-1061">**Schema**元素可能包含零或多個下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="62ba9-1062">使用</span><span class="sxs-lookup"><span data-stu-id="62ba9-1062">Using</span></span>
-   <span data-ttu-id="62ba9-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="62ba9-1063">EntityContainer</span></span>
-   <span data-ttu-id="62ba9-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1064">EntityType</span></span>
-   <span data-ttu-id="62ba9-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1065">EnumType</span></span>
-   <span data-ttu-id="62ba9-1066">關聯</span><span class="sxs-lookup"><span data-stu-id="62ba9-1066">Association</span></span>
-   <span data-ttu-id="62ba9-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1067">ComplexType</span></span>
-   <span data-ttu-id="62ba9-1068">函式</span><span class="sxs-lookup"><span data-stu-id="62ba9-1068">Function</span></span>

<span data-ttu-id="62ba9-1069">**架構**專案可能包含零個或一個 Annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-1070">只有 CSDL v2 和更新版本中才允許使用 **Function** 元素和 annotation 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="62ba9-1071">**Schema**專案使用**namespace**屬性來定義概念模型中的實體類型、複雜型別和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="62ba9-1072">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="62ba9-1073">命名空間可以橫跨多個 **架構** 元素和多個 csdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="62ba9-1074">概念模型命名空間與 **架構** 元素的 XML 命名空間不同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="62ba9-1075">概念模型命名空間 (如 **命名空間** 屬性所定義) 是實體類型、複雜類型和關聯類型的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="62ba9-1076">**Schema**專案的**xmlns**屬性) 所指出的 XML 命名空間 (是**schema**專案的子項目和屬性的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="62ba9-1077">格式 (的 XML 命名空間， https://schemas.microsoft.com/ado/YYYY/MM/edm 其中 YYYY 和 MM 分別代表年份和月份，) 會保留給 CSDL。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1078">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1079">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1079">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1080">下表說明可套用至 **Schema** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="62ba9-1081">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-1081">Attribute Name</span></span> | <span data-ttu-id="62ba9-1082">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-1082">Is Required</span></span> | <span data-ttu-id="62ba9-1083">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1084">**Namespace**</span></span>  | <span data-ttu-id="62ba9-1085">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1085">Yes</span></span>         | <span data-ttu-id="62ba9-1086">概念模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="62ba9-1087">**Namespace**屬性的值是用來形成型別的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="62ba9-1088">例如，如果名為*Customer*的**entitytype**位於簡單的. 範例. 模型命名空間中，則**Entitytype**的完整名稱是 simpleexamplemodel.customer。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="62ba9-1089">下列字串不能當做 Namespace 屬性（ **Namespace** ）的值使用： **System**、 **暫時性**或 **Edm**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="62ba9-1090">**Namespace**屬性的值不能與 SSDL Schema 元素中**namespace**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="62ba9-1091">**別名**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1091">**Alias**</span></span>      | <span data-ttu-id="62ba9-1092">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1092">No</span></span>          | <span data-ttu-id="62ba9-1093">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="62ba9-1094">例如，如果名為*Customer*的**EntityType**位於簡單的. 模型命名空間中，而**Alias**屬性的值為*Model*，則您可以使用 model. Customer 作為 EntityType 的完整名稱 **。**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="62ba9-1095">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Schema** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="62ba9-1096">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1097">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-1098">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1098">Example</span></span>

<span data-ttu-id="62ba9-1099">下列範例顯示包含**EntityContainer**元素、兩個**EntityType**專案和一個**Association**元素的**Schema**專案。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="62ba9-1100">TypeRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="62ba9-1101">概念結構定義語言 (CSDL 中的 **TypeRef** 元素) 提供現有命名類型的參考。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="62ba9-1102">**TypeRef**元素可以是 CollectionType 專案的子系，用來指定函式的集合為參數或傳回型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="62ba9-1103">**TypeRef**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="62ba9-1104">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="62ba9-1105">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1106">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1106">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1107">下表說明可套用至 **TypeRef** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="62ba9-1108">請注意， **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**和定 **序** 屬性僅適用于 **EDMSimpleTypes**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="62ba9-1109">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="62ba9-1110">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-1110">Is Required</span></span> | <span data-ttu-id="62ba9-1111">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-1112">**型別**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1112">**Type**</span></span>                                                           | <span data-ttu-id="62ba9-1113">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1113">No</span></span>          | <span data-ttu-id="62ba9-1114">所參考的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="62ba9-1115">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="62ba9-1116">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1116">No</span></span>          | <span data-ttu-id="62ba9-1117">**True** (預設值) 或 **False**，取決於屬性是否可以具有 null 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="62ba9-1118">CSDL v1 中的 >，複雜型別屬性必須具有 `Nullable="False"` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="62ba9-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="62ba9-1120">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1120">No</span></span>          | <span data-ttu-id="62ba9-1121">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="62ba9-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="62ba9-1123">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1123">No</span></span>          | <span data-ttu-id="62ba9-1124">屬性值的長度上限</span><span class="sxs-lookup"><span data-stu-id="62ba9-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="62ba9-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="62ba9-1126">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1126">No</span></span>          | <span data-ttu-id="62ba9-1127">**True** 或 **False** ，取決於屬性值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="62ba9-1128">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1128">**Precision**</span></span>                                                      | <span data-ttu-id="62ba9-1129">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1129">No</span></span>          | <span data-ttu-id="62ba9-1130">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="62ba9-1131">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1131">**Scale**</span></span>                                                          | <span data-ttu-id="62ba9-1132">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1132">No</span></span>          | <span data-ttu-id="62ba9-1133">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="62ba9-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1134">**SRID**</span></span>                                                           | <span data-ttu-id="62ba9-1135">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1135">No</span></span>          | <span data-ttu-id="62ba9-1136">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="62ba9-1137">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="62ba9-1138">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="62ba9-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="62ba9-1140">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1140">No</span></span>          | <span data-ttu-id="62ba9-1141">**True** 或 **False** ，取決於屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="62ba9-1142">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1142">**Collation**</span></span>                                                      | <span data-ttu-id="62ba9-1143">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1143">No</span></span>          | <span data-ttu-id="62ba9-1144">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="62ba9-1145">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **CollectionType** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="62ba9-1146">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1147">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-1148">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1148">Example</span></span>

<span data-ttu-id="62ba9-1149">下列範例示範模型定義的函式，此函式會使用 **TypeRef** 元素 (作為 **CollectionType** 專案的子系) 指定函式接受 **部門** 實體類型的集合。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="62ba9-1150">Using 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="62ba9-1151">概念結構定義語言 (CSDL 中的 **Using** 元素) 會匯入存在於不同命名空間中之概念模型的內容。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="62ba9-1152">藉由設定 **Namespace** 屬性的值，您可以參考實體類型、複雜類型，以及在另一個概念模型中定義的關聯類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="62ba9-1153">一個以上的 **Using** 元素可以是 **Schema** 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-1154">CSDL 中的 **using** 元素不像程式語言中的 **using** 語句一樣運作。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="62ba9-1155">藉由在程式設計語言中使用 **using** 語句匯入命名空間，您就不會影響原始命名空間中的物件。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="62ba9-1156">在 CSDL 中，匯入的命名空間可以包含實體類型，該實體類型是衍生自原始命名空間中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="62ba9-1157">這會影響在原始命名空間中宣告的實體集。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="62ba9-1158">**Using**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="62ba9-1159">Documentation (可允許零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="62ba9-1160">Annotation 項目 (允許零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="62ba9-1161">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1161">Applicable Attributes</span></span>

<span data-ttu-id="62ba9-1162">下表說明屬性可以套用至 **Using** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="62ba9-1163">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="62ba9-1163">Attribute Name</span></span> | <span data-ttu-id="62ba9-1164">必要</span><span class="sxs-lookup"><span data-stu-id="62ba9-1164">Is Required</span></span> | <span data-ttu-id="62ba9-1165">值</span><span class="sxs-lookup"><span data-stu-id="62ba9-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1166">**Namespace**</span></span>  | <span data-ttu-id="62ba9-1167">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1167">Yes</span></span>         | <span data-ttu-id="62ba9-1168">匯入的命名空間名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="62ba9-1169">**別名**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1169">**Alias**</span></span>      | <span data-ttu-id="62ba9-1170">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1170">Yes</span></span>         | <span data-ttu-id="62ba9-1171">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="62ba9-1172">雖然這個屬性是必要的，但是不必使用此屬性取代命名空間名稱，來限定物件名稱。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="62ba9-1173">您可以將任何數目的注釋屬性 (自訂 XML 屬性，) 可以套用至 **Using** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="62ba9-1174">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="62ba9-1175">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="62ba9-1176">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1176">Example</span></span>

<span data-ttu-id="62ba9-1177">下列範例示範用來匯入在其他地方定義之命名空間的 **Using** 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="62ba9-1178">請注意，顯示的 **架構** 元素命名空間為 `BooksModel` 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="62ba9-1179">`Address`EntityType 上的屬性 `Publisher` **EntityType**是在命名空間中定義的複雜型別， `ExtendedBooksModel` (**使用**) 所匯入的 Using 元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="62ba9-1180">註釋屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="62ba9-1181">概念結構描述定義語言 (CSDL) 中的附註屬性是概念模型中自訂的 XML 屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="62ba9-1182">除了擁有有效的 XML 結構外，下列附註屬性的條件必須成立：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="62ba9-1183">附註屬性不能在任何 XML 命名空間之中，這是保留供 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="62ba9-1184">超過一個以上的附註屬性可以套用至給定的 CSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="62ba9-1185">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="62ba9-1186">附註屬性可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="62ba9-1187">您可以使用 system.string 命名空間中的類別，在執行時間存取批註專案中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-1188">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1188">Example</span></span>

<span data-ttu-id="62ba9-1189">下列範例顯示具有 annotation 屬性 (**CustomAttribute**) 的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="62ba9-1190">下列範例也顯示套用至實體類型項目的 Annotation 項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="62ba9-1191">下列程式碼會擷取附註屬性中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="62ba9-1192">以上程式碼假設 `School.csdl` 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="62ba9-1193">註釋項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="62ba9-1194">概念結構定義語言 (CSDL) 中的 Annotation 項目是概念模型中的自訂 XML 項目。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="62ba9-1195">除了擁有有效的 XML 結構外，下列 Annotation 項目的條件也必須成立：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="62ba9-1196">Annotation 項目不能存在於保留供 CSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="62ba9-1197">多個 Annotation 項目可以同時為指定 CSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="62ba9-1198">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="62ba9-1199">Annotation 項目必須出現在指定 CSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="62ba9-1200">Annotation 項目可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="62ba9-1201">從 .NET Framework 第4版開始，可以使用 system.string 命名空間中的類別，在執行時間存取批註專案中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-1202">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1202">Example</span></span>

<span data-ttu-id="62ba9-1203">下列範例示範具有 annotation 元素 (**CustomElement**) 的**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="62ba9-1204">該範例也顯示套用至實體類型項目的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="62ba9-1205">下列程式碼會擷取 annotation 項目中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="62ba9-1206">以上程式碼假設 School.csdl 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="62ba9-1207">概念模型類型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="62ba9-1208">概念結構定義語言 (CSDL) 支援一組稱為 **EDMSimpleTypes**的抽象基本資料類型，可定義概念模型中的屬性。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="62ba9-1209">**EDMSimpleTypes** 是儲存或裝載環境中支援之基本資料類型的 proxy。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="62ba9-1210">下表列出 CSDL 所支援的基本資料型別。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="62ba9-1211">此表格也會列出可以套用至每個 **EDMSimpleType**的 facet。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="62ba9-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="62ba9-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="62ba9-1213">描述</span><span class="sxs-lookup"><span data-stu-id="62ba9-1213">Description</span></span>                                                | <span data-ttu-id="62ba9-1214">適用的 Facet</span><span class="sxs-lookup"><span data-stu-id="62ba9-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="62ba9-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="62ba9-1216">包含二進位資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="62ba9-1217">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="62ba9-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="62ba9-1219">包含 **true** 或 **false**值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="62ba9-1220">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="62ba9-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="62ba9-1222">包含不帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="62ba9-1223">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="62ba9-1225">表示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="62ba9-1226">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="62ba9-1228">包含與 GMT 的日期和時間時差 (以分鐘為單位)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="62ba9-1229">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="62ba9-1231">包含固定有效位數和小數位數的數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="62ba9-1232">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="62ba9-1234">包含具有15位數精確度的浮點數</span><span class="sxs-lookup"><span data-stu-id="62ba9-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="62ba9-1235">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1236">**Edm. Float**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="62ba9-1237">包含具有 7 位數精確度的浮點數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="62ba9-1238">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="62ba9-1240">包含 16 位元組的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="62ba9-1241">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="62ba9-1243">包含帶正負號的 16 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="62ba9-1244">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="62ba9-1246">包含帶正負號的 32 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="62ba9-1247">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="62ba9-1249">包含帶正負號的 64 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="62ba9-1250">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="62ba9-1252">包含帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="62ba9-1253">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1254">**Edm.String**</span></span>                   | <span data-ttu-id="62ba9-1255">包含字元資料。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1255">Contains character data.</span></span>                                   | <span data-ttu-id="62ba9-1256">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="62ba9-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="62ba9-1258">包含一天的時間。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="62ba9-1259">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="62ba9-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="62ba9-1260">**Edm. Geography**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="62ba9-1261">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="62ba9-1263">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1264">**>geographylinestring**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="62ba9-1265">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1266">**Edm.geographypolygon**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="62ba9-1267">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1268">**>geographymultipoint**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="62ba9-1269">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1270">**>geographymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="62ba9-1271">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1272">**>geographymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="62ba9-1273">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1274">**GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="62ba9-1275">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1276">**Edm. Geometry**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="62ba9-1277">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1278">**>geometrypoint**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="62ba9-1279">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1280">**>geometrylinestring**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="62ba9-1281">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1282">**>geometrypolygon**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="62ba9-1283">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1284">**>geometrymultipoint**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="62ba9-1285">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1286">**>geometrymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="62ba9-1287">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1288">**>geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="62ba9-1289">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="62ba9-1290">**>geometrycollection**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="62ba9-1291">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="62ba9-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="62ba9-1292">Facet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="62ba9-1292">Facets (CSDL)</span></span>

<span data-ttu-id="62ba9-1293">概念結構定義語言 (CSDL) 中的 Facet 表示實體型別和複雜型別屬性上的條件約束。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="62ba9-1294">Facet 在下列 CSDL 元素上會以 XML 屬性的形式出現：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="62ba9-1295">屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1295">Property</span></span>
-   <span data-ttu-id="62ba9-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="62ba9-1296">TypeRef</span></span>
-   <span data-ttu-id="62ba9-1297">參數</span><span class="sxs-lookup"><span data-stu-id="62ba9-1297">Parameter</span></span>

<span data-ttu-id="62ba9-1298">下表說明 CSDL 中支援的 Facet。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="62ba9-1299">所有的 Facet 都是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1299">All facets are optional.</span></span> <span data-ttu-id="62ba9-1300">從概念模型產生資料庫時，Entity Framework 會使用下面所列的部分 facet。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="62ba9-1301">如需概念模型中資料類型的詳細資訊，請參閱 (CSDL) 的概念模型類型。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="62ba9-1302">Facet</span><span class="sxs-lookup"><span data-stu-id="62ba9-1302">Facet</span></span>               | <span data-ttu-id="62ba9-1303">描述</span><span class="sxs-lookup"><span data-stu-id="62ba9-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="62ba9-1304">適用於</span><span class="sxs-lookup"><span data-stu-id="62ba9-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="62ba9-1305">用於產生資料庫</span><span class="sxs-lookup"><span data-stu-id="62ba9-1305">Used for the database generation</span></span> | <span data-ttu-id="62ba9-1306">由執行階段所使用</span><span class="sxs-lookup"><span data-stu-id="62ba9-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="62ba9-1307">**定序**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1307">**Collation**</span></span>       | <span data-ttu-id="62ba9-1308">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="62ba9-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="62ba9-1310">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1310">Yes</span></span>                              | <span data-ttu-id="62ba9-1311">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1311">No</span></span>                  |
| <span data-ttu-id="62ba9-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="62ba9-1313">表示屬性值應用於開放式並行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="62ba9-1314">所有 **EDMSimpleType** 屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="62ba9-1315">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1315">No</span></span>                               | <span data-ttu-id="62ba9-1316">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1316">Yes</span></span>                 |
| <span data-ttu-id="62ba9-1317">**預設值**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1317">**Default**</span></span>         | <span data-ttu-id="62ba9-1318">執行個體化時如果沒有提供值，請指定屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="62ba9-1319">所有 **EDMSimpleType** 屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="62ba9-1320">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1320">Yes</span></span>                              | <span data-ttu-id="62ba9-1321">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1321">Yes</span></span>                 |
| <span data-ttu-id="62ba9-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1322">**FixedLength**</span></span>     | <span data-ttu-id="62ba9-1323">指定屬性值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="62ba9-1324">**Edm. 二進位**、 **edm. 字串**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="62ba9-1325">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1325">Yes</span></span>                              | <span data-ttu-id="62ba9-1326">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1326">No</span></span>                  |
| <span data-ttu-id="62ba9-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1327">**MaxLength**</span></span>       | <span data-ttu-id="62ba9-1328">指定屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="62ba9-1329">**Edm. 二進位**、 **edm. 字串**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="62ba9-1330">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1330">Yes</span></span>                              | <span data-ttu-id="62ba9-1331">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1331">No</span></span>                  |
| <span data-ttu-id="62ba9-1332">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1332">**Nullable**</span></span>        | <span data-ttu-id="62ba9-1333">指定屬性是否可以有 **null** 值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="62ba9-1334">所有 **EDMSimpleType** 屬性</span><span class="sxs-lookup"><span data-stu-id="62ba9-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="62ba9-1335">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1335">Yes</span></span>                              | <span data-ttu-id="62ba9-1336">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1336">Yes</span></span>                 |
| <span data-ttu-id="62ba9-1337">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1337">**Precision**</span></span>       | <span data-ttu-id="62ba9-1338">針對 **Decimal**類型的屬性，指定屬性值可擁有的位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="62ba9-1339">針對 **Time**、 **DateTime**和 **DateTimeOffset**類型的屬性，指定屬性值的秒數小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="62ba9-1340">**Edm. 日期時間**、 **edm**、DateTimeOffset **、edm。\*\*\*\*時間**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="62ba9-1341">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1341">Yes</span></span>                              | <span data-ttu-id="62ba9-1342">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1342">No</span></span>                  |
| <span data-ttu-id="62ba9-1343">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1343">**Scale**</span></span>           | <span data-ttu-id="62ba9-1344">指定屬性值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="62ba9-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="62ba9-1346">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1346">Yes</span></span>                              | <span data-ttu-id="62ba9-1347">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1347">No</span></span>                  |
| <span data-ttu-id="62ba9-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1348">**SRID**</span></span>            | <span data-ttu-id="62ba9-1349">指定空間系統參考系統識別碼。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="62ba9-1350">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="62ba9-1351">**GeographyPoint，edm. >geographylinestring，Edm. Edm.geographypolygon，Edm. >geographymultipoint，Edm. >geographymultilinestring，Edm. >geographymultipolygon，Edm. GeographyCollection，Edm. >geometrypoint，edm. >geometrylinestring，Edm. >geometrypolygon，edm. >geometrymultipoint，edm. >geometrymultilinestring，edm. >geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="62ba9-1352">否</span><span class="sxs-lookup"><span data-stu-id="62ba9-1352">No</span></span>                               | <span data-ttu-id="62ba9-1353">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1353">Yes</span></span>                 |
| <span data-ttu-id="62ba9-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1354">**Unicode**</span></span>         | <span data-ttu-id="62ba9-1355">指出屬性值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="62ba9-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="62ba9-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="62ba9-1357">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1357">Yes</span></span>                              | <span data-ttu-id="62ba9-1358">是</span><span class="sxs-lookup"><span data-stu-id="62ba9-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="62ba9-1359">從概念模型產生資料庫時，如果屬性專案在下列命名空間中，[產生資料庫] 將會辨識該**屬性**專案的**StoreGeneratedPattern**屬性值： https://schemas.microsoft.com/ado/2009/02/edm/annotation 。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="62ba9-1360">屬性支援的值為 **Identity** 和 **計算**。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="62ba9-1361">**Identity**的值會產生資料庫資料行，此資料行具有在資料庫中產生的識別值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="62ba9-1362">**計算**的值將會產生一個資料行，其中包含在資料庫中計算的值。</span><span class="sxs-lookup"><span data-stu-id="62ba9-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="62ba9-1363">範例</span><span class="sxs-lookup"><span data-stu-id="62ba9-1363">Example</span></span>

<span data-ttu-id="62ba9-1364">下列範例顯示 Facet 套用至實體類型的屬性：</span><span class="sxs-lookup"><span data-stu-id="62ba9-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
