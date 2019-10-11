---
title: SSDL 規格-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182538"
---
# <a name="ssdl-specification"></a><span data-ttu-id="cf89a-102">SSDL 規格</span><span class="sxs-lookup"><span data-stu-id="cf89a-102">SSDL Specification</span></span>
<span data-ttu-id="cf89a-103">存放結構定義語言 (SSDL) 是一種 XML 架構語言，會描述 Entity Framework 應用程式的儲存體模型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="cf89a-104">在 Entity Framework 應用程式中，儲存體模型中繼資料是從 ssdl 檔案（以 SSDL 撰寫）載入至 StoreItemCollection 的實例，而且可以使用中的方法來存取。System.string. system.string 類別。</span><span class="sxs-lookup"><span data-stu-id="cf89a-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="cf89a-105">Entity Framework 會使用儲存體模型中繼資料，將針對概念模型的查詢轉譯為存放區特有的命令。</span><span class="sxs-lookup"><span data-stu-id="cf89a-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="cf89a-106">Entity Framework Designer （EF Designer）會在設計階段將儲存模型資訊儲存在 .edmx 檔案中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="cf89a-107">在組建期間，Entity Designer 會使用 .edmx 檔案中的資訊，建立在執行時間 Entity Framework 所需的 ssdl 檔案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="cf89a-108">SSDL 的版本可藉由 XML 命名空間來區別。</span><span class="sxs-lookup"><span data-stu-id="cf89a-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="cf89a-109">SSDL 版本</span><span class="sxs-lookup"><span data-stu-id="cf89a-109">SSDL Version</span></span> | <span data-ttu-id="cf89a-110">XML 命名空間</span><span class="sxs-lookup"><span data-stu-id="cf89a-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="cf89a-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="cf89a-111">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="cf89a-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="cf89a-112">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="cf89a-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="cf89a-113">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="cf89a-114">Association 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-114">Association Element (SSDL)</span></span>

<span data-ttu-id="cf89a-115">存放結構定義語言（SSDL）中的**Association**元素會指定參與基礎資料庫外鍵條件約束的資料表資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="cf89a-116">兩個必要的子專案 End 元素會在關聯的兩端指定資料表，並在每個端的多重性處。</span><span class="sxs-lookup"><span data-stu-id="cf89a-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="cf89a-117">選擇性的 ReferentialConstraint 元素會指定關聯的主體和相依端點，以及參與的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="cf89a-118">如果沒有**ReferentialConstraint**元素存在，則必須使用 AssociationSetMapping 專案來指定關聯的資料行對應。</span><span class="sxs-lookup"><span data-stu-id="cf89a-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="cf89a-119">**Association**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-120">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-121">結束（剛好兩個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-121">End (exactly two)</span></span>
-   <span data-ttu-id="cf89a-122">ReferentialConstraint （零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="cf89a-123">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-124">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-124">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-125">下表描述可套用至**Association**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="cf89a-126">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-126">Attribute Name</span></span> | <span data-ttu-id="cf89a-127">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-127">Is Required</span></span> | <span data-ttu-id="cf89a-128">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-129">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-129">**Name**</span></span>       | <span data-ttu-id="cf89a-130">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-130">Yes</span></span>         | <span data-ttu-id="cf89a-131">基礎資料庫中對應之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-132">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="cf89a-133">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-134">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-135">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-135">Example</span></span>

<span data-ttu-id="cf89a-136">下列範例顯示的**Association**元素，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-3CustomerOrders** foreign key 條件約束的資料行：</span><span class="sxs-lookup"><span data-stu-id="cf89a-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="cf89a-137">AssociationSet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="cf89a-138">存放結構定義語言（SSDL）中的**AssociationSet**元素代表基礎資料庫中兩個數據表之間的外鍵條件約束。</span><span class="sxs-lookup"><span data-stu-id="cf89a-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="cf89a-139">參與外鍵條件約束的資料表資料行是在 Association 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="cf89a-140">對應至指定**associationset**元素的**association**專案，是在**AssociationSet**元素的**Association**屬性中指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="cf89a-141">SSDL 關聯集會透過 AssociationSetMapping 元素對應到 CSDL 關聯集。</span><span class="sxs-lookup"><span data-stu-id="cf89a-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="cf89a-142">不過，如果指定之 CSDL 關聯集的 CSDL 關聯是使用 ReferentialConstraint 元素所定義，則不需要對應的**AssociationSetMapping**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="cf89a-143">在此情況下，如果**AssociationSetMapping**元素存在，則**ReferentialConstraint**元素會覆寫它所定義的對應。</span><span class="sxs-lookup"><span data-stu-id="cf89a-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="cf89a-144">**AssociationSet**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-145">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-146">結束（零或兩個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-146">End (zero or two)</span></span>
-   <span data-ttu-id="cf89a-147">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-148">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-148">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-149">下表描述可套用至**AssociationSet**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="cf89a-150">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-150">Attribute Name</span></span>  | <span data-ttu-id="cf89a-151">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-151">Is Required</span></span> | <span data-ttu-id="cf89a-152">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-153">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-153">**Name**</span></span>        | <span data-ttu-id="cf89a-154">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-154">Yes</span></span>         | <span data-ttu-id="cf89a-155">關聯集所表示之外部索引鍵條件約束的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="cf89a-156">**技術協會**</span><span class="sxs-lookup"><span data-stu-id="cf89a-156">**Association**</span></span> | <span data-ttu-id="cf89a-157">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-157">Yes</span></span>         | <span data-ttu-id="cf89a-158">定義參與外部索引鍵條件約束之資料行的關聯名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-159">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**AssociationSet**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="cf89a-160">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-161">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-162">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-162">Example</span></span>

<span data-ttu-id="cf89a-163">下列範例顯示**AssociationSet**元素，此專案代表基礎資料庫中的 @no__t 1 外鍵條件約束：</span><span class="sxs-lookup"><span data-stu-id="cf89a-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="cf89a-164">CollectionType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="cf89a-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="cf89a-165">存放結構定義語言（SSDL）中的**CollectionType**元素會指定函式的傳回型別為集合。</span><span class="sxs-lookup"><span data-stu-id="cf89a-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="cf89a-166">**CollectionType**元素是 ReturnType 元素的子系。</span><span class="sxs-lookup"><span data-stu-id="cf89a-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="cf89a-167">集合的類型是使用 RowType 子項目所指定：</span><span class="sxs-lookup"><span data-stu-id="cf89a-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="cf89a-168">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**CollectionType**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="cf89a-169">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-170">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-171">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-171">Example</span></span>

<span data-ttu-id="cf89a-172">下列範例顯示的函式會使用**CollectionType**專案來指定該函數傳回資料列集合。</span><span class="sxs-lookup"><span data-stu-id="cf89a-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="cf89a-173">CommandText 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="cf89a-174">存放結構定義語言（SSDL）中的**CommandText**專案是 Function 元素的子系，可讓您定義在資料庫上執行的 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="cf89a-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="cf89a-175">**CommandText**元素可讓您加入與資料庫中的預存程式類似的功能，但您會在儲存模型中定義**CommandText**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="cf89a-176">**CommandText**元素不能有子項目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="cf89a-177">**CommandText**元素的主體必須是基礎資料庫的有效 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="cf89a-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="cf89a-178">**CommandText**元素沒有適用的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-179">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-179">Example</span></span>

