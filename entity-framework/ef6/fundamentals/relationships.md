---
title: 關聯、導覽屬性與外鍵 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434322"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>關聯、導航屬性和外鍵

本文概述了實體框架如何管理實體之間的關係。 它還就如何映射和操作關係提供了一些指導。

## <a name="relationships-in-ef"></a>EF 中的關係

在關係資料庫中,表之間的關係(也稱為關聯)通過外鍵定義。 外部索引鍵 (FK) 是可用來建立與強制兩資料表的資料之間連結的一個資料行或資料行組合。 通常有三種類型的關係:一對一、一對多和多對多。 在一對多關係中,外鍵在表上定義,該表表示關係的多端。 多對多關係涉及定義第三個表(稱為連接表或聯接表),其主鍵由兩個相關表中的外鍵組成。 在一對一關係中,主鍵另外充當外鍵,並且兩個表沒有單獨的外鍵列。

下圖顯示了兩個參與一對多關係的表。 **課程**表是從屬表,因為它包含將其連結到 **「部門」** 表的 **「部門 ID」** 列。

![部門和課程表](~/ef6/media/database2.png)

在實體框架中,實體可以通過關聯或關係與其他實體關聯。 每個關係包含兩個端,用於描述該關係中兩個實體的實體類型和類型的多重性(一個、零或一個或多個)。 關聯性可能由參考條件約束控制，它會描述關聯性中的哪一端是主要角色，哪一端是相依角色。

導航屬性提供了一種導航兩種實體類型之間的關聯的方法。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導航屬性允許您在兩個方向上導航和管理關係,傳回引用物件(如果多重性為1或為零或1)或集合(如果多重性多)。 您還可以選擇單向導航,在這種情況下,您只對參與關係的一種類型定義導航屬性,而不是同時在兩種類型上定義導航屬性。

建議在模型中包括映射到資料庫中的外鍵的屬性。 包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。 這個類型的關聯稱為外部索引鍵關聯。 使用斷開連接的實體時,使用外鍵更為重要。 請注意,使用 1 比 1 或 1 比 0 時。1 個關係,沒有單獨的外鍵列,主鍵屬性充當外鍵,始終包含在模型中。

當模型中不包括外鍵列時,關聯資訊將作為獨立物件進行管理。 關係通過物件引用而不是外鍵屬性進行跟蹤。 這種類型的關聯稱為*獨立關聯*。 修改*獨立關聯的*最常見方法是修改為參與關聯的每個實體生成的導航屬性。

您可以選擇在模型中使用一種或兩種關聯類型。 但是,如果您有一個純多對多關係,該關係由僅包含外鍵的聯接表連接,則 EF 將使用獨立關聯來管理此類多對多關係。   

下圖顯示了使用實體框架設計器創建的概念模型。 該模型包含兩個參與一對多關係的實體。 兩個實體都具有導航屬性。 **課程**是從屬實體,並且定義了**DepartmentID**外鍵屬性。

![具有導覽屬性的部門和課程表](~/ef6/media/relationshipefdesigner.png)

以下代碼段顯示使用代碼優先創建的模型。

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>設定或對應關係

本頁的其餘部分介紹如何使用關係訪問和操作數據。 有關在模型中設置關係的資訊,請參閱以下頁面。

-   在程式碼優先中設定關聯,請參考[資料註解](~/ef6/modeling/code-first/data-annotations.md)與[Fluent API = 關係](~/ef6/modeling/code-first/fluent/relationships.md)。
-   要使用實體框架設計器設定關係,請參閱[與 EF 設計器的關係](~/ef6/modeling/designer/relationships.md)。

## <a name="creating-and-modifying-relationships"></a>建立與修改關係

在*外鍵關聯*中,當您變更關係時`EntityState.Unchanged`,具有狀態的從屬物件的狀態將更改`EntityState.Modified`為 。 在獨立關係中,更改關係不會更新從屬物件的狀態。

以下範例展示如何使用外鍵屬性和導航屬性來關聯相關物件。 使用外鍵關聯,可以使用任一方法更改、創建或修改關係。 使用獨立關聯，則無法使用外部索引鍵屬性。

