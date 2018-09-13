---
title: CSDL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: f5bf0dc75a8195e9af979c9e044f36171f46c9b7
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490515"
---
# <a name="csdl-specification"></a><span data-ttu-id="e0bcc-102">CSDL 規格</span><span class="sxs-lookup"><span data-stu-id="e0bcc-102">CSDL Specification</span></span>
<span data-ttu-id="e0bcc-103">概念結構定義語言 (CSDL) 是 XML架構語言，可描述組成資料驅動應用程式之概念模型的實體、關聯性和函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="e0bcc-104">此概念模型可供 Entity Framework 或 WCF Data Services。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="e0bcc-105">以 CSDL 描述的中繼資料由 Entity Framework 用來對應實體和資料來源的概念模型中定義的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="e0bcc-106">如需詳細資訊，請參閱 [SSDL 規格](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)並[MSL 規格](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="e0bcc-107">CSDL 是實體資料模型的 Entity Framework 的實作。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="e0bcc-108">Entity Framework 應用程式中，概念模型中繼資料載入來自.csdl 檔 （於 CSDL 中寫入） System.Data.Metadata.Edm.EdmItemCollection 的執行個體，則可存取使用中的方法System.Data.Metadata.Edm.MetadataWorkspace 類別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="e0bcc-109">Entity Framework 會使用概念模型中繼資料，以針對概念模型資料來源特有的命令以將查詢轉譯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="e0bcc-110">EF 設計工具會在設計階段，將概念模型資訊儲存在.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="e0bcc-111">在建置時，EF 設計工具會使用資訊來建立由 Entity Framework 在執行階段所需的.csdl 檔案.edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="e0bcc-112">CSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="e0bcc-113">CSDL 的版本</span><span class="sxs-lookup"><span data-stu-id="e0bcc-113">CSDL Version</span></span> | <span data-ttu-id="e0bcc-114">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="e0bcc-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="e0bcc-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="e0bcc-115">CSDL v1</span></span>      | http://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="e0bcc-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="e0bcc-116">CSDL v2</span></span>      | http://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="e0bcc-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="e0bcc-117">CSDL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="e0bcc-118">Association 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-118">Association Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-119">**關聯**項目會定義兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="e0bcc-120">關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="e0bcc-121">關聯 end 的多重性可以有值為一 (1)、 零或一個 (0..1)，或多個 (\*)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="e0bcc-122">這項資訊是在兩個子結束項目中指定。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="e0bcc-123">關聯其中一端的實體類型執行個體可透過導覽屬性或外部索引鍵來存取 (若在實體類型上公開)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="e0bcc-124">在應用程式中，關聯的執行個體代表實體類型之間的特定關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="e0bcc-125">關聯執行個體會在邏輯上群組於關聯集中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="e0bcc-126">**關聯**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-127">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-128">結束 （2 個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="e0bcc-129">ReferentialConstraint （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-130">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-131">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-131">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-132">下表描述可套用至的屬性**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="e0bcc-133">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-133">Attribute Name</span></span> | <span data-ttu-id="e0bcc-134">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-134">Is Required</span></span> | <span data-ttu-id="e0bcc-135">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="e0bcc-136">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-136">**Name**</span></span>       | <span data-ttu-id="e0bcc-137">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-137">Yes</span></span>         | <span data-ttu-id="e0bcc-138">關聯的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-139">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="e0bcc-140">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-141">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-142">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-142">Example</span></span>

<span data-ttu-id="e0bcc-143">下列範例所示**關聯**項目，定義**CustomerOrders**時不上公開外部索引鍵的關聯**客戶**並**順序**實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="e0bcc-144">**多重性**每個值**端**關聯的指示，許多**訂單**可以與相關聯**客戶**，但只有一個**客戶**可以與相關聯**順序**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="e0bcc-145">此外， **OnDelete**項目表示所有**訂單**與相關的特定**客戶**和已載入至 ObjectContext 會被刪除。如果**客戶**會被刪除。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="e0bcc-146">下列範例所示**關聯**項目，定義**CustomerOrders**時已經上公開外部索引鍵的關聯**客戶**並**順序**實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="e0bcc-147">公開外部索引鍵，使用管理實體之間的關聯性**ReferentialConstraint**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="e0bcc-148">對應的 AssociationSetMapping 項目不需要將此關聯對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="e0bcc-149">AssociationSet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-150">**AssociationSet**概念結構定義語言 (CSDL) 中的項目是相同類型的關聯執行個體的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="e0bcc-151">關聯集提供群組關聯執行個體的定義，執行個體才能對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="e0bcc-152">**AssociationSet**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-153">文件 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-154">結束 （所需的兩個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="e0bcc-155">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="e0bcc-156">**關聯**屬性會指定關聯集中包含的關聯型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="e0bcc-157">指定組成關聯集的兩端的實體集只由兩個子**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-158">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-158">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-159">下表描述可套用至的屬性**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="e0bcc-160">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-160">Attribute Name</span></span>  | <span data-ttu-id="e0bcc-161">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-161">Is Required</span></span> | <span data-ttu-id="e0bcc-162">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-163">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-163">**Name**</span></span>        | <span data-ttu-id="e0bcc-164">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-164">Yes</span></span>         | <span data-ttu-id="e0bcc-165">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-165">The name of the entity set.</span></span> <span data-ttu-id="e0bcc-166">值**名稱**屬性不能為的值相同**關聯**屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="e0bcc-167">**關聯**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-167">**Association**</span></span> | <span data-ttu-id="e0bcc-168">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-168">Yes</span></span>         | <span data-ttu-id="e0bcc-169">關聯執行個體 (由關聯集包含) 之關聯的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="e0bcc-170">關聯必須存在與關聯集相同的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-171">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="e0bcc-172">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-173">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-174">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-174">Example</span></span>

<span data-ttu-id="e0bcc-175">下列範例所示**EntityContainer**具有兩個項目**AssociationSet**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="e0bcc-176">CollectionType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-177">**CollectionType**概念結構定義語言 (CSDL) 中的項目會指定函式參數或函式傳回類型是集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="e0bcc-178">**CollectionType**項目可以是 Parameter 元素或 ReturnType （函式） 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="e0bcc-179">可以使用其中一種指定集合的型別**型別**屬性或其中一個下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-180">**CollectionType**</span></span>
-   <span data-ttu-id="e0bcc-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-181">ReferenceType</span></span>
-   <span data-ttu-id="e0bcc-182">RowType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-182">RowType</span></span>
-   <span data-ttu-id="e0bcc-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="e0bcc-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-184">如果指定的集合類型，但兩者不會驗證模型**型別**屬性和子元素。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-185">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-185">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-186">下表描述可套用至的屬性**CollectionType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="e0bcc-187">請注意， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，並**定序**屬性僅適用於集合**EDMSimpleTypes**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="e0bcc-188">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-188">Attribute Name</span></span>                                                          | <span data-ttu-id="e0bcc-189">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-189">Is Required</span></span> | <span data-ttu-id="e0bcc-190">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-191">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-191">**Type**</span></span>                                                                | <span data-ttu-id="e0bcc-192">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-192">No</span></span>          | <span data-ttu-id="e0bcc-193">集合的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="e0bcc-194">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-194">**Nullable**</span></span>                                                            | <span data-ttu-id="e0bcc-195">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-195">No</span></span>          | <span data-ttu-id="e0bcc-196">**真**（預設值） 或**False**根據屬性是否有 null 值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="e0bcc-197">> 在 CSDL v1 中，複雜類型屬性必須有`Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="e0bcc-198">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="e0bcc-199">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-199">No</span></span>          | <span data-ttu-id="e0bcc-200">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="e0bcc-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="e0bcc-202">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-202">No</span></span>          | <span data-ttu-id="e0bcc-203">屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="e0bcc-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="e0bcc-205">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-205">No</span></span>          | <span data-ttu-id="e0bcc-206">**真**或是**False**根據屬性值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="e0bcc-207">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-207">**Precision**</span></span>                                                           | <span data-ttu-id="e0bcc-208">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-208">No</span></span>          | <span data-ttu-id="e0bcc-209">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="e0bcc-210">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-210">**Scale**</span></span>                                                               | <span data-ttu-id="e0bcc-211">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-211">No</span></span>          | <span data-ttu-id="e0bcc-212">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-213">**SRID**</span></span>                                                                | <span data-ttu-id="e0bcc-214">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-214">No</span></span>          | <span data-ttu-id="e0bcc-215">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-216">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-216">Valid only for properties of spatial types.</span></span>   <span data-ttu-id="e0bcc-217">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)和[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-217">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="e0bcc-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-218">**Unicode**</span></span>                                                             | <span data-ttu-id="e0bcc-219">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-219">No</span></span>          | <span data-ttu-id="e0bcc-220">**真**或是**False**根據屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="e0bcc-221">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-221">**Collation**</span></span>                                                           | <span data-ttu-id="e0bcc-222">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-222">No</span></span>          | <span data-ttu-id="e0bcc-223">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-224">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="e0bcc-225">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-226">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-227">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-227">Example</span></span>

<span data-ttu-id="e0bcc-228">下列範例示範模型定義函式使用**CollectionType**項目來指定函式傳回的集合**人員**實體類型 (為指定的**ElementType**屬性)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="e0bcc-229">下列範例示範使用的模型定義函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="e0bcc-230">下列範例示範使用的模型定義函式**CollectionType**項目來指定，此函式會接受做為參數的集合**部門**實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="e0bcc-231">ComplexType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-232">A **ComplexType**項目會定義所組成的資料結構**EdmSimpleType**屬性或其他複雜型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span>  <span data-ttu-id="e0bcc-233">複雜類型可以是實體類型的屬性或另一個複雜類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-233">A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="e0bcc-234">複雜類型與實體類型相似之處在於複雜類型會定義資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="e0bcc-235">不過，複雜型別和實體類型之間還是有些重大的差異：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="e0bcc-236">複雜型別不具有識別 (或索引鍵)，因此無法獨立存在。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="e0bcc-237">複雜型別只能以實體類型或其他複雜型別的屬性形式存在。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="e0bcc-238">複雜型別不能參與關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="e0bcc-239">既不關聯 end 的可以是複雜類型，並因此不能定義導覽屬性的複雜型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="e0bcc-240">雖然複雜型別的純量屬性可能每個都設為 null，但複雜型別屬性不可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="e0bcc-241">A **ComplexType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-242">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-243">屬性 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="e0bcc-244">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="e0bcc-245">下表描述可套用至的屬性**ComplexType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="e0bcc-246">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="e0bcc-247">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-247">Is Required</span></span> | <span data-ttu-id="e0bcc-248">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-249">名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-249">Name</span></span>                                                                                                           | <span data-ttu-id="e0bcc-250">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-250">Yes</span></span>         | <span data-ttu-id="e0bcc-251">複雜類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-251">The name of the complex type.</span></span> <span data-ttu-id="e0bcc-252">複雜類型的名稱不可與其他複雜類型、實體類型或模型範圍內之關聯的名稱相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="e0bcc-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="e0bcc-254">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-254">No</span></span>          | <span data-ttu-id="e0bcc-255">其他複雜類型的名稱是即將定義之複雜類型的基底型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="e0bcc-256">> 這個屬性並不適用於 CSDL v1。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="e0bcc-257">該版本不支援複雜類型的繼承。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="e0bcc-258">抽象</span><span class="sxs-lookup"><span data-stu-id="e0bcc-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="e0bcc-259">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-259">No</span></span>          | <span data-ttu-id="e0bcc-260">**True**或是**False** （預設值） 根據複雜的型別是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="e0bcc-261">> 這個屬性並不適用於 CSDL v1。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="e0bcc-262">該版本的複雜類型不可以是抽象型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-263">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ComplexType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="e0bcc-264">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-265">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-266">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-266">Example</span></span>

<span data-ttu-id="e0bcc-267">下列範例示範複雜型別**地址**，以**EdmSimpleType**屬性**StreetAddress**，**縣 （市)**， **StateOrProvince**，**國家/地區**，以及**PostalCode**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="e0bcc-268">若要定義的複雜型別**地址**（上方），您也必須在實體類型，屬性宣告中的實體類型定義的屬性類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="e0bcc-269">下列範例所示**地址**複雜型別的實體類型上的屬性 (**發行者**):</span><span class="sxs-lookup"><span data-stu-id="e0bcc-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="e0bcc-270">DefiningExpression 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-271">**DefiningExpression**概念結構定義語言 (CSDL) 中的項目包含概念模型中定義的函式的 Entity SQL 運算式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="e0bcc-272">進行驗證**DefiningExpression**元素可以包含任意內容。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="e0bcc-273">不過，Entity Framework 將會擲回例外狀況在執行階段**DefiningExpression**元素未包含有效的 Entity SQL。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-274">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-274">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-275">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**DefiningExpression**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="e0bcc-276">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-277">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-278">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-278">Example</span></span>

