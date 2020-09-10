---
title: 關聯性-EF Designer-EF6
description: 關聯性-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: f7c801dd8f8ae81cfe44283b7575cfe869ce26d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620419"
---
# <a name="relationships---ef-designer"></a>關聯性-EF 設計工具
> [!NOTE]
> 此頁面提供使用 EF 設計工具在模型中設定關聯性的相關資訊。 如需 EF 中關聯性的一般資訊，以及如何使用關聯性存取和運算元據，請參閱 [關聯性 & 導覽屬性](xref:ef6/fundamentals/relationships)。

關聯會定義模型中實體類型之間的關聯性。 本主題說明如何將關聯與 Entity Framework Designer (EF 設計工具) 對應。 下圖顯示使用 EF 設計工具時使用的主視窗。

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> 建置概念模型時，與未對應之實體和關聯有關的警告可能會在 [錯誤清單] 中出現。 您可以忽略這些警告，因為在您選擇從模型產生資料庫之後，錯誤就會消失。

## <a name="associations-overview"></a>關聯總覽

當您使用 EF 設計工具來設計模型時，.edmx 檔案會代表您的模型。 在 .edmx 檔案中，Association 元素會定義兩個實體類型之間的 **關聯** 性。 關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。 關聯 end 的多重性可以有一個 (1) 、零個或一個 (0 ..1) 或許多 () 中的值。 \* 這項資訊是在兩個 **子項目** 中指定。

在執行時間，您可以透過導覽屬性或外鍵來存取位於關聯某一端的實體類型實例， (如果您選擇在實體中公開外鍵) 。 公開外鍵之後，實體之間的關聯性會以 **ReferentialConstraint** 專案來管理， (**Association** 元素) 的子項目。 建議您一律公開實體中關聯性的外鍵。

> [!NOTE]
> 在多對多 (\* ： \*) 您無法將外鍵加入至實體。 在 \* ：關聯性中 \* ，會使用獨立物件來管理關聯資訊。

如需 CSDL 元素 (**ReferentialConstraint**、 **關聯**等的詳細資訊，) 查看 [csdl 規格](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)。

## <a name="create-and-delete-associations"></a>建立和刪除關聯

使用 EF 設計工具建立關聯會更新 .edmx 檔案的模型內容。 建立關聯之後，您必須為此主題稍後討論的關聯建立對應 () 。

> [!NOTE]
> 本節假設您已新增您想要在模型之間建立關聯的實體。

### <a name="to-create-an-association"></a>建立關聯

1.  以滑鼠右鍵按一下設計介面的空白區域，指向 [ **加入新**的]，然後選取 [ **關聯 ...**]。
2.  在 [ **加入關聯** ] 對話方塊中，填入關聯的設定。

    ![加入關聯](~/ef6/media/addassociation.png)

    > [!NOTE]
    > 您可以藉由清除 **導覽屬性 **，並 **將外鍵屬性加入至 &lt; 實體類型名稱 &gt; 實體 **核取方塊，來選擇不要將導覽屬性或外鍵屬性加入至關聯兩端的實體。 如果您只加入一個導覽屬性，則關聯只能單向周遊。 如果不加入任何導覽屬性，您必須選擇加入外部索引鍵屬性，才能存取關聯 End 的實體。
    
3.  按一下 **[確定]**。

### <a name="to-delete-an-association"></a>若要刪除關聯

若要刪除關聯，請執行下列其中一項：

-   以滑鼠右鍵按一下 EF 設計工具介面上的關聯，然後選取 [ **刪除**]。

- 或 -

-   選取一個或多個關聯，然後按下 DELETE 鍵。

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>在您的實體中包含外鍵屬性 (參考條件約束) 

建議您一律公開實體中關聯性的外鍵。 Entity Framework 使用參考條件約束來識別某個屬性做為關聯性的外鍵。

如果您在建立關聯性時，核取 [ ***將外鍵屬性加入至 &lt; 實體類型名稱 &gt; 實體*** ] 核取方塊，就會為您加入這個參考條件約束。

當您使用 EF Designer 加入或編輯參考條件約束時，EF 設計工具會在 .edmx 檔的 CSDL 內容中加入或修改**ReferentialConstraint**   元素。

-   按兩下您要編輯的關聯。
    [ **參考條件約束**   ] 對話方塊隨即出現。
-   從 [ **主體**]   下拉式清單中，選取參考條件約束中的主體實體。
    實體的索引鍵屬性會加入至對話方塊中的 [ **主要索引鍵**   ] 清單。
-   從 **相依**的   下拉式清單中，選取參考條件約束中的相依實體。
-   針對每個具有相依索引鍵的主體索引鍵，從 **相依索引鍵**資料行的下拉式清單中選取對應的相依索引鍵   。

    ![Ref 條件約束](~/ef6/media/refconstraint.png)

-   按一下 **[確定]**。

## <a name="create-and-edit-association-mappings"></a>建立和編輯關聯對應

您可以在 EF Designer 的 [**對應詳細資料**] 視窗中，指定關聯如何對應到資料庫   。

> [!NOTE]
> 您只能對應未指定參考條件約束之關聯的詳細資料。 如果指定了參考條件約束，則實體中包含外鍵屬性，而且您可以使用實體的對應詳細資料來控制外鍵所對應的資料行。

### <a name="create-an-association-mapping"></a>建立關聯對應

-   以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。
    這會在 [ **對應詳細資料**] 視窗中顯示關聯對應   。
-   按一下 [ **加入資料表或視圖**]。
    包含儲存體模型中所有資料表的下拉式清單便會出現。
-   選取此關聯將要對應的資料表。
    [ **對應詳細資料**   ] 視窗會顯示關聯的兩端，以及每**一端**實體類型的索引鍵屬性。
-   針對每個索引鍵屬性，按一下 [資料 **行**]   欄位，然後選取屬性將對應的資料行。

    ![對應詳細資料4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>編輯關聯對應

-   以滑鼠右鍵按一下設計介面中的關聯，然後選取 [ **資料表對應**]。
    這會在 [ **對應詳細資料**] 視窗中顯示關聯對應   。
-   按一下 [ **對應至 &lt; 資料表 &gt; 名稱**]。
    包含儲存體模型中所有資料表的下拉式清單便會出現。
-   選取此關聯將要對應的資料表。
    [ **對應詳細資料**   ] 視窗會顯示關聯的兩端，以及每一端實體類型的索引鍵屬性。
-   針對每個索引鍵屬性，按一下 [資料 **行**]   欄位，然後選取屬性將對應的資料行。

## <a name="edit-and-delete-navigation-properties"></a>編輯和刪除導覽屬性

導覽屬性是用來在模型中的關聯端點上找出實體的快捷方式屬性。 當您在兩個實體類型之間建立關聯時，可以建立導覽屬性。

#### <a name="to-edit-navigation-properties"></a>編輯導覽屬性

-   選取 EF 設計工具介面上的導覽屬性。
    導覽屬性的相關資訊會顯示在 [Visual Studio **屬性**   ] 視窗中。
-   變更 [ **屬性**] 視窗中的屬性設定   。

#### <a name="to-delete-navigation-properties"></a>刪除導覽屬性

-   如果未在概念模型中的實體類型上公開外部索引鍵，刪除導覽屬性可能會使對應的關聯只能單向周遊，或者完全不能周遊。
-   以滑鼠右鍵按一下 EF 設計工具介面上的導覽屬性，然後選取 [ **刪除**]。
