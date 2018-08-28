---
title: 關聯性-EF 設計工具-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: 72efe76956c930a787449e6cce453ab0317adc7c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994644"
---
# <a name="relationships---ef-designer"></a>關聯性-EF 設計工具
> [!NOTE]
> 此頁面會提供設定模型使用 EF 設計工具中的關聯性相關資訊。 如需 EF 以及如何存取和操作資料使用關聯性的關聯性的一般資訊，請參閱[關聯性和導覽屬性](~/ef6/fundamentals/relationships.md)。

關聯會定義模型中的實體類型之間的關聯性。 本主題說明如何將對應關聯與 Entity Framework Designer （EF 設計工具）。 下圖顯示使用 EF 設計工具時所使用的主視窗。

![EFDesigner](~/ef6/media/efdesigner.png)

> [!NOTE]
> 當您建置概念模型時，未對應的實體和關聯有關的警告可能會出現在 錯誤清單。 您可以忽略這些警告，因為錯誤您選擇從模型產生資料庫之後，就會消失。

## <a name="associations-overview"></a>關聯概觀

當您設計您的模型使用 EF 設計工具時，.edmx 檔案會代表您的模型。 在.edmx 檔案中，**關聯**項目會定義兩個實體類型之間的關聯性。 關聯 (Association) 必須指定關聯性 (relationship) 中的相關實體類型，以及關聯性每一端可能的實體類型數量 (也就是「多重性」)。 關聯 end 的多重性可以有值為一 (1)、 零或一個 (0..1)，或多個 (\*)。 這項資訊會指定在兩個子**結束**項目。

在執行階段，位於關聯某一端的實體類型執行個體可以存取透過導覽屬性或外部索引鍵 （如果您選擇要公開 （expose） 實體中的外部索引鍵）。 公開外部索引鍵，使用管理實體之間的關聯性**ReferentialConstraint**項目 (的子元素**關聯**項目)。 建議您一律公開實體中的外部索引鍵關聯性。

> [!NOTE]
> 在多對多 (\*:\*) 您無法將外部索引鍵新增至實體。 在  \*:\*關聯性，關聯資訊管理與獨立的物件。

如需 CSDL 元素 (**ReferentialConstraint**，**關聯**等等) 請參閱[CSDL 規格](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)。

## <a name="create-and-delete-associations"></a>建立和刪除關聯

建立關聯的 EF 設計工具更新.edmx 檔案的模型內容。 建立關聯之後, 您必須建立關聯 （在本主題稍後討論） 的對應。

> [!NOTE]
> 本節假設您已經新增您想要建立您的模型之間的關聯的實體。

### <a name="to-create-an-association"></a>建立關聯

1.  以滑鼠右鍵按一下設計介面的空白區域，指向**加入新**，然後選取**關聯...**.
2.  中的關聯的設定中填滿**新增關聯**對話方塊。

    ![AddAssociation](~/ef6/media/addassociation.png)

    > [!NOTE]
    > 您可以選擇不導覽屬性或外部索引鍵屬性加入至位於關聯兩端之實體的藉由清除 * * 導覽屬性 * * 和 * * 加入外部索引鍵的屬性，來&lt;實體類型名稱&gt;實體 * *核取方塊。 如果您只加入一個導覽屬性，則關聯只能單向周遊。 如果不加入任何導覽屬性，您必須選擇加入外部索引鍵屬性，才能存取關聯 End 的實體。
    
3.  按一下 [確定 **Deploying Office Solutions**]。

### <a name="to-delete-an-association"></a>若要刪除關聯

若要刪除關聯，執行下列其中一項：

-   以滑鼠右鍵按一下介面，然後選取 EF 設計工具上的關聯**刪除**。

- 或-

-   選取一個或多個關聯，然後按下 DELETE 鍵。

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>在您的實體 （參考條件約束） 包含外部索引鍵屬性

建議您一律公開實體中的外部索引鍵關聯性。 Entity Framework 會使用參考條件約束，來識別屬性，做為外部索引鍵關聯性。

如果您已核取***新增至外部索引鍵屬性&lt;實體類型名稱&gt;實體***核取方塊為您建立關聯時，已加入這個參考條件約束。

當您使用 EF 設計工具來新增或編輯參考條件約束時，EF 設計工具加入或修改**ReferentialConstraint** .edmx 檔的 CSDL 內容中的項目。

-   按兩下您要編輯的關聯。
    **參考條件約束** 對話方塊隨即出現。
-   從**主體**下拉式清單中，選取 條件約束中的 主要實體。
    實體索引鍵屬性加入**主體金鑰**在對話方塊中的清單。
-   從**相依**下拉式清單中，選取相依的實體條件約束中。
-   針對每個具有相依索引鍵的主體金鑰，請從中的下拉式清單中選取對應的相依索引鍵**相依索引鍵**資料行。

    ![RefConstraint](~/ef6/media/refconstraint.png)

-   按一下 [確定 **Deploying Office Solutions**]。

## <a name="create-and-edit-association-mappings"></a>建立和編輯關聯對應

您可以指定如何將關聯對應至資料庫中**對應詳細資料**的 EF 設計工具視窗。

> [!NOTE]
> 您只能對應沒有指定參考條件約束的關聯的詳細資料。 如果指定了參考條件約束，在實體中包含外部索引鍵屬性，以及您可以使用對應的詳細資料，來控制哪些資料行的外部索引鍵對應至實體。

### <a name="create-an-association-mapping"></a>建立關聯對應

-   以滑鼠右鍵按一下設計介面，並選取中的關聯**的資料表對應**。
    這會顯示在關聯對應**對應詳細資料**視窗。
-   按一下 **將資料表或檢視**。
    包含儲存體模型中所有資料表的下拉式清單便會出現。
-   選取此關聯將要對應的資料表。
    **對應詳細資料** 視窗會顯示在每個的關聯和實體類型的索引鍵屬性的兩端**結束**。
-   針對每個索引鍵的屬性，按一下**資料行**欄位，然後選取此屬性將要對應的資料行。

    ![MappingDetails4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>編輯關聯對應

-   以滑鼠右鍵按一下設計介面，並選取中的關聯**的資料表對應**。
    這會顯示在關聯對應**對應詳細資料**視窗。
-   按一下 **對應至&lt;資料表名稱&gt;**。
    包含儲存體模型中所有資料表的下拉式清單便會出現。
-   選取此關聯將要對應的資料表。
    **對應詳細資料**視窗會顯示在每一端的關聯和實體類型的索引鍵屬性的兩端。
-   針對每個索引鍵的屬性，按一下**資料行**欄位，然後選取此屬性將要對應的資料行。

## <a name="edit-and-delete-navigation-properties"></a>編輯和刪除導覽屬性

導覽屬性是用來尋找位於關聯兩端之實體模型中的捷徑屬性。 當您在兩個實體類型之間建立關聯時，可以建立導覽屬性。

#### <a name="to-edit-navigation-properties"></a>若要編輯導覽屬性

-   選取在 EF 設計工具介面上的瀏覽屬性。
    導覽屬性的相關資訊會顯示在 Visual Studio**屬性**視窗。
-   變更中的屬性設定**屬性**視窗。

#### <a name="to-delete-navigation-properties"></a>若要刪除導覽屬性

-   如果未在概念模型中的實體類型上公開外部索引鍵，刪除導覽屬性可能會使對應的關聯只能單向周遊，或者完全不能周遊。
-   以滑鼠右鍵按一下 瀏覽屬性在 EF 設計工具介面，然後選取**刪除**。
