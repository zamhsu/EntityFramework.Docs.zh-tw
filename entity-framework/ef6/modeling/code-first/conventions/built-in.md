---
title: Code First 慣例-EF6
description: Entity Framework 6 中的 Code First 慣例
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: d5b7419258a9e45af5437fae5084a78ebc05f115
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066443"
---
# <a name="code-first-conventions"></a><span data-ttu-id="25caa-103">Code First 慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-103">Code First Conventions</span></span>
<span data-ttu-id="25caa-104">Code First 可讓您使用 c # 或 Visual Basic .NET 類別來描述模型。</span><span class="sxs-lookup"><span data-stu-id="25caa-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="25caa-105">使用慣例偵測模型的基本圖形。</span><span class="sxs-lookup"><span data-stu-id="25caa-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="25caa-106">慣例是一組規則，可用來在使用 Code First 時，根據類別定義自動設定概念模型。</span><span class="sxs-lookup"><span data-stu-id="25caa-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="25caa-107">慣例會定義在 ModelConfiguration 命名空間中。</span><span class="sxs-lookup"><span data-stu-id="25caa-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="25caa-108">您可以使用資料批註或流暢的 API 進一步設定您的模型。</span><span class="sxs-lookup"><span data-stu-id="25caa-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="25caa-109">優先順序是透過流暢的 API 提供，後面接著資料批註和慣例。</span><span class="sxs-lookup"><span data-stu-id="25caa-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="25caa-110">如需詳細資訊，請參閱 [資料批註](xref:ef6/modeling/code-first/data-annotations)、 [流暢的 api 關聯](xref:ef6/modeling/code-first/fluent/relationships)性、 [流暢的 api 類型 & 屬性](xref:ef6/modeling/code-first/fluent/types-and-properties) ，以及 [VB.NET 的流暢 api](xref:ef6/modeling/code-first/fluent/vb)。</span><span class="sxs-lookup"><span data-stu-id="25caa-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="25caa-111">您可以在 [API 檔](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)中取得 Code First 慣例的詳細清單。</span><span class="sxs-lookup"><span data-stu-id="25caa-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="25caa-112">本主題概要說明 Code First 所使用的慣例。</span><span class="sxs-lookup"><span data-stu-id="25caa-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="25caa-113">類型探索</span><span class="sxs-lookup"><span data-stu-id="25caa-113">Type Discovery</span></span>  

<span data-ttu-id="25caa-114">使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。</span><span class="sxs-lookup"><span data-stu-id="25caa-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="25caa-115">除了定義類別之外，您還需要讓 **DbCoNtext** 知道您想要包含在模型中的類型。</span><span class="sxs-lookup"><span data-stu-id="25caa-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="25caa-116">若要這樣做，您可以定義衍生自 **DbCoNtext** 的內容類別，並公開您想要成為模型一部分之類型的 **DbSet** 屬性。</span><span class="sxs-lookup"><span data-stu-id="25caa-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="25caa-117">Code First 將包含這些型別，而且也會提取任何參考的型別，即使參考的型別是在不同的元件中定義也一樣。</span><span class="sxs-lookup"><span data-stu-id="25caa-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="25caa-118">如果您的型別參與繼承階層架構，就足以定義基類的 **DbSet** 屬性，而且如果衍生型別和基類的元件位於相同的元件中，就會自動包含這些類型。</span><span class="sxs-lookup"><span data-stu-id="25caa-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="25caa-119">在下列範例中，只有一個 **DbSet** 屬性定義在 **SchoolEntities** 類別 (**部門**) 。</span><span class="sxs-lookup"><span data-stu-id="25caa-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="25caa-120">Code First 使用這個屬性來探索和提取任何參考的型別。</span><span class="sxs-lookup"><span data-stu-id="25caa-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

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

<span data-ttu-id="25caa-121">如果您要從模型中排除型別，請使用 **NotMapped** 屬性或 **DbModelBuilder。忽略** 流暢的 API。</span><span class="sxs-lookup"><span data-stu-id="25caa-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="25caa-122">Primary Key 慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-122">Primary Key Convention</span></span>  

<span data-ttu-id="25caa-123">Code First 推斷如果類別上的屬性命名為 "ID" (不區分大小寫的) ，或類別名稱後面加上 "ID"，則該屬性是主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="25caa-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="25caa-124">如果主鍵屬性的類型是數值或 GUID，則會將它設定為識別資料行。</span><span class="sxs-lookup"><span data-stu-id="25caa-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="25caa-125">關聯性慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-125">Relationship Convention</span></span>  

