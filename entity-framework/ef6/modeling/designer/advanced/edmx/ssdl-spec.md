---
title: SSDL 規格-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
caps.latest.revision: 3
ms.openlocfilehash: a9977c80d9a9401afdcad2284a705bcb28790fb8
ms.sourcegitcommit: 9ae4473425c5e76337c9d032b0e5dbfedf1fcf57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120539"
---
# <a name="ssdl-specification"></a><span data-ttu-id="20ec7-102">SSDL 規格</span><span class="sxs-lookup"><span data-stu-id="20ec7-102">SSDL Specification</span></span>
<span data-ttu-id="20ec7-103">存放結構定義語言 (SSDL) 是一種 XML 架構語言，會描述 Entity Framework 應用程式的儲存體模型。</span><span class="sxs-lookup"><span data-stu-id="20ec7-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="20ec7-104">Entity Framework 應用程式中，儲存體模型中繼資料載入來自.ssdl 檔 （以 SSDL 撰寫） System.Data.Metadata.Edm.StoreItemCollection 的執行個體，則可存取使用中的方法System.Data.Metadata.Edm.MetadataWorkspace 類別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="20ec7-105">Entity Framework 會使用儲存體模型中繼資料，以針對概念模型存放區特有的命令以將查詢轉譯。</span><span class="sxs-lookup"><span data-stu-id="20ec7-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="20ec7-106">Entity Framework Designer （EF 設計工具） 會在設計階段，將儲存體模型資訊儲存在.edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="20ec7-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="20ec7-107">在建置階段 Entity Designer 會使用資訊來建立.ssdl 檔案所需的 Entity Framework 在執行階段在.edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="20ec7-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="20ec7-108">SSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="20ec7-109">SSDL 的版本</span><span class="sxs-lookup"><span data-stu-id="20ec7-109">SSDL Version</span></span> | <span data-ttu-id="20ec7-110">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="20ec7-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="20ec7-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="20ec7-111">SSDL v1</span></span>      | http://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="20ec7-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="20ec7-112">SSDL v2</span></span>      | http://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="20ec7-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="20ec7-113">SSDL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="20ec7-114">Association 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-114">Association Element (SSDL)</span></span>

<span data-ttu-id="20ec7-115">**關聯**存放結構定義語言 (SSDL) 中的項目會指定參與的資料表資料行中的基礎資料庫中的外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="20ec7-116">兩個必要的子端項目會指定在此關聯各端的資料表和每一端的多重性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="20ec7-117">選擇性的 ReferentialConstraint 項目會指定主體和相依端點的關聯，以及參與的資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="20ec7-118">如果沒有**ReferentialConstraint**項目不存在、 AssociationSetMapping 項目必須用來指定關聯的資料行對應。</span><span class="sxs-lookup"><span data-stu-id="20ec7-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="20ec7-119">**關聯**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-120">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-121">結束 （正好兩個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-121">End (exactly two)</span></span>
-   <span data-ttu-id="20ec7-122">ReferentialConstraint （零或一個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="20ec7-123">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-124">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-124">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-125">下表描述可套用至的屬性**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="20ec7-126">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-126">Attribute Name</span></span> | <span data-ttu-id="20ec7-127">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-127">Is Required</span></span> | <span data-ttu-id="20ec7-128">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-129">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-129">**Name**</span></span>       | <span data-ttu-id="20ec7-130">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-130">Yes</span></span>         | <span data-ttu-id="20ec7-131">基礎資料庫中對應之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-132">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="20ec7-133">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-134">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-135">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-135">Example</span></span>

<span data-ttu-id="20ec7-136">下列範例所示**關聯**使用的項目**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders** foreign key 條件約束：</span><span class="sxs-lookup"><span data-stu-id="20ec7-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a><span data-ttu-id="20ec7-137">AssociationSet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="20ec7-138">**AssociationSet**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的兩個資料表之間的外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="20ec7-139">Association 項目中，會指定參與外部索引鍵條件約束的資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="20ec7-140">**關聯**項目，對應至給定**AssociationSet**中所指定的項目**關聯**屬性**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="20ec7-141">SSDL 關聯集會對應至 CSDL 關聯集 AssociationSetMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="20ec7-142">不過，如果指定之 CSDL 關聯的 CSDL 關聯設定會定義使用 ReferentialConstraint 項目，沒有對應的**AssociationSetMapping**項目是必要。</span><span class="sxs-lookup"><span data-stu-id="20ec7-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="20ec7-143">在此情況下，如果**AssociationSetMapping**項目出現時，它會定義的對應會覆寫**ReferentialConstraint**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="20ec7-144">**AssociationSet**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-145">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-146">結束 （零或兩個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-146">End (zero or two)</span></span>
-   <span data-ttu-id="20ec7-147">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-148">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-148">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-149">下表描述可套用至的屬性**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="20ec7-150">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-150">Attribute Name</span></span>  | <span data-ttu-id="20ec7-151">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-151">Is Required</span></span> | <span data-ttu-id="20ec7-152">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-153">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-153">**Name**</span></span>        | <span data-ttu-id="20ec7-154">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-154">Yes</span></span>         | <span data-ttu-id="20ec7-155">關聯集所表示之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="20ec7-156">**關聯**</span><span class="sxs-lookup"><span data-stu-id="20ec7-156">**Association**</span></span> | <span data-ttu-id="20ec7-157">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-157">Yes</span></span>         | <span data-ttu-id="20ec7-158">定義參與外部索引鍵條件約束之資料行的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-159">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="20ec7-160">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-161">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-162">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-162">Example</span></span>

<span data-ttu-id="20ec7-163">下列範例所示**AssociationSet**項目，表示`FK_CustomerOrders`基礎資料庫中的外部索引鍵條件約束：</span><span class="sxs-lookup"><span data-stu-id="20ec7-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="20ec7-164">CollectionType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="20ec7-165">**CollectionType**存放結構定義語言 (SSDL) 中的項目會指定函式的傳回型別是集合。</span><span class="sxs-lookup"><span data-stu-id="20ec7-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="20ec7-166">**CollectionType**項目是子系的 ReturnType 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="20ec7-167">集合型別是由使用資料列型別子項目指定：</span><span class="sxs-lookup"><span data-stu-id="20ec7-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="20ec7-168">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**CollectionType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="20ec7-169">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-170">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-171">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-171">Example</span></span>

<span data-ttu-id="20ec7-172">下列範例示範使用的函式**CollectionType**項目來指定此函數會傳回資料列集合。</span><span class="sxs-lookup"><span data-stu-id="20ec7-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="20ec7-173">CommandText 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="20ec7-174">**CommandText**存放結構定義語言 (SSDL) 中的項目是可讓您定義在資料庫上執行的 SQL 陳述式的函式項目子系。</span><span class="sxs-lookup"><span data-stu-id="20ec7-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="20ec7-175">**CommandText**項目可讓您新增類似於資料庫中的預存程序的功能，但您定義**CommandText**儲存模型中的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="20ec7-176">**CommandText**項目不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="20ec7-177">主體**CommandText**項目必須是有效的 SQL 陳述式為基礎的資料庫。</span><span class="sxs-lookup"><span data-stu-id="20ec7-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="20ec7-178">任何屬性都適用於**CommandText**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-179">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-179">Example</span></span>

