---
title: 關聯性、 導覽屬性和外部索引鍵-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
caps.latest.revision: 3
ms.openlocfilehash: 0cc18ee8d1b9d1633535e4b8186ffc4c68daf32b
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120246"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>關聯性、 導覽屬性和外部索引鍵
本主題提供 Entity Framework 如何管理實體之間的關聯性的概觀。 它也會提供有關如何對應及操作關聯性的一些指引。

## <a name="relationships-in-ef"></a>在 EF 中的關聯性

在關聯式資料庫 （也稱為關聯） 的資料表之間的關聯是透過外部索引鍵定義。 外部索引鍵 (FK) 是資料行或資料行的組合，用來建立與強制兩個資料表中的資料之間的連結。 通常有三種類型的關聯性： 一對一、 一個一對多和多對多。 在一對多關聯性，表示關聯性的多端資料表定義外部索引鍵。 多對多關聯性需要定義第三個資料表 （稱為聯合或聯結資料表），其主索引鍵由兩個相關資料表的外部索引鍵所組成。 在一對一的關聯性中，主索引鍵做為外部索引鍵，此外並沒有不同外部索引鍵資料行的任兩個資料表。

下圖顯示兩個參與的資料表，一個對多關聯性。 **課程**資料表會是相依的資料表，因為它包含**DepartmentID**其連結至的資料行**部門**資料表。

![Database2](~/ef6/media/database2.png)

在 Entity Framework 中，實體可以與其他機構可透過關聯或關係。 每個關聯性包含兩端，描述實體類型，而該關聯性中的兩個實體的類型 （一個、 零或一，或多個）。 關聯性可能會受到所參考的條件約束，當中會描述關聯性中的結尾是主要角色，也是相依角色。

導覽屬性可用來瀏覽兩個實體類型之間的關聯。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導覽屬性可讓您巡覽及管理兩個方向，傳回參考物件的關聯性 (如果多重性是一個或零或一個) 或集合 （如果多重性是許多項目）。 您也可以選擇在此情況下您定義的導覽屬性上只有其中一個參與的關聯性的類型，而不是在同時擁有單向導覽。

