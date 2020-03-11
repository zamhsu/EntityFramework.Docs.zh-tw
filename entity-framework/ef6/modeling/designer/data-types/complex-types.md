---
title: 複雜類型-EF 設計工具-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
ms.openlocfilehash: a3c5578acee23688c04772d2dd0a2221779af562
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418648"
---
# <a name="complex-types---ef-designer"></a>複雜類型-EF 設計工具
本主題說明如何使用 Entity Framework Designer （EF Designer）對應複雜型別，以及如何查詢包含複雜類型屬性的實體。

下圖顯示使用 EF 設計工具時的主要視窗。

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> 建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。 您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。

## <a name="what-is-a-complex-type"></a>什麼是複雜型別

複雜型別是實體型別的非純量屬性，可讓純量屬性得以在實體內組織。 如同實體，複雜類型包含純量屬性或其他複雜類型屬性。

當您使用代表複雜類型的物件時，請注意下列事項：

-   複雜型別沒有索引鍵，因此無法獨立存在。 複雜型別只能以實體類型或其他複雜型別的屬性形式存在。
-   複雜類型不能參與關聯，也不能包含導覽屬性。
-   複雜型別屬性不可以是 **null**。 呼叫**DbCoNtext** 時，如果遇到 null 複雜物件，就會發生**InvalidOperationException **。 複雜物件的純量屬性可以是 **null**。
-   複雜型別無法繼承自其他複雜型別。
-   您必須將複雜型別定義為 **類別**。 
-   呼叫**DbCoNtext. DetectChanges**時，EF 會偵測複雜類型物件上的成員變更。 當呼叫下列成員時，Entity Framework 會自動呼叫**DetectChanges** ： **DbSet. Find**、 **DbSet**、 **DbSet. Remove**、 **DbSet. Add**、DbSet、 **Attach**、 **DbCoNtext. SaveChanges**、 **DbCoNtext**、GetValidationErrors、 **DbCoNtext.** **Entry**。

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>將實體的屬性重構為新的複雜型別

如果您的概念模型中已經有實體，您可能會想要將某些屬性重構成複雜型別屬性。

在設計工具介面上，選取實體的一個或多個屬性（不包括導覽屬性），然後以滑鼠右鍵按一下並選取 [ **重構-&gt; 移至新的複雜類型**]。

![重構](~/ef6/media/refactor.png)

具有所選屬性的新複雜型別會加入至 [**模型瀏覽器**] 中。 預設名稱會指定給該複雜型別。

新建立型別的複雜屬性會取代已選取的屬性。 所有屬性對應均會保留。

![重構2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>建立新的複雜類型

您也可以建立不包含現有實體之屬性的新複雜型別。

以滑鼠右鍵按一下 [模型瀏覽器] 中的 [**複雜類型**] 資料夾，指向 [ **AddNew 複雜類型 ...** ]。 或者，您也可以選取 **複雜類型** 資料夾，然後按下鍵盤上的  **插入** 鍵。

![加入新的複雜類型](~/ef6/media/addnewcomplextype.png)

含有預設名稱的新屬性隨即會加入到該複雜型別。 您現在可以將屬性加入至類型。

## <a name="add-properties-to-a-complex-type"></a>將屬性加入至複雜型別

複雜型別屬性可以是純量型別或現有複雜型別。 不過，複雜型別屬性不能有循環參考。 例如，複雜型別 **OnsiteCourseDetails** 不能有複雜型別 **OnsiteCourseDetails**的屬性。

您可以利用下列任何一種方法，將屬性加入至複雜型別。

-   以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，指向 [ **加入**]，然後指向 [純量 **屬性**] 或 [ **複雜屬性**]，然後選取所需的屬性類型。 或者，您也可以選取複雜型別，然後按下鍵盤上的 **插入** 鍵。  

    ![將屬性加入至複雜型別](~/ef6/media/addpropertiestocomplextype.png)

    含有預設名稱的新屬性隨即會加入至該複雜型別。

- 或 -

-   以滑鼠右鍵按一下**EF 設計**工具介面上的實體屬性，然後選取 [ **複製**]，再以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **貼**上]。

## <a name="rename-a-complex-type"></a>重新命名複雜型別

重新命名複雜型別時，所有型別的參考都會經由專案更新。

-   在**模型瀏覽器**中，以緩慢的方式按兩下複雜型別。
    所選取的名稱會在編輯模式中。

- 或 -

-   以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **重新命名**]。

- 或 -

-   選取 [模型瀏覽器] 中的 [複雜型別]，並按下 F2 鍵。

- 或 -

