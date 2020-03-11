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
# <a name="code-first-conventions"></a>Code First 慣例
Code First 可讓您使用C#或 Visual Basic .net 類別來描述模型。 使用慣例偵測模型的基本形狀。 慣例是一組規則，可在使用 Code First 時，用來根據類別定義自動設定概念模型。 慣例會定義在 ModelConfiguration 命名空間中。  

您可以使用資料批註或 Fluent API 進一步設定您的模型。 優先順序是透過 Fluent API 後面接著資料批註，然後是慣例來設定。 如需詳細資訊，請參閱[資料批註](~/ef6/modeling/code-first/data-annotations.md)、[流暢的 api 關聯](~/ef6/modeling/code-first/fluent/relationships.md)性、[流暢的 api 類型 & 屬性](~/ef6/modeling/code-first/fluent/types-and-properties.md)和[使用 VB.NET 的流暢 api](~/ef6/modeling/code-first/fluent/vb.md)。  

如需 Code First 慣例的詳細清單，請[參閱 API 檔](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。 本主題提供 Code First 所使用之慣例的總覽。  

## <a name="type-discovery"></a>類型探索  

使用 Code First 開發時，您通常會從撰寫定義概念（網域）模型的 .NET Framework 類別開始著手。 除了定義類別以外，您還需要讓**DbCoNtext**知道您想要包含在模型中的類型。 若要這樣做，您可以定義衍生自**DbCoNtext**的內容類別，並針對您想要成為模型一部分的類型公開**DbSet**屬性。 Code First 將包含這些類型，而且也會提取任何參考的類型，即使參考的類型是在不同的元件中定義也一樣。  

如果您的型別參與繼承階層架構，就足以定義基類的**DbSet**屬性，而且如果衍生的型別與基類位於相同的元件中，就會自動包含該類別。  

在下列範例中， **SchoolEntities**類別（**部門**）上只定義了一個**DbSet**屬性。 Code First 使用這個屬性來探索和提取任何參考的類型。  

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

如果您想要從模型中排除型別，請使用**NotMapped**屬性或**DbModelBuilder。請略**過 Fluent API。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>主要金鑰慣例  

Code First 推斷，如果類別上的屬性名稱為 "ID" （不區分大小寫），或類別名稱後面接著 "ID"，則屬性是主要索引鍵。 如果 primary key 屬性的類型是數值或 GUID，則會將它設定為識別欄位。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>關聯性慣例  

在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導覽屬性可讓您流覽和管理雙向的關聯性，傳回參考物件（如果多重性為一個或零或一個）或集合（如果多重性為多個）。 Code First 會根據您類型上定義的導覽屬性來推斷關聯性。  

除了導覽屬性以外，我們也建議您在代表相依物件的類型上包含外鍵屬性。 任何資料類型與主體主鍵屬性相同的屬性，以及遵循下列其中一種格式的名稱，都代表關聯性的外鍵： '\<導覽屬性名稱\>\<主體主鍵屬性名稱\>'、'\<主體類別名稱\>\<主鍵屬性名稱\>' 或 '\<主要主鍵屬性名稱\>'。 如果找到多個相符專案，則會依上述順序提供優先順序。 外鍵偵測不區分大小寫。 當偵測到外鍵屬性時，Code First 會根據外鍵的 null 屬性來推斷關聯性的多重性。 如果屬性可為 null，則會將關聯性註冊為選擇性;否則，會視需要註冊關聯性。  

如果相依實體的外鍵不可為 null，則 Code First 在關聯性上設定 cascade delete。 如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當主體被刪除時，外鍵將會設定為 null。 依照慣例偵測到的多重性和 cascade 刪除行為，可以使用 Fluent API 加以覆寫。  

在下列範例中，會使用導覽屬性和外鍵來定義部門和課程類別之間的關聯性。  

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
> 如果您有相同類型之間的多個關聯性（例如，假設您定義**person**和**Book**類別，其中**Person**類別包含**ReviewedBooks**和**AuthoredBooks**導覽屬性，而**Book**類別包含**作者**和**審核者**導覽屬性），則您需要使用資料批註或 Fluent API 手動設定關聯性。 如需詳細資訊，請參閱[資料批註-關聯](~/ef6/modeling/code-first/data-annotations.md)性和[流暢的 API 關聯](~/ef6/modeling/code-first/fluent/relationships.md)性。  

## <a name="complex-types-convention"></a>複雜類型慣例  

當 Code First 探索無法推斷主鍵的類別定義，而且沒有透過資料批註或 Fluent API 註冊主鍵時，會自動將該類型註冊為複雜類型。 複雜型別偵測也會要求類型沒有參考實體類型的屬性，而且不會從另一個類型的集合屬性中參考。 假設下列類別定義 Code First 會推斷該**詳細資料**是複雜型別，因為它沒有主鍵。  

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

若要瞭解 DbCoNtext 用來探索連線的慣例，請參閱[連接和模型](~/ef6/fundamentals/configuring/connection-strings.md)。  

## <a name="removing-conventions"></a>移除慣例  

您可以移除 ModelConfiguration 中所定義的任何慣例，也就是命名空間。 下列範例會移除**PluralizingTableNameConvention**。  

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

EF6 開始支援自訂慣例。 如需詳細資訊，請參閱[自訂 Code First 慣例](~/ef6/modeling/code-first/conventions/custom.md)。
