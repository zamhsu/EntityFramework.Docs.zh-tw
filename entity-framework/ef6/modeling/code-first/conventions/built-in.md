---
title: 程式碼第一次慣例 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
ms.openlocfilehash: 4d03a32db5d84eb37c22617a95005b272172a65d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490993"
---
# <a name="code-first-conventions"></a>程式碼的第一個慣例
Code First 能讓您藉由使用 C# 或 Visual Basic.NET 類別，描述模型。 模型的基本形狀會偵測到使用的慣例。 慣例是用來自動設定概念模型時使用 Code First，根據類別定義的規則的集合。 System.Data.Entity.ModelConfiguration.Conventions 命名空間中定義的慣例。  

您可以使用資料註解或 fluent API，進一步設定您的模型。 會優先設定透過 fluent API 接著資料註解和慣例。 如需詳細資訊，請參閱[資料註解](~/ef6/modeling/code-first/data-annotations.md)， [Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)， [Fluent API-型別和屬性](~/ef6/modeling/code-first/fluent/types-and-properties.md)並[VB.NET使用FluentAPI](~/ef6/modeling/code-first/fluent/vb.md).  

Code First 慣例的詳細的清單位於[API 文件](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。 本主題提供使用 Code First 慣例的概觀。  

## <a name="type-discovery"></a>類型探索  

使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。 除了定義類別，您也需要讓**DbContext**知道您想要包含在模型中的類型。 若要這樣做，您定義的內容類別衍生自**DbContext** ，並公開**DbSet**屬性的類型，您想要模型的一部分。 程式碼會先將包含這些類型，並也會納入任何參考的類型，即使參考的型別在不同的組件中定義。  

如果您的型別參與繼承階層架構，就已足夠定義**DbSet**屬性的基底類別和衍生的類型將會自動包含在內，如果它們位於相同的組件的基底類別。  

在下列範例中，有一個只**DbSet**上定義的屬性**SchoolEntities**類別 (**部門**)。 首先，程式碼會使用這個屬性來探索及任何參考型別中提取。  

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

如果您想要從模型中排除的型別，使用**NotMapped**屬性或**DbModelBuilder.Ignore** fluent API。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>主索引鍵的慣例  

程式碼會先推斷屬性類別上的屬性名為 「 識別碼 」 （不區分大小寫），則類別名稱加上"ID"是主索引鍵。 如果主索引鍵屬性的型別是數值或 GUID 就會設定為識別欄位。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>關聯性慣例  

在 Entity Framework 中，導覽屬性會提供瀏覽兩個實體類型之間的關聯性的方法。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導覽屬性可讓您巡覽及管理兩個方向，傳回參考物件的關聯性 (如果多重性是一個或零或一個) 或集合 （如果多重性是許多項目）。 程式碼會先推斷您的型別上定義的導覽屬性為基礎的關聯性。  

除了導覽屬性，我們建議您包含類型，可表示相依物件上的外部索引鍵屬性。 遵循下列格式之一的名稱作為主體主索引鍵的屬性相同的資料類型與任何屬性表示關聯性的外部索引鍵: '\<導覽屬性名稱\>\<主體主索引鍵屬性名稱\>'，'\<主體的類別名稱\>\<主索引鍵屬性名稱\>'，或 '\<主體的主索引鍵屬性名稱\>'。 如果找到多個相符項目優先順序可以在上面所列的順序。 外部索引鍵的偵測不區分大小寫。 偵測到外部索引鍵屬性時，Code First 會推斷為基礎的外部索引鍵的 null 屬性的關聯性的多重性。 如果屬性是可為 null 然後關聯性會註冊為選擇性的。否則會註冊關聯性，視需要而定。  

如果相依的實體上的外部索引鍵不是可為 null，然後第一個程式碼設定串聯刪除關聯性。 如果相依的實體上的外部索引鍵可為 null，第一個程式碼不會設定串聯刪除關聯性，而且當主體已刪除的外部索引鍵將會設定為 null。 多重性與串聯刪除所偵測到的行為使用 fluent API 可以覆寫慣例。  

在下列範例中的導覽屬性和外部索引鍵用來定義科系與課程類別之間的關聯性。  

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
> 如果您有多個相同型別之間的關聯性 (比方說，假設您定義**Person**並**活頁簿**類別，其中**人員**類別包含**ReviewedBooks**並**AuthoredBooks**導覽屬性和**活頁簿**類別包含**作者**和**檢閱者**導覽屬性) 您要使用資料註解或 fluent API 來手動設定關聯性。 如需詳細資訊，請參閱 <<c0> [ 資料註解-關聯性](~/ef6/modeling/code-first/data-annotations.md)並[Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)。  

## <a name="complex-types-convention"></a>複雜型別慣例  

Code First 會探索其中的主索引鍵無法推斷，而沒有主索引鍵透過資料註解或 fluent API 註冊的類別定義，然後類型會自動註冊為複雜型別。 複雜型別偵測也需要型別沒有參考實體類型，而且不從另一種類型的集合屬性參考的屬性。 指定下列類別定義 Code First 會推斷，**詳細資料**是複雜類型，因為它有沒有主索引鍵。  

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

## <a name="connection-string-convention"></a>連接字串慣例  

若要了解慣例來探索要使用，請參閱連線，使用 DbContext[連線和模型](~/ef6/fundamentals/configuring/connection-strings.md)。  

## <a name="removing-conventions"></a>移除慣例  

您可以移除任何 System.Data.Entity.ModelConfiguration.Conventions 命名空間中定義的慣例。 下列範例會移除**PluralizingTableNameConvention**。  

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

## <a name="custom-conventions"></a>自訂慣例  

自訂慣例及更新版本支援在 EF6 中。 如需詳細資訊，請參閱[自訂程式碼的第一個慣例](~/ef6/modeling/code-first/conventions/custom.md)。