<span data-ttu-id="20ec7-180">下列範例所示**函式**項目具有子系**CommandText**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="20ec7-181">公開**UpdateProductInOrder**藉由將它匯入概念模型函式做為方法，藉由對 ObjectContext。</span><span class="sxs-lookup"><span data-stu-id="20ec7-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="20ec7-182">DefiningQuery 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="20ec7-183">**DefiningQuery**存放結構定義語言 (SSDL) 中的項目可讓您直接在基礎資料庫中執行 SQL 陳述式。</span><span class="sxs-lookup"><span data-stu-id="20ec7-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="20ec7-184">**DefiningQuery**資料庫檢視的方式常用項目，但該檢視定義在儲存體模型中，而非資料庫。</span><span class="sxs-lookup"><span data-stu-id="20ec7-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="20ec7-185">中所定義的檢視**DefiningQuery**項目可以對應至概念模型，透過 EntitySetMapping 項目中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="20ec7-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="20ec7-186">這些對應是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="20ec7-186">These mappings are read-only.</span></span>  

<span data-ttu-id="20ec7-187">下列 SSDL 語法顯示的宣告**EntitySet**後面**DefiningQuery**元素，其中包含用來擷取檢視的查詢。</span><span class="sxs-lookup"><span data-stu-id="20ec7-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

<span data-ttu-id="20ec7-188">您可以使用 Entity Framework 中的預存程序，可透過檢視的讀寫案例。</span><span class="sxs-lookup"><span data-stu-id="20ec7-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span> <span data-ttu-id="20ec7-189">您可以使用資料來源檢視或 Entity SQL 檢視的基底資料表擷取資料及進行變更處理預存程序。</span><span class="sxs-lookup"><span data-stu-id="20ec7-189">You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="20ec7-190">您可以使用**DefiningQuery**來以 Microsoft SQL Server Compact 3.5 為目標的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="20ec7-191">雖然 SQL Server Compact 3.5 不支援預存程序，您可以實作類似的功能與**DefiningQuery**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="20ec7-192">該項目的另一個用處是可建立預存程序，克服程式設計語言中所使用之資料類型與資料來源之資料類型間不相符的問題。</span><span class="sxs-lookup"><span data-stu-id="20ec7-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="20ec7-193">您可以撰寫**DefiningQuery**採用一組特定的參數，並接著呼叫具有一組不同的參數，例如，預存程序會刪除資料的預存程序。</span><span class="sxs-lookup"><span data-stu-id="20ec7-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="20ec7-194">Dependent 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="20ec7-195">**相依**存放結構定義語言 (SSDL) 中的項目是 ReferentialConstraint 項目，定義 （也稱為參考條件約束） 的外部索引鍵條件約束的相依端點的子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="20ec7-196">**相依**項目會指定資料行 （或資料行） 在資料表中參考的主索引鍵資料行 （或資料行）。</span><span class="sxs-lookup"><span data-stu-id="20ec7-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="20ec7-197">**PropertyRef**項目會指定哪些資料行參考。</span><span class="sxs-lookup"><span data-stu-id="20ec7-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="20ec7-198">主體項目會指定中所指定的資料行所參考的主索引鍵資料行**相依**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="20ec7-199">**相依**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-200">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="20ec7-201">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-202">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-202">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-203">下表描述可套用至的屬性**相依**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="20ec7-204">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-204">Attribute Name</span></span> | <span data-ttu-id="20ec7-205">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-205">Is Required</span></span> | <span data-ttu-id="20ec7-206">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-207">**角色**</span><span class="sxs-lookup"><span data-stu-id="20ec7-207">**Role**</span></span>       | <span data-ttu-id="20ec7-208">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-208">Yes</span></span>         | <span data-ttu-id="20ec7-209">相同的值**角色**屬性對應的結束項目 （如果使用）; 否則資料表的名稱，包含參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-210">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**相依**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="20ec7-211">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-212">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-213">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-213">Example</span></span>

<span data-ttu-id="20ec7-214">下列範例示範會使用 Association 元素**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders**外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="20ec7-215">**相依**項目會指定**CustomerId**資料行**順序**做為條件約束的相依端的資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a><span data-ttu-id="20ec7-216">Documentation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="20ec7-217">**文件**存放結構定義語言 (SSDL) 中的項目可以用來提供父元素中定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="20ec7-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="20ec7-218">**文件**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-219">**摘要**： 父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="20ec7-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="20ec7-220">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="20ec7-220">(zero or one element)</span></span>
-   <span data-ttu-id="20ec7-221">**LongDescription**： 父項目的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="20ec7-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="20ec7-222">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="20ec7-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-223">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-223">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-224">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**文件**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="20ec7-225">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-226">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-227">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-227">Example</span></span>

<span data-ttu-id="20ec7-228">下列範例所示**文件**為 EntityType 元素的子元素的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a><span data-ttu-id="20ec7-229">End 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-229">End Element (SSDL)</span></span>

<span data-ttu-id="20ec7-230">**結束**存放結構定義語言 (SSDL) 中的項目指定基礎資料庫中的資料表和外部索引鍵條件約束的其中一端的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="20ec7-231">**結束**項目可以是子系的 Association 元素或 AssociationSet 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="20ec7-232">在這兩種情況下，可能的子項目和適用的屬性都不相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="20ec7-233">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="20ec7-234">**結束**項目 (為子系**關聯**項目) 指定的資料表和外部索引鍵條件約束與結尾的資料列數目**型別**和**多重性**分別屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="20ec7-235">外部索引鍵條件約束的端點是定義為 SSDL 關聯的一部分；SSDL 關聯必須擁有兩個端點。</span><span class="sxs-lookup"><span data-stu-id="20ec7-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="20ec7-236">**結束**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-237">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-238">OnDelete （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-239">Annotation 項目 （零或多個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-240">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-240">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-241">下表描述可套用至的屬性**結束**項目時的子系**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="20ec7-242">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-242">Attribute Name</span></span>   | <span data-ttu-id="20ec7-243">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-243">Is Required</span></span> | <span data-ttu-id="20ec7-244">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-245">**Type**</span><span class="sxs-lookup"><span data-stu-id="20ec7-245">**Type**</span></span>         | <span data-ttu-id="20ec7-246">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-246">Yes</span></span>         | <span data-ttu-id="20ec7-247">外部索引鍵條件約束結尾 SSDL 實體集的完整的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="20ec7-248">**角色**</span><span class="sxs-lookup"><span data-stu-id="20ec7-248">**Role**</span></span>         | <span data-ttu-id="20ec7-249">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-249">No</span></span>          | <span data-ttu-id="20ec7-250">值**角色**對應 ReferentialConstraint 項目 主體 或 相依項目中的屬性 （如果使用的話）。</span><span class="sxs-lookup"><span data-stu-id="20ec7-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="20ec7-251">**多重性**</span><span class="sxs-lookup"><span data-stu-id="20ec7-251">**Multiplicity**</span></span> | <span data-ttu-id="20ec7-252">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-252">Yes</span></span>         | <span data-ttu-id="20ec7-253">**1**， **0..1**，或**\*** 可以是結尾的外部索引鍵條件約束的資料列數目而定。</span><span class="sxs-lookup"><span data-stu-id="20ec7-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="20ec7-254">**1**表示外部索引鍵條件約束端點上存在該只有一個資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="20ec7-255">**0..1**表示外部索引鍵條件約束端點上存在零個或一個資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="20ec7-256">**\*** 表示零個、 一個或多個資料列存在外部索引鍵條件約束結尾。</span><span class="sxs-lookup"><span data-stu-id="20ec7-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-257">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="20ec7-258">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-259">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="20ec7-260">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-260">Example</span></span>

