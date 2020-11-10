---
title: 設計階段服務-EF Core
description: Entity Framework Core 設計階段服務的資訊
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431467"
---
# <a name="design-time-services"></a>設計階段服務

工具所使用的某些服務只會在設計階段使用。 這些服務是與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。 若要覆寫這些服務的其中之一 (例如，用來產生遷移檔) 的服務，請將的執行新增 `IDesignTimeServices` 至啟始專案。

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>參考 Microsoft.entityframeworkcore 設計

Microsoft.entityframeworkcore 是 DevelopmentDependency 套件。 這表示，相依性不會以傳址方式傳遞至其他專案，而且您預設無法參考其類型。

若要參考其類型和覆寫設計階段服務，請更新專案檔中的 PackageReference 專案中繼資料。

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

如果透過 Microsoft.entityframeworkcore 將封裝參考為可轉移，則您必須將明確的 PackageReference 新增至套件，並變更其中繼資料。

## <a name="list-of-services"></a>服務清單

以下是設計階段服務的清單。

服務                                                                              | 描述
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | 為對應的模型批註產生程式碼。
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | 有助於產生 c # 程式碼。
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | Pluralizes 和 singularizes 字組。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | 產生遷移的程式碼。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | 用於管理遷移檔的主要類別。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | 從資料庫建立資料庫模型。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | 產生模型的程式碼。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | 產生 OnConfiguring 程式碼。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | 樣板反轉工程模型的主要類別。

## <a name="using-services"></a>使用服務

這些服務也有助於建立您自己的工具。 例如，當您想要自動化設計階段工作流程的一部分時。

您可以使用 AddEntityFrameworkDesignTimeServices 和 AddDbCoNtextDesignTimeServices 擴充方法來建立包含這些服務的服務提供者。

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