<span data-ttu-id="cf89a-180">下列範例顯示具有子**CommandText**元素的**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="cf89a-181">藉由將**UpdateProductInOrder**函式匯入到概念模型中，將其公開為 ObjectCoNtext 的方法。</span><span class="sxs-lookup"><span data-stu-id="cf89a-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="cf89a-182">DefiningQuery 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="cf89a-183">存放結構定義語言（SSDL）中的**DefiningQuery**元素可讓您直接在基礎資料庫中執行 SQL 語句。</span><span class="sxs-lookup"><span data-stu-id="cf89a-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="cf89a-184">**DefiningQuery**元素常用於資料庫檢視，但此視圖定義于儲存體模型中，而不是資料庫中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="cf89a-185">**DefiningQuery**元素中定義的視圖可以透過 EntitySetMapping 專案對應到概念模型中的實體類型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="cf89a-186">這些對應是唯讀的。</span><span class="sxs-lookup"><span data-stu-id="cf89a-186">These mappings are read-only.</span></span>  

<span data-ttu-id="cf89a-187">下列 SSDL 語法顯示**EntitySet**的宣告，後面接著**DefiningQuery**元素，其中包含用來抓取視圖的查詢。</span><span class="sxs-lookup"><span data-stu-id="cf89a-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="cf89a-188">您可以使用 Entity Framework 中的預存程式，透過 views 啟用讀寫案例。</span><span class="sxs-lookup"><span data-stu-id="cf89a-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="cf89a-189"> 您可以使用資料來源 view 或 Entity SQL view，做為用來抓取資料的基表和預存程式的變更處理。</span><span class="sxs-lookup"><span data-stu-id="cf89a-189"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="cf89a-190">您可以使用**DefiningQuery**元素，以 Microsoft SQL Server Compact 3.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="cf89a-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="cf89a-191">雖然 SQL Server Compact 3.5 不支援預存程式，但您可以使用**DefiningQuery**元素來執行類似的功能。</span><span class="sxs-lookup"><span data-stu-id="cf89a-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="cf89a-192">該項目的另一個用處是可建立預存程序，克服程式設計語言中所使用之資料類型與資料來源之資料類型間不相符的問題。</span><span class="sxs-lookup"><span data-stu-id="cf89a-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="cf89a-193">您可以撰寫一個**DefiningQuery** ，它會接受一組特定的參數，然後使用一組不同的參數來呼叫預存程式，例如，刪除資料的預存程式。</span><span class="sxs-lookup"><span data-stu-id="cf89a-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="cf89a-194">Dependent 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="cf89a-195">存放結構定義語言（SSDL）中的**相依**專案是 ReferentialConstraint 元素的子專案，可定義外鍵條件約束（也稱為參考條件約束）的相依端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="cf89a-196">**Dependent**元素會在參考主鍵資料行（或資料行）的資料表中指定資料行（或資料行）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="cf89a-197">**PropertyRef**元素會指定要參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="cf89a-198">Principal 元素會指定**相依**專案中所指定之資料行所參考的主鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="cf89a-199">相依元素可以具有下列子專案（**依**列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-200">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="cf89a-201">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-202">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-202">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-203">下表描述可套用至**相依**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="cf89a-204">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-204">Attribute Name</span></span> | <span data-ttu-id="cf89a-205">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-205">Is Required</span></span> | <span data-ttu-id="cf89a-206">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-207">**角色**</span><span class="sxs-lookup"><span data-stu-id="cf89a-207">**Role**</span></span>       | <span data-ttu-id="cf89a-208">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-208">Yes</span></span>         | <span data-ttu-id="cf89a-209">與對應之 End 專案的**Role**屬性（如果使用的話）相同的值。否則，就是包含參考資料行之資料表的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-210">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**相依**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="cf89a-211">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-212">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-213">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-213">Example</span></span>

<span data-ttu-id="cf89a-214">下列範例顯示的 Association 專案，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-2CustomerOrders** foreign key 條件約束的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="cf89a-215">**Dependent**元素會將**Order**資料表的**CustomerId**資料行指定為條件約束的相依端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="cf89a-216">Documentation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="cf89a-217">存放結構定義語言（SSDL）中的**檔**專案可以用來提供父元素中所定義之物件的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="cf89a-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="cf89a-218">**檔**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-219">**摘要**：父元素的簡短描述。</span><span class="sxs-lookup"><span data-stu-id="cf89a-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="cf89a-220">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="cf89a-220">(zero or one element)</span></span>
-   <span data-ttu-id="cf89a-221">**LongDescription**：父元素的詳細描述。</span><span class="sxs-lookup"><span data-stu-id="cf89a-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="cf89a-222">(零或一個項目)</span><span class="sxs-lookup"><span data-stu-id="cf89a-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-223">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-223">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-224">可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**檔**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="cf89a-225">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-226">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-227">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-227">Example</span></span>

<span data-ttu-id="cf89a-228">下列範例顯示**檔**元素做為 EntityType 元素的子專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="cf89a-229">End 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-229">End Element (SSDL)</span></span>

<span data-ttu-id="cf89a-230">存放結構定義語言（SSDL）中的**End**元素會指定基礎資料庫中外鍵條件約束一端的資料表和資料列數目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="cf89a-231">**End**元素可以是 Association 元素或 AssociationSet 專案的子系。</span><span class="sxs-lookup"><span data-stu-id="cf89a-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="cf89a-232">在這兩種情況下，可能的子項目和適用的屬性都不相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="cf89a-233">End 項目為 Association 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="cf89a-234">**End**專案（做為**Association**專案的子系）會指定 foreign key 條件約束結尾的資料表和資料列數目，分別具有**Type**和**多重性**屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="cf89a-235">外部索引鍵條件約束的端點是定義為 SSDL 關聯的一部分；SSDL 關聯必須擁有兩個端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="cf89a-236">**End**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-237">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-238">OnDelete （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-239">Annotation 元素（零或多個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-240">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-240">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-241">下表描述當屬性為**Association**元素的子系時，可以套用至**結尾**專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="cf89a-242">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-242">Attribute Name</span></span>   | <span data-ttu-id="cf89a-243">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-243">Is Required</span></span> | <span data-ttu-id="cf89a-244">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-245">**型別**</span><span class="sxs-lookup"><span data-stu-id="cf89a-245">**Type**</span></span>         | <span data-ttu-id="cf89a-246">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-246">Yes</span></span>         | <span data-ttu-id="cf89a-247">位於外鍵條件約束結尾之 SSDL 實體集的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="cf89a-248">**角色**</span><span class="sxs-lookup"><span data-stu-id="cf89a-248">**Role**</span></span>         | <span data-ttu-id="cf89a-249">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-249">No</span></span>          | <span data-ttu-id="cf89a-250">在對應之 ReferentialConstraint 專案的 Principal 或 Dependent 元素中， **Role**屬性的值（如果有使用的話）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="cf89a-251">**數**</span><span class="sxs-lookup"><span data-stu-id="cf89a-251">**Multiplicity**</span></span> | <span data-ttu-id="cf89a-252">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-252">Yes</span></span>         | <span data-ttu-id="cf89a-253">**1**、 **0 ..1**或 **\*** ，視外鍵條件約束結尾的資料列數目而定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="cf89a-254">**1**表示外鍵條件約束結尾只存在一個資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="cf89a-255">**0 ..1**表示外鍵條件約束結尾處有零或一個資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="cf89a-256">**\*** 表示外鍵條件約束結尾有零個、一個或多個資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-257">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="cf89a-258">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-259">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="cf89a-260">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-260">Example</span></span>