<span data-ttu-id="25caa-126">在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="25caa-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="25caa-127">每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="25caa-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="25caa-128">導覽屬性可讓您流覽和管理雙向的關聯性，如果多重性是一個或零或一) ，則傳回參考物件 (; 如果多重性是許多) ，則會傳回集合 (。</span><span class="sxs-lookup"><span data-stu-id="25caa-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="25caa-129">Code First 會根據您類型上定義的導覽屬性來推斷關聯性。</span><span class="sxs-lookup"><span data-stu-id="25caa-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="25caa-130">除了導覽屬性以外，我們也建議您在代表相依物件的類型上加入外鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="25caa-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="25caa-131">任何具有與主體主鍵屬性相同之資料類型的屬性，以及具有下列其中一種格式之名稱的任何屬性，都代表關聯性的外鍵： ' \<navigation property name\> \<principal primary key property name\> '、' \<principal class name\> \<primary key property name\> ' 或 ' \<principal primary key property name\> '。</span><span class="sxs-lookup"><span data-stu-id="25caa-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="25caa-132">如果找到多個相符專案，則會依上列順序提供優先順序。</span><span class="sxs-lookup"><span data-stu-id="25caa-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="25caa-133">外鍵偵測不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="25caa-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="25caa-134">當偵測到外鍵屬性時，Code First 會根據外鍵的可 null 性來推斷關聯性的多重性。</span><span class="sxs-lookup"><span data-stu-id="25caa-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="25caa-135">如果屬性可為 null，則關聯性會註冊為選擇性;否則，就會視需要註冊關聯性。</span><span class="sxs-lookup"><span data-stu-id="25caa-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="25caa-136">如果相依實體上的外鍵不可為 null，則 Code First 會在關聯性上設定串聯刪除。</span><span class="sxs-lookup"><span data-stu-id="25caa-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="25caa-137">如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當刪除主體時，外鍵將會設定為 null。</span><span class="sxs-lookup"><span data-stu-id="25caa-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="25caa-138">您可以使用流暢的 API 來覆寫慣例所偵測到的多重性和串聯刪除行為。</span><span class="sxs-lookup"><span data-stu-id="25caa-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="25caa-139">在下列範例中，會使用導覽屬性和外鍵來定義部門與課程類別之間的關聯性。</span><span class="sxs-lookup"><span data-stu-id="25caa-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

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
> <span data-ttu-id="25caa-140">如果您在相同類型之間有多個關聯性 (例如，假設您定義 **person** 和 **book** 類別，其中 **Person** 類別包含 **ReviewedBooks** 和 **AuthoredBooks** 導覽屬性，而 **Book** 類別包含 **Author** 和 **審核者** 導覽屬性) 您需要使用資料批註或流暢 API 手動設定關聯性。</span><span class="sxs-lookup"><span data-stu-id="25caa-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="25caa-141">如需詳細資訊，請參閱 [資料批註-關聯](xref:ef6/modeling/code-first/data-annotations) 性和 [流暢的 API 關聯](xref:ef6/modeling/code-first/fluent/relationships)性。</span><span class="sxs-lookup"><span data-stu-id="25caa-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="25caa-142">複雜類型慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-142">Complex Types Convention</span></span>  

<span data-ttu-id="25caa-143">當 Code First 探索無法推斷主鍵的類別定義，而且沒有透過資料批註或流暢 API 註冊的主鍵時，就會自動將型別註冊為複雜型別。</span><span class="sxs-lookup"><span data-stu-id="25caa-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="25caa-144">複雜型別偵測也要求型別沒有參考實體類型的屬性，而且不會從另一個型別的集合屬性參考。</span><span class="sxs-lookup"><span data-stu-id="25caa-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="25caa-145">假設有下列類別定義 Code First 會推斷出 **詳細資料** 是複雜型別，因為它沒有主鍵。</span><span class="sxs-lookup"><span data-stu-id="25caa-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

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

## <a name="connection-string-convention"></a><span data-ttu-id="25caa-146">連接字串慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-146">Connection String Convention</span></span>  

<span data-ttu-id="25caa-147">若要瞭解 DbCoNtext 用來探索連接使用的慣例，請參閱 [連接和模型](xref:ef6/fundamentals/configuring/connection-strings)。</span><span class="sxs-lookup"><span data-stu-id="25caa-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="25caa-148">移除慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-148">Removing Conventions</span></span>  

<span data-ttu-id="25caa-149">您可以移除 ModelConfiguration 命名空間中所定義的任何慣例。</span><span class="sxs-lookup"><span data-stu-id="25caa-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="25caa-150">下列範例會移除 **PluralizingTableNameConvention**。</span><span class="sxs-lookup"><span data-stu-id="25caa-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

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

## <a name="custom-conventions"></a><span data-ttu-id="25caa-151">自訂慣例</span><span class="sxs-lookup"><span data-stu-id="25caa-151">Custom Conventions</span></span>  

<span data-ttu-id="25caa-152">EF6 中支援自訂慣例。</span><span class="sxs-lookup"><span data-stu-id="25caa-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="25caa-153">如需詳細資訊，請參閱 [自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)。</span><span class="sxs-lookup"><span data-stu-id="25caa-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
