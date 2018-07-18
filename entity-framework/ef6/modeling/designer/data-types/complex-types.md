---
title: 複雜型別-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
caps.latest.revision: 3
ms.openlocfilehash: 6d0744921085afdafa35d137d276c54738cdd465
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120332"
---
# <a name="complex-types---ef-designer"></a>複雜型別-EF 設計工具
本主題說明如何將使用 Entity Framework Designer （EF 設計工具） 的複雜型別對應，以及如何查詢實體包含複雜型別的屬性。

下圖顯示使用 EF 設計工具時所使用的主視窗。

![EFDesigner](~/ef6/media/efdesigner.png)

> [!NOTE]
> 當您建置概念模型時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。 您可以忽略這些警告，因為錯誤您選擇從模型產生資料庫之後，就會消失。

## <a name="what-is-a-complex-type"></a>什麼是複雜型別

複雜型別是實體型別的非純量屬性，可讓純量屬性得以在實體內組織。 如同實體，複雜類型包含純量屬性或其他複雜類型屬性。

當您使用代表複雜類型的物件時，請注意下列項目：

-   複雜型別沒有索引鍵，因此無法獨立存在。 複雜型別只能以實體類型或其他複雜型別的屬性形式存在。
-   複雜型別不能參與關聯，而且不能包含導覽屬性。
-   複雜型別屬性不能**null**。 * * InvalidOperationException * *，就會發生時**DbContext.SaveChanges**稱為且遇到 null 複雜物件。 複雜物件的純量屬性可以是**null**。
-   複雜型別無法繼承自其他複雜型別。
-   您必須定義為複雜型別**類別**。 
-   EF 會偵測到變更的複雜型別物件的成員時**DbContext.DetectChanges**呼叫。 Entity Framework 會呼叫**DetectChanges**自動呼叫下列成員是： **DbSet.Find**， **DbSet.Local**， **DbSet.Remove**， **DbSet.Add**， **DbSet.Attach**， **DbContext.SaveChanges**， **DbContext.GetValidationErrors**，**DbContext.Entry**， **DbChangeTracker.Entries**。

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>將實體屬性重構至新的複雜型別

如果您已在您要將部分屬性重構到複雜型別屬性的概念模型實體。

在設計工具介面中，選取一或多個屬性 （不含導覽屬性） 的實體，然後以滑鼠右鍵按一下並選取**重構-&gt;移至新的複雜類型**。

![重構](~/ef6/media/refactor.png)

使用所選內容的新複雜型別新增至**模型瀏覽器**。 預設名稱會指定給該複雜型別。

新建立型別的複雜屬性會取代已選取的屬性。 所有屬性對應均會保留。

