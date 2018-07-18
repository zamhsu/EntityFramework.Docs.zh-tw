---
title: 程式碼第一次慣例 EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
caps.latest.revision: 4
ms.openlocfilehash: b124a034ba900780cc4d7e1354408cd3995e874e
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120231"
---
# <a name="code-first-conventions"></a><span data-ttu-id="c5d5b-102">程式碼的第一個慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-102">Code First Conventions</span></span>
<span data-ttu-id="c5d5b-103">Code First 能讓您藉由使用 C# 或 Visual Basic.NET 類別，描述模型。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-103">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="c5d5b-104">模型的基本形狀會偵測到使用的慣例。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-104">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="c5d5b-105">慣例是用來自動設定概念模型時使用 Code First，根據類別定義的規則的集合。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-105">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="c5d5b-106">System.Data.Entity.ModelConfiguration.Conventions 命名空間中定義的慣例。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-106">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="c5d5b-107">您可以使用資料註解或 fluent API，進一步設定您的模型。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-107">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="c5d5b-108">會優先設定透過 fluent API 接著資料註解和慣例。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-108">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="c5d5b-109">如需詳細資訊，請參閱[資料註解](~/ef6/modeling/code-first/data-annotations.md)， [Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)， [Fluent API-型別和屬性](~/ef6/modeling/code-first/fluent/types-and-properties.md)並[VB.NET使用FluentAPI](~/ef6/modeling/code-first/fluent/vb.md).</span><span class="sxs-lookup"><span data-stu-id="c5d5b-109">For more information see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md), [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md), [Fluent API - Types & Properties](~/ef6/modeling/code-first/fluent/types-and-properties.md) and [Fluent API with VB.NET](~/ef6/modeling/code-first/fluent/vb.md).</span></span>  