<span data-ttu-id="e0bcc-279">下列範例會使用**DefiningExpression**項目來定義發行活頁簿之後，傳回的年數的函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="e0bcc-280">內容**DefiningExpression**項目會寫入 Entity sql。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="e0bcc-281">Dependent 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-282">**相依**概念結構定義語言 (CSDL) 中的項目是 ReferentialConstraint 項目子元素和定義參考條件約束的相依端。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="e0bcc-283">A **ReferentialConstraint**項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="e0bcc-284">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="e0bcc-285">參考的實體類型稱為*主體端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="e0bcc-286">參考主要端點的實體類型稱為*相依端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="e0bcc-287">**PropertyRef**元素用來指定參考主要端點的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="e0bcc-288">**相依**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-289">PropertyRef （一或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="e0bcc-290">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-291">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-291">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-292">下表描述可套用至的屬性**相依**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="e0bcc-293">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-293">Attribute Name</span></span> | <span data-ttu-id="e0bcc-294">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-294">Is Required</span></span> | <span data-ttu-id="e0bcc-295">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-296">**角色**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-296">**Role**</span></span>       | <span data-ttu-id="e0bcc-297">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-297">Yes</span></span>         | <span data-ttu-id="e0bcc-298">位於關聯之相依端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-299">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**相依**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="e0bcc-300">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-301">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-302">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-302">Example</span></span>

<span data-ttu-id="e0bcc-303">下列範例所示**ReferentialConstraint**做為定義的一部分的項目**PublishedBy**關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="e0bcc-304">**PublisherId**屬性**活頁簿**構成參考條件約束的相依端點的實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="e0bcc-305">Documentation 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-306">**文件**概念結構定義語言 (CSDL) 中的項目可以用來提供父元素中定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="e0bcc-307">在.edmx 檔案中，當**文件**項目是顯示為物件 （例如實體、 關聯或屬性），在 EF 設計工具的設計介面上的內容項目的子系**文件**項目會出現在 Visual Studio**屬性**視窗物件。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="e0bcc-308">**文件**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-309">**摘要**： 父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="e0bcc-310">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-310">(zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-311">**LongDescription**： 父項目的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="e0bcc-312">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-312">(zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-313">Annotation 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-313">Annotation elements.</span></span> <span data-ttu-id="e0bcc-314">(零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-315">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-315">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-316">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**文件**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="e0bcc-317">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-318">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-319">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-319">Example</span></span>

<span data-ttu-id="e0bcc-320">下列範例所示**文件**為 EntityType 元素的子元素的項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="e0bcc-321">如果是在 CSDL 中的下列程式碼片段內容的.edmx 檔案的內容**摘要**並**LongDescription**項目會出現在 Visual Studio**屬性**當您按一下視窗`Customer`實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="e0bcc-322">End 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-322">End Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-323">**結束**概念結構定義語言 (CSDL) 中的項目可以是子系的 Association 元素或 AssociationSet 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="e0bcc-324">在每個案例中，所扮演的角色**結束**項目不同，且適用的屬性會不同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="e0bcc-325">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="e0bcc-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="e0bcc-326">**結束**項目 (為子系**關聯**項目) 識別關聯的一端和可存在於該關聯 end 的實體類型執行個體數目的實體型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="e0bcc-327">關聯 End 會定義為關聯的部分。關聯必須具有兩個關聯 End。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="e0bcc-328">關聯其中一端的實體型別執行個體可透過巡覽屬性或外部索引鍵來存取 (若在實體型別上公開)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="e0bcc-329">**結束**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-330">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-331">OnDelete （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-332">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-333">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-333">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-334">下表描述可套用至的屬性**結束**項目時的子系**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="e0bcc-335">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-335">Attribute Name</span></span>   | <span data-ttu-id="e0bcc-336">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-336">Is Required</span></span> | <span data-ttu-id="e0bcc-337">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-338">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-338">**Type**</span></span>         | <span data-ttu-id="e0bcc-339">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-339">Yes</span></span>         | <span data-ttu-id="e0bcc-340">其中一個關聯 End 的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="e0bcc-341">**角色**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-341">**Role**</span></span>         | <span data-ttu-id="e0bcc-342">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-342">No</span></span>          | <span data-ttu-id="e0bcc-343">關聯 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-343">A name for the association end.</span></span> <span data-ttu-id="e0bcc-344">如果未提供任何名稱，則會使用關聯 End 上之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="e0bcc-345">**多重性**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-345">**Multiplicity**</span></span> | <span data-ttu-id="e0bcc-346">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-346">Yes</span></span>         | <span data-ttu-id="e0bcc-347">**1**， **0..1**，或**\*** 根據可在關聯一端的實體類型執行個體的數目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="e0bcc-348">**1**指出該只有一個實體類型執行個體存在於關聯 end。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="e0bcc-349">**0..1**表示零個或一個實體類型執行個體存在於關聯 end。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="e0bcc-350">**\*** 表示零個、 一個或多個實體類型執行個體存在於關聯 end。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-351">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="e0bcc-352">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-353">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-354">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-354">Example</span></span>

<span data-ttu-id="e0bcc-355">下列範例所示**關聯**項目，定義**CustomerOrders**關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="e0bcc-356">**多重性**每個值**端**關聯的指示，許多**訂單**可以與相關聯**客戶**，但只有一個**客戶**可以與相關聯**順序**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="e0bcc-357">此外， **OnDelete**項目表示所有**訂單**與相關的特定**客戶**和，已載入至 ObjectContext 會已刪除的 if**客戶**會被刪除。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="e0bcc-358">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="e0bcc-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="e0bcc-359">**結束**項目會指定一個關聯集的結尾。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="e0bcc-360">**AssociationSet**項目必須包含兩個**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="e0bcc-361">中包含的資訊**結束**項目用於資料集與資料來源對應。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="e0bcc-362">**結束**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-363">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-364">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-365">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="e0bcc-366">Annotation 項目才允許在 CSDL v2 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-367">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-367">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-368">下表描述可套用至的屬性**結束**項目時的子系**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="e0bcc-369">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-369">Attribute Name</span></span> | <span data-ttu-id="e0bcc-370">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-370">Is Required</span></span> | <span data-ttu-id="e0bcc-371">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-372">**EntitySet**</span></span>  | <span data-ttu-id="e0bcc-373">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-373">Yes</span></span>         | <span data-ttu-id="e0bcc-374">名稱**EntitySet**項目，定義某一端之父代**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="e0bcc-375">**EntitySet**項目必須定義在相同的實體容器當成父系**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="e0bcc-376">**角色**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-376">**Role**</span></span>       | <span data-ttu-id="e0bcc-377">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-377">No</span></span>          | <span data-ttu-id="e0bcc-378">關聯集 End 的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-378">The name of the association set end.</span></span> <span data-ttu-id="e0bcc-379">如果**角色**不使用屬性、 關聯集 end 的名稱會是實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-380">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="e0bcc-381">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-382">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-383">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-383">Example</span></span>

<span data-ttu-id="e0bcc-384">下列範例所示**EntityContainer**具有兩個項目**AssociationSet**項目，各有兩個**結束**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="e0bcc-385">EntityContainer 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-386">**EntityContainer**概念結構定義語言 (CSDL) 中的項目是實體集、 關聯集和函式匯入的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="e0bcc-387">概念模型實體容器對應到儲存體模型實體容器透過 EntityContainerMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="e0bcc-388">儲存體模型實體容器描述資料的結構：實體集描述資料表、關聯集描述外部索引建條件約束，而函式匯入則描述資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="e0bcc-389">**EntityContainer**項目可以有零個或一個文件項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="e0bcc-390">如果**文件**項目已存在，它必須在所有**EntitySet**， **AssociationSet**，以及**FunctionImport**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="e0bcc-391">**EntityContainer**項目可以有零或多個下列子項目 （依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="e0bcc-392">EntitySet</span></span>
-   <span data-ttu-id="e0bcc-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="e0bcc-393">AssociationSet</span></span>
-   <span data-ttu-id="e0bcc-394">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="e0bcc-394">FunctionImport</span></span>
-   <span data-ttu-id="e0bcc-395">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="e0bcc-395">Annotation elements</span></span>

<span data-ttu-id="e0bcc-396">您可以延伸**EntityContainer**項目包含的另一個內容**EntityContainer** ，位於相同的命名空間。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="e0bcc-397">若要包含的另一個內容**EntityContainer**，在參考**EntityContainer**項目，來設定值**擴充**名稱屬性**EntityContainer**您想要包含的項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="e0bcc-398">包含之所有子項目**EntityContainer**項目將會被視為子項目的參考**EntityContainer**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-399">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-399">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-400">下表描述可套用至的屬性**Using**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="e0bcc-401">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-401">Attribute Name</span></span> | <span data-ttu-id="e0bcc-402">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-402">Is Required</span></span> | <span data-ttu-id="e0bcc-403">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="e0bcc-404">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-404">**Name**</span></span>       | <span data-ttu-id="e0bcc-405">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-405">Yes</span></span>         | <span data-ttu-id="e0bcc-406">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="e0bcc-407">**擴充**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-407">**Extends**</span></span>    | <span data-ttu-id="e0bcc-408">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-408">No</span></span>          | <span data-ttu-id="e0bcc-409">相同命名空間中另一個實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-410">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityContainer**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="e0bcc-411">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-412">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-413">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-413">Example</span></span>