![Refactor2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>建立新的複雜型別

您也可以建立新的複雜型別不包含現有實體的屬性。

以滑鼠右鍵按一下**複雜型別**資料夾 模型瀏覽器中，指向**AddNew 複雜型別...**. 或者，您可以選取**複雜型別**資料夾，然後按**插入**鍵盤上。

![AddNewComplextype](~/ef6/media/addnewcomplextype.png)

含有預設名稱的新屬性隨即會加入到該複雜型別。 您現在可以將屬性加入類型。

## <a name="add-properties-to-a-complex-type"></a>將屬性加入至複雜型別

複雜型別屬性可以是純量型別或現有複雜型別。 不過，複雜型別屬性不能有循環參考。 例如，複雜型別**OnsiteCourseDetails**不能有複雜型別的屬性**OnsiteCourseDetails**。

您可以利用下列任何一種方法，將屬性加入至複雜型別。

-   以滑鼠右鍵按一下 模型瀏覽器中的複雜類型，指向**新增**，再指向**純量屬性**或**複雜屬性**，然後選取所需的屬性類型。 或者，您可以在這裡選取的複雜類型，然後按**插入**鍵盤上。  

    ![AddPropertiestoComplexType](~/ef6/media/addpropertiestocomplextype.png)

    含有預設名稱的新屬性隨即會加入至該複雜型別。

- 或-

-   以滑鼠右鍵按一下實體屬性**EF 設計工具**介面，然後選取**複製**，然後以滑鼠右鍵按一下複雜型別**模型瀏覽器**，然後選取**貼上**。

## <a name="rename-a-complex-type"></a>重新命名複雜型別

重新命名複雜型別時，所有型別的參考都會經由專案更新。

-   緩慢地按兩下 複雜型別**模型瀏覽器**。
    所選取的名稱會在編輯模式中。

- 或-

-   以滑鼠右鍵按一下 複雜型別**模型瀏覽器**，然後選取**重新命名**。

- 或-

-   選取 [模型瀏覽器] 中的 [複雜型別]，並按下 F2 鍵。

- 或-

-   以滑鼠右鍵按一下 複雜型別**模型瀏覽器**，然後選取**屬性**。 編輯中的名稱**屬性**視窗。

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>將現有的複雜型別新增至實體，並將它的屬性對應到資料表資料行

1.  以滑鼠右鍵按一下實體，指向**加入新**，然後選取**複雜屬性**。
    含有預設名稱的複雜型別屬性隨即會加入到該實體。 將預設型別 (從現有複雜型別選出) 指派至該屬性。
2.  中的屬性指派所需的型別**屬性**視窗。
    將複雜型別屬性加入到實體之後，必須將複雜型別屬性對應到資料表資料行。
3.  以滑鼠右鍵按一下設計介面上或在實體型別**模型瀏覽器**，然後選取**資料表對應**。
    資料表對應會顯示在**對應詳細資料**視窗。
4.  依序展開**對應至&lt;資料表名稱&gt;** 節點。
    A**資料行對應**節點隨即出現。
5.  依序展開**資料行對應**節點。
    資料表中所有資料行的清單隨即出現。 （如果有的話） 的預設屬性的資料行的對應會列在下**值/屬性**標題。
6.  選取您想要對應的資料行，然後以滑鼠右鍵按一下對應**值/屬性**欄位。
    所有純量屬性的下拉式清單便會顯示。
7.  選取適當的屬性。

    ![MapComplexType](~/ef6/media/mapcomplextype.png)

8.  針對每一個資料表資料行重複步驟 6 和 7。

>[!NOTE]
> 若要刪除的資料行對應，選取您想要將對應，然後按一下 資料行**值/屬性**欄位。 然後，選取**刪除**從下拉式清單。

## <a name="map-a-function-import-to-a-complex-type"></a>函式匯入對應到複雜型別

函式匯入是以預存程序為基礎。 若要將函式匯入對應到複雜型別，由對應預存程序傳回的資料行與此複雜型別的屬性數目必須相符，而且必須具有與屬性型別相容的儲存型別。

-   按兩下您想要對應至複雜型別匯入函式。

    ![FunctionImports](~/ef6/media/functionimports.png)

-   填入新函式匯入的設定，如下所示：
    -   指定您要為其建立中的之函式匯入的預存程序**預存程序名稱**欄位。 此欄位是下拉式清單，其中顯示儲存體模型中所有的預存程序。
    -   指定的名稱中的函式匯入**Function Import 名稱**欄位。
    -   選取 **複雜**做為傳回類型然後再從下拉式清單選擇適當的型別來指定特定的複雜傳回型別。

        ![EditFunctionImport](~/ef6/media/editfunctionimport.png)

-   按一下 [確定 **Deploying Office Solutions**]。
    這個函式匯入項目便會在概念模型中建立。

### <a name="customize-column-mapping-for-function-import"></a>自訂資料行對應的函式匯入

-   以滑鼠右鍵按一下 模型瀏覽器中的函式匯入，然後選取**函式匯入對應**。
    **對應詳細資料**視窗隨即出現並顯示函式匯入的預設對應。 箭頭是指示資料行值與屬性值之間的對應。 根據預設，資料行名稱是假設與此複雜型別的屬性名稱相同。 預設資料行名稱會以灰色文字顯示。
-   如有必要，可將資料行名稱變更為與對應函式匯入的預存程序所傳回的資料行名稱相同。

## <a name="delete-a-complex-type"></a>刪除複雜型別

刪除複雜型別時，是從概念模型中刪除該型別，且所有該型別所有執行個體的對應都會刪除。 但是，該型別的參考不會更新。 例如，如果實體有 ComplexType1 型別的複雜型別屬性，並在刪除 ComplexType1**模型瀏覽器**，不會更新對應的實體屬性。 因為它包含實體參考已刪除的複雜型別，將不會驗證模型。 您可以使用 Entity Designer 來更新或刪除已刪除之複雜型別的參考。

-   以滑鼠右鍵按一下 模型瀏覽器中的複雜類型，然後選取**刪除**。

- 或-

-   在 [模型瀏覽器] 中選取 [複雜型別]，然後按下鍵盤上的 Delete 鍵。

## <a name="query-for-entities-containing-properties-of-complex-type"></a>查詢實體包含複雜型別的屬性

下列程式碼示範如何執行可傳回實體集合包含複雜型別屬性的型別物件的查詢。

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