<span data-ttu-id="cf89a-261">下列範例顯示定義**FK @ no__t-2CustomerOrders** foreign key 條件約束的**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="cf89a-262">在每個**End**專案上指定的**多重性**值表示**Orders**資料表中的許多資料列都可以與**customers**資料表中的資料列相關聯，但是**customers**資料表中只有一個資料列可以與資料列相關聯在**Orders**資料表中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="cf89a-263">此外， **OnDelete**元素表示如果**customers**資料表中的資料列遭到刪除，則會刪除**Orders**資料表中，參考**customers**資料表中之特定資料列的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="cf89a-264">End 項目為 AssociationSet 項目的子項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="cf89a-265">**End**專案（做為**AssociationSet**專案的子專案）會指定基礎資料庫中外鍵條件約束一端的資料表。</span><span class="sxs-lookup"><span data-stu-id="cf89a-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="cf89a-266">**End**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-267">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-268">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-269">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-269">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-270">下表描述當元素是**AssociationSet**專案的子系時，可以套用至**結尾**專案的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="cf89a-271">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-271">Attribute Name</span></span> | <span data-ttu-id="cf89a-272">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-272">Is Required</span></span> | <span data-ttu-id="cf89a-273">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="cf89a-274">**EntitySet**</span></span>  | <span data-ttu-id="cf89a-275">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-275">Yes</span></span>         | <span data-ttu-id="cf89a-276">位於外鍵條件約束結尾之 SSDL 實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="cf89a-277">**角色**</span><span class="sxs-lookup"><span data-stu-id="cf89a-277">**Role**</span></span>       | <span data-ttu-id="cf89a-278">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-278">No</span></span>          | <span data-ttu-id="cf89a-279">在對應關聯專案的一個**End**專案上指定的其中一個**角色**屬性的值。</span><span class="sxs-lookup"><span data-stu-id="cf89a-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-280">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**End**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="cf89a-281">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-282">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="cf89a-283">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-283">Example</span></span>

<span data-ttu-id="cf89a-284">下列範例顯示具有**AssociationSet**元素並具有兩個**End**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="cf89a-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="cf89a-285">EntityContainer 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="cf89a-286">存放結構定義語言（SSDL）中的**EntityContainer**元素會描述 Entity Framework 應用程式中基礎資料來源的結構：SSDL 實體集（定義于 EntitySet 專案中）表示資料庫中的資料表、SSDL 實體類型（定義于 EntityType 元素中）代表資料表中的資料列，以及關聯集（定義于 AssociationSet 專案中）代表中的外鍵條件約束資料.</span><span class="sxs-lookup"><span data-stu-id="cf89a-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="cf89a-287">儲存體模型實體容器會透過 EntityContainerMapping 元素對應至概念模型實體容器。</span><span class="sxs-lookup"><span data-stu-id="cf89a-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="cf89a-288">**EntityContainer**元素可以有零個或一個檔元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="cf89a-289">如果**檔**元素存在，則必須在所有其他子專案之前。</span><span class="sxs-lookup"><span data-stu-id="cf89a-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="cf89a-290">**EntityContainer**元素可以有零或多個下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-291">EntitySet</span><span class="sxs-lookup"><span data-stu-id="cf89a-291">EntitySet</span></span>
-   <span data-ttu-id="cf89a-292">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="cf89a-292">AssociationSet</span></span>
-   <span data-ttu-id="cf89a-293">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-294">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-294">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-295">下表描述可套用至**EntityContainer**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="cf89a-296">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-296">Attribute Name</span></span> | <span data-ttu-id="cf89a-297">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-297">Is Required</span></span> | <span data-ttu-id="cf89a-298">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-299">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-299">**Name**</span></span>       | <span data-ttu-id="cf89a-300">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-300">Yes</span></span>         | <span data-ttu-id="cf89a-301">實體容器的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-301">The name of the entity container.</span></span> <span data-ttu-id="cf89a-302">此名稱不得包含任何句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-303">可以將任何數目的注釋屬性（自訂 XML 屬性）套用至**EntityContainer**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="cf89a-304">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-305">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-306">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-306">Example</span></span>

<span data-ttu-id="cf89a-307">下列範例顯示的**EntityContainer**元素會定義兩個實體集和一個關聯集。</span><span class="sxs-lookup"><span data-stu-id="cf89a-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="cf89a-308">請注意實體類型和關聯類型名稱要以概念模型命名空間名稱限定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="cf89a-309">EntitySet 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="cf89a-310">存放結構定義語言（SSDL）中的**EntitySet**元素代表基礎資料庫中的資料表或視圖。</span><span class="sxs-lookup"><span data-stu-id="cf89a-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="cf89a-311">SSDL 中的 EntityType 元素代表資料表或視圖中的資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="cf89a-312">**EntitySet**元素的**EntityType**屬性會指定代表 ssdl 實體集內資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="cf89a-313">CSDL 實體集與 SSDL 實體集之間的對應是在 EntitySetMapping 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="cf89a-314">**EntitySet**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-315">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-316">DefiningQuery （零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-317">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-318">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-318">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-319">下表描述可套用至**EntitySet**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="cf89a-320">某些屬性（此處未列出）可能會以**存放區**別名來限定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="cf89a-321">更新模型時會使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="cf89a-322">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-322">Attribute Name</span></span> | <span data-ttu-id="cf89a-323">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-323">Is Required</span></span> | <span data-ttu-id="cf89a-324">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-325">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-325">**Name**</span></span>       | <span data-ttu-id="cf89a-326">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-326">Yes</span></span>         | <span data-ttu-id="cf89a-327">實體集的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="cf89a-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="cf89a-328">**EntityType**</span></span> | <span data-ttu-id="cf89a-329">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-329">Yes</span></span>         | <span data-ttu-id="cf89a-330">實體類型 (實體集包含其執行個體) 的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="cf89a-331">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="cf89a-331">**Schema**</span></span>     | <span data-ttu-id="cf89a-332">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-332">No</span></span>          | <span data-ttu-id="cf89a-333">資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="cf89a-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="cf89a-334">**資料表**</span><span class="sxs-lookup"><span data-stu-id="cf89a-334">**Table**</span></span>      | <span data-ttu-id="cf89a-335">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-335">No</span></span>          | <span data-ttu-id="cf89a-336">資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="cf89a-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="cf89a-337">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntitySet**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="cf89a-338">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-339">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-340">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-340">Example</span></span>

