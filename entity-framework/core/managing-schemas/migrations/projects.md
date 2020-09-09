---
title: 使用個別的遷移專案-EF Core
description: 使用不同的遷移專案來管理具有 Entity Framework Core 的資料庫架構
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617909"
---
# <a name="using-a-separate-migrations-project"></a>使用個別的遷移專案

您可能會想要將您的遷移儲存在與包含的元件不同的元件中 `DbContext` 。 您也可以使用這項策略來維護多個遷移集，例如一個用於開發，另一個用於發行到發行的升級。

作法...

1. 建立新的類別庫。

2. 新增 DbCoNtext 元件的參考。

3. 將遷移和模型快照集檔案移至類別庫。
   > [!TIP]
   > 如果您沒有任何現有的遷移，請在包含 DbCoNtext 的專案中產生一個，然後將它移動。
   > 這點很重要，因為如果遷移元件不包含現有的遷移，則 [新增-遷移] 命令將無法找到 DbCoNtext。

4. 設定遷移元件：

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. 從啟動元件加入您的遷移元件的參考。
   * 如果這會造成迴圈相依性，請更新類別庫的輸出路徑：

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

如果一切都正確，您應該能夠將新的遷移新增至專案。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