<span data-ttu-id="e0bcc-414">下列範例所示**EntityContainer**定義三個實體集和兩個關聯集的項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="e0bcc-415">EntitySet 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-416">**EntitySet**中概念結構定義語言項目是實體類型的執行個體和衍生自該實體類型的任何類型的執行個體的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="e0bcc-417">實體類型和實體集之間的關聯性，類似於關聯式資料庫中資料列與資料表的關係。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="e0bcc-418">實體類型和資料列一樣可以定義相關的資料集，而實體集則和資料表一樣可以包含該定義的執行個體。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="e0bcc-419">實體集提供群組實體類型執行個體的建構，以便將它們對應至資料來源中的相關資料結構。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="e0bcc-420">您可以為特定的實體類型定義多個實體集。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-421">EF 設計工具不支援包含每個類型的多個實體集的概念模型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="e0bcc-422">**EntitySet**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-423">Documentation 項目 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-424">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-425">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-425">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-426">下表描述可套用至的屬性**EntitySet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="e0bcc-427">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-427">Attribute Name</span></span> | <span data-ttu-id="e0bcc-428">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-428">Is Required</span></span> | <span data-ttu-id="e0bcc-429">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-430">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-430">**Name**</span></span>       | <span data-ttu-id="e0bcc-431">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-431">Yes</span></span>         | <span data-ttu-id="e0bcc-432">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="e0bcc-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-433">**EntityType**</span></span> | <span data-ttu-id="e0bcc-434">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-434">Yes</span></span>         | <span data-ttu-id="e0bcc-435">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-436">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntitySet**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="e0bcc-437">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-438">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-439">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-439">Example</span></span>

<span data-ttu-id="e0bcc-440">下列範例所示**EntityContainer**具有三個項目**EntitySet**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="e0bcc-441">您可以定義每個類型的多重實體集 (MEST)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="e0bcc-442">下列範例會定義的實體容器具有兩個實體集，如**活頁簿**實體類型：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="e0bcc-443">EntityType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-444">**EntityType**項目代表最上層概念，例如客戶或訂單，概念模型中的結構。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="e0bcc-445">實體類型是應用程式中實體類型之執行個體的範本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="e0bcc-446">每個範本包含下列資訊：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="e0bcc-447">唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-447">A unique name.</span></span> <span data-ttu-id="e0bcc-448">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-448">(Required.)</span></span>
-   <span data-ttu-id="e0bcc-449">實體索引鍵是由一個或多個屬性定義。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="e0bcc-450">(必要項。)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-450">(Required.)</span></span>
-   <span data-ttu-id="e0bcc-451">包含資料的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-451">Properties for containing data.</span></span> <span data-ttu-id="e0bcc-452">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-452">(Optional.)</span></span>
-   <span data-ttu-id="e0bcc-453">導覽屬性允許從關聯的一端巡覽至另一端。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="e0bcc-454">(選擇性。)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-454">(Optional.)</span></span>

<span data-ttu-id="e0bcc-455">在應用程式中，實體類型的執行個體代表特定的物件 (例如特定的客戶或訂單)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="e0bcc-456">實體類型的每一個執行個體都必須在實體集中有唯一的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="e0bcc-457">如果兩個實體類型執行個體屬於相同類型，而且索引鍵的值也相同，則會將這兩個執行個體視為相等。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="e0bcc-458">**EntityType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-459">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-460">索引鍵 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-461">屬性 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="e0bcc-462">NavigationProperty （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="e0bcc-463">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-464">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-464">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-465">下表描述可套用至的屬性**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="e0bcc-466">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="e0bcc-467">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-467">Is Required</span></span> | <span data-ttu-id="e0bcc-468">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-469">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="e0bcc-470">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-470">Yes</span></span>         | <span data-ttu-id="e0bcc-471">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="e0bcc-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="e0bcc-473">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-473">No</span></span>          | <span data-ttu-id="e0bcc-474">其他實體類型的名稱是即將定義之實體類型的基底類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="e0bcc-475">**抽象**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="e0bcc-476">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-476">No</span></span>          | <span data-ttu-id="e0bcc-477">**True**或是**False**，取決於實體類型是否為抽象型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="e0bcc-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="e0bcc-479">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-479">No</span></span>          | <span data-ttu-id="e0bcc-480">**真**或是**False**根據實體類型是否為開放實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="e0bcc-481">> **OpenType**屬性僅適用於使用 ADO.NET Data Services 使用的概念模型中所定義的實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-482">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="e0bcc-483">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-484">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-485">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-485">Example</span></span>

<span data-ttu-id="e0bcc-486">下列範例所示**EntityType**具有三個項目**屬性**項目和兩個**NavigationProperty**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="e0bcc-487">EnumType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-488">**EnumType**項目代表列舉型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="e0bcc-489">**EnumType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-490">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-491">成員 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="e0bcc-492">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-493">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-493">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-494">下表描述可套用至的屬性**EnumType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="e0bcc-495">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-495">Attribute Name</span></span>     | <span data-ttu-id="e0bcc-496">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-496">Is Required</span></span> | <span data-ttu-id="e0bcc-497">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-498">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-498">**Name**</span></span>           | <span data-ttu-id="e0bcc-499">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-499">Yes</span></span>         | <span data-ttu-id="e0bcc-500">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="e0bcc-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-501">**IsFlags**</span></span>        | <span data-ttu-id="e0bcc-502">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-502">No</span></span>          | <span data-ttu-id="e0bcc-503">**True**或是**False**，取決於是否列舉型別可用來當做一組旗標。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="e0bcc-504">預設值是 **，則為 False。**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="e0bcc-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-505">**UnderlyingType**</span></span> | <span data-ttu-id="e0bcc-506">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-506">No</span></span>          | <span data-ttu-id="e0bcc-507">**Edm.Byte**， **Edm.Int16**， **Edm.Int32**， **Edm.Int64**或是**Edm.SByte**定義類型的值的範圍。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span>   <span data-ttu-id="e0bcc-508">預設基礎列舉項目類型是**Edm.Int32.** 。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-508">The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-509">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EnumType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="e0bcc-510">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-511">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-512">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-512">Example</span></span>

<span data-ttu-id="e0bcc-513">下列範例所示**EnumType**具有三個項目**成員**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="e0bcc-514">Function 屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-514">Function Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-515">**函式**概念結構定義語言 (CSDL) 中的項目用以定義或宣告概念模型中的函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="e0bcc-516">函式是使用 DefiningExpression 項目所定義。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="e0bcc-517">A**函式**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-518">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-519">參數 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="e0bcc-520">DefiningExpression （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-521">ReturnType （函式） （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-522">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="e0bcc-523">傳回類型函式必須指定使用**ReturnType** (Function) 項目或有**ReturnType**屬性 （如下所示），但非兩者。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="e0bcc-524">可能的傳回型別包括任何 EdmSimpleType、實體類型、複雜類型、資料列型別或 ref 型別 (或這些類型其中之一的集合)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-525">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-525">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-526">下表描述可套用至的屬性**函式**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="e0bcc-527">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-527">Attribute Name</span></span> | <span data-ttu-id="e0bcc-528">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-528">Is Required</span></span> | <span data-ttu-id="e0bcc-529">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="e0bcc-530">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-530">**Name**</span></span>       | <span data-ttu-id="e0bcc-531">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-531">Yes</span></span>         | <span data-ttu-id="e0bcc-532">函式的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-532">The name of the function.</span></span>          |
| <span data-ttu-id="e0bcc-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-533">**ReturnType**</span></span> | <span data-ttu-id="e0bcc-534">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-534">No</span></span>          | <span data-ttu-id="e0bcc-535">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-536">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**函式**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="e0bcc-537">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-538">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-539">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-539">Example</span></span>

<span data-ttu-id="e0bcc-540">下列範例會使用**函式**項目來定義傳回講師受雇之後的年數的函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="e0bcc-541">FunctionImport 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-542">**FunctionImport**概念結構定義語言 (CSDL) 中的項目代表已定義資料來源中，但可透過概念模型物件的函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="e0bcc-543">例如，儲存體模型中的函式項目可用來代表在資料庫中的預存程序。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="e0bcc-544">A **FunctionImport**概念模型中的項目代表 Entity Framework 應用程式中對應的函式，並使用 FunctionImportMapping 項目對應至儲存體模型函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="e0bcc-545">在應用程式中呼叫函式時，對應的預存程序會在資料庫中執行。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="e0bcc-546">**FunctionImport**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-547">文件 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-548">參數 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-549">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-550">ReturnType (FunctionImport) （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="e0bcc-551">一**參數**項目應定義為每個函數可接受的參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="e0bcc-552">傳回使用，就必須指定函式的類型**ReturnType** (FunctionImport) 項目或有**ReturnType**屬性 （如下所示），但非兩者。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="e0bcc-553">傳回型別值必須是 EdmSimpleType、 EntityType 或 ComplexType 的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-554">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-554">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-555">下表描述可套用至的屬性**FunctionImport**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="e0bcc-556">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-556">Attribute Name</span></span>   | <span data-ttu-id="e0bcc-557">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-557">Is Required</span></span> | <span data-ttu-id="e0bcc-558">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-559">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-559">**Name**</span></span>         | <span data-ttu-id="e0bcc-560">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-560">Yes</span></span>         | <span data-ttu-id="e0bcc-561">匯入函式的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="e0bcc-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-562">**ReturnType**</span></span>   | <span data-ttu-id="e0bcc-563">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-563">No</span></span>          | <span data-ttu-id="e0bcc-564">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-564">The type that the function returns.</span></span> <span data-ttu-id="e0bcc-565">如果函式不會傳回值，請不要使用這個屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="e0bcc-566">否則，值必須是 ComplexType、 EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="e0bcc-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-567">**EntitySet**</span></span>    | <span data-ttu-id="e0bcc-568">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-568">No</span></span>          | <span data-ttu-id="e0bcc-569">如果函數傳回實體集合類型的值**EntitySet**必須實體集所屬的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="e0bcc-570">否則，請**EntitySet**屬性不能使用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="e0bcc-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-571">**IsComposable**</span></span> | <span data-ttu-id="e0bcc-572">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-572">No</span></span>          | <span data-ttu-id="e0bcc-573">如果值設為 true，函式是可組合 （資料表值函式） 且可以用於 LINQ 查詢中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span>  <span data-ttu-id="e0bcc-574">預設值為 **false**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-574">The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-575">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**FunctionImport**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="e0bcc-576">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-577">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-578">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-578">Example</span></span>