<span data-ttu-id="cf89a-341">下列範例顯示具有兩個**EntitySet**元素和一個**AssociationSet**元素的**EntityContainer**元素：</span><span class="sxs-lookup"><span data-stu-id="cf89a-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="cf89a-342">EntityType 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="cf89a-343">存放結構定義語言（SSDL）中的**EntityType**元素代表基礎資料庫之資料表或視圖中的資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="cf89a-344">SSDL 中的 EntitySet 元素代表資料列發生所在的資料表或視圖。</span><span class="sxs-lookup"><span data-stu-id="cf89a-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="cf89a-345">**EntitySet**元素的**EntityType**屬性會指定代表 ssdl 實體集內資料列的特定 SSDL 實體類型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="cf89a-346">SSDL 實體類型和 CSDL 實體類型之間的對應是在 EntityTypeMapping 元素中指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="cf89a-347">**EntityType**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-348">檔（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-349">索引鍵（零或一個元素）</span><span class="sxs-lookup"><span data-stu-id="cf89a-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="cf89a-350">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-351">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-351">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-352">下表描述可套用至**EntityType**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="cf89a-353">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-353">Attribute Name</span></span> | <span data-ttu-id="cf89a-354">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-354">Is Required</span></span> | <span data-ttu-id="cf89a-355">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-356">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-356">**Name**</span></span>       | <span data-ttu-id="cf89a-357">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-357">Yes</span></span>         | <span data-ttu-id="cf89a-358">實體類型的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-358">The name of the entity type.</span></span> <span data-ttu-id="cf89a-359">此值通常與資料表名稱相同，在資料表中實體類型表示資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="cf89a-360">此值不可以包含句號 (.)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-361">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**EntityType**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="cf89a-362">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-363">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-364">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-364">Example</span></span>

<span data-ttu-id="cf89a-365">下列範例顯示具有兩個屬性的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="cf89a-365">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="cf89a-366">Function 屬性 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-366">Function Element (SSDL)</span></span>

<span data-ttu-id="cf89a-367">存放結構定義語言（SSDL）中的**Function**元素會指定存在於基礎資料庫中的預存程式。</span><span class="sxs-lookup"><span data-stu-id="cf89a-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="cf89a-368">**Function**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-369">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-370">參數（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="cf89a-371">CommandText （零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="cf89a-372">ReturnType （零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="cf89a-373">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="cf89a-374">函式的傳回型別必須使用**returntype**元素或**returntype**屬性（請參閱下文）來指定，但不能同時指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="cf89a-375">儲存體模型中所指定的預存程序可以匯入應用程式的概念模型中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="cf89a-376">如需詳細資訊，請參閱[使用預存程式進行查詢](~/ef6/modeling/designer/stored-procedures/query.md)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="cf89a-377">**Function**元素也可以用來定義儲存體模型中的自訂函數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-378">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-378">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-379">下表描述可套用至**Function**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="cf89a-380">某些屬性（此處未列出）可能會以**存放區**別名來限定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="cf89a-381">更新模型時會使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="cf89a-382">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-382">Attribute Name</span></span>             | <span data-ttu-id="cf89a-383">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-383">Is Required</span></span> | <span data-ttu-id="cf89a-384">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-385">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-385">**Name**</span></span>                   | <span data-ttu-id="cf89a-386">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-386">Yes</span></span>         | <span data-ttu-id="cf89a-387">預存程序 (Stored Procedure) 的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="cf89a-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="cf89a-388">**ReturnType**</span></span>             | <span data-ttu-id="cf89a-389">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-389">No</span></span>          | <span data-ttu-id="cf89a-390">預存程序的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="cf89a-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="cf89a-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="cf89a-391">**Aggregate**</span></span>              | <span data-ttu-id="cf89a-392">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-392">No</span></span>          | <span data-ttu-id="cf89a-393">如果預存程式傳回匯總值，則**為 True** ;否則**為 False**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="cf89a-394">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="cf89a-394">**BuiltIn**</span></span>                | <span data-ttu-id="cf89a-395">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-395">No</span></span>          | <span data-ttu-id="cf89a-396">如果函式是內建的<sup>1</sup>函數，則為**True** ;否則**為 False**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="cf89a-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="cf89a-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="cf89a-398">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-398">No</span></span>          | <span data-ttu-id="cf89a-399">預存程序 (Stored Procedure) 的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="cf89a-400">**且具有 niladicfunction**</span><span class="sxs-lookup"><span data-stu-id="cf89a-400">**NiladicFunction**</span></span>        | <span data-ttu-id="cf89a-401">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-401">No</span></span>          | <span data-ttu-id="cf89a-402">如果函數是 niladic<sup>2</sup>函式，則為**True** ;否則**為 False** 。</span><span class="sxs-lookup"><span data-stu-id="cf89a-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="cf89a-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="cf89a-403">**IsComposable**</span></span>           | <span data-ttu-id="cf89a-404">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-404">No</span></span>          | <span data-ttu-id="cf89a-405">如果函式是可組合的<sup>3</sup>個函數，則為**True** ;否則**為 False** 。</span><span class="sxs-lookup"><span data-stu-id="cf89a-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="cf89a-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="cf89a-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="cf89a-407">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-407">No</span></span>          | <span data-ttu-id="cf89a-408">列舉型別，可定義用來解決函式多載的型別語意。</span><span class="sxs-lookup"><span data-stu-id="cf89a-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="cf89a-409">列舉型別會定義在每個函式定義的提供者資訊清單。</span><span class="sxs-lookup"><span data-stu-id="cf89a-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="cf89a-410">預設值為**AllowImplicitConversion**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="cf89a-411">**結構描述**</span><span class="sxs-lookup"><span data-stu-id="cf89a-411">**Schema**</span></span>                 | <span data-ttu-id="cf89a-412">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-412">No</span></span>          | <span data-ttu-id="cf89a-413">結構描述的名稱，可在其中定義預存程序。</span><span class="sxs-lookup"><span data-stu-id="cf89a-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="cf89a-414"><sup>1</sup>內建函數是在資料庫中定義的函數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="cf89a-415">如需儲存體模型中所定義之函式的詳細資訊，請參閱 CommandText 元素（SSDL）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="cf89a-416"><sup>2</sup> niladic 函式是不接受任何參數的函式，呼叫時，不需要括弧。</span><span class="sxs-lookup"><span data-stu-id="cf89a-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="cf89a-417">如果一個函式的輸出可以是另一個函式的輸入，<sup>則兩個</sup>函式是可組合的。</span><span class="sxs-lookup"><span data-stu-id="cf89a-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="cf89a-418">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="cf89a-419">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-420">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-421">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-421">Example</span></span>

<span data-ttu-id="cf89a-422">下列範例會顯示對應至**UpdateOrderQuantity**預存程式的**Function**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="cf89a-423">預存程序可接受兩個參數且不傳回值。</span><span class="sxs-lookup"><span data-stu-id="cf89a-423">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="cf89a-424">Key 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-424">Key Element (SSDL)</span></span>

