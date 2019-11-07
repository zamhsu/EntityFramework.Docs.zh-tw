---
title: Entity Framework 詞彙-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: df0da4a68b3d2c882d9673417ee5fe335eccae2b
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656150"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="f3b04-102">Entity Framework 詞彙</span><span class="sxs-lookup"><span data-stu-id="f3b04-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="f3b04-103">Code First</span><span class="sxs-lookup"><span data-stu-id="f3b04-103">Code First</span></span>
<span data-ttu-id="f3b04-104">使用程式碼建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="f3b04-105">模型可以目標為現有的資料庫或新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3b04-105">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="f3b04-106">內容</span><span class="sxs-lookup"><span data-stu-id="f3b04-106">Context</span></span>
<span data-ttu-id="f3b04-107">代表與資料庫之會話的類別，可讓您查詢和儲存資料。</span><span class="sxs-lookup"><span data-stu-id="f3b04-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="f3b04-108">內容衍生自 DbCoNtext 或 ObjectCoNtext 類別。</span><span class="sxs-lookup"><span data-stu-id="f3b04-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="f3b04-109">慣例（Code First）</span><span class="sxs-lookup"><span data-stu-id="f3b04-109">Convention (Code First)</span></span>
<span data-ttu-id="f3b04-110">一種規則，Entity Framework 用來從您的類別中推斷您模型的形狀。</span><span class="sxs-lookup"><span data-stu-id="f3b04-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="f3b04-111">Database First</span><span class="sxs-lookup"><span data-stu-id="f3b04-111">Database First</span></span>
<span data-ttu-id="f3b04-112">使用 EF 設計工具建立以現有資料庫為目標的 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="f3b04-113">積極式載入</span><span class="sxs-lookup"><span data-stu-id="f3b04-113">Eager loading</span></span>
<span data-ttu-id="f3b04-114">載入相關資料的模式，其中一種實體類型的查詢也會在查詢過程中載入相關的實體。</span><span class="sxs-lookup"><span data-stu-id="f3b04-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="f3b04-115">EF Designer</span><span class="sxs-lookup"><span data-stu-id="f3b04-115">EF Designer</span></span>
<span data-ttu-id="f3b04-116">Visual Studio 中的視覺化設計工具，可讓您使用方塊和線條建立 Entity Framework 模型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="f3b04-117">實體</span><span class="sxs-lookup"><span data-stu-id="f3b04-117">Entity</span></span>
<span data-ttu-id="f3b04-118">代表像是客戶、產品和訂單這類應用程式資料的類別或物件。</span><span class="sxs-lookup"><span data-stu-id="f3b04-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="f3b04-119">實體資料模型</span><span class="sxs-lookup"><span data-stu-id="f3b04-119">Entity Data Model</span></span>
<span data-ttu-id="f3b04-120">描述實體的模型，以及它們之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="f3b04-121">EF 使用 EDM 來描述開發人員程式所針對的概念模型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="f3b04-122">EDM 是以 Chen 所引進的實體關聯性模型為基礎。</span><span class="sxs-lookup"><span data-stu-id="f3b04-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="f3b04-123">EDM 原本的開發主要目標，是要成為 Microsoft 開發人員和伺服器技術的一套通用資料模型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="f3b04-124">EDM 也會當做 OData 通訊協定的一部分來使用。</span><span class="sxs-lookup"><span data-stu-id="f3b04-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="f3b04-125">明確式載入</span><span class="sxs-lookup"><span data-stu-id="f3b04-125">Explicit loading</span></span>
<span data-ttu-id="f3b04-126">載入相關資料的模式，其中會呼叫 API 來載入相關的物件。</span><span class="sxs-lookup"><span data-stu-id="f3b04-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f3b04-127">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f3b04-127">Fluent API</span></span>
<span data-ttu-id="f3b04-128">可以用來設定 Code First 模型的 API。</span><span class="sxs-lookup"><span data-stu-id="f3b04-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="f3b04-129">外鍵關聯</span><span class="sxs-lookup"><span data-stu-id="f3b04-129">Foreign key association</span></span>
<span data-ttu-id="f3b04-130">實體之間的關聯，其中代表外鍵的屬性會包含在相依實體的類別中。</span><span class="sxs-lookup"><span data-stu-id="f3b04-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="f3b04-131">例如，Product 包含 [類別] 屬性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="f3b04-132">識別關聯性</span><span class="sxs-lookup"><span data-stu-id="f3b04-132">Identifying relationship</span></span>
<span data-ttu-id="f3b04-133">一種關聯性，其中主要實體的主索引鍵也是相依實體之主索引鍵的一部分。</span><span class="sxs-lookup"><span data-stu-id="f3b04-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="f3b04-134">在這種關聯性中，如果沒有主要實體，相依實體就無法存在。</span><span class="sxs-lookup"><span data-stu-id="f3b04-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="f3b04-135">獨立關聯</span><span class="sxs-lookup"><span data-stu-id="f3b04-135">Independent association</span></span>
<span data-ttu-id="f3b04-136">實體之間的關聯，其中沒有屬性代表相依實體之類別中的外鍵。</span><span class="sxs-lookup"><span data-stu-id="f3b04-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="f3b04-137">例如，Product 類別包含與 Category 的關聯性，但沒有任何類別目錄屬性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="f3b04-138">Entity Framework 會在兩個關聯端的實體狀態以外，追蹤關聯的狀態。</span><span class="sxs-lookup"><span data-stu-id="f3b04-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="f3b04-139">消極式載入</span><span class="sxs-lookup"><span data-stu-id="f3b04-139">Lazy loading</span></span>
<span data-ttu-id="f3b04-140">當存取導覽屬性時，會自動載入相關物件的載入相關資料的模式。</span><span class="sxs-lookup"><span data-stu-id="f3b04-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="f3b04-141">Model First</span><span class="sxs-lookup"><span data-stu-id="f3b04-141">Model First</span></span>
<span data-ttu-id="f3b04-142">使用 EF 設計工具建立 Entity Framework 模型，然後用它來建立新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="f3b04-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="f3b04-143">導覽屬性</span><span class="sxs-lookup"><span data-stu-id="f3b04-143">Navigation property</span></span>
<span data-ttu-id="f3b04-144">參考另一個實體之實體的屬性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="f3b04-145">例如，Product 包含 Category 導覽屬性，而 Category 包含 Products 導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="f3b04-146">POCO</span><span class="sxs-lookup"><span data-stu-id="f3b04-146">POCO</span></span>
<span data-ttu-id="f3b04-147">一般舊 CLR 物件的縮寫。</span><span class="sxs-lookup"><span data-stu-id="f3b04-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="f3b04-148">沒有任何架構相依性的簡單使用者類別。</span><span class="sxs-lookup"><span data-stu-id="f3b04-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="f3b04-149">在 EF 的內容中，不是從 EntityObject 衍生的實體類別會執行任何介面，或攜帶 EF 中定義的任何屬性。</span><span class="sxs-lookup"><span data-stu-id="f3b04-149">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="f3b04-150">與持續性架構分離的這類實體類別也稱為「持續性未知」。</span><span class="sxs-lookup"><span data-stu-id="f3b04-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="f3b04-151">反向關聯性</span><span class="sxs-lookup"><span data-stu-id="f3b04-151">Relationship inverse</span></span>
<span data-ttu-id="f3b04-152">關聯性的相反結束，例如 product。類別和類別。基礎.</span><span class="sxs-lookup"><span data-stu-id="f3b04-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="f3b04-153">自我追蹤實體</span><span class="sxs-lookup"><span data-stu-id="f3b04-153">Self-tracking entity</span></span>
<span data-ttu-id="f3b04-154">由程式碼產生範本所建立的實體，可協助進行多層式開發。</span><span class="sxs-lookup"><span data-stu-id="f3b04-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="f3b04-155">每個具體的資料表類型（TPC）</span><span class="sxs-lookup"><span data-stu-id="f3b04-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="f3b04-156">對應繼承的方法，其中階層中的每個非抽象類別型都會對應到資料庫中的個別資料表。</span><span class="sxs-lookup"><span data-stu-id="f3b04-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="f3b04-157">每個階層的資料表（TPH）</span><span class="sxs-lookup"><span data-stu-id="f3b04-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="f3b04-158">對應繼承的方法，其中階層中的所有類型都會對應到資料庫中的相同資料表。</span><span class="sxs-lookup"><span data-stu-id="f3b04-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="f3b04-159">鑒別子資料行是用來識別與每個資料列相關聯的類型。</span><span class="sxs-lookup"><span data-stu-id="f3b04-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="f3b04-160">每一類型的資料表（TPT）</span><span class="sxs-lookup"><span data-stu-id="f3b04-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="f3b04-161">對應繼承的方法，其中階層中所有類型的通用屬性會對應至資料庫中的相同資料表，但每個類型特有的屬性會對應至個別的資料表。</span><span class="sxs-lookup"><span data-stu-id="f3b04-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="f3b04-162">類型探索</span><span class="sxs-lookup"><span data-stu-id="f3b04-162">Type discovery</span></span>
<span data-ttu-id="f3b04-163">識別應該是 Entity Framework 模型之一部分之類型的程式。</span><span class="sxs-lookup"><span data-stu-id="f3b04-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>
