---
title: 列舉支援-Code First-EF6
description: 列舉支援-Entity Framework 6 中的 Code First
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: 2ed5cd73f8477469d3651ef4bf9f79ce80598ee7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617168"
---
# <a name="enum-support---code-first"></a>列舉支援-Code First
> [!NOTE]
> **EF5 只** 會提供此 Entity Framework 頁面中所討論的功能、api 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。

這段影片和逐步解說會示範如何搭配 Entity Framework Code First 來使用列舉類型。 它也會示範如何在 LINQ 查詢中使用列舉。

本逐步解說會使用 Code First 來建立新的資料庫，但您也可以使用 [Code First 來對應至現有的資料庫](xref:ef6/modeling/code-first/workflows/existing-database)。

列舉支援是在 Entity Framework 5 中引進。 若要使用列舉、空間資料類型和資料表值函式之類的新功能，您必須將目標設為 .NET Framework 4.5。 Visual Studio 2012 預設會以 .NET 4.5 為目標。

在 Entity Framework 中，列舉可以具有下列基礎類型： **Byte**、 **Int16**、 **Int32**、 **Int64** 或 **SByte**。

## <a name="watch-the-video"></a>觀賞影片
這段影片示範如何使用列舉類型搭配 Entity Framework Code First。 它也會示範如何在 LINQ 查詢中使用列舉。

**提供者**： Julia Kornich

**影片**： [wmv](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)的  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [wmv .wmv (ZIP) ](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>必要條件

您必須安裝 Visual Studio 2012、旗艦版、Premium、Professional 或 Web Express edition，才能完成此逐步解說。

 

## <a name="set-up-the-project"></a>設定專案

1.  開啟 Visual Studio 2012
2.  **在 [檔案**] 功能表上，指向 [**新增**]，然後按一下 [**專案**]。
3.  在左窗格中，按一下 [ **Visual \# C**]，然後選取**主控台**範本
4.  輸入 **EnumCodeFirst** 做為專案的名稱，然後按一下 **[確定]**

## <a name="define-a-new-model-using-code-first"></a>使用 Code First 定義新模型

使用 Code First 開發時，通常會先撰寫 .NET Framework 類別，以定義您的概念 (網域) 模型。 下列程式碼會定義部門類別。

程式碼也會定義 DepartmentNames 列舉。 根據預設，列舉是 **int** 類型。 部門類別的 Name 屬性屬於 DepartmentNames 型別。

開啟 Program.cs 檔案，並貼上下列類別定義。

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
 

## <a name="define-the-dbcontext-derived-type"></a>定義 DbCoNtext 衍生類型

除了定義實體之外，您還必須定義衍生自 DbCoNtext 的類別，並公開 DbSet &lt; TEntity &gt; 屬性。 DbSet &lt; TEntity &gt; 屬性可讓內容知道您想要包含在模型中的類型。

DbCoNtext 衍生類型的實例會在運行時間管理實體物件，包括將資料庫的資料填入物件、變更追蹤，以及將資料保存到資料庫。

DbCoNtext 和 DbSet 類型是在 EntityFramework 元件中定義。 我們會使用 EntityFramework NuGet 封裝來新增此 DLL 的參考。

1.  在方案總管中，以滑鼠右鍵按一下專案名稱。
2.  選取 [**管理 NuGet 套件 ...** ]
3.  在 [管理 NuGet 封裝] 對話方塊中，選取 [ **線上** ] 索引標籤，然後選擇 **EntityFramework** 套件。
4.  按一下 [安裝]

請注意，除了 EntityFramework 元件之外，也會加入 ComponentModel. DataAnnotations 和 System.object 元件的參考。

在 Program.cs 檔案的頂端，新增下列 using 語句：

``` csharp
using System.Data.Entity;
```

在 Program.cs 中加入內容定義。 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>保存和取出資料

開啟定義 Main 方法的 Program.cs 檔案。 將下列程式碼新增至 Main 函數。 程式碼會將新的部門物件新增至內容。 然後，它會儲存資料。 程式碼也會執行 LINQ 查詢，該查詢會傳回名稱為 DepartmentNames 的部門。

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
 

## <a name="view-the-generated-database"></a>查看產生的資料庫

當您第一次執行應用程式時，Entity Framework 會為您建立資料庫。 因為我們安裝了 Visual Studio 2012，所以會在 LocalDB 實例上建立資料庫。 根據預設，Entity Framework 會在衍生 (內容的完整名稱之後，將資料庫命名為此範例中的 **EnumCodeFirst. EnumTestCoNtext**) 。 將會使用現有資料庫的後續時間。  

請注意，如果您在建立資料庫之後對模型進行任何變更，則應該使用 Code First 移轉來更新資料庫架構。 如需使用遷移的範例，請參閱 [Code First 至新的資料庫](xref:ef6/modeling/code-first/workflows/new-database) 。

若要查看資料庫和資料，請執行下列動作：

1.  在 [Visual Studio 2012] 主功能表中，選取 [ **View**  - &gt; **SQL Server 物件總管**。
2.  如果 LocalDB 不在伺服器清單中，請在 **SQL Server** 上按一下滑鼠右鍵，然後選取 [ **加入] SQL Server** 使用預設的 **Windows 驗證** 連接到 LocalDB 實例
3.  展開 LocalDB 節點
4.  展開 [ **資料庫** ] 資料夾以查看新的資料庫，並流覽至 **部門** 資料表附注，那 Code First 不會建立對應至列舉類型的資料表。
5.  若要查看資料，請以滑鼠右鍵按一下資料表，然後選取 [ **View data** ]

## <a name="summary"></a>摘要

在本逐步解說中，我們探討了如何搭配 Entity Framework Code First 來使用列舉類型。 