-   以滑鼠右鍵按一下 [**模型瀏覽器**] 中的複雜類型，然後選取 [ **屬性**]。 在 [ **屬性** ] 視窗中編輯名稱。

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>將現有的複雜型別加入至實體，並將其屬性對應至資料表資料行

1.  以滑鼠右鍵按一下實體，指向 [ **加入新**的]，然後選取 [ **複雜屬性**]。
    含有預設名稱的複雜型別屬性隨即會加入到該實體。 將預設型別 (從現有複雜型別選出) 指派至該屬性。
2.  在 [ **屬性** ] 視窗中，將所需的類型指派給屬性。
    將複雜型別屬性加入到實體之後，必須將複雜型別屬性對應到資料表資料行。
3.  以滑鼠右鍵按一下設計介面上的實體類型，或在 **模型瀏覽器**中 並選取 [ **資料表**對應]。
     **對應詳細資料**  視窗中會顯示資料表對應。
4.  展開  **對應 以 &lt;資料表名稱&gt;**   節點。
    [資料 **行**對應 ] 節點隨即出現。
5.  展開 [資料 **行**對應 ] 節點。
    資料表中所有資料行的清單隨即出現。 資料行對應的預設屬性（如果有的話）會列在 [ **值/屬性**] 標題底下。
6.  選取您要對應的資料行，然後以滑鼠右鍵按一下對應的 [ **值/屬性** ] 欄位。
    所有純量屬性的下拉式清單便會顯示。
7.  選取適當的屬性。

    ![對應複雜類型](~/ef6/media/mapcomplextype.png)

8.  針對每一個資料表資料行重複步驟 6 和 7。

>[!NOTE]
> 若要刪除資料行對應，請選取您要對應的資料行，然後按一下 [ **值/屬性** ] 欄位。 然後，從下拉式清單中選取 [**刪除**]。

## <a name="map-a-function-import-to-a-complex-type"></a>將函式匯入對應到複雜類型

函式匯入是以預存程序為基礎。 若要將函式匯入對應到複雜型別，由對應預存程序傳回的資料行與此複雜型別的屬性數目必須相符，而且必須具有與屬性型別相容的儲存型別。

-   按兩下您想要對應至複雜類型的匯入函數。

    ![函式匯入](~/ef6/media/functionimports.png)

-   填入新函式匯入的設定，如下所示：
    -   在 [ **預存程式名稱** ] 欄位中，指定您要在其中建立函數匯入的預存程式。 此欄位是下拉式清單，其中顯示儲存體模型中所有的預存程序。
    -   在 [函式匯 **入名稱** ] 欄位中，指定函式匯入的名稱。
    -   選取 [ **複雜** ] 做為傳回類型，然後從下拉式清單中選擇適當的類型，以指定特定的複雜傳回類型。

        ![編輯函式匯入](~/ef6/media/editfunctionimport.png)

-   按一下 **[確定]** 。
    這個函式匯入項目便會在概念模型中建立。

### <a name="customize-column-mapping-for-function-import"></a>自訂函式匯入的資料行對應

-   以滑鼠右鍵按一下 [模型瀏覽器] 中的函式匯入，然後選取 [ **函數匯入對應**]。
     **對應詳細資料**  視窗隨即出現，並顯示函式匯入的預設對應。 箭頭是指示資料行值與屬性值之間的對應。 根據預設，資料行名稱是假設與此複雜型別的屬性名稱相同。 預設資料行名稱會以灰色文字顯示。
-   如有必要，可將資料行名稱變更為與對應函式匯入的預存程序所傳回的資料行名稱相同。

## <a name="delete-a-complex-type"></a>刪除複雜型別

刪除複雜型別時，是從概念模型中刪除該型別，且所有該型別所有執行個體的對應都會刪除。 但是，該型別的參考不會更新。 例如，如果實體具有類型為 ComplexType1 的複雜類型屬性，而且在**模型瀏覽器**中刪除了 ComplexType1，則不會更新對應的實體屬性。 模型將不會驗證，因為它包含參考已刪除之複雜類型的實體。 您可以使用 Entity Designer 來更新或刪除已刪除之複雜型別的參考。

-   以滑鼠右鍵按一下 [模型瀏覽器] 中的複雜類型，然後選取 [ **刪除**]。

- 或 -

-   在 [模型瀏覽器] 中選取 [複雜型別]，然後按下鍵盤上的 Delete 鍵。

## <a name="query-for-entities-containing-properties-of-complex-type"></a>查詢包含複雜類型屬性的實體

下列程式碼會示範如何執行查詢，以傳回包含複雜型別屬性之實體型別物件的集合。

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
