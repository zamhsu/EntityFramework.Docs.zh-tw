---
title: 關聯性、導覽屬性和外鍵-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 5807f7aeeb68328821cf45ac4f8c28efa17ed399
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664191"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>關聯性、導覽屬性和外鍵

本文概述 Entity Framework 如何管理實體之間的關聯性。 同時也提供一些關於如何對應和操作關聯性的指引。

## <a name="relationships-in-ef"></a>EF 中的關聯性

在關係資料庫中，資料表之間的關聯性（也稱為關聯）是透過外鍵定義。 外部索引鍵 (FK) 是可用來建立與強制兩資料表的資料之間連結的一個資料行或資料行組合。 通常有三種類型的關聯性：一對一、一對多和多對多之間。 在一對多關聯性中，外鍵是在代表關聯性的多個端的資料表上定義的。 多對多關聯性牽涉到定義第三個數據表（稱為「連接點」或「聯結資料表」），其主鍵是由兩個相關資料表中的外鍵所組成。 在一對一關聯性中，主鍵會當做外鍵來額外運作，而且不會有任何一個資料表的個別外鍵資料行。

下圖顯示兩個參與一對多關聯性的資料表。 **課程**資料表是相依資料表，因為它包含將它連結至**部門**資料表的**DepartmentID**資料行。

![部門和課程資料表](~/ef6/media/database2.png)

在 Entity Framework 中，實體可以透過關聯或關聯性與其他實體相關聯。 每個關聯性都包含兩個端，描述該關聯性中兩個實體的實體類型和類型的多重性（一個、零或一或多個）。 關聯性可能由參考條件約束控制，它會描述關聯性中的哪一端是主要角色，哪一端是相依角色。

導覽屬性提供一種方式，可流覽兩個實體類型之間的關聯。 每個物件都可以針對它所參與的每項關聯性擁有導覽屬性。 導覽屬性可讓您流覽和管理雙向的關聯性，傳回參考物件（如果多重性為一個或零或一個）或集合（如果多重性為多個）。 您也可以選擇使用單向導覽，在這種情況下，您只會在參與關聯性的其中一個類型上定義導覽屬性，而不是在兩者上。

建議您在模型中包含屬性，以對應至資料庫中的外鍵。 包括了外部索引鍵之後，您便可以透過修改相依物件上的外部索引鍵值來建立或變更關聯性。 這個類型的關聯稱為外部索引鍵關聯。 使用已中斷連線的實體時，使用外鍵更為重要。 請注意，當您使用1對1或1對0時。1關聯性，沒有個別的外鍵資料行，主鍵屬性會做為外鍵，而且一律包含在模型中。

當外鍵資料行不包含在模型中時，會將關聯資訊當做獨立物件進行管理。 關聯性是透過物件參考而非外鍵屬性來追蹤。 這種類型的關聯稱為*獨立關聯*。 修改*獨立關聯*的最常見方式是修改針對參與關聯之每個實體所產生的導覽屬性。

您可以選擇在模型中使用一種或兩種關聯類型。 不過，如果您的單純多對多關聯性是由只包含外鍵的聯結資料表所連接，則 EF 會使用獨立關聯來管理這類多對多關聯性。   

下圖顯示使用 Entity Framework Designer 建立的概念模型。 此模型包含兩個參與一對多關聯性的實體。 這兩個實體都有導覽屬性。 **課程**是相依實體，並已定義**DepartmentID**外鍵屬性。

![具有導覽屬性的部門和課程資料表](~/ef6/media/relationshipefdesigner.png)

下列程式碼片段顯示使用 Code First 建立的相同模型。

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

## <a name="configuring-or-mapping-relationships"></a>設定或對應關聯性

此頁面的其餘部分涵蓋如何使用關聯性存取和運算元據。 如需有關在模型中設定關聯性的詳細資訊，請參閱下列頁面。

-   若要設定 Code First 中的關聯性，請參閱[資料注釋](~/ef6/modeling/code-first/data-annotations.md)和[流暢的 API-關聯](~/ef6/modeling/code-first/fluent/relationships.md)性。
-   若要使用 Entity Framework Designer 來設定關聯性，請參閱[與 EF 設計](~/ef6/modeling/designer/relationships.md)工具的關聯性。

## <a name="creating-and-modifying-relationships"></a>建立和修改關聯性

在*外鍵關聯*中，當您變更關聯性時，狀態會變更為的相依物件狀態 `EntityState.Unchanged` `EntityState.Modified` 。 在獨立的關聯性中，變更關聯性並不會更新相依物件的狀態。

下列範例示範如何使用外鍵屬性和導覽屬性來建立相關物件的關聯性。 使用外鍵關聯時，您可以使用任何一種方法來變更、建立或修改關聯性。 使用獨立關聯，則無法使用外部索引鍵屬性。