建議您包含在模型中對應至資料庫中的外部索引鍵的屬性。 包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。 這個類型的關聯稱為外部索引鍵關聯。 使用已中斷連線實體時，使用外部索引鍵是更重要的。 請注意，當使用 1-1] 或 [1-0...1 的關聯性沒有個別的外部索引鍵資料行、 主索引鍵屬性做為外部索引鍵，並一律包含在模型中。

當外部索引鍵資料行不包含在模型中時，關聯資訊被管理做為獨立的物件。 關聯性會追蹤透過物件參考，而不是外部索引鍵屬性。 這種類型的關聯稱為*獨立關聯*。 若要修改的最常見方式*獨立關聯*是修改針對參與關聯的每個實體所產生的導覽屬性。

您可以選擇在模型中使用一種或兩種關聯類型。 不過，如果您有連接只包含外部索引鍵的聯結資料表的純粹多對多關聯性時，EF 會使用獨立關聯來管理這類多對多關聯性。   

下圖顯示使用 Entity Framework 設計工具建立概念模型。 模型包含兩個參與一對多關聯性的實體。 這兩個實體都有導覽屬性。 **Course** depend 實體，且具有**DepartmentID**定義的外部索引鍵屬性。

![RelationshipEFDesigner](~/ef6/media/relationshipefdesigner.png)

下列程式碼片段會顯示使用 Code First 所建立的相同模型。

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class DepartmentID
{
   public Department()
   {
     this.Course = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>設定或對應關聯性

此頁面的其餘部分將說明如何存取及管理使用關聯性的資料。 如需設定您的模型中關聯性的資訊，請參閱下列頁面。

-   若要設定關聯性中第一個程式碼，請參閱[資料註解](~/ef6/modeling/code-first/data-annotations.md)並[Fluent API-關聯性](~/ef6/modeling/code-first/fluent/relationships.md)。
-   若要設定關聯性使用 Entity Framework 設計工具，請參閱[EF 設計工具關聯性](~/ef6/modeling/designer/relationships.md)。

## <a name="creating-and-modifying-relationships"></a>建立和修改關聯性

在 *外部索引鍵關聯*，當您變更關聯性，而狀態會變更為 EntityState.Modified EntityState.Unchanged 的相依物件的狀態。 在獨立關聯中，變更關聯性不會更新相依物件的狀態。

下列範例示範如何使用的外部索引鍵屬性和導覽屬性，將相關的物件產生關聯。 與外部索引鍵的關聯，您可以使用任一種方法來變更、 建立或修改關聯性。 使用獨立關聯，則無法使用外部索引鍵屬性。

-   將新值指派到外部索引鍵屬性，如下列範例所示。  
    ``` csharp
    course.DepartmentID = newCourse.DepartmentID;
    ```

-   下列程式碼藉由設定外部索引鍵移除關聯性**null**。 請注意，外部索引鍵屬性必須是可為 null。  
    ``` csharp
    course.DepartmentID = null;
    ```  
    >[!NOTE]
    > 如果參考是已加入狀態 （在此範例中，課程物件），參考導覽屬性將不會同步具有新物件的索引鍵值直到呼叫 SaveChanges。 不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。 如果您必須有完全同步處理為您設定的關聯性的新物件，使用其中一個下列 methods.*

-   將新物件指派給導覽屬性。 下列程式碼會建立 course 之間的關聯性和`department`。 這些物件會附加至內容，如果`course`也會加入至`department.Courses`集合物件和對應外部索引鍵屬性上`course`物件設定為部門的索引鍵的屬性值。  
    ``` csharp
    course.Department = department;
    ```

 -   若要刪除關聯性，設定的導覽屬性為`null`。 如果您正在使用 Entity Framework 為基礎的.NET 4.0，相關的端必須先載入才能將它設定為 null。 例如:   
    ``` chsarp
    context.Entry(course).Reference(c => c.Department).Load();  
    course.Department = null;
    ```  
    從 Entity Framework 5.0，以.NET 4.5 為基礎，您可以設定關聯性設為 null 而不需要載入相關的端。 您也可以設定為 null，使用下列方法目前的值。  
    ``` csharp
    context.Entry(course).Reference(c => c.Department).CurrentValue = null;
    ```

-   在實體集合中刪除或加入物件。 例如，您可以在其中新增類型的物件`Course`至`department.Courses`集合。 此作業會建立特定之間的關聯性**課程**與特定`department`。 如果這些物件會附加至內容、 部門參考和外部索引鍵屬性上**課程**物件將會設定為適當`department`。  
    ``` csharp
    department.Courses.Add(newCourse);
    ```

- 使用`ChangeRelationshipState`方法，以變更兩個實體物件之間指定關聯性的狀態。 使用多層式架構應用程式時，將最常使用這個方法和*獨立關聯*（它不能與外部索引鍵關聯）。 此外，才能使用這個方法就必須卸除至`ObjectContext`，如下列範例所示。  
在下列範例中，沒有講師和課程之間的多對多關聯性。 呼叫`ChangeRelationshipState`方法並傳遞`EntityState.Added`參數，可讓`SchoolContext`知道兩個物件之間的關聯性，已加入：

``` csharp

       ((IObjectContextAdapter)context).ObjectContext.
                 ObjectStateManager.
                  ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
```

    Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:

``` csharp
       ((IObjectContextAdapter)context).ObjectContext.
                  ObjectStateManager.
                  ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>同步處理之間的外部索引鍵和導覽屬性的變更

當您變更使用其中一種以上所述的方法附加至內容之物件的關聯性時，Entity Framework 必須保持同步的外部索引鍵、 參考和集合。Entity Framework 會自動管理此同步處理 （也就是關聯性修正程式-最多），使用 proxy 的 POCO 實體。 如需詳細資訊，請參閱 <<c0> [ 使用 Proxy](~/ef6/fundamentals/proxies.md)。

如果您使用沒有 proxy 的 POCO 實體，您必須確定**DetectChanges**呼叫方法來同步處理內容中的相關的物件。 請注意，下列 Api 自動觸發**DetectChanges**呼叫。

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
-   執行 LINQ 查詢 `DbSet`

## <a name="loading-related-objects"></a>載入相關物件

您最常使用的 Entity Framework 中使用導覽屬性載入與所定義的關聯所傳回的實體的實體。 如需詳細資訊，請參閱 <<c0> [ 載入相關物件](~/ef6/querying/related-data.md)。

> [!NOTE]
> 在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：

``` csharp
    // Get the course where currently DepartmentID = 1.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。 不過，如果已修改的關聯性，並指向不同的主體物件載入物件內容中，Entity Framework 的相依物件上的 [參考] 屬性將會嘗試建立關聯性，如同它被定義在用戶端上。

## <a name="managing-concurrency"></a>管理並行

在外部索引鍵和獨立關聯中，並行存取檢查會根據實體索引鍵和其他模型中定義的實體屬性而定。 當使用 EF 設計工具來建立模型時，設定`ConcurrencyMode`屬性設定為**修正**指定屬性，應檢查並行存取。 當使用 Code First 定義的模型，使用`ConcurrencyCheck`上想要檢查並行存取的屬性註釋。 使用 Code First 時您也可以使用`TimeStamp`註解來指定應該檢查並行存取的屬性。 您可以在某一特定類別只能有一個時間戳記屬性。 程式碼首先會將此屬性對應至資料庫中的非可為 null 的欄位。

我們建議使用參與並行存取檢查和解析實體時，一律使用外部索引鍵的關聯。

如需詳細資訊，請參閱 <<c0> [ 處理並行衝突](~/ef6/saving/concurrency.md)。

## <a name="working-with-overlapping-keys"></a>使用索引鍵重疊

重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。 在獨立關聯中不能有重疊索引鍵。 若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。