<span data-ttu-id="e0bcc-579">下列範例所示**FunctionImport**接受一個參數，並傳回的實體類型集合的項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="e0bcc-580">Key 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-580">Key Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-581">**金鑰**項目是 EntityType 元素的子項目，並定義*實體索引鍵*（的屬性或一組實體類型的屬性，可判斷識別）。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="e0bcc-582">構成實體索引鍵的屬性是在設計階段選取的。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="e0bcc-583">實體索引鍵屬性的值必須在執行階段的實體集中，單獨識別實體類型執行個體。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="e0bcc-584">您應選取構成實體索引鍵的屬性，以保證執行個體在實體集中的唯一性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="e0bcc-585">**金鑰**項目會定義實體索引鍵參考一或多個實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="e0bcc-586">**金鑰**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-587">PropertyRef （一或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="e0bcc-588">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-589">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-589">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-590">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**金鑰**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="e0bcc-591">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-592">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-593">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-593">Example</span></span>

<span data-ttu-id="e0bcc-594">以下範例定義名為實體型別**活頁簿**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="e0bcc-595">藉由參考定義的實體索引鍵**ISBN**之實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="e0bcc-596">**ISBN**屬性是實體索引鍵的理想選擇，因為國際標準書號 (ISBN) 可唯一識別一本書。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="e0bcc-597">下列範例顯示的實體類型 (**作者**)，有包含兩個屬性，實體索引鍵**名稱**並**位址**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="e0bcc-598">使用**名稱**並**地址**實體索引鍵是合理的選擇，因為同名的兩位作者不太可能住在相同的位址。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="e0bcc-599">不過，針對實體索引鍵所做的這個選擇不能絕對保證實體集中的唯一實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="e0bcc-600">新增屬性，例如**AuthorId**，無法用來唯一識別作者就會在此情況下建議使用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="e0bcc-601">Member 元素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-601">Member Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-602">**成員**項目是 EnumType 元素的子項目，並定義列舉類型的成員。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-603">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-603">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-604">下表描述可套用至的屬性**FunctionImport**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="e0bcc-605">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-605">Attribute Name</span></span> | <span data-ttu-id="e0bcc-606">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-606">Is Required</span></span> | <span data-ttu-id="e0bcc-607">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-608">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-608">**Name**</span></span>       | <span data-ttu-id="e0bcc-609">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-609">Yes</span></span>         | <span data-ttu-id="e0bcc-610">成員的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="e0bcc-611">**值**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-611">**Value**</span></span>      | <span data-ttu-id="e0bcc-612">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-612">No</span></span>          | <span data-ttu-id="e0bcc-613">成員的值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-613">The value of the member.</span></span> <span data-ttu-id="e0bcc-614">根據預設，第一個成員具有值為 0，而且每個連續的列舉值的值會遞增 1。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="e0bcc-615">可能存在多個成員具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-616">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**FunctionImport**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="e0bcc-617">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-618">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-619">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-619">Example</span></span>

<span data-ttu-id="e0bcc-620">下列範例所示**EnumType**具有三個項目**成員**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="e0bcc-621">NavigationProperty 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-622">A **NavigationProperty**項目定義導覽屬性，提供關聯另一端的參考。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="e0bcc-623">不像與屬性的項目定義的屬性，瀏覽屬性不會定義圖案和特性的資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="e0bcc-624">他們提供巡覽兩個實體類型間之關聯的方式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="e0bcc-625">請注意，在關聯各端點的實體類型上，導覽屬性是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="e0bcc-626">如果您在關聯其中一個端點的實體類型上定義導覽屬性，就不必在關聯另一個端點的實體類型上定義導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="e0bcc-627">導覽屬性傳回的資料類型是由其遠端關聯端點的多重性所判斷。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="e0bcc-628">例如，假設一個導覽屬性**OrdersNavProp**，存在於**客戶**實體類型並瀏覽一對多關聯**客戶**和**順序**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="e0bcc-629">因為導覽屬性的遠端關聯 end 具有多重性許多 (\*)，其資料型別是集合 (的**順序**)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="e0bcc-630">同樣地，如果導覽屬性， **CustomerNavProp**，存在於**順序**實體型別，其資料類型會是**客戶**因為遠端 end 的多重性是一 (1)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="e0bcc-631">A **NavigationProperty**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-632">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-633">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-634">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-634">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-635">下表描述可套用至的屬性**NavigationProperty**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="e0bcc-636">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-636">Attribute Name</span></span>   | <span data-ttu-id="e0bcc-637">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-637">Is Required</span></span> | <span data-ttu-id="e0bcc-638">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-639">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-639">**Name**</span></span>         | <span data-ttu-id="e0bcc-640">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-640">Yes</span></span>         | <span data-ttu-id="e0bcc-641">導覽屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="e0bcc-642">**關聯性**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-642">**Relationship**</span></span> | <span data-ttu-id="e0bcc-643">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-643">Yes</span></span>         | <span data-ttu-id="e0bcc-644">關聯的名稱在模型的範圍之內。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="e0bcc-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-645">**ToRole**</span></span>       | <span data-ttu-id="e0bcc-646">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-646">Yes</span></span>         | <span data-ttu-id="e0bcc-647">位於導覽端點的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="e0bcc-648">值**ToRole**屬性必須是其中一個的值相同**角色**某個關聯端點 （定義於 AssociationEnd 項目） 所定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="e0bcc-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-649">**FromRole**</span></span>     | <span data-ttu-id="e0bcc-650">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-650">Yes</span></span>         | <span data-ttu-id="e0bcc-651">開始導覽的關聯端點。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="e0bcc-652">值**FromRole**屬性必須是其中一個的值相同**角色**某個關聯端點 （定義於 AssociationEnd 項目） 所定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-653">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**NavigationProperty**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="e0bcc-654">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-655">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-656">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-656">Example</span></span>

<span data-ttu-id="e0bcc-657">下列範例會定義實體類型 (**活頁簿**) 具有兩個導覽屬性 (**PublishedBy**並**WrittenBy**):</span><span class="sxs-lookup"><span data-stu-id="e0bcc-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="e0bcc-658">OnDelete 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-659">**OnDelete**概念結構定義語言 (CSDL) 中的項目會定義連接關聯的行為。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="e0bcc-660">如果**動作**屬性設為**Cascade**上關聯的一端，相關的刪除第一個端點上的實體類型時，會刪除關聯另一端的實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="e0bcc-661">如果兩個實體類型之間的關聯是主索引鍵對主索引鍵關聯性，則不論刪除關聯另一端的主體物件時，會刪除載入的相依物件**OnDelete**規格。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="e0bcc-662">**OnDelete**項目只會影響應用程式的執行階段行為，不會影響資料來源中的行為。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="e0bcc-663">資料來源中定義的行為應與應用程式中定義的行為相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="e0bcc-664">**OnDelete**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-665">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-666">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-667">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-667">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-668">下表描述可套用至的屬性**OnDelete**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="e0bcc-669">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-669">Attribute Name</span></span> | <span data-ttu-id="e0bcc-670">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-670">Is Required</span></span> | <span data-ttu-id="e0bcc-671">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-672">**動作**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-672">**Action**</span></span>     | <span data-ttu-id="e0bcc-673">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-673">Yes</span></span>         | <span data-ttu-id="e0bcc-674">**Cascade**或是**None**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-674">**Cascade** or **None**.</span></span> <span data-ttu-id="e0bcc-675">如果**Cascade**，刪除主要實體類型時，將會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="e0bcc-676">如果**無**，刪除主要實體類型時，將不會刪除相依實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-677">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="e0bcc-678">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-679">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-680">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-680">Example</span></span>

<span data-ttu-id="e0bcc-681">下列範例所示**關聯**項目，定義**CustomerOrders**關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="e0bcc-682">**OnDelete**項目表示所有**訂單**與相關的特定**客戶**和已載入至 ObjectContext 會被刪除時**客戶**會被刪除。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="e0bcc-683">Parameter 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-684">**參數**概念結構定義語言 (CSDL) 中的項目可以在 FunctionImport 項目或 Function 項目子系。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="e0bcc-685">FunctionImport 項目的應用</span><span class="sxs-lookup"><span data-stu-id="e0bcc-685">FunctionImport Element Application</span></span>

<span data-ttu-id="e0bcc-686">A**參數**項目 (為子系**FunctionImport**項目) 用來在 CSDL 中定義宣告的函式匯入的輸入和輸出參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="e0bcc-687">**參數**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-688">文件 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-689">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-690">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-690">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-691">下表描述可套用至的屬性**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="e0bcc-692">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-692">Attribute Name</span></span> | <span data-ttu-id="e0bcc-693">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-693">Is Required</span></span> | <span data-ttu-id="e0bcc-694">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-695">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-695">**Name**</span></span>       | <span data-ttu-id="e0bcc-696">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-696">Yes</span></span>         | <span data-ttu-id="e0bcc-697">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="e0bcc-698">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-698">**Type**</span></span>       | <span data-ttu-id="e0bcc-699">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-699">Yes</span></span>         | <span data-ttu-id="e0bcc-700">參數型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-700">The parameter type.</span></span> <span data-ttu-id="e0bcc-701">此值必須是**EDMSimpleType**或是模型範圍內的複雜類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="e0bcc-702">**模式**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-702">**Mode**</span></span>       | <span data-ttu-id="e0bcc-703">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-703">No</span></span>          | <span data-ttu-id="e0bcc-704">**在 **， **Out**，或**InOut**取決於參數是輸入、 輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="e0bcc-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-705">**MaxLength**</span></span>  | <span data-ttu-id="e0bcc-706">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-706">No</span></span>          | <span data-ttu-id="e0bcc-707">可允許的最大參數長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="e0bcc-708">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-708">**Precision**</span></span>  | <span data-ttu-id="e0bcc-709">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-709">No</span></span>          | <span data-ttu-id="e0bcc-710">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-711">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-711">**Scale**</span></span>      | <span data-ttu-id="e0bcc-712">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-712">No</span></span>          | <span data-ttu-id="e0bcc-713">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="e0bcc-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-714">**SRID**</span></span>       | <span data-ttu-id="e0bcc-715">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-715">No</span></span>          | <span data-ttu-id="e0bcc-716">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-717">僅適用於空間類型的參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="e0bcc-718">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-718">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-719">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="e0bcc-720">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-721">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-722">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-722">Example</span></span>

<span data-ttu-id="e0bcc-723">下列範例所示**FunctionImport**項目包含一個**參數**子項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="e0bcc-724">函式接受一個輸入參數，然後傳回實體類型的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="e0bcc-725">Function 項目的應用</span><span class="sxs-lookup"><span data-stu-id="e0bcc-725">Function Element Application</span></span>