<span data-ttu-id="cf89a-425">存放結構定義語言（SSDL）中的**Key**元素代表基礎資料庫中資料表的主鍵。</span><span class="sxs-lookup"><span data-stu-id="cf89a-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="cf89a-426">**Key**是 EntityType 元素的子項目，代表資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="cf89a-427">主要索引鍵是藉由參考**EntityType**元素上定義的一個或多個屬性專案，定義在**key**元素中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="cf89a-428">**Key**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-429">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="cf89a-430">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-430">Annotation elements</span></span>

<span data-ttu-id="cf89a-431">索引**鍵**元素沒有適用的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-432">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-432">Example</span></span>

<span data-ttu-id="cf89a-433">下列範例顯示的**EntityType**元素具有參考一個屬性的索引鍵：</span><span class="sxs-lookup"><span data-stu-id="cf89a-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="cf89a-434">OnDelete 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="cf89a-435">當參與外鍵條件約束的資料列遭到刪除時，存放結構定義語言（SSDL）中的**OnDelete**元素會反映資料庫行為。</span><span class="sxs-lookup"><span data-stu-id="cf89a-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="cf89a-436">如果動作設定為 [ **Cascade**]，則會一併刪除參考正在刪除之資料列的資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="cf89a-437">如果動作設定為 [**無**]，則參考所刪除之資料列的資料列也不會被刪除。</span><span class="sxs-lookup"><span data-stu-id="cf89a-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="cf89a-438">**OnDelete**元素是 End 元素的子項目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="cf89a-439">**OnDelete**元素可以有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-440">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-441">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-442">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-442">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-443">下表描述可套用至**OnDelete**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="cf89a-444">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-444">Attribute Name</span></span> | <span data-ttu-id="cf89a-445">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-445">Is Required</span></span> | <span data-ttu-id="cf89a-446">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-447">**動作**</span><span class="sxs-lookup"><span data-stu-id="cf89a-447">**Action**</span></span>     | <span data-ttu-id="cf89a-448">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-448">Yes</span></span>         | <span data-ttu-id="cf89a-449">**Cascade**或**None**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-449">**Cascade** or **None**.</span></span> <span data-ttu-id="cf89a-450">（**限制**的值是有效的，但其行為與 [**無**] 相同）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-451">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**OnDelete**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="cf89a-452">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-453">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-454">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-454">Example</span></span>

<span data-ttu-id="cf89a-455">下列範例顯示定義**FK @ no__t-2CustomerOrders** foreign key 條件約束的**Association**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="cf89a-456">**OnDelete**元素表示如果**customers**資料表中的資料列被刪除，則會刪除**Orders**資料表中，參考**customers**資料表中之特定資料列的所有資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="cf89a-457">Parameter 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="cf89a-458">存放結構定義語言（SSDL）中的**Parameter**專案是 Function 元素的子系，可指定資料庫中預存程式的參數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="cf89a-459">**Parameter**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-460">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-461">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-462">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-462">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-463">下表描述可套用至**參數**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="cf89a-464">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-464">Attribute Name</span></span> | <span data-ttu-id="cf89a-465">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-465">Is Required</span></span> | <span data-ttu-id="cf89a-466">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-467">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-467">**Name**</span></span>       | <span data-ttu-id="cf89a-468">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-468">Yes</span></span>         | <span data-ttu-id="cf89a-469">參數名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="cf89a-470">**型別**</span><span class="sxs-lookup"><span data-stu-id="cf89a-470">**Type**</span></span>       | <span data-ttu-id="cf89a-471">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-471">Yes</span></span>         | <span data-ttu-id="cf89a-472">參數型別。</span><span class="sxs-lookup"><span data-stu-id="cf89a-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="cf89a-473">**下**</span><span class="sxs-lookup"><span data-stu-id="cf89a-473">**Mode**</span></span>       | <span data-ttu-id="cf89a-474">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-474">No</span></span>          | <span data-ttu-id="cf89a-475">**In**、 **Out**或**InOut** ，取決於參數是輸入、輸出或輸入/輸出參數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="cf89a-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="cf89a-476">**MaxLength**</span></span>  | <span data-ttu-id="cf89a-477">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-477">No</span></span>          | <span data-ttu-id="cf89a-478">參數的長度上限。</span><span class="sxs-lookup"><span data-stu-id="cf89a-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="cf89a-479">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="cf89a-479">**Precision**</span></span>  | <span data-ttu-id="cf89a-480">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-480">No</span></span>          | <span data-ttu-id="cf89a-481">參數的精確度。</span><span class="sxs-lookup"><span data-stu-id="cf89a-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="cf89a-482">**縮放**</span><span class="sxs-lookup"><span data-stu-id="cf89a-482">**Scale**</span></span>      | <span data-ttu-id="cf89a-483">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-483">No</span></span>          | <span data-ttu-id="cf89a-484">參數的小數位數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="cf89a-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="cf89a-485">**SRID**</span></span>       | <span data-ttu-id="cf89a-486">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-486">No</span></span>          | <span data-ttu-id="cf89a-487">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="cf89a-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="cf89a-488">僅對空間類型的參數有效。</span><span class="sxs-lookup"><span data-stu-id="cf89a-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="cf89a-489">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-489">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-490">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**Parameter**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="cf89a-491">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-492">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-493">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-493">Example</span></span>

<span data-ttu-id="cf89a-494">下列範例**顯示的函式元素具有**兩個指定輸入參數的**參數**元素：</span><span class="sxs-lookup"><span data-stu-id="cf89a-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="cf89a-495">Principal 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="cf89a-496">存放結構定義語言（SSDL）中的**Principal**元素是 ReferentialConstraint 元素的子專案，可定義外鍵條件約束（也稱為參考條件約束）的主要端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="cf89a-497">**Principal**元素會在另一個資料行（或資料行）所參考的資料表中，指定主鍵資料行（或資料行）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="cf89a-498">**PropertyRef**元素會指定要參考的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="cf89a-499">Dependent 元素會指定參考**Principal**元素中所指定之主鍵資料行的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="cf89a-500">**Principal**元素可以具有下列子專案（依列出的順序）：</span><span class="sxs-lookup"><span data-stu-id="cf89a-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="cf89a-501">PropertyRef （一或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="cf89a-502">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-503">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-503">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-504">下表描述可套用至**主體**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="cf89a-505">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-505">Attribute Name</span></span> | <span data-ttu-id="cf89a-506">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-506">Is Required</span></span> | <span data-ttu-id="cf89a-507">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-508">**角色**</span><span class="sxs-lookup"><span data-stu-id="cf89a-508">**Role**</span></span>       | <span data-ttu-id="cf89a-509">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-509">Yes</span></span>         | <span data-ttu-id="cf89a-510">與對應之 End 專案的**Role**屬性（如果使用的話）相同的值。否則，就是包含參考資料行之資料表的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-511">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**主體**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="cf89a-512">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-513">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-514">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-514">Example</span></span>

<span data-ttu-id="cf89a-515">下列範例顯示的 Association 專案，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-2CustomerOrders** foreign key 條件約束的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="cf89a-516">**Principal**元素會將**Customer**資料表的**CustomerId**資料行指定為條件約束的主要端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="cf89a-517">Property 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-517">Property Element (SSDL)</span></span>