<span data-ttu-id="20ec7-261">下列範例所示**關聯**項目，定義**FK\_CustomerOrders** foreign key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="20ec7-262">**多重性**值，指定在每個**端**項目表示，多個資料列**訂單**表可以與中的資料列相關聯**客戶**資料表，但只有一個資料列中的**客戶**表可以與中的資料列相關聯**訂單**資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="20ec7-263">此外， **OnDelete**項目表示的所有資料列中**訂單**參考中的特定資料列的資料表**客戶**如果，就會刪除資料表中的資料列**客戶**刪除資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="20ec7-264">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="20ec7-265">**結束**項目 (為子系**AssociationSet**項目) 指定基礎資料庫中的其中一端的外部索引鍵條件約束的資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="20ec7-266">**結束**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-267">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-268">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-269">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-269">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-270">下表描述可套用至的屬性**結束**項目時的子系**AssociationSet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="20ec7-271">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-271">Attribute Name</span></span> | <span data-ttu-id="20ec7-272">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-272">Is Required</span></span> | <span data-ttu-id="20ec7-273">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="20ec7-274">**EntitySet**</span></span>  | <span data-ttu-id="20ec7-275">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-275">Yes</span></span>         | <span data-ttu-id="20ec7-276">外部索引鍵條件約束結尾的 SSDL 實體集名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="20ec7-277">**角色**</span><span class="sxs-lookup"><span data-stu-id="20ec7-277">**Role**</span></span>       | <span data-ttu-id="20ec7-278">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-278">No</span></span>          | <span data-ttu-id="20ec7-279">其中的值**角色**上其中一個指定的屬性**結束**相對應的關聯項目的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-280">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**結束**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="20ec7-281">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-282">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="20ec7-283">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-283">Example</span></span>

<span data-ttu-id="20ec7-284">下列範例所示**EntityContainer**項目**AssociationSet**具有兩個項目**結束**項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="20ec7-285">EntityContainer 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="20ec7-286">**EntityContainer**存放結構定義語言 (SSDL) 中的項目會描述 Entity Framework 應用程式的基礎資料來源的結構： SSDL 實體集 （EntitySet 項目中定義） 表示中的資料表資料庫中，SSDL 實體類型 （EntityType 項目中定義） 表示的資料表，資料列和關聯集 （定義於 AssociationSet 項目） 代表在資料庫中的 foreign key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="20ec7-287">儲存體模型實體容器對應到概念模型實體容器透過 EntityContainerMapping 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="20ec7-288">**EntityContainer**項目可以有零個或一個文件項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="20ec7-289">如果**文件**項目已存在，它必須在所有其他子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="20ec7-290">**EntityContainer**項目可以有零或多個下列子項目 （依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-291">EntitySet</span><span class="sxs-lookup"><span data-stu-id="20ec7-291">EntitySet</span></span>
-   <span data-ttu-id="20ec7-292">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="20ec7-292">AssociationSet</span></span>
-   <span data-ttu-id="20ec7-293">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-294">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-294">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-295">下表描述可套用至的屬性**EntityContainer**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="20ec7-296">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-296">Attribute Name</span></span> | <span data-ttu-id="20ec7-297">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-297">Is Required</span></span> | <span data-ttu-id="20ec7-298">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-299">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-299">**Name**</span></span>       | <span data-ttu-id="20ec7-300">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-300">Yes</span></span>         | <span data-ttu-id="20ec7-301">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-301">The name of the entity container.</span></span> <span data-ttu-id="20ec7-302">此名稱不得包含任何句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-303">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityContainer**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="20ec7-304">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-305">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-306">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-306">Example</span></span>

<span data-ttu-id="20ec7-307">下列範例所示**EntityContainer**定義兩個實體集和一個關聯集的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="20ec7-308">請注意實體類型和關聯類型名稱要以概念模型命名空間名稱限定。</span><span class="sxs-lookup"><span data-stu-id="20ec7-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a><span data-ttu-id="20ec7-309">EntitySet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="20ec7-310">**EntitySet**存放結構定義語言 (SSDL) 中的項目代表資料表或檢視基礎資料庫中的。</span><span class="sxs-lookup"><span data-stu-id="20ec7-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="20ec7-311">在 SSDL 中的 EntityType 項目代表資料表或檢視表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="20ec7-312">**EntityType**屬性**EntitySet**項目會指定表示 SSDL 實體集中的資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="20ec7-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="20ec7-313">EntitySetMapping 項目中指定的 CSDL 實體集和 SSDL 實體集之間的對應。</span><span class="sxs-lookup"><span data-stu-id="20ec7-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="20ec7-314">**EntitySet**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-315">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-316">DefiningQuery （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-317">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-318">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-318">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-319">下表描述可套用至的屬性**EntitySet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="20ec7-320">（這裡未列出） 的某些屬性可能限定**儲存**別名。</span><span class="sxs-lookup"><span data-stu-id="20ec7-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="20ec7-321">更新模型時，[更新模型精靈] 會使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="20ec7-322">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-322">Attribute Name</span></span> | <span data-ttu-id="20ec7-323">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-323">Is Required</span></span> | <span data-ttu-id="20ec7-324">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-325">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-325">**Name**</span></span>       | <span data-ttu-id="20ec7-326">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-326">Yes</span></span>         | <span data-ttu-id="20ec7-327">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="20ec7-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="20ec7-328">**EntityType**</span></span> | <span data-ttu-id="20ec7-329">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-329">Yes</span></span>         | <span data-ttu-id="20ec7-330">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="20ec7-331">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="20ec7-331">**Schema**</span></span>     | <span data-ttu-id="20ec7-332">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-332">No</span></span>          | <span data-ttu-id="20ec7-333">資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="20ec7-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="20ec7-334">**資料表**</span><span class="sxs-lookup"><span data-stu-id="20ec7-334">**Table**</span></span>      | <span data-ttu-id="20ec7-335">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-335">No</span></span>          | <span data-ttu-id="20ec7-336">資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="20ec7-337">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntitySet**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="20ec7-338">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-339">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-340">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-340">Example</span></span>

