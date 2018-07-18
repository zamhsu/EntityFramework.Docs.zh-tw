---
title: 列舉支援-Code First-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
caps.latest.revision: 3
ms.openlocfilehash: 698c84a9f0679c67a086574de2f253f214fb8d0b
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2018
ms.locfileid: "39120462"
---
# <a name="enum-support---code-first"></a>列舉支援-Code First
> [!NOTE]
> **EF5 及更新版本僅**-功能、 Api、 Entity Framework 5 中導入等本頁所述。 如果您使用的是較早版本，則不適用部分或全部的資訊。

本影片以及逐步說明的逐步解說會示範如何使用 Entity Framework Code First 使用列舉型別。 它也會示範如何使用 LINQ 查詢中的列舉。

本逐步解說將使用 Code First 來建立新的資料庫，但您也可以使用[Code First 至對應到現有的資料庫](~/ef6/modeling/code-first/workflows/existing-database.md)。

在 Entity Framework 5 中引進了列舉支援。 若要使用新的功能，例如列舉、 空間資料類型和資料表值函式，您必須以.NET Framework 4.5 為目標。 Visual Studio 2012 的預設值為目標.NET 4.5。

在 Entity Framework 列舉型別可以有下列的基礎類型：**位元組**， **Int16**， **Int32**， **Int64** ，或**SByte**。

## <a name="watch-the-video"></a>觀看影片
這段影片示範如何使用 Entity Framework Code First 使用列舉型別。 它也會示範如何使用 LINQ 查詢中的列舉。

**由**: Julia Kornich

**視訊**: [WMV](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>必要條件

您必須擁有 Visual Studio 2012、 Ultimate、 Premium、 Professional 或 Web Express edition 安裝才能完成此逐步解說。

 

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  在 **檔案**功能表上，指向**新增**，然後按一下 **專案**
3.  在左窗格中，按一下**Visual C\#**，然後選取**主控台**範本
4.  請輸入**EnumCodeFirst**做為名稱的專案，然後按一下 **[確定]**

## <a name="define-a-new-model-using-code-first"></a>定義新的模型使用 Code First

使用 Code First 開發時您通常會開始撰寫定義概念 （網域） 模型的.NET Framework 類別。 下列程式碼定義部門類別。

程式碼也會定義 DepartmentNames 列舉型別。 根據預設，列舉型別屬於**int**型別。 部門類別上的 Name 屬性是 DepartmentNames 型別。

開啟 Program.cs 檔案並貼上下列類別定義。

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a>定義 DbContext 衍生型別

除了定義實體，您需要定義類別，衍生自 DbContext，並公開 DbSet&lt;TEntity&gt;屬性。 DbSet&lt;TEntity&gt;屬性可讓知道您想要在模型中包含哪些的類型的內容。

DbContext 衍生型別的執行個體在執行階段，其中包含填入資料庫的資料的物件期間同時管理實體物件、 變更追蹤，和保存資料至資料庫。

EntityFramework 組件中定義的 DbContext 和 DbSet 型別。 我們將使用 EntityFramework NuGet 套件，以新增此 DLL 的參考。

1.  在 [方案總管] 中，以滑鼠右鍵按一下專案名稱。
2.  選取**管理 NuGet 封裝...**
3.  在 [管理 NuGet 套件] 對話方塊中，選取**線上**索引標籤，然後選擇**EntityFramework**封裝。
4.  按一下 **安裝**

請注意，除了 EntityFramework 組件參考 System.ComponentModel.DataAnnotations 和 System.Data.Entity 組件會新增以及。

在 Program.cs 檔案頂端，新增下列 using 陳述式：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中新增內容定義。 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>持續儲存並擷取資料

開啟 Program.cs 檔案的 Main 方法定義的位置。 將下列程式碼新增至 Main 函式。 程式碼會將新的部門物件加入內容。 然後，它會儲存資料。 程式碼也會執行可傳回的部門，其中 name 是 DepartmentNames.English 的 LINQ 查詢。

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

編譯並執行應用程式。 此程式會產生下列輸出：

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>檢視所產生的資料庫

當您執行應用程式第一次時，Entity Framework 會為您建立資料庫。 因為我們沒有安裝 Visual Studio 2012，則會在 LocalDB 執行個體上建立資料庫。 根據預設，Entity Framework 資料庫的名稱衍生內容的完整名稱 (此範例中是針對**EnumCodeFirst.EnumTestContext**)。 後續的時間，將會使用現有的資料庫。  

請注意，是否您的模型建立資料庫之後，進行任何變更，您應該使用 Code First 移轉來更新資料庫結構描述。 請參閱[Code First 至新的資料庫](~/ef6/modeling/code-first/workflows/new-database.md)如使用移轉的範例。

若要檢視的資料庫和資料，請執行下列作業：

1.  在 Visual Studio 2012 主功能表中，選取**檢視** - &gt; **SQL Server 物件總管**。
2.  如果 LocalDB 不在伺服器清單中，按一下滑鼠右鍵**SQL Server** ，然後選取**加入 SQL Server**使用預設值**Windows 驗證**連接到LocalDB 執行個體
3.  展開 [LocalDB] 節點
4.  Unfold**資料庫**查看新的資料庫，並瀏覽至資料夾**部門**資料表注意，Code First 不會建立對應至列舉類型的資料表
5.  若要檢視資料，以滑鼠右鍵按一下資料表並選取**檢視資料**

## <a name="summary"></a>總結

在此逐步解說中，我們討論過如何使用 Entity Framework Code First 使用列舉型別。 
