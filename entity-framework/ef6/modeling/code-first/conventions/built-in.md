---
title: Code First 慣例-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
ms.openlocfilehash: 4d03a32db5d84eb37c22617a95005b272172a65d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78419287"
---
# <a name="code-first-conventions"></a><span data-ttu-id="c5149-102">Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-102">Code First Conventions</span></span>
<span data-ttu-id="c5149-103">Code First 可讓您使用C#或 Visual Basic .net 類別來描述模型。</span><span class="sxs-lookup"><span data-stu-id="c5149-103">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="c5149-104">使用慣例偵測模型的基本形狀。</span><span class="sxs-lookup"><span data-stu-id="c5149-104">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="c5149-105">慣例是一組規則，可在使用 Code First 時，用來根據類別定義自動設定概念模型。</span><span class="sxs-lookup"><span data-stu-id="c5149-105">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="c5149-106">慣例會定義在 ModelConfiguration 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="c5149-106">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="c5149-107">您可以使用資料批註或 Fluent API 進一步設定您的模型。</span><span class="sxs-lookup"><span data-stu-id="c5149-107">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="c5149-108">優先順序是透過 Fluent API 後面接著資料批註，然後是慣例來設定。</span><span class="sxs-lookup"><span data-stu-id="c5149-108">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="c5149-109">如需詳細資訊，請參閱[資料批註](~/ef6/modeling/code-first/data-annotations.md)、[流暢的 api 關聯](~/ef6/modeling/code-first/fluent/relationships.md)性、[流暢的 api 類型 & 屬性](~/ef6/modeling/code-first/fluent/types-and-properties.md)和[使用 VB.NET 的流暢 api](~/ef6/modeling/code-first/fluent/vb.md)。</span><span class="sxs-lookup"><span data-stu-id="c5149-109">For more information see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md), [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md), [Fluent API - Types & Properties](~/ef6/modeling/code-first/fluent/types-and-properties.md) and [Fluent API with VB.NET](~/ef6/modeling/code-first/fluent/vb.md).</span></span>  