<span data-ttu-id="cf89a-518">存放結構定義語言（SSDL）中的**屬性**專案代表基礎資料庫中資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="cf89a-519">**屬性**元素是 EntityType 元素的子系，代表資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="cf89a-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="cf89a-520">**EntityType**元素上定義的每個**屬性**專案都代表一個資料行。</span><span class="sxs-lookup"><span data-stu-id="cf89a-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="cf89a-521">**Property**元素不能有任何子專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-522">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-522">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-523">下表描述可套用至**Property**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="cf89a-524">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-524">Attribute Name</span></span>            | <span data-ttu-id="cf89a-525">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-525">Is Required</span></span> | <span data-ttu-id="cf89a-526">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-527">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-527">**Name**</span></span>                  | <span data-ttu-id="cf89a-528">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-528">Yes</span></span>         | <span data-ttu-id="cf89a-529">對應資料行的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="cf89a-530">**型別**</span><span class="sxs-lookup"><span data-stu-id="cf89a-530">**Type**</span></span>                  | <span data-ttu-id="cf89a-531">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-531">Yes</span></span>         | <span data-ttu-id="cf89a-532">對應資料行的類型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="cf89a-533">**Null**</span><span class="sxs-lookup"><span data-stu-id="cf89a-533">**Nullable**</span></span>              | <span data-ttu-id="cf89a-534">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-534">No</span></span>          | <span data-ttu-id="cf89a-535">**True** （預設值）或**False** ，取決於對應的資料行是否可以有 null 值。</span><span class="sxs-lookup"><span data-stu-id="cf89a-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="cf89a-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="cf89a-536">**DefaultValue**</span></span>          | <span data-ttu-id="cf89a-537">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-537">No</span></span>          | <span data-ttu-id="cf89a-538">對應資料行的預設值。</span><span class="sxs-lookup"><span data-stu-id="cf89a-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="cf89a-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="cf89a-539">**MaxLength**</span></span>             | <span data-ttu-id="cf89a-540">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-540">No</span></span>          | <span data-ttu-id="cf89a-541">對應資料行的長度上限。</span><span class="sxs-lookup"><span data-stu-id="cf89a-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="cf89a-542">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="cf89a-542">**FixedLength**</span></span>           | <span data-ttu-id="cf89a-543">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-543">No</span></span>          | <span data-ttu-id="cf89a-544">**True**或**False** ，取決於對應的資料行值是否會儲存為固定長度的字串。</span><span class="sxs-lookup"><span data-stu-id="cf89a-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="cf89a-545">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="cf89a-545">**Precision**</span></span>             | <span data-ttu-id="cf89a-546">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-546">No</span></span>          | <span data-ttu-id="cf89a-547">對應資料行的精確度。</span><span class="sxs-lookup"><span data-stu-id="cf89a-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="cf89a-548">**縮放**</span><span class="sxs-lookup"><span data-stu-id="cf89a-548">**Scale**</span></span>                 | <span data-ttu-id="cf89a-549">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-549">No</span></span>          | <span data-ttu-id="cf89a-550">對應資料行的小數位數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="cf89a-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="cf89a-551">**Unicode**</span></span>               | <span data-ttu-id="cf89a-552">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-552">No</span></span>          | <span data-ttu-id="cf89a-553">**True**或**False** ，取決於對應的資料行值是否會儲存為 Unicode 字串。</span><span class="sxs-lookup"><span data-stu-id="cf89a-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="cf89a-554">**定序**</span><span class="sxs-lookup"><span data-stu-id="cf89a-554">**Collation**</span></span>             | <span data-ttu-id="cf89a-555">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-555">No</span></span>          | <span data-ttu-id="cf89a-556">指定資料來源中使用之定序順序的字串。</span><span class="sxs-lookup"><span data-stu-id="cf89a-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="cf89a-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="cf89a-557">**SRID**</span></span>                  | <span data-ttu-id="cf89a-558">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-558">No</span></span>          | <span data-ttu-id="cf89a-559">空間系統參考識別碼。</span><span class="sxs-lookup"><span data-stu-id="cf89a-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="cf89a-560">僅對空間類型的屬性有效。</span><span class="sxs-lookup"><span data-stu-id="cf89a-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="cf89a-561">如需詳細資訊，請參閱 [SRID](https://en.wikipedia.org/wiki/SRID)並[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-561">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="cf89a-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="cf89a-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="cf89a-563">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-563">No</span></span>          | <span data-ttu-id="cf89a-564">**無**、**識別**（如果對應的資料行值是在資料庫中產生的身分識別），或**計算**（如果對應的資料行值是在資料庫中計算）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="cf89a-565">對 RowType 屬性無效。</span><span class="sxs-lookup"><span data-stu-id="cf89a-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-566">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**屬性**專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="cf89a-567">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-568">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-569">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-569">Example</span></span>

<span data-ttu-id="cf89a-570">下列範例顯示具有兩個子**屬性**元素的**EntityType**元素：</span><span class="sxs-lookup"><span data-stu-id="cf89a-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="cf89a-571">PropertyRef 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="cf89a-572">存放結構定義語言（SSDL）中的**PropertyRef**元素會參考 EntityType 元素上定義的屬性，以指出屬性將會執行下列其中一個角色：</span><span class="sxs-lookup"><span data-stu-id="cf89a-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="cf89a-573">屬於**EntityType**所代表之資料表的主鍵。</span><span class="sxs-lookup"><span data-stu-id="cf89a-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="cf89a-574">一或多個**PropertyRef**元素可以用來定義主鍵。</span><span class="sxs-lookup"><span data-stu-id="cf89a-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="cf89a-575">如需詳細資訊，請參閱 Key 元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="cf89a-576">做為參考條件約束的相依端點和主要端點。</span><span class="sxs-lookup"><span data-stu-id="cf89a-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="cf89a-577">如需詳細資訊，請參閱 ReferentialConstraint 元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="cf89a-578">**PropertyRef**元素只能有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="cf89a-579">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-580">Annotation 項目</span><span class="sxs-lookup"><span data-stu-id="cf89a-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-581">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-581">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-582">下表描述可套用至**PropertyRef**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="cf89a-583">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-583">Attribute Name</span></span> | <span data-ttu-id="cf89a-584">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-584">Is Required</span></span> | <span data-ttu-id="cf89a-585">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="cf89a-586">**名稱**</span><span class="sxs-lookup"><span data-stu-id="cf89a-586">**Name**</span></span>       | <span data-ttu-id="cf89a-587">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-587">Yes</span></span>         | <span data-ttu-id="cf89a-588">參考屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="cf89a-589">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**PropertyRef**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="cf89a-590">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留給 CSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="cf89a-591">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-592">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-592">Example</span></span>