- 通過將新值分配給外鍵屬性,如以下示例所示。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 以下代碼通過將外鍵設置為**null**來刪除關係。 請注意,外鍵屬性必須為空。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 如果引用處於添加狀態(在此示例中為課程物件),則在調用 SaveChanges 之前,引用導航屬性不會與新物件的鍵值同步。 不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。 如果在設置關係后立即必須完全同步新物件,請使用以下方法之一。

- 將新物件指派給導覽屬性。 以下代碼在課程和之間`department`創建關係。 如果物件附加到上下文,`course`則 還會將`department.Courses`添加到集合`course`中,並且 物件上的相應外鍵屬性將設置為部門的關鍵屬性值。  
  ``` csharp
  course.Department = department;
  ```

- 要刪除關聯,請將導覽屬性設定`null`為 。 如果使用基於 .NET 4.0 的實體框架,則需要在將其設置為 null 之前載入相關端。 例如：   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  從基於 .NET 4.5 的實體框架 5.0 開始,可以將關係設置為 null,而無需載入相關端。 您還可以使用以下方法將當前值設定為 null。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- 在實體集合中刪除或加入物件。 例如,可以將類型`Course`物件添加到集合中`department.Courses`。 此操作在特定**課程**和特定`department`之間創建關係。 如果物件附加到上下文,**則課程**物件的部門參考和外鍵屬性設定為`department`相應的 。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- 通過使用方法`ChangeRelationshipState`更改兩個實體對象之間指定關係的狀態。 此方法最常用於使用 N-Tier 應用程式和獨立*關聯*(不能與外鍵關聯一起使用)。 此外,要使用此方法,必須下拉至`ObjectContext`,如下例所示。  
在下面的示例中,教師和課程之間存在多對多關係。 呼叫`ChangeRelationshipState`方法並傳遞`EntityState.Added`參數`SchoolContext`, 可以知道兩個物件之間已新增了關係:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  請注意,如果要更新(而不僅僅是添加)關係,則必須在添加新關係后刪除舊關係:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>同步外鍵與導覽屬性之間的變更

使用上述方法之一更改附加到上下文的對象之間的關係時,實體框架需要保持外鍵、引用和集合同步。實體框架自動管理具有代理的 POCO 實體的此同步(也稱為關係修復)。 有關詳細資訊,請參閱[使用代理](~/ef6/fundamentals/proxies.md)。

如果使用沒有代理的 POCO 實體,則必須確保調用**檢測更改**方法以同步上下文中的相關物件。 請注意,以下 API 會自動觸發**檢測更改**調用。

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   執行 LINQ 查詢`DbSet`

## <a name="loading-related-objects"></a>載入相關物件

在實體框架中,通常使用導航屬性載入由定義的關聯與返回的實體相關的實體。 有關詳細資訊,請參閱[載入相關物件](~/ef6/querying/related-data.md)。

> [!NOTE]
> 在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。 但是,如果修改了關係,並且從屬物件的引用屬性指向在物件上下文中載入的不同主體物件,實體框架將嘗試創建在用戶端上定義的關係。

## <a name="managing-concurrency"></a>管理並行存取

在外鍵和獨立關聯中,併發檢查基於模型中定義的實體密鑰和其他實體屬性。 使用 EF 設計器建立模型時`ConcurrencyMode`,將 屬性設置為**固定**以指定應檢查屬性為併發性。 使用 Code First 定義模型`ConcurrencyCheck`時,對 要檢查為併發的屬性使用註釋。 使用 Code First`TimeStamp`時, 還可以使用註釋指定應檢查屬性為併發性。 給定類中只能有一個時間戳屬性。 代碼 First 將此屬性映射到資料庫中的非空欄位。

我們建議您在使用參與併發檢查和解析的實體時始終使用外鍵關聯。

有關詳細資訊,請參閱[處理併發衝突](~/ef6/saving/concurrency.md)。

## <a name="working-with-overlapping-keys"></a>使用重疊鍵

重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。 在獨立關聯中不能有重疊索引鍵。 若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。
