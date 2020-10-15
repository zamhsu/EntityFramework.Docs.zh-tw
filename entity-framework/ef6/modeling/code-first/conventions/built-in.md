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
# <a name="code-first-conventions"></a>Code First 慣例
Code First 可讓您使用 c # 或 Visual Basic .NET 類別來描述模型。 使用慣例偵測模型的基本圖形。 慣例是一組規則，可用來在使用 Code First 時，根據類別定義自動設定概念模型。 慣例會定義在 ModelConfiguration 命名空間中。  

您可以使用資料批註或流暢的 API 進一步設定您的模型。 優先順序是透過流暢的 API 提供，後面接著資料批註和慣例。 如需詳細資訊，請參閱 [資料批註](xref:ef6/modeling/code-first/data-annotations)、 [流暢的 api 關聯](xref:ef6/modeling/code-first/fluent/relationships)性、 [流暢的 api 類型 & 屬性](xref:ef6/modeling/code-first/fluent/types-and-properties) ，以及 [VB.NET 的流暢 api](xref:ef6/modeling/code-first/fluent/vb)。  

您可以在 [API 檔](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)中取得 Code First 慣例的詳細清單。 本主題概要說明 Code First 所使用的慣例。  

## <a name="type-discovery"></a>類型探索  

使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。 除了定義類別之外，您還需要讓 **DbCoNtext** 知道您想要包含在模型中的類型。 若要這樣做，您可以定義衍生自 **DbCoNtext** 的內容類別，並公開您想要成為模型一部分之類型的 **DbSet** 屬性。 Code First 將包含這些型別，而且也會提取任何參考的型別，即使參考的型別是在不同的元件中定義也一樣。  

如果您的型別參與繼承階層架構，就足以定義基類的 **DbSet** 屬性，而且如果衍生型別和基類的元件位於相同的元件中，就會自動包含這些類型。  

在下列範例中，只有一個 **DbSet** 屬性定義在 **SchoolEntities** 類別 (**部門**) 。 Code First 使用這個屬性來探索和提取任何參考的型別。  

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

如果您要從模型中排除型別，請使用 **NotMapped** 屬性或 **DbModelBuilder。忽略** 流暢的 API。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Primary Key 慣例  

Code First 推斷如果類別上的屬性命名為 "ID" (不區分大小寫的) ，或類別名稱後面加上 "ID"，則該屬性是主要索引鍵。 如果主鍵屬性的類型是數值或 GUID，則會將它設定為識別資料行。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>關聯性慣例  

在 Entity Framework 中，導覽屬性會提供一種方式來導覽兩個實體類型之間的關聯性。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導覽屬性可讓您流覽和管理雙向的關聯性，如果多重性是一個或零或一) ，則傳回參考物件 (; 如果多重性是許多) ，則會傳回集合 (。 Code First 會根據您類型上定義的導覽屬性來推斷關聯性。  

除了導覽屬性以外，我們也建議您在代表相依物件的類型上加入外鍵屬性。 任何具有與主體主鍵屬性相同之資料類型的屬性，以及具有下列其中一種格式之名稱的任何屬性，都代表關聯性的外鍵： ' \<navigation property name\> \<principal primary key property name\> '、' \<principal class name\> \<primary key property name\> ' 或 ' \<principal primary key property name\> '。 如果找到多個相符專案，則會依上列順序提供優先順序。 外鍵偵測不區分大小寫。 當偵測到外鍵屬性時，Code First 會根據外鍵的可 null 性來推斷關聯性的多重性。 如果屬性可為 null，則關聯性會註冊為選擇性;否則，就會視需要註冊關聯性。  

如果相依實體上的外鍵不可為 null，則 Code First 會在關聯性上設定串聯刪除。 如果相依實體上的外鍵可為 null，Code First 不會在關聯性上設定串聯刪除，而且當刪除主體時，外鍵將會設定為 null。 您可以使用流暢的 API 來覆寫慣例所偵測到的多重性和串聯刪除行為。  

在下列範例中，會使用導覽屬性和外鍵來定義部門與課程類別之間的關聯性。  

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
> 如果您在相同類型之間有多個關聯性 (例如，假設您定義 **person** 和 **book** 類別，其中 **Person** 類別包含 **ReviewedBooks** 和 **AuthoredBooks** 導覽屬性，而 **Book** 類別包含 **Author** 和 **審核者** 導覽屬性) 您需要使用資料批註或流暢 API 手動設定關聯性。 如需詳細資訊，請參閱 [資料批註-關聯](xref:ef6/modeling/code-first/data-annotations) 性和 [流暢的 API 關聯](xref:ef6/modeling/code-first/fluent/relationships)性。  

## <a name="complex-types-convention"></a>複雜類型慣例  

當 Code First 探索無法推斷主鍵的類別定義，而且沒有透過資料批註或流暢 API 註冊的主鍵時，就會自動將型別註冊為複雜型別。 複雜型別偵測也要求型別沒有參考實體類型的屬性，而且不會從另一個型別的集合屬性參考。 假設有下列類別定義 Code First 會推斷出 **詳細資料** 是複雜型別，因為它沒有主鍵。  

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

若要瞭解 DbCoNtext 用來探索連接使用的慣例，請參閱 [連接和模型](xref:ef6/fundamentals/configuring/connection-strings)。  

## <a name="removing-conventions"></a>移除慣例  

您可以移除 ModelConfiguration 命名空間中所定義的任何慣例。 下列範例會移除 **PluralizingTableNameConvention**。  

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

EF6 中支援自訂慣例。 如需詳細資訊，請參閱 [自訂 Code First 慣例](xref:ef6/modeling/code-first/conventions/custom)。