<span data-ttu-id="cf89a-593">下列範例顯示的**PropertyRef**專案，是用來藉由參考**EntityType**元素上定義的屬性來定義主鍵。</span><span class="sxs-lookup"><span data-stu-id="cf89a-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="cf89a-594">ReferentialConstraint 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="cf89a-595">存放結構定義語言（SSDL）中的**ReferentialConstraint**元素代表基礎資料庫中的外鍵條件約束（也稱為參考完整性條件約束）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="cf89a-596">條件約束的主體和相依端點會分別由主體和相依子項目指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="cf89a-597">參與主體和相依端點的資料行會以 PropertyRef 元素來參考。</span><span class="sxs-lookup"><span data-stu-id="cf89a-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="cf89a-598">**ReferentialConstraint**元素是 Association 元素的選擇性子項目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="cf89a-599">如果**ReferentialConstraint**元素不是用來對應**Association**元素中所指定的 foreign key 條件約束，就必須使用 AssociationSetMapping 專案來執行此動作。</span><span class="sxs-lookup"><span data-stu-id="cf89a-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="cf89a-600">**ReferentialConstraint**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="cf89a-601">檔（零或一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="cf89a-602">Principal （只有一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="cf89a-603">相依（只有一個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="cf89a-604">Annotation 元素（零或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-605">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-605">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-606">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReferentialConstraint**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="cf89a-607">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-608">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-609">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-609">Example</span></span>

<span data-ttu-id="cf89a-610">下列範例顯示的**Association**元素，會使用**ReferentialConstraint**專案來指定參與**FK @ no__t-3CustomerOrders** foreign key 條件約束的資料行：</span><span class="sxs-lookup"><span data-stu-id="cf89a-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="cf89a-611">ReturnType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="cf89a-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="cf89a-612">存放結構定義語言（SSDL）中的**ReturnType**元素會指定在**function**元素中定義之函式的傳回類型。</span><span class="sxs-lookup"><span data-stu-id="cf89a-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="cf89a-613">函式傳回型別也可以使用**ReturnType**屬性來指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="cf89a-614">函式的傳回型別是使用**type**屬性或**ReturnType**元素所指定。</span><span class="sxs-lookup"><span data-stu-id="cf89a-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="cf89a-615">**ReturnType**元素可以具有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="cf89a-616">CollectionType （一）</span><span class="sxs-lookup"><span data-stu-id="cf89a-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="cf89a-617">任何數目的注釋屬性（自訂 XML 屬性）都可以套用至**ReturnType**元素。</span><span class="sxs-lookup"><span data-stu-id="cf89a-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="cf89a-618">不過，自訂屬性不可屬於任何 XML 命名空間，這是保留供 SSDL 使用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="cf89a-619">任兩個自訂屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-620">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-620">Example</span></span>

<span data-ttu-id="cf89a-621">下列範例會使用傳回資料列集合的**函數**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-621">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="cf89a-622">RowType 元素（SSDL）</span><span class="sxs-lookup"><span data-stu-id="cf89a-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="cf89a-623">存放結構定義語言（SSDL）中的**RowType**專案會將未命名的結構定義為存放區中定義之函式的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="cf89a-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="cf89a-624">**RowType**元素是**CollectionType**元素的子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="cf89a-625">**RowType**元素可以有下列子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="cf89a-626">屬性（一或多個）</span><span class="sxs-lookup"><span data-stu-id="cf89a-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="cf89a-627">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-627">Example</span></span>

<span data-ttu-id="cf89a-628">下列範例顯示使用**CollectionType**元素的存放區函式，以指定函式傳回資料列集合（如**RowType**元素中所指定）。</span><span class="sxs-lookup"><span data-stu-id="cf89a-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="cf89a-629">Schema 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="cf89a-630">存放結構定義語言（SSDL）中的**架構**元素是儲存模型定義的根項目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="cf89a-631">其中包含組成儲存模型的物件、函式和容器等定義。</span><span class="sxs-lookup"><span data-stu-id="cf89a-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="cf89a-632">**Schema**元素可以包含零個或多個下列子項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="cf89a-633">關聯</span><span class="sxs-lookup"><span data-stu-id="cf89a-633">Association</span></span>
-   <span data-ttu-id="cf89a-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="cf89a-634">EntityType</span></span>
-   <span data-ttu-id="cf89a-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="cf89a-635">EntityContainer</span></span>
-   <span data-ttu-id="cf89a-636">函數</span><span class="sxs-lookup"><span data-stu-id="cf89a-636">Function</span></span>

<span data-ttu-id="cf89a-637">**Schema**元素會使用**namespace**屬性來定義儲存模型中實體類型和關聯物件的命名空間。</span><span class="sxs-lookup"><span data-stu-id="cf89a-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="cf89a-638">在命名空間中，兩個物件不能有相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="cf89a-639">儲存模型命名空間與**架構**元素的 XML 命名空間不同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="cf89a-640">儲存模型命名空間（如**命名空間**屬性所定義）是實體類型和關聯類型的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="cf89a-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="cf89a-641">**Schema**元素的 XML 命名空間（由**xmlns**屬性所表示）是**schema**專案之子專案和屬性的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="cf89a-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="cf89a-642">XML 命名空間的表單 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl （其中 YYYY 和 MM 表示年和月分別） 是保留供 SSDL。</span><span class="sxs-lookup"><span data-stu-id="cf89a-642">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="cf89a-643">自訂項目和屬性不能出現在擁有此格式的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="cf89a-644">適用屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-644">Applicable Attributes</span></span>

<span data-ttu-id="cf89a-645">下表描述可套用至**架構**元素的屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="cf89a-646">屬性名稱</span><span class="sxs-lookup"><span data-stu-id="cf89a-646">Attribute Name</span></span>            | <span data-ttu-id="cf89a-647">必要</span><span class="sxs-lookup"><span data-stu-id="cf89a-647">Is Required</span></span> | <span data-ttu-id="cf89a-648">值</span><span class="sxs-lookup"><span data-stu-id="cf89a-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-649">**命名空間**</span><span class="sxs-lookup"><span data-stu-id="cf89a-649">**Namespace**</span></span>             | <span data-ttu-id="cf89a-650">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-650">Yes</span></span>         | <span data-ttu-id="cf89a-651">儲存模型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="cf89a-651">The namespace of the storage model.</span></span> <span data-ttu-id="cf89a-652">**Namespace**屬性的值是用來形成類型的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="cf89a-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="cf89a-653">例如，如果名為*Customer*的**EntityType**是在 examplemodel.store.customer 命名空間中，則**entitytype**的完整限定名稱為 examplemodel.store.customer。</span><span class="sxs-lookup"><span data-stu-id="cf89a-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="cf89a-654">下列字串不能當做**Namespace**屬性的值使用：**系統**、**暫時性**或**Edm**。</span><span class="sxs-lookup"><span data-stu-id="cf89a-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="cf89a-655">**Namespace**屬性的值不能與 CSDL Schema 元素中**namespace**屬性的值相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="cf89a-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="cf89a-656">**Alias**</span></span>                 | <span data-ttu-id="cf89a-657">否</span><span class="sxs-lookup"><span data-stu-id="cf89a-657">No</span></span>          | <span data-ttu-id="cf89a-658">用來取代命名空間名稱的識別項。</span><span class="sxs-lookup"><span data-stu-id="cf89a-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="cf89a-659">例如，如果名為*Customer*的**EntityType**是在 Examplemodel.store.customer 中，而且**Alias**屬性的值是*StorageModel*，則您可以使用 StorageModel 做為的完整名稱 **。EntityType。**</span><span class="sxs-lookup"><span data-stu-id="cf89a-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="cf89a-660">**提供者**</span><span class="sxs-lookup"><span data-stu-id="cf89a-660">**Provider**</span></span>              | <span data-ttu-id="cf89a-661">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-661">Yes</span></span>         | <span data-ttu-id="cf89a-662">資料提供者。</span><span class="sxs-lookup"><span data-stu-id="cf89a-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="cf89a-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="cf89a-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="cf89a-664">是</span><span class="sxs-lookup"><span data-stu-id="cf89a-664">Yes</span></span>         | <span data-ttu-id="cf89a-665">向提供者表示要傳回哪個提供者資訊清單的語彙基元。</span><span class="sxs-lookup"><span data-stu-id="cf89a-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="cf89a-666">未定義此語彙基元的格式。</span><span class="sxs-lookup"><span data-stu-id="cf89a-666">No format for the token is defined.</span></span> <span data-ttu-id="cf89a-667">語彙基元的值是由提供者定義。</span><span class="sxs-lookup"><span data-stu-id="cf89a-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="cf89a-668">如需有關 SQL Server 提供者資訊清單標記的詳細資訊，請參閱 SqlClient for Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="cf89a-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="cf89a-669">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-669">Example</span></span>

