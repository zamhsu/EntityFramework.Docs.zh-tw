---
title: 列舉支援-EF Designer-EF6
description: 列舉支援-Entity Framework 6 中的 EF 設計工具
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620511"
---
# <a name="enum-support---ef-designer"></a>列舉支援-EF 設計工具
> [!NOTE]
> **EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

這段影片和逐步解說會示範如何搭配 Entity Framework Designer 使用列舉類型。 它也會示範如何在 LINQ 查詢中使用列舉。

本逐步解說會使用 Model First 來建立新的資料庫，但 EF 設計工具也可以與 [Database First](xref:ef6/modeling/designer/workflows/database-first) 工作流程搭配使用，以對應至現有的資料庫。

列舉支援是在 Entity Framework 5 中引進。 若要使用列舉、空間資料類型和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。 Visual Studio 2012 預設會以 .NET 4.5 為目標。

在 Entity Framework 中，列舉可以具有下列基礎類型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。

## <a name="watch-the-video"></a>觀賞影片
這段影片示範如何搭配 Entity Framework Designer 使用列舉類型。 它也會示範如何在 LINQ 查詢中使用列舉。

**提供者**： Julia Kornich

**影片**： [wmv](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)的  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>必要條件

您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  **在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。
3.  在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本
4.  輸入 **EnumEFDesigner** 做為專案的名稱，然後按一下 **[確定]**

## <a name="create-a-new-model-using-the-ef-designer"></a>使用 EF 設計工具建立新的模型

1.  在方案總管中的專案名稱上按一下滑鼠右鍵，指向 [**加入**]，然後按一下 [**新增專案**]。
2.  從左側功能表中選取 [ **資料** ]，然後選取 [範本] 窗格中的 [ **ADO.NET 實體資料模型** ]
3.  輸入**EnumTestModel**的檔案名，然後按一下 [**新增**]。
4.  在實體資料模型 Wizard] 頁面的 [選擇模型內容] 對話方塊中，選取 [ **空的模型** ]。
5.  按一下 [完成]

隨即會顯示 Entity Designer，其提供用來編輯模型的設計介面。

精靈會執行下列動作：

-   產生定義概念模型、儲存體模型和兩者之間對應的 EnumTestModel .edmx 檔。 設定 .edmx 檔的中繼資料成品處理屬性，以內嵌在輸出元件中，以便將產生的中繼資料檔案內嵌至元件。
-   將參考加入至下列元件： EntityFramework、ComponentModel DataAnnotations 和 System.object。
-   建立 EnumTestModel.tt 和 EnumTestModel.CoNtext.tt 檔案，並將它們新增至 .edmx 檔案底下。 這些 T4 範本檔產生的程式碼會定義 DbCoNtext 衍生型別，以及對應至 .edmx 模型中之實體的 POCO 型別。

## <a name="add-a-new-entity-type"></a>加入新的實體類型

1.  以滑鼠右鍵按一下設計介面的空白區域，然後選取 [ **加入 &gt; 實體**]，[新增實體] 對話方塊隨即出現。
2.  指定類型名稱的 **部門** ，並指定索引鍵屬性名稱的 **DepartmentID** ，將類型保留為 **Int32**
3.  按一下 [檔案] &gt; [新增] &gt; [專案] 
4.  以滑鼠右鍵按一下實體，然後選取 [**加入 &gt; 新的-** 純量屬性]
5.  將新屬性重新命名為 **Name**
6.  根據預設，將新屬性的類型變更為 **Int32** (，新屬性的字串類型) 若要變更類型，請開啟屬性視窗，然後將 type 屬性變更為 **Int32**
7.  新增另一個純量屬性，並將其重新命名為 **預算**，將類型變更為 **Decimal**

## <a name="add-an-enum-type"></a>新增列舉型別

1.  在 Entity Framework Designer 中，以滑鼠右鍵按一下 [名稱] 屬性，然後選取 [**轉換為列舉**]

    ![轉換成列舉](~/ef6/media/converttoenum.png)

2.  在 [ **加入列舉** ] 對話方塊中，輸入 **DepartmentNames** 作為列舉型別名稱，將基礎類型變更為 **Int32**，然後將下列成員加入至類型：英文、數學和經濟效益

    ![新增列舉類型](~/ef6/media/addenumtype.png)

3.  按下 [確定]****
4.  儲存模型並建立專案
    > [!NOTE]
    > 當您建立時，有關未對應實體和關聯的警告可能會出現在 [錯誤清單] 中。 您可以忽略這些警告，因為在我們選擇從模型產生資料庫之後，錯誤就會消失。

如果您查看屬性視窗，您會發現 Name 屬性的型別已變更為 **DepartmentNames** ，而新增的列舉型別已加入至類型清單。

如果您切換到 [模型瀏覽器] 視窗，您將會看到類型也已加入至 [列舉類型] 節點。

![模型瀏覽器](~/ef6/media/modelbrowser.png)

>[!NOTE]
> 您也可以在此視窗中加入新的列舉類型，方法是按一下滑鼠右鍵，然後選取 [ **加入列舉類型**]。 一旦建立型別，它就會出現在類型清單中，而且您可以與屬性產生關聯。

## <a name="generate-database-from-model"></a>從模型產生資料庫

現在我們可以產生以模型為基礎的資料庫。

1.  以滑鼠右鍵按一下 Entity Designer 介面上的空白處，然後選取 [**從模型產生資料庫**]
2.  [產生資料庫嚮導] 的 [選擇您的資料連線] 對話方塊隨即顯示，請按一下 [**新增連接**] 按鈕，為資料庫的伺服器名稱和**EnumTest**指定** (localdb) \\ Mssqllocaldb** ，然後按一下 **[確定]** 。
3.  對話方塊會詢問您是否要建立新的資料庫，請按一下 **[是**]。
4.  按一下 **[下一步** ]，[建立資料庫嚮導] 會產生資料定義語言 (DDL) 若要建立資料庫，則會在 [摘要和設定] 對話方塊中顯示產生的 DDL，請注意，ddl 不包含對應至列舉類型之資料表的定義
5.  按一下 **[** 完成]，然後按一下 [完成]，就不會執行 DDL 腳本。
6.  Create Database Wizard 會執行下列動作：在 T-sql 編輯器中開啟 **EnumTest** 會產生儲存架構，而 edmx 檔案的對應區段會將連接字串資訊加入 App.config 檔案
7.  在 T-sql 編輯器中按一下滑鼠右鍵，然後選取 [**執行**連接至伺服器] 對話方塊，輸入步驟2中的連接資訊，然後按一下 **[連接]** 。
8.  若要查看產生的架構，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵，**然後選取 [** 重新整理]。

## <a name="persist-and-retrieve-data"></a>保存和取出資料

開啟定義 Main 方法的 Program.cs 檔案。 將下列程式碼新增至 Main 函數。 程式碼會將新的部門物件新增至內容。 然後，它會儲存資料。 程式碼也會執行 LINQ 查詢，該查詢會傳回名稱為 DepartmentNames 的部門。

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

編譯並執行應用程式。 此程式會產生下列輸出：

```console
DepartmentID: 1 Name: English
```

若要在資料庫中查看資料，請在 SQL Server 物件總管中的資料庫名稱上按一下滑鼠右鍵， **然後選取 [** 重新整理]。 然後，按一下資料表上的滑鼠右鍵，然後選取 [ **View Data**]。

## <a name="summary"></a>摘要

在這個逐步解說中，我們探討了如何使用 Entity Framework Designer 來對應列舉型別，以及如何在程式碼中使用列舉。 
