---
title: Entity Framework 詞彙 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
ms.openlocfilehash: 9ed7a2d841c625de35de57edb4e57e69b89a3db9
ms.sourcegitcommit: 5d74ac575f813110db6d870720f50dd7606446bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2018
ms.locfileid: "48881263"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="0a8be-102">Entity Framework 詞彙</span><span class="sxs-lookup"><span data-stu-id="0a8be-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="0a8be-103">Code First</span><span class="sxs-lookup"><span data-stu-id="0a8be-103">Code First</span></span>
<span data-ttu-id="0a8be-104">建立 Entity Framework 模型使用的程式碼。</span><span class="sxs-lookup"><span data-stu-id="0a8be-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="0a8be-105">目標模型可以和現有的資料庫或新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0a8be-105">The model can target and existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="0a8be-106">內容</span><span class="sxs-lookup"><span data-stu-id="0a8be-106">Context</span></span>
<span data-ttu-id="0a8be-107">類別，表示與資料庫，可讓您查詢和儲存資料的工作階段。</span><span class="sxs-lookup"><span data-stu-id="0a8be-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="0a8be-108">內容是衍生自 DbContext 或 ObjectContext 的類別。</span><span class="sxs-lookup"><span data-stu-id="0a8be-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="0a8be-109">慣例 (Code First)</span><span class="sxs-lookup"><span data-stu-id="0a8be-109">Convention (Code First)</span></span>
<span data-ttu-id="0a8be-110">Entity Framework 使用推斷的模型從您的類別圖形的規則。</span><span class="sxs-lookup"><span data-stu-id="0a8be-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="0a8be-111">第一次資料庫</span><span class="sxs-lookup"><span data-stu-id="0a8be-111">Database First</span></span>
<span data-ttu-id="0a8be-112">建立使用 EF 設計工具中，Entity Framework 模型為目標的現有資料庫。</span><span class="sxs-lookup"><span data-stu-id="0a8be-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="0a8be-113">積極式載入</span><span class="sxs-lookup"><span data-stu-id="0a8be-113">Eager loading</span></span>
<span data-ttu-id="0a8be-114">載入模式，其中某一類型實體的查詢也會載入相關的實體做為查詢一部分的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="0a8be-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="0a8be-115">EF Designer</span><span class="sxs-lookup"><span data-stu-id="0a8be-115">EF Designer</span></span>
<span data-ttu-id="0a8be-116">在 Visual Studio 中，可讓您建立使用方塊和線的 Entity Framework 模型的視覺化設計工具。</span><span class="sxs-lookup"><span data-stu-id="0a8be-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="0a8be-117">實體</span><span class="sxs-lookup"><span data-stu-id="0a8be-117">Entity</span></span>
<span data-ttu-id="0a8be-118">代表像是客戶、產品和訂單這類應用程式資料的類別或物件。</span><span class="sxs-lookup"><span data-stu-id="0a8be-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="0a8be-119">實體資料模型</span><span class="sxs-lookup"><span data-stu-id="0a8be-119">Entity Data Model</span></span>
<span data-ttu-id="0a8be-120">描述實體與其間的關聯性模型。</span><span class="sxs-lookup"><span data-stu-id="0a8be-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="0a8be-121">EF 使用 EDM 來描述概念模型，針對開發人員計劃。</span><span class="sxs-lookup"><span data-stu-id="0a8be-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="0a8be-122">EDM 建置在 Dr 所導入的實體關聯性模型之上。Peter Chen。</span><span class="sxs-lookup"><span data-stu-id="0a8be-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="0a8be-123">原本是 EDM 所開發的一系列來自 Microsoft 的開發人員和伺服器技術，成為 common data model 的主要目標。</span><span class="sxs-lookup"><span data-stu-id="0a8be-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="0a8be-124">EDM 也會做為 OData 通訊協定的一部分。</span><span class="sxs-lookup"><span data-stu-id="0a8be-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="0a8be-125">明確式載入</span><span class="sxs-lookup"><span data-stu-id="0a8be-125">Explicit loading</span></span>
<span data-ttu-id="0a8be-126">載入模式，藉由呼叫 API 載入相關的物件的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="0a8be-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="0a8be-127">Fluent API</span><span class="sxs-lookup"><span data-stu-id="0a8be-127">Fluent API</span></span>
<span data-ttu-id="0a8be-128">API 可用來設定 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="0a8be-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="0a8be-129">外部索引鍵的關聯</span><span class="sxs-lookup"><span data-stu-id="0a8be-129">Foreign key association</span></span>
<span data-ttu-id="0a8be-130">代表外部索引鍵的屬性，其中包含相依的實體類別中的實體之間的關聯。</span><span class="sxs-lookup"><span data-stu-id="0a8be-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="0a8be-131">例如，產品包含 CategoryId 屬性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="0a8be-132">識別關聯性</span><span class="sxs-lookup"><span data-stu-id="0a8be-132">Identifying relationship</span></span>
<span data-ttu-id="0a8be-133">一種關聯性，其中主要實體的主索引鍵也是相依實體之主索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="0a8be-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="0a8be-134">在這種關聯性中，如果沒有主要實體，相依實體就無法存在。</span><span class="sxs-lookup"><span data-stu-id="0a8be-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="0a8be-135">獨立關聯</span><span class="sxs-lookup"><span data-stu-id="0a8be-135">Independent association</span></span>
<span data-ttu-id="0a8be-136">實體之間的關聯沒有任何表示外部索引鍵為相依實體類別中的屬性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="0a8be-137">例如，Product 類別包含類別，但沒有 CategoryId 屬性關聯性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="0a8be-138">Entity Framework 會追蹤與兩個關聯兩端之實體的狀態分開關聯的狀態。</span><span class="sxs-lookup"><span data-stu-id="0a8be-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="0a8be-139">消極式載入</span><span class="sxs-lookup"><span data-stu-id="0a8be-139">Lazy loading</span></span>
<span data-ttu-id="0a8be-140">載入模式，其中相關的物件時，自動載入在存取導覽屬性的相關的資料。</span><span class="sxs-lookup"><span data-stu-id="0a8be-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="0a8be-141">第一次建立模型</span><span class="sxs-lookup"><span data-stu-id="0a8be-141">Model First</span></span>
<span data-ttu-id="0a8be-142">建立 Entity Framework 模型，使用 EF 設計工具中，然後用來建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="0a8be-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="0a8be-143">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="0a8be-143">Navigation property</span></span>
<span data-ttu-id="0a8be-144">參考另一個實體的實體屬性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="0a8be-145">例如，產品包含的類別目錄 」 瀏覽屬性，而類別包含的產品導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="0a8be-146">POCO</span><span class="sxs-lookup"><span data-stu-id="0a8be-146">POCO</span></span>
<span data-ttu-id="0a8be-147">純舊 CLR 物件的首字母縮寫。</span><span class="sxs-lookup"><span data-stu-id="0a8be-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="0a8be-148">簡單的使用者具有的類別，沒有與任何架構的相依性。</span><span class="sxs-lookup"><span data-stu-id="0a8be-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="0a8be-149">在 EF，不是衍生自 EntityObject、 會實作任何介面或攜帶在 EF 中定義任何屬性的實體類別的內容。</span><span class="sxs-lookup"><span data-stu-id="0a8be-149">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="0a8be-150">這種分離持續性架構的實體類別也會稱為 「 「 持續 」。</span><span class="sxs-lookup"><span data-stu-id="0a8be-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="0a8be-151">關聯性反向</span><span class="sxs-lookup"><span data-stu-id="0a8be-151">Relationship inverse</span></span>
<span data-ttu-id="0a8be-152">相反的另一端的關聯性，例如，產品。分類和分類。產品。</span><span class="sxs-lookup"><span data-stu-id="0a8be-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="0a8be-153">自我追蹤實體</span><span class="sxs-lookup"><span data-stu-id="0a8be-153">Self-tracking entity</span></span>
<span data-ttu-id="0a8be-154">從程式碼產生範本，可協助進行多層式架構開發建置實體。</span><span class="sxs-lookup"><span data-stu-id="0a8be-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="0a8be-155">資料表每個具象類型 (TPC)</span><span class="sxs-lookup"><span data-stu-id="0a8be-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="0a8be-156">對應繼承階層中的每個非抽象類型對應至個別資料庫中的資料表的其中一種方法。</span><span class="sxs-lookup"><span data-stu-id="0a8be-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="0a8be-157">每個階層的資料表 (TPH)</span><span class="sxs-lookup"><span data-stu-id="0a8be-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="0a8be-158">對應其中在階層中的所有型別會對應到資料庫中相同的資料表繼承的方法。</span><span class="sxs-lookup"><span data-stu-id="0a8be-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="0a8be-159">鑑別子資料行用來識別哪種類型的每個資料列相關聯。</span><span class="sxs-lookup"><span data-stu-id="0a8be-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="0a8be-160">每一類一表 (TPT)</span><span class="sxs-lookup"><span data-stu-id="0a8be-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="0a8be-161">對應的繼承階層中的所有類型的通用屬性會對應至在資料庫中，相同的資料表，但每個類型特有的屬性會對應至個別資料表的方法。</span><span class="sxs-lookup"><span data-stu-id="0a8be-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="0a8be-162">類型探索</span><span class="sxs-lookup"><span data-stu-id="0a8be-162">Type discovery</span></span>
<span data-ttu-id="0a8be-163">識別應該是 Entity Framework 模型的一部分的型別程序。</span><span class="sxs-lookup"><span data-stu-id="0a8be-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>
