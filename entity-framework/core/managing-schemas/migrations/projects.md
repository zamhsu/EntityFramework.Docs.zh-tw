---
title: 使用個別的遷移專案-EF Core
description: 使用不同的遷移專案來管理具有 Entity Framework Core 的資料庫架構
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429802"
---
# <a name="using-a-separate-migrations-project"></a>使用個別的遷移專案

您可能會想要將您的遷移儲存在與包含的專案不同的專案中 `DbContext` 。 您也可以使用這項策略來維護多個遷移集，例如一個用於開發，另一個用於發行到發行的升級。

> [!TIP]
> 您可以檢視本文中的 [GitHut 範例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations)。

## <a name="steps"></a>步驟

1. 建立新的類別庫。

2. 新增 DbCoNtext 專案的參考。

3. 將遷移和模型快照集檔案移至類別庫。
   > [!TIP]
   > 如果您沒有任何現有的遷移，請在包含 DbCoNtext 的專案中產生一個，然後將它移動。
   > 這點很重要，因為如果遷移專案不包含現有的遷移，Add-Migration 命令將找不到 DbCoNtext。

4. 設定遷移元件：

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. 從 **啟始** 專案加入您的遷移專案的參考。

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   如果這會造成迴圈相依性，您可以改為更新 **遷移** 專案的基底輸出路徑：

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

如果一切都正確，您應該能夠將新的遷移新增至專案。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