<span data-ttu-id="c5d5b-110">Code First 慣例的詳細的清單位於[API 文件](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-110">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="c5d5b-111">本主題提供使用 Code First 慣例的概觀。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-111">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="c5d5b-112">類型探索</span><span class="sxs-lookup"><span data-stu-id="c5d5b-112">Type Discovery</span></span>  

<span data-ttu-id="c5d5b-113">使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-113">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="c5d5b-114">除了定義類別，您也需要讓**DbContext**知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-114">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="c5d5b-115">若要這樣做，您定義的內容類別衍生自**DbContext** ，並公開**DbSet**屬性的類型，您想要模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-115">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="c5d5b-116">程式碼會先將包含這些類型，並也會納入任何參考的類型，即使參考的型別在不同的組件中定義。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-116">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="c5d5b-117">如果您的型別參與繼承階層架構，就已足夠定義**DbSet**屬性的基底類別和衍生的類型將會自動包含在內，如果它們位於相同的組件的基底類別。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-117">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="c5d5b-118">在下列範例中，有一個只**DbSet**上定義的屬性**SchoolEntities**類別 (**部門**)。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-118">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="c5d5b-119">首先，程式碼會使用這個屬性來探索及任何參考型別中提取。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-119">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="c5d5b-120">如果您想要從模型中排除的型別，使用**NotMapped**屬性或**DbModelBuilder.Ignore** fluent API。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-120">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="c5d5b-121">主索引鍵的慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-121">Primary Key Convention</span></span>  

<span data-ttu-id="c5d5b-122">程式碼會先推斷屬性類別上的屬性名為 「 識別碼 」 （不區分大小寫），則類別名稱加上"ID"是主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-122">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="c5d5b-123">如果主索引鍵屬性的型別是數值或 GUID 就會設定為識別欄位。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-123">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="c5d5b-124">關聯性慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-124">Relationship Convention</span></span>  

<span data-ttu-id="c5d5b-125">在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-125">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="c5d5b-126">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-126">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="c5d5b-127">導覽屬性可讓您巡覽及管理兩個方向，傳回參考物件的關聯性 (如果多重性是一個或零或一個) 或集合 （如果多重性是許多項目）。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-127">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="c5d5b-128">程式碼會先推斷您的型別上定義的導覽屬性為基礎的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-128">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="c5d5b-129">除了導覽屬性，我們建議您包含類型，可表示相依物件上的外部索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-129">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="c5d5b-130">遵循下列格式之一的名稱作為主體主索引鍵的屬性相同的資料類型與任何屬性表示關聯性的外部索引鍵: '\<導覽屬性名稱\>\<主體主索引鍵屬性名稱\>'，'\<主體的類別名稱\>\<主索引鍵屬性名稱\>'，或 '\<主體的主索引鍵屬性名稱\>'。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-130">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="c5d5b-131">如果找到多個相符項目優先順序可以在上面所列的順序。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-131">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="c5d5b-132">外部索引鍵的偵測不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-132">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="c5d5b-133">偵測到外部索引鍵屬性時，Code First 會推斷為基礎的外部索引鍵的 null 屬性的關聯性的多重性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-133">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="c5d5b-134">如果屬性是可為 null 然後關聯性會註冊為選擇性的。否則會註冊關聯性，視需要而定。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-134">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="c5d5b-135">如果相依的實體上的外部索引鍵不是可為 null，然後第一個程式碼設定串聯刪除關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-135">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="c5d5b-136">如果相依的實體上的外部索引鍵可為 null，第一個程式碼不會設定串聯刪除關聯性，而且當主體已刪除的外部索引鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-136">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="c5d5b-137">多重性與串聯刪除所偵測到的行為使用 fluent API 可以覆寫慣例。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-137">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="c5d5b-138">在下列範例中的導覽屬性和外部索引鍵用來定義科系與課程類別之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-138">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="c5d5b-139">如果您有多個相同型別之間的關聯性 (比方說，假設您定義**Person**並**活頁簿**類別，其中**人員**類別包含**ReviewedBooks**並**AuthoredBooks**導覽屬性和**活頁簿**類別包含**作者**和**檢閱者**導覽屬性) 您要使用資料註解或 fluent API 來手動設定關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-139">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="c5d5b-140">如需詳細資訊，請參閱 <<c0> [ 資料註解-關聯性](~/ef6/modeling/code-first/data-annotations.md)並[Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-140">For more information, see [Data Annotations - Relationships](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="c5d5b-141">複雜型別慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-141">Complex Types Convention</span></span>  

<span data-ttu-id="c5d5b-142">Code First 會探索其中的主索引鍵無法推斷，而沒有主索引鍵透過資料註解或 fluent API 註冊的類別定義，然後類型會自動註冊為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-142">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="c5d5b-143">複雜型別偵測也需要型別沒有參考實體類型，而且不從另一種類型的集合屬性參考的屬性。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-143">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="c5d5b-144">指定下列類別定義 Code First 會推斷，**詳細資料**是複雜類型，因為它有沒有主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-144">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}
```  

## <a name="connection-string-convention"></a><span data-ttu-id="c5d5b-145">連接字串慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-145">Connection String Convention</span></span>  

<span data-ttu-id="c5d5b-146">若要了解慣例來探索要使用，請參閱連線，使用 DbContext[連線和模型](~/ef6/fundamentals/configuring/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-146">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](~/ef6/fundamentals/configuring/connection-strings.md).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="c5d5b-147">移除慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-147">Removing Conventions</span></span>  

<span data-ttu-id="c5d5b-148">您可以移除任何 System.Data.Entity.ModelConfiguration.Conventions 命名空間中定義的慣例。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-148">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="c5d5b-149">下列範例會移除**PluralizingTableNameConvention**。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-149">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="c5d5b-150">自訂慣例</span><span class="sxs-lookup"><span data-stu-id="c5d5b-150">Custom Conventions</span></span>  

<span data-ttu-id="c5d5b-151">自訂慣例及更新版本支援在 EF6 中。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-151">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="c5d5b-152">如需詳細資訊，請參閱[自訂程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/custom.md)。</span><span class="sxs-lookup"><span data-stu-id="c5d5b-152">For more information see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>