<span data-ttu-id="20ec7-341">下列範例所示**EntityContainer**具有兩個項目**EntitySet**項目和一個**AssociationSet**項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="20ec7-342">EntityType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="20ec7-343">**EntityType**存放結構定義語言 (SSDL) 中的項目代表資料表或檢視的基礎資料庫中的資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="20ec7-344">在 SSDL 中的 EntitySet 項目代表資料表或檢視的資料列會發生。</span><span class="sxs-lookup"><span data-stu-id="20ec7-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="20ec7-345">**EntityType**屬性**EntitySet**項目會指定表示 SSDL 實體集中的資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="20ec7-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="20ec7-346">EntityTypeMapping 項目中指定的 SSDL 實體類型和 CSDL 實體類型之間的對應。</span><span class="sxs-lookup"><span data-stu-id="20ec7-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="20ec7-347">**EntityType**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-348">文件 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-349">索引鍵 （零或一個項目）</span><span class="sxs-lookup"><span data-stu-id="20ec7-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="20ec7-350">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-351">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-351">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-352">下表描述可套用至的屬性**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="20ec7-353">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-353">Attribute Name</span></span> | <span data-ttu-id="20ec7-354">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-354">Is Required</span></span> | <span data-ttu-id="20ec7-355">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-356">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-356">**Name**</span></span>       | <span data-ttu-id="20ec7-357">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-357">Yes</span></span>         | <span data-ttu-id="20ec7-358">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-358">The name of the entity type.</span></span> <span data-ttu-id="20ec7-359">此值通常與資料表名稱相同，在資料表中實體類型表示資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="20ec7-360">此值不可以包含句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-361">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="20ec7-362">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-363">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-364">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-364">Example</span></span>

<span data-ttu-id="20ec7-365">下列範例所示**EntityType**具有兩個屬性的項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-365">The following example shows an **EntityType** element with two properties:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a><span data-ttu-id="20ec7-366">Function 屬性 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-366">Function Element (SSDL)</span></span>

<span data-ttu-id="20ec7-367">**函式**存放結構定義語言 (SSDL) 中的項目會指定是否存在的預存程序基礎資料庫中。</span><span class="sxs-lookup"><span data-stu-id="20ec7-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="20ec7-368">**函式**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-369">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-370">參數 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="20ec7-371">CommandText （零或一個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="20ec7-372">ReturnType （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="20ec7-373">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="20ec7-374">傳回類型函式必須指定使用**ReturnType**項目或有**ReturnType**屬性 （如下所示），但非兩者。</span><span class="sxs-lookup"><span data-stu-id="20ec7-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="20ec7-375">儲存體模型中所指定的預存程序可以匯入應用程式的概念模型中。</span><span class="sxs-lookup"><span data-stu-id="20ec7-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="20ec7-376">如需詳細資訊，請參閱 <<c0> [ 預存程序使用查詢](~/ef6/modeling/designer/stored-procedures/query.md)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="20ec7-377">**函式**項目也可用來在儲存體模型中定義自訂函式。</span><span class="sxs-lookup"><span data-stu-id="20ec7-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-378">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-378">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-379">下表描述可套用至的屬性**函式**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="20ec7-380">（這裡未列出） 的某些屬性可能限定**儲存**別名。</span><span class="sxs-lookup"><span data-stu-id="20ec7-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="20ec7-381">更新模型時，[更新模型精靈] 會使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="20ec7-382">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-382">Attribute Name</span></span>             | <span data-ttu-id="20ec7-383">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-383">Is Required</span></span> | <span data-ttu-id="20ec7-384">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-385">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-385">**Name**</span></span>                   | <span data-ttu-id="20ec7-386">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-386">Yes</span></span>         | <span data-ttu-id="20ec7-387">預存程序 (Stored Procedure) 的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="20ec7-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="20ec7-388">**ReturnType**</span></span>             | <span data-ttu-id="20ec7-389">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-389">No</span></span>          | <span data-ttu-id="20ec7-390">預存程序的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="20ec7-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="20ec7-391">**Aggregate**</span></span>              | <span data-ttu-id="20ec7-392">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-392">No</span></span>          | <span data-ttu-id="20ec7-393">**真**如果預存程序傳回彙總的值; 否則為**False**。</span><span class="sxs-lookup"><span data-stu-id="20ec7-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="20ec7-394">**內建**</span><span class="sxs-lookup"><span data-stu-id="20ec7-394">**BuiltIn**</span></span>                | <span data-ttu-id="20ec7-395">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-395">No</span></span>          | <span data-ttu-id="20ec7-396">**真**內建函式是否<sup>1</sup>函式，否則**False**。</span><span class="sxs-lookup"><span data-stu-id="20ec7-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="20ec7-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="20ec7-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="20ec7-398">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-398">No</span></span>          | <span data-ttu-id="20ec7-399">預存程序 (Stored Procedure) 的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="20ec7-400">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="20ec7-400">**NiladicFunction**</span></span>        | <span data-ttu-id="20ec7-401">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-401">No</span></span>          | <span data-ttu-id="20ec7-402">**真**niladic 函數是否<sup>2</sup>函式，**False**否則。</span><span class="sxs-lookup"><span data-stu-id="20ec7-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="20ec7-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="20ec7-403">**IsComposable**</span></span>           | <span data-ttu-id="20ec7-404">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-404">No</span></span>          | <span data-ttu-id="20ec7-405">**真**如果函式是可組合<sup>3</sup>函式，**False**否則。</span><span class="sxs-lookup"><span data-stu-id="20ec7-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="20ec7-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="20ec7-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="20ec7-407">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-407">No</span></span>          | <span data-ttu-id="20ec7-408">列舉型別，可定義用來解決函式多載的型別語意。</span><span class="sxs-lookup"><span data-stu-id="20ec7-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="20ec7-409">列舉型別會定義在每個函式定義的提供者資訊清單。</span><span class="sxs-lookup"><span data-stu-id="20ec7-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="20ec7-410">預設值是**AllowImplicitConversion**。</span><span class="sxs-lookup"><span data-stu-id="20ec7-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="20ec7-411">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="20ec7-411">**Schema**</span></span>                 | <span data-ttu-id="20ec7-412">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-412">No</span></span>          | <span data-ttu-id="20ec7-413">結構描述的名稱，可在其中定義預存程序。</span><span class="sxs-lookup"><span data-stu-id="20ec7-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="20ec7-414"><sup>1</sup>內建函式是在資料庫中定義的函式。</span><span class="sxs-lookup"><span data-stu-id="20ec7-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="20ec7-415">如需儲存體模型中定義的函式的詳細資訊，請參閱 CommandText 項目 (SSDL)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="20ec7-416"><sup>2</sup> niladic 函數會不接受任何參數，並呼叫時，不需要括號。</span><span class="sxs-lookup"><span data-stu-id="20ec7-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="20ec7-417"><sup>3</sup>兩個函式是可組合，如果有一個函式的輸出可以是其他函式的輸入。</span><span class="sxs-lookup"><span data-stu-id="20ec7-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="20ec7-418">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**函式**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="20ec7-419">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-420">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-421">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-421">Example</span></span>