<span data-ttu-id="e0bcc-726">A**參數**項目 (為子系**函式**項目) 概念模型中定義之函式的定義或宣告的參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="e0bcc-727">**參數**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-728">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="e0bcc-729">CollectionType （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="e0bcc-730">ReferenceType （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="e0bcc-731">資料列型別 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-732">只有其中一個**CollectionType**， **ReferenceType**，或**RowType**項目可以是子項目**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="e0bcc-733">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-734">Annotation 項目必須出現在所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="e0bcc-735">Annotation 項目才允許在 CSDL v2 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-736">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-736">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-737">下表描述可套用至的屬性**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="e0bcc-738">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-738">Attribute Name</span></span>   | <span data-ttu-id="e0bcc-739">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-739">Is Required</span></span> | <span data-ttu-id="e0bcc-740">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-741">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-741">**Name**</span></span>         | <span data-ttu-id="e0bcc-742">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-742">Yes</span></span>         | <span data-ttu-id="e0bcc-743">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="e0bcc-744">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-744">**Type**</span></span>         | <span data-ttu-id="e0bcc-745">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-745">No</span></span>          | <span data-ttu-id="e0bcc-746">參數型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-746">The parameter type.</span></span> <span data-ttu-id="e0bcc-747">參數可以是下列任何一種型別 (或這些型別的集合)：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="e0bcc-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="e0bcc-749">Entity Type - 實體類型</span><span class="sxs-lookup"><span data-stu-id="e0bcc-749">entity type</span></span> <br/> <span data-ttu-id="e0bcc-750">複雜類型</span><span class="sxs-lookup"><span data-stu-id="e0bcc-750">complex type</span></span> <br/> <span data-ttu-id="e0bcc-751">資料列型別</span><span class="sxs-lookup"><span data-stu-id="e0bcc-751">row type</span></span> <br/> <span data-ttu-id="e0bcc-752">參考類型</span><span class="sxs-lookup"><span data-stu-id="e0bcc-752">reference type</span></span>                             |
| <span data-ttu-id="e0bcc-753">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-753">**Nullable**</span></span>     | <span data-ttu-id="e0bcc-754">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-754">No</span></span>          | <span data-ttu-id="e0bcc-755">**真**（預設值） 或**False**根據屬性是否可以有**null**值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="e0bcc-756">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-756">**DefaultValue**</span></span> | <span data-ttu-id="e0bcc-757">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-757">No</span></span>          | <span data-ttu-id="e0bcc-758">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="e0bcc-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-759">**MaxLength**</span></span>    | <span data-ttu-id="e0bcc-760">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-760">No</span></span>          | <span data-ttu-id="e0bcc-761">屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-762">**FixedLength**</span></span>  | <span data-ttu-id="e0bcc-763">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-763">No</span></span>          | <span data-ttu-id="e0bcc-764">**真**或是**False**根據屬性值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="e0bcc-765">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-765">**Precision**</span></span>    | <span data-ttu-id="e0bcc-766">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-766">No</span></span>          | <span data-ttu-id="e0bcc-767">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="e0bcc-768">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-768">**Scale**</span></span>        | <span data-ttu-id="e0bcc-769">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-769">No</span></span>          | <span data-ttu-id="e0bcc-770">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="e0bcc-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-771">**SRID**</span></span>         | <span data-ttu-id="e0bcc-772">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-772">No</span></span>          | <span data-ttu-id="e0bcc-773">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-774">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="e0bcc-775">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-775">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="e0bcc-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-776">**Unicode**</span></span>      | <span data-ttu-id="e0bcc-777">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-777">No</span></span>          | <span data-ttu-id="e0bcc-778">**真**或是**False**根據屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="e0bcc-779">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-779">**Collation**</span></span>    | <span data-ttu-id="e0bcc-780">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-780">No</span></span>          | <span data-ttu-id="e0bcc-781">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-782">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="e0bcc-783">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-784">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-785">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-785">Example</span></span>

