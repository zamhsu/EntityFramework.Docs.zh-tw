---
title: 移轉具有多個專案的 EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 76e88dd486b1c53dc69a24e35710511bf9cb673b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997983"
---
<a name="using-a-separate-project"></a>使用個別的專案
========================
您可以將移轉儲存在不同的組件，比一個包含您`DbContext`。 您也可以使用此策略，以維護多組移轉，例如，一個用於開發，另一個版本到版本升級。

若要執行相關作業…

1. 建立新的類別庫。

2. 加入您的 DbContext 組件的參考。

3. 將移轉與模型快照集檔案移至 類別庫中。
   > [!TIP]
   > 如果您不有任何現有的移轉時，產生一個專案中包含 DbContext，則會移動它。 這很重要，因為如果移轉組件不包含現有的移轉，會找不到 DbContext 新增移轉命令。

4. 設定移轉組件：

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. 新增您移轉的組件中的啟動組件的參考。
   * 如果這導致循環相依性時，更新類別庫的輸出的路徑：

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

如果您正確做到，您應該能夠將新的移轉新增至專案。

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