<span data-ttu-id="20ec7-422">下列範例所示**函式**對應至項目**UpdateOrderQuantity**預存程序。</span><span class="sxs-lookup"><span data-stu-id="20ec7-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="20ec7-423">預存程序可接受兩個參數且不傳回值。</span><span class="sxs-lookup"><span data-stu-id="20ec7-423">The stored procedure accepts two parameters and does not return a value.</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a><span data-ttu-id="20ec7-424">Key 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-424">Key Element (SSDL)</span></span>

<span data-ttu-id="20ec7-425">**金鑰**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中資料表的主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="20ec7-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="20ec7-426">**索引鍵**是 EntityType 項目，表示資料表中的資料列的子元素。</span><span class="sxs-lookup"><span data-stu-id="20ec7-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="20ec7-427">中定義的主索引鍵**金鑰**藉由參考一或多個屬性項目上所定義的項目**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="20ec7-428">**金鑰**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-429">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="20ec7-430">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-430">Annotation elements</span></span>

<span data-ttu-id="20ec7-431">任何屬性都適用於**金鑰**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-432">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-432">Example</span></span>

<span data-ttu-id="20ec7-433">下列範例所示**EntityType**參考一個屬性的索引鍵的項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="20ec7-434">OnDelete 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="20ec7-435">**OnDelete**參與的外部索引鍵條件約束的資料列刪除時，存放結構定義語言 (SSDL) 中的項目會反映資料庫行為。</span><span class="sxs-lookup"><span data-stu-id="20ec7-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="20ec7-436">如果動作設定為**Cascade**，則也會刪除參考正在刪除的資料列的資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="20ec7-437">如果動作設定為**無**，則不會一併刪除參考正在刪除的資料列的資料列。</span><span class="sxs-lookup"><span data-stu-id="20ec7-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="20ec7-438">**OnDelete**項目是子元素的結束項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="20ec7-439">**OnDelete**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-440">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-441">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-442">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-442">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-443">下表描述可套用至的屬性**OnDelete**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="20ec7-444">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-444">Attribute Name</span></span> | <span data-ttu-id="20ec7-445">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-445">Is Required</span></span> | <span data-ttu-id="20ec7-446">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-447">**動作**</span><span class="sxs-lookup"><span data-stu-id="20ec7-447">**Action**</span></span>     | <span data-ttu-id="20ec7-448">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-448">Yes</span></span>         | <span data-ttu-id="20ec7-449">**Cascade**或是**None**。</span><span class="sxs-lookup"><span data-stu-id="20ec7-449">**Cascade** or **None**.</span></span> <span data-ttu-id="20ec7-450">(值**Restricted**有效，但具有相同的行為**無**。)</span><span class="sxs-lookup"><span data-stu-id="20ec7-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-451">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**OnDelete**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="20ec7-452">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-453">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-454">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-454">Example</span></span>