<span data-ttu-id="c5149-110">如需 Code First 慣例的詳細清單，請[參閱 API 檔](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c5149-110">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="c5149-111">本主題提供 Code First 所使用之慣例的總覽。</span><span class="sxs-lookup"><span data-stu-id="c5149-111">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="c5149-112">類型探索</span><span class="sxs-lookup"><span data-stu-id="c5149-112">Type Discovery</span></span>  

<span data-ttu-id="c5149-113">使用 Code First 開發時，您通常會從撰寫定義概念（網域）模型的 .NET Framework 類別開始著手。</span><span class="sxs-lookup"><span data-stu-id="c5149-113">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="c5149-114">除了定義類別以外，您還需要讓**DbCoNtext**知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="c5149-114">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="c5149-115">若要這樣做，您可以定義衍生自**DbCoNtext**的內容類別，並針對您想要成為模型一部分的類型公開**DbSet**屬性。</span><span class="sxs-lookup"><span data-stu-id="c5149-115">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="c5149-116">Code First 將包含這些類型，而且也會提取任何參考的類型，即使參考的類型是在不同的元件中定義也一樣。</span><span class="sxs-lookup"><span data-stu-id="c5149-116">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="c5149-117">如果您的型別參與繼承階層架構，就足以定義基類的**DbSet**屬性，而且如果衍生的型別與基類位於相同的元件中，就會自動包含該類別。</span><span class="sxs-lookup"><span data-stu-id="c5149-117">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="c5149-118">在下列範例中， **SchoolEntities**類別（**部門**）上只定義了一個**DbSet**屬性。</span><span class="sxs-lookup"><span data-stu-id="c5149-118">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="c5149-119">Code First 使用這個屬性來探索和提取任何參考的類型。</span><span class="sxs-lookup"><span data-stu-id="c5149-119">Code First uses this property to discover and pull in any referenced types.</span></span>  

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

<span data-ttu-id="c5149-120">如果您想要從模型中排除型別，請使用**NotMapped**屬性或**DbModelBuilder。請略**過 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="c5149-120">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="c5149-121">主要金鑰慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-121">Primary Key Convention</span></span>  

<span data-ttu-id="c5149-122">Code First 推斷，如果類別上的屬性名稱為 "ID" （不區分大小寫），或類別名稱後面接著 "ID"，則屬性是主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="c5149-122">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="c5149-123">如果 primary key 屬性的類型是數值或 GUID，則會將它設定為識別欄位。</span><span class="sxs-lookup"><span data-stu-id="c5149-123">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="c5149-124">關聯性慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-124">Relationship Convention</span></span>  

<span data-ttu-id="c5149-125">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5149-125">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="c5149-126">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="c5149-126">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="c5149-127">導覽屬性可讓您流覽和管理雙向的關聯性，傳回參考物件（如果多重性為一個或零或一個）或集合（如果多重性為多個）。</span><span class="sxs-lookup"><span data-stu-id="c5149-127">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="c5149-128">Code First 會根據您類型上定義的導覽屬性來推斷關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5149-128">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="c5149-129">除了導覽屬性以外，我們也建議您在代表相依物件的類型上包含外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="c5149-129">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="c5149-130">任何資料類型與主體主鍵屬性相同的屬性，以及遵循下列其中一種格式的名稱，都代表關聯性的外鍵： '\<導覽屬性名稱\>\<主體主鍵屬性名稱\>'、'\<主體類別名稱\>\<主鍵屬性名稱\>' 或 '\<主要主鍵屬性名稱\>'。</span><span class="sxs-lookup"><span data-stu-id="c5149-130">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="c5149-131">如果找到多個相符專案，則會依上述順序提供優先順序。</span><span class="sxs-lookup"><span data-stu-id="c5149-131">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="c5149-132">外鍵偵測不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="c5149-132">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="c5149-133">當偵測到外鍵屬性時，Code First 會根據外鍵的 null 屬性來推斷關聯性的多重性。</span><span class="sxs-lookup"><span data-stu-id="c5149-133">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="c5149-134">如果屬性可為 null，則會將關聯性註冊為選擇性;否則，會視需要註冊關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5149-134">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="c5149-135">如果相依實體的外鍵不可為 null，則 Code First 在關聯性上設定 cascade delete。</span><span class="sxs-lookup"><span data-stu-id="c5149-135">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="c5149-136">如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當主體被刪除時，外鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="c5149-136">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="c5149-137">依照慣例偵測到的多重性和 cascade 刪除行為，可以使用 Fluent API 加以覆寫。</span><span class="sxs-lookup"><span data-stu-id="c5149-137">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="c5149-138">在下列範例中，會使用導覽屬性和外鍵來定義部門和課程類別之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5149-138">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

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
> <span data-ttu-id="c5149-139">如果您有相同類型之間的多個關聯性（例如，假設您定義**person**和**Book**類別，其中**Person**類別包含**ReviewedBooks**和**AuthoredBooks**導覽屬性，而**Book**類別包含**作者**和**審核者**導覽屬性），則您需要使用資料批註或 Fluent API 手動設定關聯性。</span><span class="sxs-lookup"><span data-stu-id="c5149-139">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="c5149-140">如需詳細資訊，請參閱[資料批註-關聯](~/ef6/modeling/code-first/data-annotations.md)性和[流暢的 API 關聯](~/ef6/modeling/code-first/fluent/relationships.md)性。</span><span class="sxs-lookup"><span data-stu-id="c5149-140">For more information, see [Data Annotations - Relationships](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="c5149-141">複雜類型慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-141">Complex Types Convention</span></span>  

<span data-ttu-id="c5149-142">當 Code First 探索無法推斷主鍵的類別定義，而且沒有透過資料批註或 Fluent API 註冊主鍵時，會自動將該類型註冊為複雜類型。</span><span class="sxs-lookup"><span data-stu-id="c5149-142">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="c5149-143">複雜型別偵測也會要求類型沒有參考實體類型的屬性，而且不會從另一個類型的集合屬性中參考。</span><span class="sxs-lookup"><span data-stu-id="c5149-143">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="c5149-144">假設下列類別定義 Code First 會推斷該**詳細資料**是複雜型別，因為它沒有主鍵。</span><span class="sxs-lookup"><span data-stu-id="c5149-144">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

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

## <a name="connection-string-convention"></a><span data-ttu-id="c5149-145">連接字串慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-145">Connection String Convention</span></span>  

<span data-ttu-id="c5149-146">若要瞭解 DbCoNtext 用來探索連線的慣例，請參閱[連接和模型](~/ef6/fundamentals/configuring/connection-strings.md)。</span><span class="sxs-lookup"><span data-stu-id="c5149-146">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](~/ef6/fundamentals/configuring/connection-strings.md).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="c5149-147">移除慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-147">Removing Conventions</span></span>  

<span data-ttu-id="c5149-148">您可以移除 ModelConfiguration 中所定義的任何慣例，也就是命名空間。</span><span class="sxs-lookup"><span data-stu-id="c5149-148">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="c5149-149">下列範例會移除**PluralizingTableNameConvention**。</span><span class="sxs-lookup"><span data-stu-id="c5149-149">The following example removes **PluralizingTableNameConvention**.</span></span>  

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

## <a name="custom-conventions"></a><span data-ttu-id="c5149-150">自訂慣例</span><span class="sxs-lookup"><span data-stu-id="c5149-150">Custom Conventions</span></span>  

<span data-ttu-id="c5149-151">EF6 開始支援自訂慣例。</span><span class="sxs-lookup"><span data-stu-id="c5149-151">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="c5149-152">如需詳細資訊，請參閱[自訂 Code First 慣例](~/ef6/modeling/code-first/conventions/custom.md)。</span><span class="sxs-lookup"><span data-stu-id="c5149-152">For more information see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>