<span data-ttu-id="e0bcc-786">下列範例所示**函式**項目，會使用其中一個**參數**子項目定義的函式參數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
``` 

 

## <a name="principal-element-csdl"></a><span data-ttu-id="e0bcc-787">Principal 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-788">**主體**概念結構定義語言 (CSDL) 中的項目是 ReferentialConstraint 項目，定義參考條件約束的主體端點的子項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="e0bcc-789">A **ReferentialConstraint**項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="e0bcc-790">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="e0bcc-791">參考的實體類型稱為*主體端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="e0bcc-792">參考主要端點的實體類型稱為*相依端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="e0bcc-793">**PropertyRef**元素用來指定索引鍵所參考的相依端點。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="e0bcc-794">**主體**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-795">PropertyRef （一或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="e0bcc-796">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-797">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-797">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-798">下表描述可套用至的屬性**主體**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="e0bcc-799">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-799">Attribute Name</span></span> | <span data-ttu-id="e0bcc-800">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-800">Is Required</span></span> | <span data-ttu-id="e0bcc-801">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-802">**角色**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-802">**Role**</span></span>       | <span data-ttu-id="e0bcc-803">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-803">Yes</span></span>         | <span data-ttu-id="e0bcc-804">位於關聯之主要端點的實體類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-805">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**主體**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="e0bcc-806">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-807">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-808">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-808">Example</span></span>

<span data-ttu-id="e0bcc-809">下列範例所示**ReferentialConstraint**項目所定義的一部分**PublishedBy**關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="e0bcc-810">**識別碼**屬性**發行者**構成參考條件約束的主體端點的實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="e0bcc-811">Property 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-811">Property Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-812">**屬性**概念結構定義語言 (CSDL) 中的項目可以是 EntityType 項目、 ComplexType 項目或 RowType 項目子系。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="e0bcc-813">EntityType 和 ComplexType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="e0bcc-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="e0bcc-814">**屬性**項目 (為的子系**EntityType**或是**ComplexType**項目) 定義的圖形和特性的實體類型執行個體或複雜類型執行個體將包含的資料.</span><span class="sxs-lookup"><span data-stu-id="e0bcc-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="e0bcc-815">概念模型中的屬性類似類別中定義的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="e0bcc-816">如同類別上的屬性可定義類別的圖形並包含關於物件的資訊，概念模型的屬性可定義實體類別的圖形，並包含關於實體類型執行個體的資訊。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="e0bcc-817">**屬性**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-818">Documentation 項目 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-819">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="e0bcc-820">下列 facet 可套用至**屬性**項目： **Nullable**， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，**定序**， **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="e0bcc-821">Facet 是 XML 屬性 (attribute)，提供關於屬性 (property) 值如何儲存在資料存放區資訊。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-822">Facet 只能套用至屬性的型別**EDMSimpleType**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-823">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-823">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-824">下表描述可套用至的屬性**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="e0bcc-825">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-825">Attribute Name</span></span>                                                         | <span data-ttu-id="e0bcc-826">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-826">Is Required</span></span> | <span data-ttu-id="e0bcc-827">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-828">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-828">**Name**</span></span>                                                               | <span data-ttu-id="e0bcc-829">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-829">Yes</span></span>         | <span data-ttu-id="e0bcc-830">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-831">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-831">**Type**</span></span>                                                               | <span data-ttu-id="e0bcc-832">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-832">Yes</span></span>         | <span data-ttu-id="e0bcc-833">屬性值的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-833">The type of the property value.</span></span> <span data-ttu-id="e0bcc-834">屬性值類型必須是**EDMSimpleType**或複雜類型 （以完整的名稱） 之模型的範圍內。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="e0bcc-835">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-835">**Nullable**</span></span>                                                           | <span data-ttu-id="e0bcc-836">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-836">No</span></span>          | <span data-ttu-id="e0bcc-837">**真**（預設值） 或<strong>False</strong>根據屬性是否有 null 值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="e0bcc-838">> 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-839">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="e0bcc-840">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-840">No</span></span>          | <span data-ttu-id="e0bcc-841">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="e0bcc-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="e0bcc-843">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-843">No</span></span>          | <span data-ttu-id="e0bcc-844">屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="e0bcc-846">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-846">No</span></span>          | <span data-ttu-id="e0bcc-847">**真**或是**False**根據屬性值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="e0bcc-848">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-848">**Precision**</span></span>                                                          | <span data-ttu-id="e0bcc-849">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-849">No</span></span>          | <span data-ttu-id="e0bcc-850">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="e0bcc-851">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-851">**Scale**</span></span>                                                              | <span data-ttu-id="e0bcc-852">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-852">No</span></span>          | <span data-ttu-id="e0bcc-853">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="e0bcc-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-854">**SRID**</span></span>                                                               | <span data-ttu-id="e0bcc-855">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-855">No</span></span>          | <span data-ttu-id="e0bcc-856">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-857">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="e0bcc-858">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-858">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="e0bcc-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-859">**Unicode**</span></span>                                                            | <span data-ttu-id="e0bcc-860">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-860">No</span></span>          | <span data-ttu-id="e0bcc-861">**真**或是**False**根據屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="e0bcc-862">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-862">**Collation**</span></span>                                                          | <span data-ttu-id="e0bcc-863">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-863">No</span></span>          | <span data-ttu-id="e0bcc-864">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="e0bcc-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="e0bcc-866">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-866">No</span></span>          | <span data-ttu-id="e0bcc-867">**無**（預設值） 或**Fixed**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="e0bcc-868">如果值設定為**Fixed**，屬性值將用於開放式並行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-869">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="e0bcc-870">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-871">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-872">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-872">Example</span></span>

<span data-ttu-id="e0bcc-873">下列範例所示**EntityType**具有三個項目**屬性**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="e0bcc-874">下列範例所示**ComplexType**具有五個項目**屬性**項目：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="e0bcc-875">RowType 項目的應用程式</span><span class="sxs-lookup"><span data-stu-id="e0bcc-875">RowType Element Application</span></span>

<span data-ttu-id="e0bcc-876">**屬性**項目 (為的子系**RowType**項目) 定義的圖案和特性，可以傳遞至或從模型定義函式傳回的資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="e0bcc-877">**屬性**項目可以有一個下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-878">CollectionType</span></span>
-   <span data-ttu-id="e0bcc-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-879">ReferenceType</span></span>
-   <span data-ttu-id="e0bcc-880">RowType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-880">RowType</span></span>

<span data-ttu-id="e0bcc-881">**屬性**項目可以有任何數目的子系 annotation 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-882">Annotation 項目才允許在 CSDL v2 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-883">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-883">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-884">下表描述可套用至的屬性**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="e0bcc-885">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-885">Attribute Name</span></span>                                                     | <span data-ttu-id="e0bcc-886">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-886">Is Required</span></span> | <span data-ttu-id="e0bcc-887">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-888">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-888">**Name**</span></span>                                                           | <span data-ttu-id="e0bcc-889">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-889">Yes</span></span>         | <span data-ttu-id="e0bcc-890">屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-891">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-891">**Type**</span></span>                                                           | <span data-ttu-id="e0bcc-892">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-892">Yes</span></span>         | <span data-ttu-id="e0bcc-893">屬性值的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-894">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-894">**Nullable**</span></span>                                                       | <span data-ttu-id="e0bcc-895">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-895">No</span></span>          | <span data-ttu-id="e0bcc-896">**真**（預設值） 或**False**根據屬性是否有 null 值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="e0bcc-897">> 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-898">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="e0bcc-899">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-899">No</span></span>          | <span data-ttu-id="e0bcc-900">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="e0bcc-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="e0bcc-902">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-902">No</span></span>          | <span data-ttu-id="e0bcc-903">屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="e0bcc-905">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-905">No</span></span>          | <span data-ttu-id="e0bcc-906">**真**或是**False**根據屬性值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="e0bcc-907">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-907">**Precision**</span></span>                                                      | <span data-ttu-id="e0bcc-908">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-908">No</span></span>          | <span data-ttu-id="e0bcc-909">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="e0bcc-910">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-910">**Scale**</span></span>                                                          | <span data-ttu-id="e0bcc-911">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-911">No</span></span>          | <span data-ttu-id="e0bcc-912">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="e0bcc-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-913">**SRID**</span></span>                                                           | <span data-ttu-id="e0bcc-914">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-914">No</span></span>          | <span data-ttu-id="e0bcc-915">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-916">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="e0bcc-917">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-917">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="e0bcc-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-918">**Unicode**</span></span>                                                        | <span data-ttu-id="e0bcc-919">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-919">No</span></span>          | <span data-ttu-id="e0bcc-920">**真**或是**False**根據屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="e0bcc-921">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-921">**Collation**</span></span>                                                      | <span data-ttu-id="e0bcc-922">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-922">No</span></span>          | <span data-ttu-id="e0bcc-923">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-924">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="e0bcc-925">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-926">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="e0bcc-927">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-927">Example</span></span>

<span data-ttu-id="e0bcc-928">下列範例所示**屬性**用來定義模型定義函式的傳回類型的圖形項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="e0bcc-929">PropertyRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-930">**PropertyRef**概念結構定義語言 (CSDL) 中的項目會參考的實體類型表示的屬性將執行下列角色的其中一個屬性：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="e0bcc-931">實體索引鍵的一部分 (可判斷識別之實體類型的屬性或屬性集)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="e0bcc-932">一或多個**PropertyRef**項目可以用來定義實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="e0bcc-933">參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="e0bcc-934">**PropertyRef**元素只能有 annotation 項目 （零或多個） 做為子系項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-935">Annotation 項目才允許在 CSDL v2 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-936">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-936">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-937">下表描述可套用至的屬性**PropertyRef**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="e0bcc-938">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-938">Attribute Name</span></span> | <span data-ttu-id="e0bcc-939">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-939">Is Required</span></span> | <span data-ttu-id="e0bcc-940">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="e0bcc-941">**名稱**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-941">**Name**</span></span>       | <span data-ttu-id="e0bcc-942">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-942">Yes</span></span>         | <span data-ttu-id="e0bcc-943">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-944">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**PropertyRef**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="e0bcc-945">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-946">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-947">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-947">Example</span></span>

<span data-ttu-id="e0bcc-948">以下範例定義實體類型 (**活頁簿**)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="e0bcc-949">藉由參考定義的實體索引鍵**ISBN**之實體類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="e0bcc-950">在下一個範例中，兩個**PropertyRef**項目用來表示兩個屬性 (**識別碼**並**PublisherId**) 是主體端和相依端的參考條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="e0bcc-951">ReferenceType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-952">**ReferenceType**概念結構定義語言 (CSDL) 中的項目會指定實體類型的參考。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="e0bcc-953">**ReferenceType**項目可以是下列項目子系：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="e0bcc-954">ReturnType （函式）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="e0bcc-955">參數</span><span class="sxs-lookup"><span data-stu-id="e0bcc-955">Parameter</span></span>
-   <span data-ttu-id="e0bcc-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-956">CollectionType</span></span>

<span data-ttu-id="e0bcc-957">**ReferenceType**定義函式的參數或傳回類型時，會使用項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="e0bcc-958">A **ReferenceType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-959">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-960">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-961">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-961">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-962">下表描述可套用至的屬性**ReferenceType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="e0bcc-963">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-963">Attribute Name</span></span> | <span data-ttu-id="e0bcc-964">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-964">Is Required</span></span> | <span data-ttu-id="e0bcc-965">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="e0bcc-966">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-966">**Type**</span></span>       | <span data-ttu-id="e0bcc-967">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-967">Yes</span></span>         | <span data-ttu-id="e0bcc-968">參考之實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-969">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReferenceType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="e0bcc-970">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-971">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-972">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-972">Example</span></span>

<span data-ttu-id="e0bcc-973">下列範例所示**ReferenceType**當做子系的項目**參數**接受參考的模型定義函式中的項目**人員**實體類型：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="e0bcc-974">下列範例所示**ReferenceType**當做子系的項目**ReturnType** （函數） 傳回的參考的模型定義函式中的項目**人員**實體類型：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="e0bcc-975">ReferentialConstraint 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-976">A **ReferentialConstraint**概念結構定義語言 (CSDL) 中的項目會定義類似於關聯式資料庫中的參考完整性條件約束的功能。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="e0bcc-977">同樣地，資料庫資料表的資料行 (或多個資料行) 可以參考其他資料表的主索引鍵，實體類型的屬性 (或多個屬性) 可以參考其他實體類型的實體索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="e0bcc-978">參考的實體類型稱為*主體端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="e0bcc-979">參考主要端點的實體類型稱為*相依端點*條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="e0bcc-980">如果一個實體類型公開外部索引鍵參考另一個的實體類型上的屬性**ReferentialConstraint**項目會定義兩個實體類型之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="e0bcc-981">因為**ReferentialConstraint**項目會提供如何在兩個實體類型的相關資訊與相關，沒有對應的 AssociationSetMapping 項目是對應規格語言 (MSL) 中的必要。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="e0bcc-982">沒有公開外部索引鍵的兩個實體類型之間的關聯必須有對應**AssociationSetMapping**項目，以便將關聯資訊對應至資料來源。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="e0bcc-983">如果實體型別上沒有公開外部索引鍵**ReferentialConstraint**項目只能定義實體類型與另一個實體類型之間的主索引鍵對主索引鍵限制。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="e0bcc-984">A **ReferentialConstraint**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-985">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-986">主體 （只有一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="e0bcc-987">相依 （只有一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="e0bcc-988">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-989">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-989">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-990">**ReferentialConstraint**項目可以有任意數目的附註屬性 （自訂 XML 屬性）。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="e0bcc-991">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-992">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-993">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-993">Example</span></span>

<span data-ttu-id="e0bcc-994">下列範例所示**ReferentialConstraint**做為定義的一部分的項目**PublishedBy**關聯。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="e0bcc-995">ReturnType （函式） 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-996">**ReturnType** （函式） 在概念結構定義語言 (CSDL) 中的項目會指定函式項目中定義的函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="e0bcc-997">傳回型別也可以使用指定的函式**ReturnType**屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="e0bcc-998">傳回類型可以是任何**EdmSimpleType**，實體類型、 複雜型別、 資料列型別、 ref 型別或其中一種類型的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="e0bcc-999">可以使用其中一個指定的函式的傳回型別**型別**屬性**ReturnType** (Function) 的項目，或使用其中一個下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1000">CollectionType</span></span>
-   <span data-ttu-id="e0bcc-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1001">ReferenceType</span></span>
-   <span data-ttu-id="e0bcc-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-1003">如果您指定函式的傳回型別都不會驗證模型**型別**屬性**ReturnType** (Function) 項目，以及其中一個子項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1004">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1004">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1005">下表描述可套用至的屬性**ReturnType** (Function) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="e0bcc-1006">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1006">Attribute Name</span></span> | <span data-ttu-id="e0bcc-1007">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1007">Is Required</span></span> | <span data-ttu-id="e0bcc-1008">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="e0bcc-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1009">**ReturnType**</span></span> | <span data-ttu-id="e0bcc-1010">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1010">No</span></span>          | <span data-ttu-id="e0bcc-1011">此函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-1012">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType** (Function) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="e0bcc-1013">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1014">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-1015">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1015">Example</span></span>

<span data-ttu-id="e0bcc-1016">下列範例會使用**函式**項目來定義傳回一本書已在列印中的年數的函式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="e0bcc-1017">請注意，所指定的傳回型別**型別**屬性**ReturnType** (Function) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="e0bcc-1018">ReturnType (FunctionImport) 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-1019">**ReturnType** (FunctionImport) 在概念結構定義語言 (CSDL) 中的項目指定的 FunctionImport 項目中定義的函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="e0bcc-1020">傳回型別也可以使用指定的函式**ReturnType**屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="e0bcc-1021">傳回型別可以是實體類型、 複雜類型的任何集合或**EdmSimpleType**，</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="e0bcc-1022">使用指定的函式的傳回型別**型別**屬性**ReturnType** (FunctionImport) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1023">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1023">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1024">下表描述可套用至的屬性**ReturnType** (FunctionImport) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="e0bcc-1025">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1025">Attribute Name</span></span> | <span data-ttu-id="e0bcc-1026">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1026">Is Required</span></span> | <span data-ttu-id="e0bcc-1027">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-1028">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1028">**Type**</span></span>       | <span data-ttu-id="e0bcc-1029">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1029">No</span></span>          | <span data-ttu-id="e0bcc-1030">函式傳回的型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1030">The type that the function returns.</span></span> <span data-ttu-id="e0bcc-1031">值必須是 ComplexType、 EntityType 或 EDMSimpleType 的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="e0bcc-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1032">**EntitySet**</span></span>  | <span data-ttu-id="e0bcc-1033">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1033">No</span></span>          | <span data-ttu-id="e0bcc-1034">如果函數傳回實體集合類型的值**EntitySet**必須實體集所屬的集合。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="e0bcc-1035">否則，請**EntitySet**屬性不能使用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-1036">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType** (FunctionImport) 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="e0bcc-1037">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1038">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-1039">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1039">Example</span></span>

<span data-ttu-id="e0bcc-1040">下列範例會使用**FunctionImport**傳回書籍和發行者。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="e0bcc-1041">請注意，此函數會傳回兩個結果集，因此兩個**ReturnType** (FunctionImport) 項目指定。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="e0bcc-1042">RowType 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-1043">A **RowType**概念結構定義語言 (CSDL) 中的項目做為參數或傳回型別定義概念模型中的函式定義未命名的結構。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="e0bcc-1044">A **RowType**項目可以是下列項目子系：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="e0bcc-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1045">CollectionType</span></span>
-   <span data-ttu-id="e0bcc-1046">參數</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1046">Parameter</span></span>
-   <span data-ttu-id="e0bcc-1047">ReturnType （函式）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1047">ReturnType (Function)</span></span>

<span data-ttu-id="e0bcc-1048">A **RowType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-1049">屬性 （一或多個）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1049">Property (one or more)</span></span>
-   <span data-ttu-id="e0bcc-1050">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1051">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1051">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1052">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**RowType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="e0bcc-1053">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1054">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-1055">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1055">Example</span></span>

<span data-ttu-id="e0bcc-1056">下列範例示範使用的模型定義函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="e0bcc-1057">Schema 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-1058">**結構描述**項目是概念模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="e0bcc-1059">其中包含組成概念模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="e0bcc-1060">**結構描述**項目可能包含零或多個下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-1061">Using</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1061">Using</span></span>
-   <span data-ttu-id="e0bcc-1062">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1062">EntityContainer</span></span>
-   <span data-ttu-id="e0bcc-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1063">EntityType</span></span>
-   <span data-ttu-id="e0bcc-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1064">EnumType</span></span>
-   <span data-ttu-id="e0bcc-1065">關聯</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1065">Association</span></span>
-   <span data-ttu-id="e0bcc-1066">ComplexType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1066">ComplexType</span></span>
-   <span data-ttu-id="e0bcc-1067">功能</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1067">Function</span></span>

<span data-ttu-id="e0bcc-1068">A**結構描述**項目可以包含零個或一個註釋項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-1069">**函式**項目和註釋項目才允許在 CSDL v2 和更新版本。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="e0bcc-1070">**結構描述**項目會使用**命名空間**屬性來定義概念模型中的實體類型、 複雜型別和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="e0bcc-1071">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="e0bcc-1072">命名空間可以跨多個**結構描述**項目和多個.csdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="e0bcc-1073">概念模型命名空間是 XML 命名空間的不同**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="e0bcc-1074">概念模型命名空間 (如所定義**命名空間**屬性) 是實體類型、 複雜型別和關聯型別的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="e0bcc-1075">XML 命名空間 (由**xmlns**屬性) 的**結構描述**項目是子元素和屬性的預設命名空間**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="e0bcc-1076">XML 命名空間的表單 http://schemas.microsoft.com/ado/YYYY/MM/edm （其中 YYYY 和 MM 表示年和月分別） 是保留供 CSDL。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1076">XML namespaces of the form http://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1077">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1078">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1078">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1079">下表描述的屬性可以套用至**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="e0bcc-1080">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1080">Attribute Name</span></span> | <span data-ttu-id="e0bcc-1081">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1081">Is Required</span></span> | <span data-ttu-id="e0bcc-1082">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-1083">**命名空間**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1083">**Namespace**</span></span>  | <span data-ttu-id="e0bcc-1084">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1084">Yes</span></span>         | <span data-ttu-id="e0bcc-1085">概念模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="e0bcc-1086">值**命名空間**屬性用來構成型別的完整的名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="e0bcc-1087">例如，如果**EntityType**名為*客戶*在 Simple.Example.Model 命名空間，則完整限定的名稱是**EntityType**是SimpleExampleModel.Customer。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="e0bcc-1088">下列字串不能做為值**命名空間**屬性：**系統**，**暫時性**，或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="e0bcc-1089">值**命名空間**屬性不能做為值相同**命名空間**SSDL 結構描述項目中的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="e0bcc-1090">**Alias**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1090">**Alias**</span></span>      | <span data-ttu-id="e0bcc-1091">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1091">No</span></span>          | <span data-ttu-id="e0bcc-1092">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="e0bcc-1093">例如，如果**EntityType**名為*客戶*在 Simple.Example.Model 命名空間和值**別名**屬性是*模型*，然後您可以使用 Model.Customer 做為完整格式名稱**EntityType。**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-1094">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="e0bcc-1095">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1096">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-1097">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1097">Example</span></span>

<span data-ttu-id="e0bcc-1098">下列範例所示**結構描述**包含的項目**EntityContainer**元素中，兩個**EntityType**項目和一個**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="e0bcc-1099">TypeRef 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-1100">**TypeRef**概念結構定義語言 (CSDL) 中的項目提供現有具名型別的參考。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="e0bcc-1101">**TypeRef**項目可以是 CollectionType 項目，這用來指定函式已為參數或傳回型別集合的子系。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="e0bcc-1102">A **TypeRef**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e0bcc-1103">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e0bcc-1104">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1105">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1105">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1106">下表描述可套用至的屬性**TypeRef**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="e0bcc-1107">請注意， **DefaultValue**， **MaxLength**， **FixedLength**，**精確度**，**擴展**， **Unicode**，並**定序**屬性僅適用於**EDMSimpleTypes**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="e0bcc-1108">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="e0bcc-1109">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1109">Is Required</span></span> | <span data-ttu-id="e0bcc-1110">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-1111">**Type**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1111">**Type**</span></span>                                                           | <span data-ttu-id="e0bcc-1112">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1112">No</span></span>          | <span data-ttu-id="e0bcc-1113">所參考的型別名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="e0bcc-1114">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="e0bcc-1115">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1115">No</span></span>          | <span data-ttu-id="e0bcc-1116">**真**（預設值） 或**False**根據屬性是否有 null 值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="e0bcc-1117">> 在 CSDL v1 的複雜類型屬性必須有`Nullable="False"`。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="e0bcc-1118">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="e0bcc-1119">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1119">No</span></span>          | <span data-ttu-id="e0bcc-1120">屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="e0bcc-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="e0bcc-1122">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1122">No</span></span>          | <span data-ttu-id="e0bcc-1123">屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="e0bcc-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="e0bcc-1125">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1125">No</span></span>          | <span data-ttu-id="e0bcc-1126">**真**或是**False**根據屬性值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="e0bcc-1127">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1127">**Precision**</span></span>                                                      | <span data-ttu-id="e0bcc-1128">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1128">No</span></span>          | <span data-ttu-id="e0bcc-1129">屬性值的有效位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="e0bcc-1130">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1130">**Scale**</span></span>                                                          | <span data-ttu-id="e0bcc-1131">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1131">No</span></span>          | <span data-ttu-id="e0bcc-1132">屬性值的小數位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="e0bcc-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1133">**SRID**</span></span>                                                           | <span data-ttu-id="e0bcc-1134">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1134">No</span></span>          | <span data-ttu-id="e0bcc-1135">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e0bcc-1136">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="e0bcc-1137">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1137">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="e0bcc-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="e0bcc-1139">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1139">No</span></span>          | <span data-ttu-id="e0bcc-1140">**真**或是**False**根據屬性值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="e0bcc-1141">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1141">**Collation**</span></span>                                                      | <span data-ttu-id="e0bcc-1142">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1142">No</span></span>          | <span data-ttu-id="e0bcc-1143">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-1144">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="e0bcc-1145">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1146">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-1147">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1147">Example</span></span>

<span data-ttu-id="e0bcc-1148">下列範例示範使用的模型定義函式**TypeRef**項目 (為子系**CollectionType**項目) 來指定函數可接受的集合**部門**實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="e0bcc-1149">Using 項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="e0bcc-1150">**Using**概念結構定義語言 (CSDL) 中的項目會匯入存在於不同的命名空間的概念模型的內容。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="e0bcc-1151">藉由設定的值**命名空間**屬性，您可以參考實體類型、 複雜型別和關聯型別。 另一個概念模型中定義。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="e0bcc-1152">多個**Using**項目可以是子系**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-1153">**Using** CSDL 中的項目無法運作完全相同**使用**程式設計語言中的陳述式。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="e0bcc-1154">藉由匯入的命名空間**使用**陳述式在程式設計語言中，您不會影響原始命名空間中的物件。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="e0bcc-1155">在 CSDL 中，匯入的命名空間可以包含實體類型，該實體類型是衍生自原始命名空間中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="e0bcc-1156">這會影響在原始命名空間中宣告的實體集。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="e0bcc-1157">**Using**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="e0bcc-1158">文件 （允許零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="e0bcc-1159">Annotation 項目 （允許零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e0bcc-1160">適用屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1160">Applicable Attributes</span></span>

<span data-ttu-id="e0bcc-1161">下表描述的屬性可以套用至**Using**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="e0bcc-1162">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1162">Attribute Name</span></span> | <span data-ttu-id="e0bcc-1163">必要</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1163">Is Required</span></span> | <span data-ttu-id="e0bcc-1164">值</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-1165">**命名空間**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1165">**Namespace**</span></span>  | <span data-ttu-id="e0bcc-1166">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1166">Yes</span></span>         | <span data-ttu-id="e0bcc-1167">匯入的命名空間名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="e0bcc-1168">**Alias**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1168">**Alias**</span></span>      | <span data-ttu-id="e0bcc-1169">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1169">Yes</span></span>         | <span data-ttu-id="e0bcc-1170">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="e0bcc-1171">雖然這個屬性是必要的，但是不必使用此屬性取代命名空間名稱，來限定物件名稱。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="e0bcc-1172">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**Using**項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="e0bcc-1173">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e0bcc-1174">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="e0bcc-1175">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1175">Example</span></span>

<span data-ttu-id="e0bcc-1176">下列範例示範**Using**用來匯入的命名空間的項目定義其他位置。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="e0bcc-1177">請注意，命名空間**結構描述**所示的項目是`BooksModel`。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="e0bcc-1178">`Address`上的屬性`Publisher` **EntityType**是複雜類型中定義`ExtendedBooksModel`命名空間 (使用匯入**Using**項目)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="e0bcc-1179">註釋屬性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="e0bcc-1180">概念結構描述定義語言 (CSDL) 中的附註屬性是概念模型中自訂的 XML 屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="e0bcc-1181">除了擁有有效的 XML 結構外，下列附註屬性的條件必須成立：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="e0bcc-1182">附註屬性不能在任何 XML 命名空間之中，這是保留供 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="e0bcc-1183">超過一個以上的附註屬性可以套用至給定的 CSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="e0bcc-1184">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="e0bcc-1185">附註屬性可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="e0bcc-1186">可以使用 System.Data.Metadata.Edm 命名空間中的類別，在執行階段存取 annotation 項目中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-1187">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1187">Example</span></span>

<span data-ttu-id="e0bcc-1188">下列範例所示**EntityType**具有註釋屬性項目 (**atribut customattribute s**)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="e0bcc-1189">下列範例也顯示套用至實體類型項目的 Annotation 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1189">The example also shows an annotation element applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
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
 

<span data-ttu-id="e0bcc-1190">下列程式碼會擷取附註屬性中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="e0bcc-1191">以上程式碼假設 `School.csdl` 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="e0bcc-1192">註釋項目 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="e0bcc-1193">概念結構定義語言 (CSDL) 中的 Annotation 項目是概念模型中的自訂 XML 項目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="e0bcc-1194">除了擁有有效的 XML 結構外，下列 Annotation 項目的條件也必須成立：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="e0bcc-1195">Annotation 項目不能存在於保留供 CSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="e0bcc-1196">多個 Annotation 項目可以同時為指定 CSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="e0bcc-1197">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="e0bcc-1198">Annotation 項目必須出現在指定 CSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="e0bcc-1199">Annotation 項目可用來提供與概念模型中之項目有關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="e0bcc-1200">從.NET Framework 第 4 版開始，中繼資料包含在 annotation 項目可以存取在執行階段使用 System.Data.Metadata.Edm 命名空間中類別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-1201">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1201">Example</span></span>

<span data-ttu-id="e0bcc-1202">下列範例所示**EntityType** annotation 項目具有項目 (**CustomElement**)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="e0bcc-1203">該範例也顯示套用至實體類型項目的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
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
 

<span data-ttu-id="e0bcc-1204">下列程式碼會擷取 annotation 項目中的中繼資料，並且將它撰寫至主控台：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="e0bcc-1205">以上程式碼假設 School.csdl 檔位於專案的輸出目錄中，而且您已將下列 `Imports` 和 `Using` 陳述式加入至專案：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="e0bcc-1206">概念模型類型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="e0bcc-1207">概念結構定義語言 (CSDL) 支援一組的抽象基本資料型別，稱為**EDMSimpleTypes**，可定義概念模型中的屬性。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="e0bcc-1208">**EDMSimpleTypes**是儲存體或裝載環境中所支援的基本資料型別的 proxy。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="e0bcc-1209">下表列出 CSDL 所支援的基本資料型別。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="e0bcc-1210">這個表格也列出可以套用至每個 facet **EDMSimpleType**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="e0bcc-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="e0bcc-1212">描述</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1212">Description</span></span>                                                | <span data-ttu-id="e0bcc-1213">適用的 Facet</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="e0bcc-1214">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="e0bcc-1215">包含二進位資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="e0bcc-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="e0bcc-1217">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="e0bcc-1218">包含值**真**或是**false**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="e0bcc-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="e0bcc-1220">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="e0bcc-1221">包含不帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="e0bcc-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1223">**以 Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="e0bcc-1224">表示日期和時間。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="e0bcc-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1226">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="e0bcc-1227">包含與 GMT 的日期和時間時差 (以分鐘為單位)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="e0bcc-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1229">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="e0bcc-1230">包含固定有效位數和小數位數的數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="e0bcc-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1232">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="e0bcc-1233">包含浮點數具有 15 位數精確度</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="e0bcc-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1235">**Edm.Float**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="e0bcc-1236">包含具有 7 位數精確度的浮點數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="e0bcc-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1238">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="e0bcc-1239">包含 16 位元組的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="e0bcc-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1241">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="e0bcc-1242">包含帶正負號的 16 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="e0bcc-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1244">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="e0bcc-1245">包含帶正負號的 32 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="e0bcc-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1247">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="e0bcc-1248">包含帶正負號的 64 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="e0bcc-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1250">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="e0bcc-1251">包含帶正負號的 8 位元整數值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="e0bcc-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1253">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1253">**Edm.String**</span></span>                   | <span data-ttu-id="e0bcc-1254">包含字元資料。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1254">Contains character data.</span></span>                                   | <span data-ttu-id="e0bcc-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="e0bcc-1256">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="e0bcc-1257">包含一天的時間。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="e0bcc-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="e0bcc-1259">**Edm.Geography**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="e0bcc-1260">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1261">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="e0bcc-1262">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1263">**Edm.GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="e0bcc-1264">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1265">**Edm.GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="e0bcc-1266">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1267">**Edm.GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="e0bcc-1268">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1269">**Edm.GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="e0bcc-1270">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1271">**Edm.GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="e0bcc-1272">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1273">**Edm.GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="e0bcc-1274">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1275">**Edm.Geometry**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="e0bcc-1276">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1277">**Edm.GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="e0bcc-1278">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1279">**Edm.GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="e0bcc-1280">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1281">**Edm.GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="e0bcc-1282">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1283">**Edm.GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="e0bcc-1284">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1285">**Edm.GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="e0bcc-1286">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1287">**Edm.GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="e0bcc-1288">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="e0bcc-1289">**Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="e0bcc-1290">可為 null，預設的 SRID</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="e0bcc-1291">Facet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1291">Facets (CSDL)</span></span>

<span data-ttu-id="e0bcc-1292">概念結構定義語言 (CSDL) 中的 Facet 表示實體型別和複雜型別屬性上的條件約束。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="e0bcc-1293">Facet 在下列 CSDL 元素上會以 XML 屬性的形式出現：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="e0bcc-1294">屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1294">Property</span></span>
-   <span data-ttu-id="e0bcc-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1295">TypeRef</span></span>
-   <span data-ttu-id="e0bcc-1296">參數</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1296">Parameter</span></span>

<span data-ttu-id="e0bcc-1297">下表說明 CSDL 中支援的 Facet。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="e0bcc-1298">所有的 Facet 都是選擇性的。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1298">All facets are optional.</span></span> <span data-ttu-id="e0bcc-1299">從概念模型產生資料庫時，Entity Framework 會使用下面所列的某些 facet。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="e0bcc-1300">如需概念模型中的資料型別資訊，請參閱概念模型型別 (CSDL)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="e0bcc-1301">Facet</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1301">Facet</span></span>               | <span data-ttu-id="e0bcc-1302">描述</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="e0bcc-1303">適用於</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="e0bcc-1304">用於產生資料庫</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1304">Used for the database generation</span></span> | <span data-ttu-id="e0bcc-1305">由執行階段所使用</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="e0bcc-1306">**定序**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1306">**Collation**</span></span>       | <span data-ttu-id="e0bcc-1307">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="e0bcc-1308">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="e0bcc-1309">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1309">Yes</span></span>                              | <span data-ttu-id="e0bcc-1310">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1310">No</span></span>                  |
| <span data-ttu-id="e0bcc-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="e0bcc-1312">表示屬性值應用於開放式並行存取檢查。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="e0bcc-1313">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="e0bcc-1314">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1314">No</span></span>                               | <span data-ttu-id="e0bcc-1315">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1315">Yes</span></span>                 |
| <span data-ttu-id="e0bcc-1316">**Default**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1316">**Default**</span></span>         | <span data-ttu-id="e0bcc-1317">執行個體化時如果沒有提供值，請指定屬性的預設值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="e0bcc-1318">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="e0bcc-1319">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1319">Yes</span></span>                              | <span data-ttu-id="e0bcc-1320">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1320">Yes</span></span>                 |
| <span data-ttu-id="e0bcc-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1321">**FixedLength**</span></span>     | <span data-ttu-id="e0bcc-1322">指定屬性值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="e0bcc-1323">**Edm.Binary**， **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="e0bcc-1324">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1324">Yes</span></span>                              | <span data-ttu-id="e0bcc-1325">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1325">No</span></span>                  |
| <span data-ttu-id="e0bcc-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1326">**MaxLength**</span></span>       | <span data-ttu-id="e0bcc-1327">指定屬性值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="e0bcc-1328">**Edm.Binary**， **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="e0bcc-1329">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1329">Yes</span></span>                              | <span data-ttu-id="e0bcc-1330">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1330">No</span></span>                  |
| <span data-ttu-id="e0bcc-1331">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1331">**Nullable**</span></span>        | <span data-ttu-id="e0bcc-1332">指定屬性是否可以有**null**值。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="e0bcc-1333">所有**EDMSimpleType**屬性</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="e0bcc-1334">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1334">Yes</span></span>                              | <span data-ttu-id="e0bcc-1335">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1335">Yes</span></span>                 |
| <span data-ttu-id="e0bcc-1336">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1336">**Precision**</span></span>       | <span data-ttu-id="e0bcc-1337">屬性的型別**十進位**，指定屬性值的數字數目。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="e0bcc-1338">屬性的型別**時間**， **DateTime**，並**DateTimeOffset**，指定屬性值秒數的小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="e0bcc-1339">**以 Edm.DateTime**， **Edm.DateTimeOffset**， **Edm.Decimal**， **Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="e0bcc-1340">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1340">Yes</span></span>                              | <span data-ttu-id="e0bcc-1341">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1341">No</span></span>                  |
| <span data-ttu-id="e0bcc-1342">**縮放**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1342">**Scale**</span></span>           | <span data-ttu-id="e0bcc-1343">指定屬性值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="e0bcc-1344">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="e0bcc-1345">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1345">Yes</span></span>                              | <span data-ttu-id="e0bcc-1346">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1346">No</span></span>                  |
| <span data-ttu-id="e0bcc-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1347">**SRID**</span></span>            | <span data-ttu-id="e0bcc-1348">指定系統的空間參考系統識別碼。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="e0bcc-1349">如需詳細資訊，請參閱 [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1349">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="e0bcc-1350">**Edm.Geography、 Edm.GeographyPoint、 Edm.GeographyLineString、 Edm.GeographyPolygon、 Edm.GeographyMultiPoint、 Edm.GeographyMultiLineString、 Edm.GeographyMultiPolygon、 Edm.GeographyCollection、 Edm.Geometry、 Edm.GeometryPoint，Edm.GeometryLineString、 Edm.GeometryPolygon、 Edm.GeometryMultiPoint、 Edm.GeometryMultiLineString、 Edm.GeometryMultiPolygon、 Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="e0bcc-1351">否</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1351">No</span></span>                               | <span data-ttu-id="e0bcc-1352">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1352">Yes</span></span>                 |
| <span data-ttu-id="e0bcc-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1353">**Unicode**</span></span>         | <span data-ttu-id="e0bcc-1354">指出屬性值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="e0bcc-1355">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="e0bcc-1356">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1356">Yes</span></span>                              | <span data-ttu-id="e0bcc-1357">是</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="e0bcc-1358">從概念模型產生資料庫時, 產生資料庫精靈會辨識的值**StoreGeneratedPattern**屬性上**屬性**如果是在下列的項目命名空間： http://schemas.microsoft.com/ado/2009/02/edm/annotation。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: http://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="e0bcc-1359">屬性支援的值為**身分識別**並**計算**。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="e0bcc-1360">值為**識別**會產生識別值在資料庫中產生的資料庫資料行。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="e0bcc-1361">值為**計算**會產生資料庫計算所得值的資料行。</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="e0bcc-1362">範例</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1362">Example</span></span>

<span data-ttu-id="e0bcc-1363">下列範例顯示 Facet 套用至實體類型的屬性：</span><span class="sxs-lookup"><span data-stu-id="e0bcc-1363">The following example shows facets applied to the properties of an entity type:</span></span>

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="http://schemas.microsoft.com/ado/2009/02/edm/annotation" />
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
