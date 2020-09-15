---
title: SSDL 規格-EF6
description: Entity Framework 6 中的 SSDL 規格
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: dbcd4c771e8ca1324a0520f9c8ca13ba4661487b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070207"
---
# <a name="ssdl-specification"></a><span data-ttu-id="94e64-103">SSDL 規格</span><span class="sxs-lookup"><span data-stu-id="94e64-103">SSDL Specification</span></span>
<span data-ttu-id="94e64-104">存放結構定義語言 (SSDL) 是一種 XML 架構語言，會描述 Entity Framework 應用程式的儲存體模型。</span><span class="sxs-lookup"><span data-stu-id="94e64-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="94e64-105">在 Entity Framework 應用程式中，系統會從 ssdl 檔案載入儲存模型中繼資料， (以 SSDL) 寫入至 StoreItemCollection 的實例中，而且可以使用 system.string 類別中的方法來存取。</span><span class="sxs-lookup"><span data-stu-id="94e64-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="94e64-106">Entity Framework 使用儲存體模型中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。</span><span class="sxs-lookup"><span data-stu-id="94e64-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="94e64-107">Entity Framework Designer (EF 設計工具) 會在設計階段將儲存模型資訊儲存在 .edmx 檔中。</span><span class="sxs-lookup"><span data-stu-id="94e64-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="94e64-108">在組建期間，Entity Designer 會使用 .edmx 檔案中的資訊，建立在執行時間 Entity Framework 所需的 ssdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="94e64-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="94e64-109">SSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="94e64-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="94e64-110">SSDL 版本</span><span class="sxs-lookup"><span data-stu-id="94e64-110">SSDL Version</span></span> | <span data-ttu-id="94e64-111">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="94e64-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="94e64-112">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="94e64-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="94e64-113">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="94e64-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="94e64-114">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="94e64-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="94e64-115">Association 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-115">Association Element (SSDL)</span></span>