- 藉由將新值指派給外鍵屬性，如下列範例所示。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 下列程式碼會將外鍵設定為**null**，藉以移除關聯性。 請注意，外鍵屬性必須是可為 null。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 如果參考處於已加入的狀態（在此範例中為課程物件），則在呼叫 SaveChanges 之前，參考導覽屬性將不會與新物件的索引鍵值同步處理。 不會同步化是因為物件內容未包含新加入之物件的永久索引鍵，除非先儲存這些索引鍵。 如果您在設定關聯性後，必須完全同步處理新物件，請使用下列其中一種方法。 *

- 將新物件指派給導覽屬性。 下列程式碼會建立課程和之間的關聯性 `department` 。 如果物件附加至內容，則 `course` 也會新增至 `department.Courses` 集合，並將物件上對應的外鍵屬性 `course` 設定為部門的索引鍵屬性值。  
  ``` csharp
  course.Department = department;
  ```

- 若要刪除關聯性，請將導覽屬性設定為 `null` 。 如果您要使用以 .NET 4.0 為基礎的 Entity Framework，則必須先載入相關端，才能將其設定為 null。 例如：   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  從以 .NET 4.5 為基礎的 Entity Framework 5.0 開始，您可以將關聯性設定為 null，而不需要載入相關端。 您也可以使用下列方法，將目前的值設定為 null。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- 在實體集合中刪除或加入物件。 例如，您可以將類型的物件加入 `Course` 至 `department.Courses` 集合。 這種作業會建立特定課程與特定**課程**之間的關聯性 `department` 。 如果物件附加至內容，則會將 [部門參考] 和 [**課程**] 物件上的外鍵屬性設定為適當的 `department` 。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- 藉由使用 `ChangeRelationshipState` 方法來變更兩個實體物件之間指定之關聯性的狀態。 當使用多層式應用程式和*獨立關聯*（無法與外鍵關聯一起使用）時，最常使用這個方法。 此外，若要使用這個方法，您必須向下下拉式 `ObjectContext` ，如下列範例所示。  
在下列範例中，講師和課程之間有多對多關聯性。 呼叫 `ChangeRelationshipState` 方法並傳遞 `EntityState.Added` 參數，可讓您 `SchoolContext` 知道這兩個物件之間已加入關聯性：
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  請注意，如果您要更新（而不只是加入）關聯性，您必須在新增新的關聯性之後，將它刪除：

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>同步處理外鍵和導覽屬性之間的變更

當您使用上述其中一個方法來變更附加至內容之物件的關聯性時，Entity Framework 必須保持外鍵、參考和集合同步。Entity Framework 會針對具有 proxy 的 POCO 實體，自動管理此同步處理（也稱為關聯性修正）。 如需詳細資訊，請參閱[使用](~/ef6/fundamentals/proxies.md)proxy。

如果您使用沒有 proxy 的 POCO 實體，您必須確定呼叫**DetectChanges**方法，以同步處理內容中的相關物件。 請注意，下列 Api 會自動觸發**DetectChanges**呼叫。

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
-   針對執行 LINQ 查詢`DbSet`

## <a name="loading-related-objects"></a>載入相關物件

在 Entity Framework 您通常會使用導覽屬性來載入與所定義關聯所傳回之實體相關的實體。 如需詳細資訊，請參閱[載入相關物件](~/ef6/querying/related-data.md)。

> [!NOTE]
> 在外部索引鍵關聯中，當您載入相依物件的相關端時，相關物件將根據目前在記憶體中之相依外部索引鍵值來載入：

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

在獨立關聯中，會根據目前在資料庫中的外部索引鍵值來查詢相依物件的相關端。 不過，如果已修改關聯性，而相依物件上的 reference 屬性指向載入物件內容中的不同主體物件，Entity Framework 將會嘗試建立關聯性，因為它是在用戶端上定義。

## <a name="managing-concurrency"></a>管理並行存取

在外鍵和獨立關聯中，平行存取檢查是以模型中所定義的實體索引鍵和其他實體屬性為基礎。 使用 EF 設計工具建立模型時，請將屬性設定 `ConcurrencyMode` 為**fixed** ，以指定應該檢查屬性是否有平行存取。 使用 Code First 定義模型時，請 `ConcurrencyCheck` 在您想要檢查並行的屬性上使用注釋。 使用 Code First 時，您也可以使用 `TimeStamp` 批註來指定應該檢查屬性是否有平行存取。 在指定的類別中，您只能有一個時間戳記屬性。 Code First 會將此屬性對應至資料庫中不可為 null 的欄位。

當您使用參與平行存取檢查和解決的實體時，建議您一律使用外鍵關聯。

如需詳細資訊，請參閱[處理並行衝突](~/ef6/saving/concurrency.md)。

## <a name="working-with-overlapping-keys"></a>使用重迭的索引鍵

重疊索引鍵是複合索引鍵，其索引鍵中的部分屬性也是實體中另一個索引鍵的一部分。 在獨立關聯中不能有重疊索引鍵。 若要變更包括重疊索引鍵的外部索引鍵關聯，我們建議您修改外部索引鍵值，而不是使用物件參考。