<span data-ttu-id="cf89a-670">下列範例顯示包含**EntityContainer**元素、兩個**EntityType**專案和一個**Association**元素的**Schema**專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="cf89a-671">註釋屬性</span><span class="sxs-lookup"><span data-stu-id="cf89a-671">Annotation Attributes</span></span>

<span data-ttu-id="cf89a-672">存放結構定義語言 (SSDL) 中的 Annotation 屬性是儲存體模型中的自訂 XML 屬性，它們可提供與儲存體模型中之項目相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="cf89a-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="cf89a-673">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 屬性：</span><span class="sxs-lookup"><span data-stu-id="cf89a-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="cf89a-674">Annotation 屬性不能在任何 XML 命名空間之中，這是保留供 SSDL 之用。</span><span class="sxs-lookup"><span data-stu-id="cf89a-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="cf89a-675">任兩個 Annotation 屬性的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="cf89a-676">可以將一個以上的 Annotation 屬性套用至指定的 SSDL 項目。</span><span class="sxs-lookup"><span data-stu-id="cf89a-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="cf89a-677">注釋專案中包含的中繼資料可在執行時間使用 system.string. 中繼資料命名空間中的類別來存取。</span><span class="sxs-lookup"><span data-stu-id="cf89a-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-678">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-678">Example</span></span>

<span data-ttu-id="cf89a-679">下列範例顯示的 EntityType 專案，具有套用至 [**訂單**] 屬性的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="cf89a-680">此範例也會顯示加入至**EntityType**元素的 annotation 專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-680">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="cf89a-681">Annotation 項目 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="cf89a-682">存放結構定義語言 (SSDL) 中的 Annotation 項目是儲存體模型中的自訂 XML 項目，它們可提供與儲存體模型相關的額外中繼資料。</span><span class="sxs-lookup"><span data-stu-id="cf89a-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="cf89a-683">除了擁有有效的 XML 結構外，下列條件約束適用於 Annotation 項目：</span><span class="sxs-lookup"><span data-stu-id="cf89a-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="cf89a-684">Annotation 項目不能存在於保留供 SSDL 使用的任何 XML 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="cf89a-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="cf89a-685">任兩個 Annotation 項目的完整名稱不能相同。</span><span class="sxs-lookup"><span data-stu-id="cf89a-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="cf89a-686">Annotation 項目必須出現在所指定 SSDL 項目的所有其他子項目之後。</span><span class="sxs-lookup"><span data-stu-id="cf89a-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="cf89a-687">多個 Annotation 項目可以同時做為所指定 SSDL 項目的子系。</span><span class="sxs-lookup"><span data-stu-id="cf89a-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="cf89a-688">從 .NET Framework 第4版開始，您可以在執行時間使用 system.servicemodel 命名空間中的類別來存取 annotation 元素中包含的中繼資料。</span><span class="sxs-lookup"><span data-stu-id="cf89a-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="cf89a-689">範例</span><span class="sxs-lookup"><span data-stu-id="cf89a-689">Example</span></span>

<span data-ttu-id="cf89a-690">下列範例顯示具有 annotation 元素（**CustomElement**）的 EntityType 專案。</span><span class="sxs-lookup"><span data-stu-id="cf89a-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="cf89a-691">此範例也會顯示套用至 [**訂單**] 屬性的 annotation 屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="cf89a-692">Facet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="cf89a-692">Facets (SSDL)</span></span>

<span data-ttu-id="cf89a-693">存放結構定義語言（SSDL）中的 facet 代表在屬性專案中指定之資料行類型的條件約束。</span><span class="sxs-lookup"><span data-stu-id="cf89a-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="cf89a-694">Facet 會實作為**屬性**元素上的 XML 屬性。</span><span class="sxs-lookup"><span data-stu-id="cf89a-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="cf89a-695">下表說明 SSDL 中支援的 Facet：</span><span class="sxs-lookup"><span data-stu-id="cf89a-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="cf89a-696">Facet</span><span class="sxs-lookup"><span data-stu-id="cf89a-696">Facet</span></span>           | <span data-ttu-id="cf89a-697">描述</span><span class="sxs-lookup"><span data-stu-id="cf89a-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cf89a-698">**定序**</span><span class="sxs-lookup"><span data-stu-id="cf89a-698">**Collation**</span></span>   | <span data-ttu-id="cf89a-699">在執行比較和排序屬性值的作業時，指定要使用的定序順序 (或排序順序)。</span><span class="sxs-lookup"><span data-stu-id="cf89a-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="cf89a-700">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="cf89a-700">**FixedLength**</span></span> | <span data-ttu-id="cf89a-701">指定資料行值的長度是否可以變更。</span><span class="sxs-lookup"><span data-stu-id="cf89a-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="cf89a-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="cf89a-702">**MaxLength**</span></span>   | <span data-ttu-id="cf89a-703">指定資料行值的最大長度。</span><span class="sxs-lookup"><span data-stu-id="cf89a-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="cf89a-704">**整數位數**</span><span class="sxs-lookup"><span data-stu-id="cf89a-704">**Precision**</span></span>   | <span data-ttu-id="cf89a-705">針對**Decimal**類型的屬性，指定屬性值可以擁有的位數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="cf89a-706">針對**Time**、 **DateTime**和**DateTimeOffset**類型的屬性，指定資料行值的秒數小數部分的位數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="cf89a-707">**縮放**</span><span class="sxs-lookup"><span data-stu-id="cf89a-707">**Scale**</span></span>       | <span data-ttu-id="cf89a-708">指定資料行值小數點右邊的位數。</span><span class="sxs-lookup"><span data-stu-id="cf89a-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="cf89a-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="cf89a-709">**Unicode**</span></span>     | <span data-ttu-id="cf89a-710">指定資料行值是否儲存為 Unicode。</span><span class="sxs-lookup"><span data-stu-id="cf89a-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