<span data-ttu-id="94e64-116">存放結構定義語言 (SSDL) 中的 **Association** 元素會指定參與基礎資料庫外鍵條件約束的資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="94e64-117">兩個必要的 End 項目的子項目會指定關聯各端上的資料表，及每一端上的多重性。</span><span class="sxs-lookup"><span data-stu-id="94e64-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="94e64-118">選擇性的 ReferentialConstraint 項目會指定關聯的主體端和相依端，以及參與的資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="94e64-119">如果沒有 **ReferentialConstraint** 專案存在，就必須使用 AssociationSetMapping 元素來指定關聯的資料行對應。</span><span class="sxs-lookup"><span data-stu-id="94e64-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="94e64-120">**Association**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-121">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-122">End (正好兩個)</span><span class="sxs-lookup"><span data-stu-id="94e64-122">End (exactly two)</span></span>
-   <span data-ttu-id="94e64-123">ReferentialConstraint (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="94e64-124">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-125">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-125">Applicable Attributes</span></span>

<span data-ttu-id="94e64-126">下表描述可套用至 **Association** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="94e64-127">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-127">Attribute Name</span></span> | <span data-ttu-id="94e64-128">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-128">Is Required</span></span> | <span data-ttu-id="94e64-129">值</span><span class="sxs-lookup"><span data-stu-id="94e64-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-130">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-130">**Name**</span></span>       | <span data-ttu-id="94e64-131">是</span><span class="sxs-lookup"><span data-stu-id="94e64-131">Yes</span></span>         | <span data-ttu-id="94e64-132">基礎資料庫中對應之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-133">您可以將任何數目的注釋屬性 (自訂 XML 屬性，) 可以套用至 **Association** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="94e64-134">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-135">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-136">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-136">Example</span></span>

<span data-ttu-id="94e64-137">下列範例會示範使用**ReferentialConstraint**元素來指定參與**FK \_ >customerorders**外鍵條件約束之資料行的**Association**元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="94e64-138">AssociationSet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="94e64-139">存放結構定義語言 (SSDL) 中的 **AssociationSet** 元素代表基礎資料庫中兩個數據表之間的外鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="94e64-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="94e64-140">參與外部索引鍵條件約束的資料表資料行會在 Association 項目中指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="94e64-141">對應至指定**associationset**專案的**association**專案是在**AssociationSet**元素的**association**屬性中指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="94e64-142">SSDL 關聯集會透過 AssociationSetMapping 項目對應至 CSDL 關聯集。</span><span class="sxs-lookup"><span data-stu-id="94e64-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="94e64-143">但是，如果指定之 CSDL 關聯集的 CSDL 關聯是使用 ReferentialConstraint 元素所定義，則不需要對應的 **AssociationSetMapping** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="94e64-144">在此情況下，如果有 **AssociationSetMapping** 元素，則 **ReferentialConstraint** 專案會覆寫它所定義的對應。</span><span class="sxs-lookup"><span data-stu-id="94e64-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="94e64-145">**AssociationSet**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-146">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-147">End (零或兩個)</span><span class="sxs-lookup"><span data-stu-id="94e64-147">End (zero or two)</span></span>
-   <span data-ttu-id="94e64-148">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-149">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-149">Applicable Attributes</span></span>

<span data-ttu-id="94e64-150">下表描述可套用至 **AssociationSet** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="94e64-151">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-151">Attribute Name</span></span>  | <span data-ttu-id="94e64-152">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-152">Is Required</span></span> | <span data-ttu-id="94e64-153">值</span><span class="sxs-lookup"><span data-stu-id="94e64-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-154">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-154">**Name**</span></span>        | <span data-ttu-id="94e64-155">是</span><span class="sxs-lookup"><span data-stu-id="94e64-155">Yes</span></span>         | <span data-ttu-id="94e64-156">關聯集所表示之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="94e64-157">**協會**</span><span class="sxs-lookup"><span data-stu-id="94e64-157">**Association**</span></span> | <span data-ttu-id="94e64-158">是</span><span class="sxs-lookup"><span data-stu-id="94e64-158">Yes</span></span>         | <span data-ttu-id="94e64-159">定義參與外部索引鍵條件約束之資料行的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-160">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **AssociationSet** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="94e64-161">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-162">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-163">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-163">Example</span></span>

<span data-ttu-id="94e64-164">下列範例顯示的 **AssociationSet** 元素表示 `FK_CustomerOrders` 基礎資料庫中的 foreign key 條件約束：</span><span class="sxs-lookup"><span data-stu-id="94e64-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="94e64-165"> (SSDL) 的 CollectionType 元素</span><span class="sxs-lookup"><span data-stu-id="94e64-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="94e64-166">存放結構定義語言 (SSDL) 中的 **CollectionType** 元素會指定函式的傳回型別為集合。</span><span class="sxs-lookup"><span data-stu-id="94e64-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="94e64-167">**CollectionType**元素是 ReturnType 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="94e64-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="94e64-168">集合的型別是使用 RowType 子項目指定的：</span><span class="sxs-lookup"><span data-stu-id="94e64-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="94e64-169">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **CollectionType** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="94e64-170">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-171">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-172">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-172">Example</span></span>

<span data-ttu-id="94e64-173">下列範例顯示的函式會使用 **CollectionType** 專案來指定函數傳回資料列的集合。</span><span class="sxs-lookup"><span data-stu-id="94e64-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="94e64-174">CommandText 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="94e64-175">存放結構定義語言 (SSDL) 中的 **CommandText** 元素是 Function 專案的子系，可讓您定義在資料庫執行的 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="94e64-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="94e64-176">**CommandText**專案可讓您加入類似于資料庫中預存程式的功能，但您會在儲存體模型中定義**CommandText**元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="94e64-177">**CommandText**元素不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="94e64-178">**CommandText**元素的主體必須是基礎資料庫的有效 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="94e64-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="94e64-179">**CommandText**元素沒有任何可用的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-180">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-180">Example</span></span>

<span data-ttu-id="94e64-181">下列範例顯示具有子**CommandText**專案的**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="94e64-182">將 **UpdateProductInOrder** 函式匯入概念模型中，以將其公開為 ObjectCoNtext 上的方法。</span><span class="sxs-lookup"><span data-stu-id="94e64-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="94e64-183">DefiningQuery 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="94e64-184">存放結構定義語言 (SSDL) 中的 **DefiningQuery** 元素可讓您直接在基礎資料庫中執行 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="94e64-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="94e64-185">**DefiningQuery**元素常用於資料庫檢視，但此視圖是在儲存體模型中定義，而不是在資料庫中定義。</span><span class="sxs-lookup"><span data-stu-id="94e64-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="94e64-186">**DefiningQuery**元素中定義的視圖可透過 EntitySetMapping 專案對應至概念模型中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="94e64-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="94e64-187">這些對應是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="94e64-187">These mappings are read-only.</span></span>  

<span data-ttu-id="94e64-188">下列 SSDL 語法會顯示 **EntitySet** 的宣告，後面接著 **DefiningQuery** 元素，其中包含用來取得視圖的查詢。</span><span class="sxs-lookup"><span data-stu-id="94e64-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="94e64-189">您可以使用 Entity Framework 中的預存程式，透過視圖啟用讀寫案例。</span><span class="sxs-lookup"><span data-stu-id="94e64-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="94e64-190">您可以使用資料來源 view 或 Entity SQL view 做為基表，以供預存程式用來抓取資料及變更處理。</span><span class="sxs-lookup"><span data-stu-id="94e64-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="94e64-191">您可以使用 **DefiningQuery** 元素將目標設為 Microsoft SQL Server Compact 3.5。</span><span class="sxs-lookup"><span data-stu-id="94e64-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="94e64-192">雖然 SQL Server Compact 3.5 不支援預存程式，但您可以使用 **DefiningQuery** 元素來執行類似的功能。</span><span class="sxs-lookup"><span data-stu-id="94e64-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="94e64-193">該項目的另一個用處是可建立預存程序，克服程式設計語言中所使用之資料類型與資料來源之資料類型間不相符的問題。</span><span class="sxs-lookup"><span data-stu-id="94e64-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="94e64-194">您可以撰寫 **DefiningQuery** 來採用一組特定的參數，然後使用一組不同的參數來呼叫預存程式，例如刪除資料的預存程式。</span><span class="sxs-lookup"><span data-stu-id="94e64-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="94e64-195">Dependent 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="94e64-196">存放結構定義語言 (SSDL) 中的 **相依** 元素是 ReferentialConstraint 專案的子專案，它會定義外鍵條件約束的相依 end (也稱為參考條件約束) 。</span><span class="sxs-lookup"><span data-stu-id="94e64-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="94e64-197">**相依**元素會指定資料表中的資料行 (或資料行) 在參考主鍵資料行 (或資料行) 的資料表中。</span><span class="sxs-lookup"><span data-stu-id="94e64-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="94e64-198">**PropertyRef** 元素會指定要參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="94e64-199">Principal 元素會指定 **相依** 專案中所指定之資料行所參考的主鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="94e64-200">相依專案可以有下列子項目， (**依** 列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-201">PropertyRef (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="94e64-202">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-203">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-203">Applicable Attributes</span></span>

<span data-ttu-id="94e64-204">下表描述可套用至 **相依** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="94e64-205">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-205">Attribute Name</span></span> | <span data-ttu-id="94e64-206">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-206">Is Required</span></span> | <span data-ttu-id="94e64-207">值</span><span class="sxs-lookup"><span data-stu-id="94e64-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-208">**角色**</span><span class="sxs-lookup"><span data-stu-id="94e64-208">**Role**</span></span>       | <span data-ttu-id="94e64-209">是</span><span class="sxs-lookup"><span data-stu-id="94e64-209">Yes</span></span>         | <span data-ttu-id="94e64-210">與 **Role** 屬性相同的值 (如果使用) 對應的 End 元素，則為。否則為包含參考資料行的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-211">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **相依** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="94e64-212">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-213">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-214">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-214">Example</span></span>

<span data-ttu-id="94e64-215">下列範例會示範使用 **ReferentialConstraint** 元素來指定參與 **FK \_ >customerorders** 外鍵條件約束之資料行的 Association 專案。</span><span class="sxs-lookup"><span data-stu-id="94e64-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="94e64-216">**相依**元素會將**Order**資料表的**CustomerId**資料行指定為條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="94e64-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="94e64-217">Documentation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="94e64-218">存放結構定義語言 (SSDL) 中的 **檔** 元素可用來提供在父元素中定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="94e64-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="94e64-219">**檔**元素可以依照列出的順序， (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-220">**摘要**：父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="94e64-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="94e64-221">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-221">(zero or one element)</span></span>
-   <span data-ttu-id="94e64-222">**LongDescription**：父元素的廣泛描述。</span><span class="sxs-lookup"><span data-stu-id="94e64-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="94e64-223">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-224">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-224">Applicable Attributes</span></span>

<span data-ttu-id="94e64-225">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **檔** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="94e64-226">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-227">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-228">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-228">Example</span></span>

<span data-ttu-id="94e64-229">下列範例會將 **檔** 專案顯示為 EntityType 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="94e64-230">End 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-230">End Element (SSDL)</span></span>

<span data-ttu-id="94e64-231">存放結構定義語言 (SSDL) 中的 **End** 元素會指定基礎資料庫中外鍵條件約束一端的資料表和資料列數目。</span><span class="sxs-lookup"><span data-stu-id="94e64-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="94e64-232">**End**元素可以是 Association 元素或 AssociationSet 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="94e64-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="94e64-233">在這兩種情況下，可能的子項目和適用的屬性都不相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="94e64-234">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="94e64-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="94e64-235">**End**專案 (為**Association**專案的子系) 指定 foreign key 條件約束結尾具有**Type**和**多重性**屬性的資料表和資料列數目。</span><span class="sxs-lookup"><span data-stu-id="94e64-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="94e64-236">外部索引鍵條件約束的端點是定義為 SSDL 關聯的一部分；SSDL 關聯必須擁有兩個端點。</span><span class="sxs-lookup"><span data-stu-id="94e64-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="94e64-237">**End**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-238">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="94e64-239">OnDelete (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="94e64-240">Annotation 項目 (零或多個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="94e64-241">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-241">Applicable Attributes</span></span>

<span data-ttu-id="94e64-242">下表描述可套用至 **End** 專案的屬性（當它是 **Association** 元素的子系時）。</span><span class="sxs-lookup"><span data-stu-id="94e64-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="94e64-243">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-243">Attribute Name</span></span>   | <span data-ttu-id="94e64-244">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-244">Is Required</span></span> | <span data-ttu-id="94e64-245">值</span><span class="sxs-lookup"><span data-stu-id="94e64-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-246">**型別**</span><span class="sxs-lookup"><span data-stu-id="94e64-246">**Type**</span></span>         | <span data-ttu-id="94e64-247">是</span><span class="sxs-lookup"><span data-stu-id="94e64-247">Yes</span></span>         | <span data-ttu-id="94e64-248">位於外部索引鍵條件約束端點上之 SSDL 實體集的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="94e64-249">**角色**</span><span class="sxs-lookup"><span data-stu-id="94e64-249">**Role**</span></span>         | <span data-ttu-id="94e64-250">否</span><span class="sxs-lookup"><span data-stu-id="94e64-250">No</span></span>          | <span data-ttu-id="94e64-251">如果使用) ，則為對應 ReferentialConstraint 專案之 Principal 或 Dependent 元素中的 **Role** 屬性值 (。</span><span class="sxs-lookup"><span data-stu-id="94e64-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="94e64-252">**多重性**</span><span class="sxs-lookup"><span data-stu-id="94e64-252">**Multiplicity**</span></span> | <span data-ttu-id="94e64-253">是</span><span class="sxs-lookup"><span data-stu-id="94e64-253">Yes</span></span>         | <span data-ttu-id="94e64-254">**1**、 **0 或 1**，或 **\*** 根據外鍵條件約束結尾可以是的資料列數目而定。</span><span class="sxs-lookup"><span data-stu-id="94e64-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="94e64-255">**1** 表示外鍵條件約束端正好有一個資料列存在。</span><span class="sxs-lookup"><span data-stu-id="94e64-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="94e64-256">**0 ..1** 表示外鍵條件約束結束時，有零個或一個資料列存在。</span><span class="sxs-lookup"><span data-stu-id="94e64-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="94e64-257">**\*** 指出外鍵條件約束端存在零個、一個或多個資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-258">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **End** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="94e64-259">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-260">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="94e64-261">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-261">Example</span></span>

<span data-ttu-id="94e64-262">下列範例顯示定義**FK \_ >customerorders** foreign key 條件約束的**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="94e64-263">每個**End**元素上指定的**多重性**值表示**orders**資料表中的許多資料列都可以與**customers**資料表中的資料列相關聯，但**customers**資料表中的資料列只能與**Orders**資料表中的一個資料列相關聯。</span><span class="sxs-lookup"><span data-stu-id="94e64-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="94e64-264">此外， **OnDelete**元素表示如果刪除**customers**資料表中的資料列，則會刪除**Orders**資料表中參考**customers**資料表中特定資料列的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="94e64-265">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="94e64-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="94e64-266">**End**元素 (為**AssociationSet**專案的子系) 指定基礎資料庫中外鍵條件約束一端的資料表。</span><span class="sxs-lookup"><span data-stu-id="94e64-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="94e64-267">**End**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-268">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-269">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="94e64-270">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-270">Applicable Attributes</span></span>

<span data-ttu-id="94e64-271">下表描述可套用至 **End** 專案的屬性（當它是 **AssociationSet** 專案的子專案時）。</span><span class="sxs-lookup"><span data-stu-id="94e64-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="94e64-272">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-272">Attribute Name</span></span> | <span data-ttu-id="94e64-273">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-273">Is Required</span></span> | <span data-ttu-id="94e64-274">值</span><span class="sxs-lookup"><span data-stu-id="94e64-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="94e64-275">**EntitySet**</span></span>  | <span data-ttu-id="94e64-276">是</span><span class="sxs-lookup"><span data-stu-id="94e64-276">Yes</span></span>         | <span data-ttu-id="94e64-277">位於外部索引鍵條件約束端點上之 SSDL 實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="94e64-278">**角色**</span><span class="sxs-lookup"><span data-stu-id="94e64-278">**Role**</span></span>       | <span data-ttu-id="94e64-279">否</span><span class="sxs-lookup"><span data-stu-id="94e64-279">No</span></span>          | <span data-ttu-id="94e64-280">在對應之 Association 專案的一個**End**元素上指定的其中一個**角色**屬性值。</span><span class="sxs-lookup"><span data-stu-id="94e64-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-281">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **End** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="94e64-282">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-283">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="94e64-284">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-284">Example</span></span>

<span data-ttu-id="94e64-285">下列範例顯示具有**AssociationSet**元素（具有兩個**End**元素）的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="94e64-286">EntityContainer 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="94e64-287">存放結構定義語言 () SSDL 中的 **EntityContainer** 元素會描述 Entity Framework 應用程式中的基礎資料來源結構：在 EntitySet 元素中定義的 ssdl 實體 (集) 代表資料庫中的資料表、在 EntityType 元素中定義的 ssdl 實體型別 (代表資料表中的資料列，以及在 AssociationSet 專案中定義的關聯集) 表示資料庫中的外鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="94e64-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="94e64-288">儲存體模型實體容器會透過 EntityContainerMapping 項目對應至概念模型實體容器。</span><span class="sxs-lookup"><span data-stu-id="94e64-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="94e64-289">**EntityContainer**元素可以有零個或一個檔元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="94e64-290">如果有 **檔** 元素存在，它必須在所有其他子專案之前。</span><span class="sxs-lookup"><span data-stu-id="94e64-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="94e64-291">**EntityContainer**元素可以有零或多個下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="94e64-292">EntitySet</span></span>
-   <span data-ttu-id="94e64-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="94e64-293">AssociationSet</span></span>
-   <span data-ttu-id="94e64-294">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="94e64-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-295">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-295">Applicable Attributes</span></span>

<span data-ttu-id="94e64-296">下表說明可套用至 **EntityContainer** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="94e64-297">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-297">Attribute Name</span></span> | <span data-ttu-id="94e64-298">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-298">Is Required</span></span> | <span data-ttu-id="94e64-299">值</span><span class="sxs-lookup"><span data-stu-id="94e64-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="94e64-300">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-300">**Name**</span></span>       | <span data-ttu-id="94e64-301">是</span><span class="sxs-lookup"><span data-stu-id="94e64-301">Yes</span></span>         | <span data-ttu-id="94e64-302">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-302">The name of the entity container.</span></span> <span data-ttu-id="94e64-303">此名稱不得包含任何句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="94e64-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-304">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntityContainer** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="94e64-305">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-306">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-307">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-307">Example</span></span>

<span data-ttu-id="94e64-308">下列範例顯示定義兩個實體集和一個關聯集的 **EntityContainer** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="94e64-309">請注意實體類型和關聯類型名稱要以概念模型命名空間名稱限定。</span><span class="sxs-lookup"><span data-stu-id="94e64-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="94e64-310">EntitySet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="94e64-311">存放結構定義語言 (SSDL) 中的 **EntitySet** 元素代表基礎資料庫中的資料表或視圖。</span><span class="sxs-lookup"><span data-stu-id="94e64-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="94e64-312">SSDL 中的 EntityType 項目表示資料表或檢視中的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="94e64-313">**EntitySet**專案的**EntityType**屬性指定了表示 ssdl 實體集中資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="94e64-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="94e64-314">CSDL 實體集和 SSDL 實體集之間的對應是在 EntitySetMapping 項目中指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="94e64-315">**EntitySet**元素可依所列的順序)  (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-316">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="94e64-317">DefiningQuery (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="94e64-318">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="94e64-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-319">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-319">Applicable Attributes</span></span>

<span data-ttu-id="94e64-320">下表描述可套用至 **EntitySet** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="94e64-321">某些 (未列在此處的屬性) 可以用 **存放區** 別名來限定。</span><span class="sxs-lookup"><span data-stu-id="94e64-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="94e64-322">這些屬性是更新模型時，更新模型精靈所使用的。</span><span class="sxs-lookup"><span data-stu-id="94e64-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="94e64-323">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-323">Attribute Name</span></span> | <span data-ttu-id="94e64-324">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-324">Is Required</span></span> | <span data-ttu-id="94e64-325">值</span><span class="sxs-lookup"><span data-stu-id="94e64-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-326">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-326">**Name**</span></span>       | <span data-ttu-id="94e64-327">是</span><span class="sxs-lookup"><span data-stu-id="94e64-327">Yes</span></span>         | <span data-ttu-id="94e64-328">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="94e64-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="94e64-329">**EntityType**</span></span> | <span data-ttu-id="94e64-330">是</span><span class="sxs-lookup"><span data-stu-id="94e64-330">Yes</span></span>         | <span data-ttu-id="94e64-331">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="94e64-332">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="94e64-332">**Schema**</span></span>     | <span data-ttu-id="94e64-333">否</span><span class="sxs-lookup"><span data-stu-id="94e64-333">No</span></span>          | <span data-ttu-id="94e64-334">資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="94e64-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="94e64-335">**Table**</span><span class="sxs-lookup"><span data-stu-id="94e64-335">**Table**</span></span>      | <span data-ttu-id="94e64-336">否</span><span class="sxs-lookup"><span data-stu-id="94e64-336">No</span></span>          | <span data-ttu-id="94e64-337">資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="94e64-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="94e64-338">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntitySet** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="94e64-339">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-340">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-341">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-341">Example</span></span>

<span data-ttu-id="94e64-342">下列範例顯示具有兩個**EntitySet**元素和一個**AssociationSet**專案的**EntityContainer**專案：</span><span class="sxs-lookup"><span data-stu-id="94e64-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="94e64-343">EntityType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="94e64-344">存放結構定義語言 (SSDL) 中的 **EntityType** 元素代表基礎資料庫之資料表或視圖中的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="94e64-345">SSDL 中的 EntitySet 項目表示資料列發生所在的資料表或檢視。</span><span class="sxs-lookup"><span data-stu-id="94e64-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="94e64-346">**EntitySet**專案的**EntityType**屬性指定了表示 ssdl 實體集中資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="94e64-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="94e64-347">SSDL 實體類型和 CSDL 實體類型間的對應是在 EntityTypeMapping 項目中指定的。</span><span class="sxs-lookup"><span data-stu-id="94e64-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="94e64-348">**EntityType**專案可以有下列子項目， (依所列的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-349">Documentation (零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="94e64-350">Key(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="94e64-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="94e64-351">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="94e64-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-352">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-352">Applicable Attributes</span></span>

<span data-ttu-id="94e64-353">下表說明可套用至 **EntityType** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="94e64-354">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-354">Attribute Name</span></span> | <span data-ttu-id="94e64-355">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-355">Is Required</span></span> | <span data-ttu-id="94e64-356">值</span><span class="sxs-lookup"><span data-stu-id="94e64-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-357">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-357">**Name**</span></span>       | <span data-ttu-id="94e64-358">是</span><span class="sxs-lookup"><span data-stu-id="94e64-358">Yes</span></span>         | <span data-ttu-id="94e64-359">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-359">The name of the entity type.</span></span> <span data-ttu-id="94e64-360">此值通常與資料表名稱相同，在資料表中實體類型表示資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="94e64-361">此值不可以包含句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="94e64-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-362">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **EntityType** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="94e64-363">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-364">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-365">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-365">Example</span></span>

<span data-ttu-id="94e64-366">下列範例顯示具有兩個屬性的 **EntityType** 元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-366">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="94e64-367">Function 屬性 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-367">Function Element (SSDL)</span></span>

<span data-ttu-id="94e64-368">存放結構定義語言 (SSDL) 中的 **Function** 元素會指定存在於基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="94e64-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="94e64-369">函 **式元素可以** 依照列出的順序， (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-370">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-371">參數 (零或多個) </span><span class="sxs-lookup"><span data-stu-id="94e64-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="94e64-372">CommandText (零或一) </span><span class="sxs-lookup"><span data-stu-id="94e64-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="94e64-373">ReturnType (零或多個) </span><span class="sxs-lookup"><span data-stu-id="94e64-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="94e64-374">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="94e64-375">函數的傳回型別必須以 **returntype** 元素或 **returntype** 屬性指定 (請參閱以下) ，但不能同時指定兩者。</span><span class="sxs-lookup"><span data-stu-id="94e64-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="94e64-376">儲存體模型中所指定的預存程序可以匯入應用程式的概念模型中。</span><span class="sxs-lookup"><span data-stu-id="94e64-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="94e64-377">如需詳細資訊，請參閱 [使用預存程式進行查詢](xref:ef6/modeling/designer/stored-procedures/query)。</span><span class="sxs-lookup"><span data-stu-id="94e64-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="94e64-378">**Function**專案也可以用來定義儲存體模型中的自訂函數。</span><span class="sxs-lookup"><span data-stu-id="94e64-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-379">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-379">Applicable Attributes</span></span>

<span data-ttu-id="94e64-380">下表說明可套用至 **Function** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="94e64-381">某些 (未列在此處的屬性) 可以用 **存放區** 別名來限定。</span><span class="sxs-lookup"><span data-stu-id="94e64-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="94e64-382">這些屬性是更新模型時，更新模型精靈所使用的。</span><span class="sxs-lookup"><span data-stu-id="94e64-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="94e64-383">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-383">Attribute Name</span></span>             | <span data-ttu-id="94e64-384">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-384">Is Required</span></span> | <span data-ttu-id="94e64-385">值</span><span class="sxs-lookup"><span data-stu-id="94e64-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-386">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-386">**Name**</span></span>                   | <span data-ttu-id="94e64-387">是</span><span class="sxs-lookup"><span data-stu-id="94e64-387">Yes</span></span>         | <span data-ttu-id="94e64-388">預存程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="94e64-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="94e64-389">**ReturnType**</span></span>             | <span data-ttu-id="94e64-390">否</span><span class="sxs-lookup"><span data-stu-id="94e64-390">No</span></span>          | <span data-ttu-id="94e64-391">預存程序的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="94e64-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="94e64-392">**彙總**</span><span class="sxs-lookup"><span data-stu-id="94e64-392">**Aggregate**</span></span>              | <span data-ttu-id="94e64-393">否</span><span class="sxs-lookup"><span data-stu-id="94e64-393">No</span></span>          | <span data-ttu-id="94e64-394">如果預存程式傳回匯總值，**則為 True** 。否則**為 False**。</span><span class="sxs-lookup"><span data-stu-id="94e64-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="94e64-395">**內置**</span><span class="sxs-lookup"><span data-stu-id="94e64-395">**BuiltIn**</span></span>                | <span data-ttu-id="94e64-396">否</span><span class="sxs-lookup"><span data-stu-id="94e64-396">No</span></span>          | <span data-ttu-id="94e64-397">如果函式是內建的<sup>1</sup>函數，則為**True** ;否則**為 False**。</span><span class="sxs-lookup"><span data-stu-id="94e64-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="94e64-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="94e64-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="94e64-399">否</span><span class="sxs-lookup"><span data-stu-id="94e64-399">No</span></span>          | <span data-ttu-id="94e64-400">預存程序的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="94e64-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="94e64-401">**NiladicFunction**</span></span>        | <span data-ttu-id="94e64-402">否</span><span class="sxs-lookup"><span data-stu-id="94e64-402">No</span></span>          | <span data-ttu-id="94e64-403">如果函式是 niladic<sup>2</sup>函數，則**為 True** ;否則**為 False** 。</span><span class="sxs-lookup"><span data-stu-id="94e64-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="94e64-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="94e64-404">**IsComposable**</span></span>           | <span data-ttu-id="94e64-405">否</span><span class="sxs-lookup"><span data-stu-id="94e64-405">No</span></span>          | <span data-ttu-id="94e64-406">如果函式是可組合的<sup>3</sup>函數，則為**True** ;否則**為 False** 。</span><span class="sxs-lookup"><span data-stu-id="94e64-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="94e64-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="94e64-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="94e64-408">否</span><span class="sxs-lookup"><span data-stu-id="94e64-408">No</span></span>          | <span data-ttu-id="94e64-409">列舉型別，可定義用來解決函式多載的型別語意。</span><span class="sxs-lookup"><span data-stu-id="94e64-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="94e64-410">列舉型別會定義在每個函式定義的提供者資訊清單。</span><span class="sxs-lookup"><span data-stu-id="94e64-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="94e64-411">預設值為 **AllowImplicitConversion**。</span><span class="sxs-lookup"><span data-stu-id="94e64-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="94e64-412">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="94e64-412">**Schema**</span></span>                 | <span data-ttu-id="94e64-413">否</span><span class="sxs-lookup"><span data-stu-id="94e64-413">No</span></span>          | <span data-ttu-id="94e64-414">結構描述的名稱，可在其中定義預存程序。</span><span class="sxs-lookup"><span data-stu-id="94e64-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="94e64-415"><sup>1</sup> 內建函數是資料庫中定義的函數。</span><span class="sxs-lookup"><span data-stu-id="94e64-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="94e64-416">如需有關儲存體模型中所定義之函數的詳細資訊，請參閱 (SSDL) 的 CommandText 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="94e64-417"><sup>2</sup> niladic 函式是不接受參數的函式，而呼叫時不需要括弧。</span><span class="sxs-lookup"><span data-stu-id="94e64-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="94e64-418"><sup>3</sup> 如果一個函式的輸出可以是其他函式的輸入，則兩個函式是可組合的。</span><span class="sxs-lookup"><span data-stu-id="94e64-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="94e64-419">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Function** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="94e64-420">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-421">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-422">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-422">Example</span></span>

<span data-ttu-id="94e64-423">下列範例顯示對應至**UpdateOrderQuantity**預存程式的**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="94e64-424">預存程序可接受兩個參數且不傳回值。</span><span class="sxs-lookup"><span data-stu-id="94e64-424">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="94e64-425">Key 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-425">Key Element (SSDL)</span></span>

<span data-ttu-id="94e64-426">存放結構定義語言 (SSDL) 中的 **重要** 元素代表基礎資料庫中資料表的主鍵。</span><span class="sxs-lookup"><span data-stu-id="94e64-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="94e64-427">**Key** 是 EntityType 元素的子項目，代表資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="94e64-428">主要索引鍵是藉由參考**EntityType**元素上定義的一或多個屬性專案，定義在**key**元素中。</span><span class="sxs-lookup"><span data-stu-id="94e64-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="94e64-429">**Key**專案可以有下列子項目， (依照列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-430">PropertyRef (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="94e64-431">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="94e64-431">Annotation elements</span></span>

<span data-ttu-id="94e64-432">沒有任何屬性適用于索引 **鍵** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-433">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-433">Example</span></span>

<span data-ttu-id="94e64-434">下列範例顯示具有參考一個屬性之索引鍵的 **EntityType** 元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="94e64-435">OnDelete 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="94e64-436">當刪除參與外鍵條件約束的資料列時，存放結構定義語言 (SSDL) 中的 **OnDelete** 專案會反映資料庫行為。</span><span class="sxs-lookup"><span data-stu-id="94e64-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="94e64-437">如果動作設定為 **Cascade**，則也會一併刪除參考所刪除之資料列的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="94e64-438">如果動作設定為 [ **無**]，則也不會刪除參考所刪除之資料列的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="94e64-439">**OnDelete**元素是 End 元素的子專案。</span><span class="sxs-lookup"><span data-stu-id="94e64-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="94e64-440">**OnDelete**元素可以依照列出的順序 (下列子項目) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-441">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-442">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-443">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-443">Applicable Attributes</span></span>

<span data-ttu-id="94e64-444">下表說明可套用至 **OnDelete** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="94e64-445">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-445">Attribute Name</span></span> | <span data-ttu-id="94e64-446">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-446">Is Required</span></span> | <span data-ttu-id="94e64-447">值</span><span class="sxs-lookup"><span data-stu-id="94e64-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-448">**動作**</span><span class="sxs-lookup"><span data-stu-id="94e64-448">**Action**</span></span>     | <span data-ttu-id="94e64-449">是</span><span class="sxs-lookup"><span data-stu-id="94e64-449">Yes</span></span>         | <span data-ttu-id="94e64-450">**Cascade**串聯或**無**。</span><span class="sxs-lookup"><span data-stu-id="94e64-450">**Cascade** or **None**.</span></span> <span data-ttu-id="94e64-451"> (**限制** 的值有效，但其行為與 **None**相同。 ) </span><span class="sxs-lookup"><span data-stu-id="94e64-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-452">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **OnDelete** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="94e64-453">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-454">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-455">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-455">Example</span></span>

<span data-ttu-id="94e64-456">下列範例顯示定義**FK \_ >customerorders** foreign key 條件約束的**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="94e64-457">**OnDelete**元素表示如果刪除**customers**資料表中的資料列，則會刪除**Orders**資料表中參考**customers**資料表中特定資料列的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="94e64-458">Parameter 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="94e64-459">存放結構定義語言 (SSDL) 中的 **參數** 元素是 Function 專案的子系，可指定資料庫中預存程式的參數。</span><span class="sxs-lookup"><span data-stu-id="94e64-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="94e64-460">**Parameter**專案可以有下列子項目， (依列出的順序) ：</span><span class="sxs-lookup"><span data-stu-id="94e64-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-461">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-462">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-463">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-463">Applicable Attributes</span></span>

<span data-ttu-id="94e64-464">下表說明可套用至 **Parameter** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="94e64-465">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-465">Attribute Name</span></span> | <span data-ttu-id="94e64-466">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-466">Is Required</span></span> | <span data-ttu-id="94e64-467">值</span><span class="sxs-lookup"><span data-stu-id="94e64-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-468">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-468">**Name**</span></span>       | <span data-ttu-id="94e64-469">是</span><span class="sxs-lookup"><span data-stu-id="94e64-469">Yes</span></span>         | <span data-ttu-id="94e64-470">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="94e64-471">**型別**</span><span class="sxs-lookup"><span data-stu-id="94e64-471">**Type**</span></span>       | <span data-ttu-id="94e64-472">是</span><span class="sxs-lookup"><span data-stu-id="94e64-472">Yes</span></span>         | <span data-ttu-id="94e64-473">參數類型。</span><span class="sxs-lookup"><span data-stu-id="94e64-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="94e64-474">**Mode**</span><span class="sxs-lookup"><span data-stu-id="94e64-474">**Mode**</span></span>       | <span data-ttu-id="94e64-475">否</span><span class="sxs-lookup"><span data-stu-id="94e64-475">No</span></span>          | <span data-ttu-id="94e64-476">**In**、 **Out**或 **InOut** ，取決於參數是輸入、輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="94e64-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="94e64-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="94e64-477">**MaxLength**</span></span>  | <span data-ttu-id="94e64-478">否</span><span class="sxs-lookup"><span data-stu-id="94e64-478">No</span></span>          | <span data-ttu-id="94e64-479">參數的長度上限。</span><span class="sxs-lookup"><span data-stu-id="94e64-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="94e64-480">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="94e64-480">**Precision**</span></span>  | <span data-ttu-id="94e64-481">否</span><span class="sxs-lookup"><span data-stu-id="94e64-481">No</span></span>          | <span data-ttu-id="94e64-482">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="94e64-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="94e64-483">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="94e64-483">**Scale**</span></span>      | <span data-ttu-id="94e64-484">否</span><span class="sxs-lookup"><span data-stu-id="94e64-484">No</span></span>          | <span data-ttu-id="94e64-485">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="94e64-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="94e64-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="94e64-486">**SRID**</span></span>       | <span data-ttu-id="94e64-487">否</span><span class="sxs-lookup"><span data-stu-id="94e64-487">No</span></span>          | <span data-ttu-id="94e64-488">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="94e64-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="94e64-489">只對空間類型的參數有效。</span><span class="sxs-lookup"><span data-stu-id="94e64-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="94e64-490">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="94e64-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-491">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Parameter** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="94e64-492">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-493">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-494">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-494">Example</span></span>

<span data-ttu-id="94e64-495">下列範例顯示具有兩個**參數**專案的**Function**元素，這些元素會指定輸入參數：</span><span class="sxs-lookup"><span data-stu-id="94e64-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="94e64-496">Principal 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="94e64-497">存放結構定義語言 (SSDL) 中的 **主體** 專案是 ReferentialConstraint 專案的子專案，可定義外鍵條件約束的主體端 (也稱為參考條件約束) 。</span><span class="sxs-lookup"><span data-stu-id="94e64-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="94e64-498">**Principal**專案會指定主鍵資料行 (或資料行) 在另一個資料行 (或資料行) 所參考的資料表中。</span><span class="sxs-lookup"><span data-stu-id="94e64-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="94e64-499">**PropertyRef** 元素會指定要參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="94e64-500">相依元素會指定參考 **主體** 專案中所指定之主鍵資料行的資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="94e64-501">**主體**專案可以依) 列出的順序 (下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="94e64-502">PropertyRef (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="94e64-503">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-504">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-504">Applicable Attributes</span></span>

<span data-ttu-id="94e64-505">下表說明可套用至 **主體** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="94e64-506">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-506">Attribute Name</span></span> | <span data-ttu-id="94e64-507">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-507">Is Required</span></span> | <span data-ttu-id="94e64-508">值</span><span class="sxs-lookup"><span data-stu-id="94e64-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-509">**角色**</span><span class="sxs-lookup"><span data-stu-id="94e64-509">**Role**</span></span>       | <span data-ttu-id="94e64-510">是</span><span class="sxs-lookup"><span data-stu-id="94e64-510">Yes</span></span>         | <span data-ttu-id="94e64-511">與 **Role** 屬性相同的值 (如果使用) 對應的 End 元素，則為。否則為包含所參考資料行的資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-512">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **主體** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="94e64-513">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-514">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-515">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-515">Example</span></span>

<span data-ttu-id="94e64-516">下列範例會示範使用 **ReferentialConstraint** 元素來指定參與 **FK \_ >customerorders** 外鍵條件約束之資料行的 Association 專案。</span><span class="sxs-lookup"><span data-stu-id="94e64-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="94e64-517">**Principal**元素會將**Customer**資料表的**CustomerId**資料行指定為條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="94e64-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="94e64-518">Property 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-518">Property Element (SSDL)</span></span>

<span data-ttu-id="94e64-519">存放結構定義語言 (SSDL) 中的 **Property** 元素代表基礎資料庫中資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="94e64-520">**屬性** 元素是 EntityType 元素的子系，代表資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="94e64-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="94e64-521">**EntityType**元素上定義的每個**Property**元素都代表一個資料行。</span><span class="sxs-lookup"><span data-stu-id="94e64-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="94e64-522">**屬性**元素不能有任何子項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-523">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-523">Applicable Attributes</span></span>

<span data-ttu-id="94e64-524">下表描述可套用至 **Property** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="94e64-525">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-525">Attribute Name</span></span>            | <span data-ttu-id="94e64-526">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-526">Is Required</span></span> | <span data-ttu-id="94e64-527">值</span><span class="sxs-lookup"><span data-stu-id="94e64-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-528">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-528">**Name**</span></span>                  | <span data-ttu-id="94e64-529">是</span><span class="sxs-lookup"><span data-stu-id="94e64-529">Yes</span></span>         | <span data-ttu-id="94e64-530">對應資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="94e64-531">**型別**</span><span class="sxs-lookup"><span data-stu-id="94e64-531">**Type**</span></span>                  | <span data-ttu-id="94e64-532">是</span><span class="sxs-lookup"><span data-stu-id="94e64-532">Yes</span></span>         | <span data-ttu-id="94e64-533">對應資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="94e64-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="94e64-534">**可為 Null**</span><span class="sxs-lookup"><span data-stu-id="94e64-534">**Nullable**</span></span>              | <span data-ttu-id="94e64-535">否</span><span class="sxs-lookup"><span data-stu-id="94e64-535">No</span></span>          | <span data-ttu-id="94e64-536">**True** (預設值) 或 **False** ，取決於對應的資料行是否可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="94e64-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="94e64-537">**值**</span><span class="sxs-lookup"><span data-stu-id="94e64-537">**DefaultValue**</span></span>          | <span data-ttu-id="94e64-538">否</span><span class="sxs-lookup"><span data-stu-id="94e64-538">No</span></span>          | <span data-ttu-id="94e64-539">對應資料行的預設值。</span><span class="sxs-lookup"><span data-stu-id="94e64-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="94e64-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="94e64-540">**MaxLength**</span></span>             | <span data-ttu-id="94e64-541">否</span><span class="sxs-lookup"><span data-stu-id="94e64-541">No</span></span>          | <span data-ttu-id="94e64-542">對應資料行的長度上限。</span><span class="sxs-lookup"><span data-stu-id="94e64-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="94e64-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="94e64-543">**FixedLength**</span></span>           | <span data-ttu-id="94e64-544">否</span><span class="sxs-lookup"><span data-stu-id="94e64-544">No</span></span>          | <span data-ttu-id="94e64-545">**True** 或 **False** ，取決於對應的資料行值是否會儲存為固定長度字串。</span><span class="sxs-lookup"><span data-stu-id="94e64-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="94e64-546">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="94e64-546">**Precision**</span></span>             | <span data-ttu-id="94e64-547">否</span><span class="sxs-lookup"><span data-stu-id="94e64-547">No</span></span>          | <span data-ttu-id="94e64-548">對應資料行的精確度。</span><span class="sxs-lookup"><span data-stu-id="94e64-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="94e64-549">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="94e64-549">**Scale**</span></span>                 | <span data-ttu-id="94e64-550">否</span><span class="sxs-lookup"><span data-stu-id="94e64-550">No</span></span>          | <span data-ttu-id="94e64-551">對應資料行的小數位數。</span><span class="sxs-lookup"><span data-stu-id="94e64-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="94e64-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="94e64-552">**Unicode**</span></span>               | <span data-ttu-id="94e64-553">否</span><span class="sxs-lookup"><span data-stu-id="94e64-553">No</span></span>          | <span data-ttu-id="94e64-554">**True** 或 **False** ，取決於對應的資料行值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="94e64-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="94e64-555">**定序**</span><span class="sxs-lookup"><span data-stu-id="94e64-555">**Collation**</span></span>             | <span data-ttu-id="94e64-556">否</span><span class="sxs-lookup"><span data-stu-id="94e64-556">No</span></span>          | <span data-ttu-id="94e64-557">指定要在資料來源使用之定序的字串。</span><span class="sxs-lookup"><span data-stu-id="94e64-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="94e64-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="94e64-558">**SRID**</span></span>                  | <span data-ttu-id="94e64-559">否</span><span class="sxs-lookup"><span data-stu-id="94e64-559">No</span></span>          | <span data-ttu-id="94e64-560">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="94e64-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="94e64-561">只對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="94e64-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="94e64-562">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID) 和 [SRID (SQL Server) ](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="94e64-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="94e64-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="94e64-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="94e64-564">否</span><span class="sxs-lookup"><span data-stu-id="94e64-564">No</span></span>          | <span data-ttu-id="94e64-565">**無**， **識別** (如果對應的資料行值是資料庫) 中所產生的身分識別，則為，如果對應的資料行值在資料庫) 中計算，則為 **計算** 的 (。</span><span class="sxs-lookup"><span data-stu-id="94e64-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="94e64-566">對 RowType 屬性無效。</span><span class="sxs-lookup"><span data-stu-id="94e64-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-567">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **Property** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="94e64-568">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-569">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-570">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-570">Example</span></span>

<span data-ttu-id="94e64-571">下列範例顯示具有兩個子**屬性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="94e64-572">PropertyRef 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="94e64-573">存放結構定義語言 () SSDL 中的 **PropertyRef** 專案會參考 EntityType 元素上定義的屬性，以指出該屬性將執行下列其中一個角色：</span><span class="sxs-lookup"><span data-stu-id="94e64-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="94e64-574">成為 **EntityType** 所代表之資料表主鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="94e64-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="94e64-575">您可以使用一或多個 **PropertyRef** 元素來定義主鍵。</span><span class="sxs-lookup"><span data-stu-id="94e64-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="94e64-576">如需詳細資訊，請參閱 Key 項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="94e64-577">做為參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="94e64-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="94e64-578">如需詳細資訊，請參閱 ReferentialConstraint 項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="94e64-579">**PropertyRef**元素只能有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="94e64-580">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-581">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="94e64-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-582">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-582">Applicable Attributes</span></span>

<span data-ttu-id="94e64-583">下表說明可套用至 **PropertyRef** 元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="94e64-584">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-584">Attribute Name</span></span> | <span data-ttu-id="94e64-585">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-585">Is Required</span></span> | <span data-ttu-id="94e64-586">值</span><span class="sxs-lookup"><span data-stu-id="94e64-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="94e64-587">**名稱**</span><span class="sxs-lookup"><span data-stu-id="94e64-587">**Name**</span></span>       | <span data-ttu-id="94e64-588">是</span><span class="sxs-lookup"><span data-stu-id="94e64-588">Yes</span></span>         | <span data-ttu-id="94e64-589">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="94e64-590">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **PropertyRef** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="94e64-591">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="94e64-592">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-593">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-593">Example</span></span>

<span data-ttu-id="94e64-594">下列範例顯示一個 **PropertyRef** 專案，用來藉由參考 **EntityType** 元素上定義的屬性來定義主鍵。</span><span class="sxs-lookup"><span data-stu-id="94e64-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="94e64-595">ReferentialConstraint 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="94e64-596">存放結構定義語言 (SSDL) 中的 **ReferentialConstraint** 專案表示外鍵條件約束 (也稱為基礎資料庫) 中的參考完整性條件約束。</span><span class="sxs-lookup"><span data-stu-id="94e64-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="94e64-597">條件約束的主體端和相依端是分別透過 Principal 和 Dependent 子項目指定的。</span><span class="sxs-lookup"><span data-stu-id="94e64-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="94e64-598">參與主體端和相依端之資料行是以 PropertyRef 項目來參考的。</span><span class="sxs-lookup"><span data-stu-id="94e64-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="94e64-599">**ReferentialConstraint**元素是 Association 元素的選擇性子項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="94e64-600">如果 **ReferentialConstraint** 專案未用來對應 **Association** 元素中指定的外鍵條件約束，則必須使用 AssociationSetMapping 元素來進行這項作業。</span><span class="sxs-lookup"><span data-stu-id="94e64-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="94e64-601">**ReferentialConstraint**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="94e64-602">Documentation (零或一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="94e64-603">Principal (只有一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="94e64-604">Dependent (只有一個)</span><span class="sxs-lookup"><span data-stu-id="94e64-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="94e64-605">註釋項目 (零或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-606">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-606">Applicable Attributes</span></span>

<span data-ttu-id="94e64-607">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **ReferentialConstraint** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="94e64-608">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-609">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-610">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-610">Example</span></span>

<span data-ttu-id="94e64-611">下列範例會示範使用**ReferentialConstraint**元素來指定參與**FK \_ >customerorders**外鍵條件約束之資料行的**Association**元素：</span><span class="sxs-lookup"><span data-stu-id="94e64-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="94e64-612">ReturnType 元素 (SSDL) </span><span class="sxs-lookup"><span data-stu-id="94e64-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="94e64-613">存放結構定義語言 (SSDL) 中的**ReturnType**元素會指定函式專案中定義之函式的傳回**Function**型別。</span><span class="sxs-lookup"><span data-stu-id="94e64-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="94e64-614">函數傳回型別也可以使用 **ReturnType** 屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="94e64-615">函式的傳回型別是以 **type** 屬性或 **ReturnType** 元素指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="94e64-616">**ReturnType**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="94e64-617">CollectionType (一) </span><span class="sxs-lookup"><span data-stu-id="94e64-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="94e64-618">任何數目的注釋屬性 (自訂 XML 屬性) 都可以套用至 **ReturnType** 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="94e64-619">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="94e64-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="94e64-620">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-621">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-621">Example</span></span>

<span data-ttu-id="94e64-622">下列範例會使用傳回資料列集合的 **函數** 。</span><span class="sxs-lookup"><span data-stu-id="94e64-622">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="94e64-623">RowType 元素 (SSDL) </span><span class="sxs-lookup"><span data-stu-id="94e64-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="94e64-624">存放結構定義語言 (SSDL) 中的 **RowType** 專案會將未命名的結構定義為存放區中所定義之函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="94e64-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="94e64-625">**RowType**元素是**CollectionType**元素的子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="94e64-626">**RowType**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="94e64-627">屬性 (一個或多個)</span><span class="sxs-lookup"><span data-stu-id="94e64-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="94e64-628">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-628">Example</span></span>

<span data-ttu-id="94e64-629">下列範例示範使用 **CollectionType** 專案的存放區函式，以指定函數傳回資料列的集合 (如 **RowType** 元素) 中所指定。</span><span class="sxs-lookup"><span data-stu-id="94e64-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="94e64-630">Schema 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="94e64-631">存放結構定義語言 (SSDL) 中的 **Schema** 元素是儲存體模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="94e64-632">其中包含組成儲存模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="94e64-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="94e64-633">**Schema**元素可能包含零或多個下列子項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="94e64-634">關聯</span><span class="sxs-lookup"><span data-stu-id="94e64-634">Association</span></span>
-   <span data-ttu-id="94e64-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="94e64-635">EntityType</span></span>
-   <span data-ttu-id="94e64-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="94e64-636">EntityContainer</span></span>
-   <span data-ttu-id="94e64-637">函式</span><span class="sxs-lookup"><span data-stu-id="94e64-637">Function</span></span>

<span data-ttu-id="94e64-638">**Schema**元素使用**namespace**屬性來定義儲存體模型中實體類型和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="94e64-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="94e64-639">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="94e64-640">儲存模型命名空間不同于 **Schema** 元素的 XML 命名空間。</span><span class="sxs-lookup"><span data-stu-id="94e64-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="94e64-641">儲存模型命名空間 (如 **命名空間** 屬性所定義) 是實體類型和關聯類型的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="94e64-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="94e64-642">**Schema**專案的**xmlns**屬性) 所指出的 XML 命名空間 (是**schema**專案的子項目和屬性的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="94e64-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="94e64-643">格式 (的 XML 命名空間， https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl 其中 YYYY 和 MM 分別表示年和月，) 保留給 SSDL。</span><span class="sxs-lookup"><span data-stu-id="94e64-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="94e64-644">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="94e64-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="94e64-645">適用的屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-645">Applicable Attributes</span></span>

<span data-ttu-id="94e64-646">下表說明可套用至 **Schema** 專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="94e64-647">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="94e64-647">Attribute Name</span></span>            | <span data-ttu-id="94e64-648">必要</span><span class="sxs-lookup"><span data-stu-id="94e64-648">Is Required</span></span> | <span data-ttu-id="94e64-649">值</span><span class="sxs-lookup"><span data-stu-id="94e64-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-650">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="94e64-650">**Namespace**</span></span>             | <span data-ttu-id="94e64-651">是</span><span class="sxs-lookup"><span data-stu-id="94e64-651">Yes</span></span>         | <span data-ttu-id="94e64-652">儲存模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="94e64-652">The namespace of the storage model.</span></span> <span data-ttu-id="94e64-653">**Namespace**屬性的值是用來形成型別的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="94e64-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="94e64-654">例如，如果名為*Customer*的**entitytype**位於 examplemodel.store.customer 命名空間中，則**Entitytype**的完整名稱是 examplemodel.store.customer。</span><span class="sxs-lookup"><span data-stu-id="94e64-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="94e64-655">下列字串不能當做 Namespace 屬性（ **Namespace** ）的值使用： **System**、 **暫時性**或 **Edm**。</span><span class="sxs-lookup"><span data-stu-id="94e64-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="94e64-656">**Namespace**屬性的值不能與 CSDL Schema 元素中**namespace**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="94e64-657">**別名**</span><span class="sxs-lookup"><span data-stu-id="94e64-657">**Alias**</span></span>                 | <span data-ttu-id="94e64-658">否</span><span class="sxs-lookup"><span data-stu-id="94e64-658">No</span></span>          | <span data-ttu-id="94e64-659">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="94e64-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="94e64-660">例如，如果名為*Customer*的**EntityType**位於 examplemodel.store.customer 命名空間中，而**Alias**屬性的值是*StorageModel*，則您可以使用 StorageModel 作為 EntityType 的完整名稱 **。**</span><span class="sxs-lookup"><span data-stu-id="94e64-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="94e64-661">**提供者**</span><span class="sxs-lookup"><span data-stu-id="94e64-661">**Provider**</span></span>              | <span data-ttu-id="94e64-662">是</span><span class="sxs-lookup"><span data-stu-id="94e64-662">Yes</span></span>         | <span data-ttu-id="94e64-663">資料提供者。</span><span class="sxs-lookup"><span data-stu-id="94e64-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="94e64-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="94e64-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="94e64-665">是</span><span class="sxs-lookup"><span data-stu-id="94e64-665">Yes</span></span>         | <span data-ttu-id="94e64-666">向提供者表示要傳回哪個提供者資訊清單的語彙基元。</span><span class="sxs-lookup"><span data-stu-id="94e64-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="94e64-667">未定義此語彙基元的格式。</span><span class="sxs-lookup"><span data-stu-id="94e64-667">No format for the token is defined.</span></span> <span data-ttu-id="94e64-668">語彙基元的值是由提供者定義。</span><span class="sxs-lookup"><span data-stu-id="94e64-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="94e64-669">如需有關 SQL Server 提供者資訊清單權杖的詳細資訊，請參閱 Entity Framework 的 SqlClient。</span><span class="sxs-lookup"><span data-stu-id="94e64-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="94e64-670">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-670">Example</span></span>

<span data-ttu-id="94e64-671">下列範例顯示包含**EntityContainer**元素、兩個**EntityType**專案和一個**Association**元素的**Schema**專案。</span><span class="sxs-lookup"><span data-stu-id="94e64-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
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

## <a name="annotation-attributes"></a><span data-ttu-id="94e64-672">註釋屬性</span><span class="sxs-lookup"><span data-stu-id="94e64-672">Annotation Attributes</span></span>

<span data-ttu-id="94e64-673">存放結構定義語言 (SSDL) 中的 Annotation 屬性是儲存體模型中的自訂 XML 屬性，它們可提供與儲存體模型中之項目相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="94e64-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="94e64-674">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 屬性：</span><span class="sxs-lookup"><span data-stu-id="94e64-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="94e64-675">Annotation 屬性不能在任何 XML 命名空間之中，這是保留供 SSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="94e64-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="94e64-676">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="94e64-677">可以將一個以上的 Annotation 屬性套用至指定的 SSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="94e64-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="94e64-678">您可以使用 system.string 命名空間中的類別，在執行時間存取批註專案中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="94e64-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-679">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-679">Example</span></span>

<span data-ttu-id="94e64-680">下列範例顯示已將 annotation 屬性套用至 [ **訂單** ] 屬性的 EntityType 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="94e64-681">此範例也會顯示新增至 **EntityType** 元素的 annotation 專案。</span><span class="sxs-lookup"><span data-stu-id="94e64-681">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="94e64-682">Annotation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="94e64-683">存放結構定義語言 (SSDL) 中的 Annotation 項目是儲存體模型中的自訂 XML 項目，它們可提供與儲存體模型相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="94e64-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="94e64-684">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 項目：</span><span class="sxs-lookup"><span data-stu-id="94e64-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="94e64-685">Annotation 項目不能存在於保留供 SSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="94e64-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="94e64-686">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="94e64-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="94e64-687">Annotation 項目必須出現在所指定 SSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="94e64-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="94e64-688">多個 Annotation 項目可以同時做為所指定 SSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="94e64-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="94e64-689">從 .NET Framework 第4版開始，可以使用 system.string 命名空間中的類別，在執行時間存取批註專案中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="94e64-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="94e64-690">範例</span><span class="sxs-lookup"><span data-stu-id="94e64-690">Example</span></span>

<span data-ttu-id="94e64-691">下列範例示範具有 annotation 元素 (**CustomElement**) 的 EntityType 元素。</span><span class="sxs-lookup"><span data-stu-id="94e64-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="94e64-692">此範例也會顯示套用至 [ **訂單 id** ] 屬性的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="94e64-693">Facet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="94e64-693">Facets (SSDL)</span></span>

<span data-ttu-id="94e64-694">存放結構定義語言 (SSDL) 中的 Facet 表示 Property 項目中指定之資料行類型上的條件約束。</span><span class="sxs-lookup"><span data-stu-id="94e64-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="94e64-695">Facet 會實作為 **Property** 元素上的 XML 屬性。</span><span class="sxs-lookup"><span data-stu-id="94e64-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="94e64-696">下表說明 SSDL 中支援的 Facet：</span><span class="sxs-lookup"><span data-stu-id="94e64-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="94e64-697">Facet</span><span class="sxs-lookup"><span data-stu-id="94e64-697">Facet</span></span>           | <span data-ttu-id="94e64-698">描述</span><span class="sxs-lookup"><span data-stu-id="94e64-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="94e64-699">**定序**</span><span class="sxs-lookup"><span data-stu-id="94e64-699">**Collation**</span></span>   | <span data-ttu-id="94e64-700">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="94e64-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="94e64-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="94e64-701">**FixedLength**</span></span> | <span data-ttu-id="94e64-702">指定資料行值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="94e64-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="94e64-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="94e64-703">**MaxLength**</span></span>   | <span data-ttu-id="94e64-704">指定資料行值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="94e64-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="94e64-705">**有效位數**</span><span class="sxs-lookup"><span data-stu-id="94e64-705">**Precision**</span></span>   | <span data-ttu-id="94e64-706">針對 **Decimal**類型的屬性，指定屬性值可擁有的位數。</span><span class="sxs-lookup"><span data-stu-id="94e64-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="94e64-707">針對 **Time**、 **DateTime**和 **DateTimeOffset**類型的屬性，指定資料行值的秒數小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="94e64-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="94e64-708">**縮放比例**</span><span class="sxs-lookup"><span data-stu-id="94e64-708">**Scale**</span></span>       | <span data-ttu-id="94e64-709">指定資料行值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="94e64-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="94e64-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="94e64-710">**Unicode**</span></span>     | <span data-ttu-id="94e64-711">指定資料行值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="94e64-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