<span data-ttu-id="20ec7-455">下列範例所示**關聯**項目，定義**FK\_CustomerOrders** foreign key 條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="20ec7-456">**OnDelete**項目表示的所有資料列中**訂單**參考中的特定資料列的資料表**客戶**將刪除資料表，如果中的資料列**客戶**刪除資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a><span data-ttu-id="20ec7-457">Parameter 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="20ec7-458">**參數**存放結構定義語言 (SSDL) 中的項目是預存程序的參數會指定資料庫中的函式項目子系。</span><span class="sxs-lookup"><span data-stu-id="20ec7-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="20ec7-459">**參數**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-460">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-461">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-462">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-462">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-463">下表描述可套用至的屬性**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="20ec7-464">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-464">Attribute Name</span></span> | <span data-ttu-id="20ec7-465">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-465">Is Required</span></span> | <span data-ttu-id="20ec7-466">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-467">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-467">**Name**</span></span>       | <span data-ttu-id="20ec7-468">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-468">Yes</span></span>         | <span data-ttu-id="20ec7-469">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="20ec7-470">**Type**</span><span class="sxs-lookup"><span data-stu-id="20ec7-470">**Type**</span></span>       | <span data-ttu-id="20ec7-471">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-471">Yes</span></span>         | <span data-ttu-id="20ec7-472">參數型別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="20ec7-473">**模式**</span><span class="sxs-lookup"><span data-stu-id="20ec7-473">**Mode**</span></span>       | <span data-ttu-id="20ec7-474">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-474">No</span></span>          | <span data-ttu-id="20ec7-475">**在 **， **Out**，或**InOut**取決於參數是輸入、 輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="20ec7-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="20ec7-476">**MaxLength**</span></span>  | <span data-ttu-id="20ec7-477">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-477">No</span></span>          | <span data-ttu-id="20ec7-478">參數的長度上限。</span><span class="sxs-lookup"><span data-stu-id="20ec7-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="20ec7-479">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="20ec7-479">**Precision**</span></span>  | <span data-ttu-id="20ec7-480">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-480">No</span></span>          | <span data-ttu-id="20ec7-481">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="20ec7-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="20ec7-482">**縮放**</span><span class="sxs-lookup"><span data-stu-id="20ec7-482">**Scale**</span></span>      | <span data-ttu-id="20ec7-483">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-483">No</span></span>          | <span data-ttu-id="20ec7-484">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="20ec7-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="20ec7-485">**SRID**</span></span>       | <span data-ttu-id="20ec7-486">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-486">No</span></span>          | <span data-ttu-id="20ec7-487">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="20ec7-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="20ec7-488">僅適用於空間類型的參數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="20ec7-489">如需詳細資訊，請參閱 < [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-489">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-490">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**參數**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="20ec7-491">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-492">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-493">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-493">Example</span></span>

<span data-ttu-id="20ec7-494">下列範例所示**函式**具有兩個項目**參數**指定輸入的參數的項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a><span data-ttu-id="20ec7-495">Principal 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="20ec7-496">**主體**存放結構定義語言 (SSDL) 中的項目是 ReferentialConstraint 項目，定義 （也稱為參考條件約束） 的外部索引鍵條件約束的主體端點的子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="20ec7-497">**主體**元素指定另一個資料行 （或資料行） 所參考的資料表中的主索引鍵資料行 （或資料行）。</span><span class="sxs-lookup"><span data-stu-id="20ec7-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="20ec7-498">**PropertyRef**項目會指定哪些資料行參考。</span><span class="sxs-lookup"><span data-stu-id="20ec7-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="20ec7-499">相依的項目會指定參考主索引鍵資料行中所指定的資料行**主體**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="20ec7-500">**主體**元素可能具有下列子項目 （列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="20ec7-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="20ec7-501">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="20ec7-502">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-503">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-503">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-504">下表描述可套用至的屬性**主體**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="20ec7-505">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-505">Attribute Name</span></span> | <span data-ttu-id="20ec7-506">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-506">Is Required</span></span> | <span data-ttu-id="20ec7-507">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-508">**角色**</span><span class="sxs-lookup"><span data-stu-id="20ec7-508">**Role**</span></span>       | <span data-ttu-id="20ec7-509">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-509">Yes</span></span>         | <span data-ttu-id="20ec7-510">相同的值**角色**屬性對應的結束項目 （如果使用）; 否則資料表的名稱，包含參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-511">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**主體**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="20ec7-512">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-513">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-514">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-514">Example</span></span>

<span data-ttu-id="20ec7-515">下列範例示範會使用 Association 元素**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders**外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="20ec7-516">**主體**項目會指定**CustomerId**資料行**客戶**做為條件約束的主體端的資料表。</span><span class="sxs-lookup"><span data-stu-id="20ec7-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a><span data-ttu-id="20ec7-517">Property 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-517">Property Element (SSDL)</span></span>

<span data-ttu-id="20ec7-518">**屬性**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的資料表中的資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="20ec7-519">**屬性**項目是 EntityType 項目，代表資料表中的資料列的子系。</span><span class="sxs-lookup"><span data-stu-id="20ec7-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="20ec7-520">每個**屬性**上定義的項目**EntityType**項目表示的資料行。</span><span class="sxs-lookup"><span data-stu-id="20ec7-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="20ec7-521">A**屬性**項目不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-522">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-522">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-523">下表描述可套用至的屬性**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="20ec7-524">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-524">Attribute Name</span></span>            | <span data-ttu-id="20ec7-525">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-525">Is Required</span></span> | <span data-ttu-id="20ec7-526">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-527">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-527">**Name**</span></span>                  | <span data-ttu-id="20ec7-528">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-528">Yes</span></span>         | <span data-ttu-id="20ec7-529">對應資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="20ec7-530">**Type**</span><span class="sxs-lookup"><span data-stu-id="20ec7-530">**Type**</span></span>                  | <span data-ttu-id="20ec7-531">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-531">Yes</span></span>         | <span data-ttu-id="20ec7-532">對應資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="20ec7-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="20ec7-533">**可為 null**</span><span class="sxs-lookup"><span data-stu-id="20ec7-533">**Nullable**</span></span>              | <span data-ttu-id="20ec7-534">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-534">No</span></span>          | <span data-ttu-id="20ec7-535">**真**（預設值） 或**False**取決於對應的資料行是否可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="20ec7-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="20ec7-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="20ec7-536">**DefaultValue**</span></span>          | <span data-ttu-id="20ec7-537">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-537">No</span></span>          | <span data-ttu-id="20ec7-538">對應資料行的預設值。</span><span class="sxs-lookup"><span data-stu-id="20ec7-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="20ec7-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="20ec7-539">**MaxLength**</span></span>             | <span data-ttu-id="20ec7-540">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-540">No</span></span>          | <span data-ttu-id="20ec7-541">對應資料行的長度上限。</span><span class="sxs-lookup"><span data-stu-id="20ec7-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="20ec7-542">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="20ec7-542">**FixedLength**</span></span>           | <span data-ttu-id="20ec7-543">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-543">No</span></span>          | <span data-ttu-id="20ec7-544">**True**或是**False**取決於對應的資料行值是否會儲存為固定的長度的字串。</span><span class="sxs-lookup"><span data-stu-id="20ec7-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="20ec7-545">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="20ec7-545">**Precision**</span></span>             | <span data-ttu-id="20ec7-546">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-546">No</span></span>          | <span data-ttu-id="20ec7-547">對應資料行的精確度。</span><span class="sxs-lookup"><span data-stu-id="20ec7-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="20ec7-548">**縮放**</span><span class="sxs-lookup"><span data-stu-id="20ec7-548">**Scale**</span></span>                 | <span data-ttu-id="20ec7-549">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-549">No</span></span>          | <span data-ttu-id="20ec7-550">對應資料行的小數位數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="20ec7-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="20ec7-551">**Unicode**</span></span>               | <span data-ttu-id="20ec7-552">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-552">No</span></span>          | <span data-ttu-id="20ec7-553">**True**或是**False**取決於對應的資料行值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="20ec7-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="20ec7-554">**定序**</span><span class="sxs-lookup"><span data-stu-id="20ec7-554">**Collation**</span></span>             | <span data-ttu-id="20ec7-555">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-555">No</span></span>          | <span data-ttu-id="20ec7-556">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="20ec7-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="20ec7-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="20ec7-557">**SRID**</span></span>                  | <span data-ttu-id="20ec7-558">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-558">No</span></span>          | <span data-ttu-id="20ec7-559">系統的空間參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="20ec7-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="20ec7-560">僅適用於空間類型的屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="20ec7-561">如需詳細資訊，請參閱 < [SRID](http://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-561">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="20ec7-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="20ec7-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="20ec7-563">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-563">No</span></span>          | <span data-ttu-id="20ec7-564">**無**，**身分識別**（如果對應的資料行值是在資料庫中產生的身分識別），或**計算**（如果對應的資料行值會計算資料庫中）。</span><span class="sxs-lookup"><span data-stu-id="20ec7-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="20ec7-565">沒有適用於資料列型別屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-566">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="20ec7-567">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-568">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-569">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-569">Example</span></span>

<span data-ttu-id="20ec7-570">下列範例所示**EntityType**具有兩個子項目**屬性**項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="20ec7-571">PropertyRef 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="20ec7-572">**PropertyRef**存放結構定義語言 (SSDL) 中的項目會參考上的 EntityType 項目，表示屬性將執行下列角色的其中一個定義的屬性：</span><span class="sxs-lookup"><span data-stu-id="20ec7-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="20ec7-573">資料表的主索引鍵的一部分， **EntityType**表示。</span><span class="sxs-lookup"><span data-stu-id="20ec7-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="20ec7-574">一或多個**PropertyRef**項目可以用來定義主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="20ec7-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="20ec7-575">如需詳細資訊，請參閱索引鍵的項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="20ec7-576">做為參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="20ec7-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="20ec7-577">如需詳細資訊，請參閱 ReferentialConstraint 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="20ec7-578">**PropertyRef**元素只能有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="20ec7-579">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-580">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="20ec7-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-581">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-581">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-582">下表描述可套用至的屬性**PropertyRef**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="20ec7-583">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-583">Attribute Name</span></span> | <span data-ttu-id="20ec7-584">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-584">Is Required</span></span> | <span data-ttu-id="20ec7-585">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="20ec7-586">**名稱**</span><span class="sxs-lookup"><span data-stu-id="20ec7-586">**Name**</span></span>       | <span data-ttu-id="20ec7-587">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-587">Yes</span></span>         | <span data-ttu-id="20ec7-588">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="20ec7-589">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**PropertyRef**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="20ec7-590">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="20ec7-591">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-592">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-592">Example</span></span>

<span data-ttu-id="20ec7-593">下列範例所示**PropertyRef**用來定義主索引鍵所參考屬性上所定義的項目**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="20ec7-594">ReferentialConstraint 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="20ec7-595">**ReferentialConstraint**存放結構定義語言 (SSDL) 中的項目代表基礎資料庫中的 （也稱為參考完整性條件約束） 的外部索引鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="20ec7-596">會分別由主體和相依的子系項目，指定條件約束的主體和相依端點。</span><span class="sxs-lookup"><span data-stu-id="20ec7-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="20ec7-597">參與主體端和相依端資料行是使用 PropertyRef 元素參考。</span><span class="sxs-lookup"><span data-stu-id="20ec7-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="20ec7-598">**ReferentialConstraint**項目是關聯項目的選用子項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="20ec7-599">如果**ReferentialConstraint**項目不用來對應中指定的外部索引鍵條件約束**關聯**項目，AssociationSetMapping 項目必須用來執行這項操作。</span><span class="sxs-lookup"><span data-stu-id="20ec7-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="20ec7-600">**ReferentialConstraint**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="20ec7-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="20ec7-601">（零或一個） 的文件</span><span class="sxs-lookup"><span data-stu-id="20ec7-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="20ec7-602">（只有一個） 的主體</span><span class="sxs-lookup"><span data-stu-id="20ec7-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="20ec7-603">相依 （只有一個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="20ec7-604">Annotation 項目 （零或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-605">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-605">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-606">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReferentialConstraint**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="20ec7-607">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-608">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-609">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-609">Example</span></span>

<span data-ttu-id="20ec7-610">下列範例所示**關聯**使用的項目**ReferentialConstraint**項目來指定參與的資料行**FK\_CustomerOrders** foreign key 條件約束：</span><span class="sxs-lookup"><span data-stu-id="20ec7-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a><span data-ttu-id="20ec7-611">ReturnType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="20ec7-612">**ReturnType**存放結構定義語言 (SSDL) 中的項目會指定在定義的函式的傳回型別**函式**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="20ec7-613">傳回型別也可以使用指定的函式**ReturnType**屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="20ec7-614">使用指定的函式的傳回型別**型別**屬性或**ReturnType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="20ec7-615">**ReturnType**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="20ec7-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="20ec7-616">CollectionType （一）</span><span class="sxs-lookup"><span data-stu-id="20ec7-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="20ec7-617">附註屬性 （自訂 XML 屬性） 的任何數字可能會套用至**ReturnType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="20ec7-618">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="20ec7-619">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-620">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-620">Example</span></span>

<span data-ttu-id="20ec7-621">下列範例會使用**函式**所傳回的資料列集合。</span><span class="sxs-lookup"><span data-stu-id="20ec7-621">The following example uses a **Function** that returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="20ec7-622">RowType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="20ec7-623">A **RowType**存放結構定義語言 (SSDL) 中的項目會定義未命名的結構做為傳回存放中所定義的函式的型別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="20ec7-624">A **RowType**項目是子元素**CollectionType**項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="20ec7-625">A **RowType**元素可能具有下列子元素：</span><span class="sxs-lookup"><span data-stu-id="20ec7-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="20ec7-626">屬性 （一或多個）</span><span class="sxs-lookup"><span data-stu-id="20ec7-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="20ec7-627">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-627">Example</span></span>

<span data-ttu-id="20ec7-628">下列範例示範使用的存放區函式**CollectionType**項目來指定此函數會傳回資料列集合 (如同在中指定**RowType**項目)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a><span data-ttu-id="20ec7-629">Schema 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="20ec7-630">**結構描述**存放結構定義語言 (SSDL) 中的項目是儲存模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="20ec7-631">其中包含組成儲存模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="20ec7-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="20ec7-632">**結構描述**項目可能包含零或多個下列子元素：</span><span class="sxs-lookup"><span data-stu-id="20ec7-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="20ec7-633">關聯</span><span class="sxs-lookup"><span data-stu-id="20ec7-633">Association</span></span>
-   <span data-ttu-id="20ec7-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="20ec7-634">EntityType</span></span>
-   <span data-ttu-id="20ec7-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="20ec7-635">EntityContainer</span></span>
-   <span data-ttu-id="20ec7-636">功能</span><span class="sxs-lookup"><span data-stu-id="20ec7-636">Function</span></span>

<span data-ttu-id="20ec7-637">**結構描述**項目會使用**命名空間**屬性來定義儲存體模型中的實體類型和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="20ec7-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="20ec7-638">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="20ec7-639">儲存模型命名空間是 XML 命名空間的不同**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="20ec7-640">儲存模型命名空間 (如所定義**命名空間**屬性) 是實體類型和關聯型別的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="20ec7-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="20ec7-641">XML 命名空間 (由**xmlns**屬性) 的**結構描述**項目是子元素和屬性的預設命名空間**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="20ec7-642">XML 命名空間的表單 http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中 YYYY 和 MM 表示年和月分別） 是保留供 SSDL。</span><span class="sxs-lookup"><span data-stu-id="20ec7-642">XML namespaces of the form http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="20ec7-643">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="20ec7-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="20ec7-644">適用屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-644">Applicable Attributes</span></span>

<span data-ttu-id="20ec7-645">下表描述的屬性可以套用至**結構描述**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="20ec7-646">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="20ec7-646">Attribute Name</span></span>            | <span data-ttu-id="20ec7-647">必要</span><span class="sxs-lookup"><span data-stu-id="20ec7-647">Is Required</span></span> | <span data-ttu-id="20ec7-648">值</span><span class="sxs-lookup"><span data-stu-id="20ec7-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-649">**命名空間**</span><span class="sxs-lookup"><span data-stu-id="20ec7-649">**Namespace**</span></span>             | <span data-ttu-id="20ec7-650">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-650">Yes</span></span>         | <span data-ttu-id="20ec7-651">儲存模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="20ec7-651">The namespace of the storage model.</span></span> <span data-ttu-id="20ec7-652">值**命名空間**屬性用來構成型別的完整的名稱。</span><span class="sxs-lookup"><span data-stu-id="20ec7-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="20ec7-653">例如，如果**EntityType**名為*客戶*處於 ExampleModel.Store 命名空間中，則完整限定的名稱**EntityType**是ExampleModel.Store.Customer。</span><span class="sxs-lookup"><span data-stu-id="20ec7-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="20ec7-654">下列字串不能做為值**命名空間**屬性：**系統**，**暫時性**，或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="20ec7-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="20ec7-655">值**命名空間**屬性不能做為值相同**命名空間**CSDL 結構描述項目中的屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="20ec7-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="20ec7-656">**Alias**</span></span>                 | <span data-ttu-id="20ec7-657">否</span><span class="sxs-lookup"><span data-stu-id="20ec7-657">No</span></span>          | <span data-ttu-id="20ec7-658">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="20ec7-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="20ec7-659">例如，如果**EntityType**名為*客戶*ExampleModel.Store 命名空間和值**別名**屬性是*StorageModel*，則您可以使用 StorageModel.Customer 做為完整格式名稱**EntityType。**</span><span class="sxs-lookup"><span data-stu-id="20ec7-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="20ec7-660">**提供者**</span><span class="sxs-lookup"><span data-stu-id="20ec7-660">**Provider**</span></span>              | <span data-ttu-id="20ec7-661">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-661">Yes</span></span>         | <span data-ttu-id="20ec7-662">資料提供者。</span><span class="sxs-lookup"><span data-stu-id="20ec7-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="20ec7-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="20ec7-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="20ec7-664">[是]</span><span class="sxs-lookup"><span data-stu-id="20ec7-664">Yes</span></span>         | <span data-ttu-id="20ec7-665">向提供者表示要傳回哪個提供者資訊清單的語彙基元。</span><span class="sxs-lookup"><span data-stu-id="20ec7-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="20ec7-666">未定義此語彙基元的格式。</span><span class="sxs-lookup"><span data-stu-id="20ec7-666">No format for the token is defined.</span></span> <span data-ttu-id="20ec7-667">語彙基元的值是由提供者定義。</span><span class="sxs-lookup"><span data-stu-id="20ec7-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="20ec7-668">如需 SQL Server 提供者資訊清單語彙基元資訊，請參閱 Entity framework 的 SqlClient。</span><span class="sxs-lookup"><span data-stu-id="20ec7-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="20ec7-669">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-669">Example</span></span>

<span data-ttu-id="20ec7-670">下列範例所示**結構描述**包含的項目**EntityContainer**元素中，兩個**EntityType**項目和一個**關聯**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a><span data-ttu-id="20ec7-671">註釋屬性</span><span class="sxs-lookup"><span data-stu-id="20ec7-671">Annotation Attributes</span></span>

<span data-ttu-id="20ec7-672">存放結構定義語言 (SSDL) 中的 Annotation 屬性是儲存體模型中的自訂 XML 屬性，它們可提供與儲存體模型中之項目相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="20ec7-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="20ec7-673">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 屬性：</span><span class="sxs-lookup"><span data-stu-id="20ec7-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="20ec7-674">Annotation 屬性不能在任何 XML 命名空間之中，這是保留供 SSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="20ec7-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="20ec7-675">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="20ec7-676">可以將一個以上的 Annotation 屬性套用至指定的 SSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="20ec7-677">可以使用 System.Data.Metadata.Edm 命名空間中的類別，在執行階段存取 annotation 項目中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="20ec7-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-678">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-678">Example</span></span>

<span data-ttu-id="20ec7-679">下列範例示範具有 annotation 屬性套用至的 EntityType 項目**OrderId**屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="20ec7-680">該範例也顯示註釋項目加入至**EntityType**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-680">The example also show an annotation element added to the **EntityType** element.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="20ec7-681">Annotation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="20ec7-682">存放結構定義語言 (SSDL) 中的 Annotation 項目是儲存體模型中的自訂 XML 項目，它們可提供與儲存體模型相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="20ec7-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="20ec7-683">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 項目：</span><span class="sxs-lookup"><span data-stu-id="20ec7-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="20ec7-684">Annotation 項目不能存在於保留供 SSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="20ec7-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="20ec7-685">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="20ec7-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="20ec7-686">Annotation 項目必須出現在所指定 SSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="20ec7-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="20ec7-687">多個 Annotation 項目可以同時做為所指定 SSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="20ec7-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="20ec7-688">從.NET Framework 第 4 版開始，中繼資料包含在 annotation 項目可以存取在執行階段使用 System.Data.Metadata.Edm 命名空間中類別。</span><span class="sxs-lookup"><span data-stu-id="20ec7-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="20ec7-689">範例</span><span class="sxs-lookup"><span data-stu-id="20ec7-689">Example</span></span>

<span data-ttu-id="20ec7-690">下列範例示範具有 annotation 項目的 EntityType 項目 (**CustomElement**)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="20ec7-691">此範例也會示範套用至的註釋屬性**OrderId**屬性。</span><span class="sxs-lookup"><span data-stu-id="20ec7-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a><span data-ttu-id="20ec7-692">Facet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="20ec7-692">Facets (SSDL)</span></span>

<span data-ttu-id="20ec7-693">存放結構定義語言 (SSDL) 中的 facet 表示屬性項目中指定的資料行型別條件約束。</span><span class="sxs-lookup"><span data-stu-id="20ec7-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="20ec7-694">Facet 會實作為 XML 屬性上**屬性**項目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="20ec7-695">下表說明 SSDL 中支援的 Facet：</span><span class="sxs-lookup"><span data-stu-id="20ec7-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="20ec7-696">Facet</span><span class="sxs-lookup"><span data-stu-id="20ec7-696">Facet</span></span>           | <span data-ttu-id="20ec7-697">描述</span><span class="sxs-lookup"><span data-stu-id="20ec7-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="20ec7-698">**定序**</span><span class="sxs-lookup"><span data-stu-id="20ec7-698">**Collation**</span></span>   | <span data-ttu-id="20ec7-699">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="20ec7-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="20ec7-700">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="20ec7-700">**FixedLength**</span></span> | <span data-ttu-id="20ec7-701">指定資料行值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="20ec7-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="20ec7-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="20ec7-702">**MaxLength**</span></span>   | <span data-ttu-id="20ec7-703">指定資料行值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="20ec7-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="20ec7-704">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="20ec7-704">**Precision**</span></span>   | <span data-ttu-id="20ec7-705">屬性的型別**十進位**，指定屬性值的數字數目。</span><span class="sxs-lookup"><span data-stu-id="20ec7-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="20ec7-706">屬性的型別**時間**， **DateTime**，並**DateTimeOffset**，指定資料行值秒數的小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="20ec7-707">**縮放**</span><span class="sxs-lookup"><span data-stu-id="20ec7-707">**Scale**</span></span>       | <span data-ttu-id="20ec7-708">指定資料行值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="20ec7-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="20ec7-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="20ec7-709">**Unicode**</span></span>     | <span data-ttu-id="20ec7-710">指定資料行值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="20ec7-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
