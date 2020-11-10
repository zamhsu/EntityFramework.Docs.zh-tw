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
# <a name="design-time-services"></a><span data-ttu-id="3555c-103">設計階段服務</span><span class="sxs-lookup"><span data-stu-id="3555c-103">Design-time services</span></span>

<span data-ttu-id="3555c-104">工具所使用的某些服務只會在設計階段使用。</span><span class="sxs-lookup"><span data-stu-id="3555c-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="3555c-105">這些服務是與 EF Core 的執行時間服務分開管理，以防止它們與您的應用程式一起部署。</span><span class="sxs-lookup"><span data-stu-id="3555c-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="3555c-106">若要覆寫這些服務的其中之一 (例如，用來產生遷移檔) 的服務，請將的執行新增 `IDesignTimeServices` 至啟始專案。</span><span class="sxs-lookup"><span data-stu-id="3555c-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="3555c-107">參考 Microsoft.entityframeworkcore 設計</span><span class="sxs-lookup"><span data-stu-id="3555c-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="3555c-108">Microsoft.entityframeworkcore 是 DevelopmentDependency 套件。</span><span class="sxs-lookup"><span data-stu-id="3555c-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="3555c-109">這表示，相依性不會以傳址方式傳遞至其他專案，而且您預設無法參考其類型。</span><span class="sxs-lookup"><span data-stu-id="3555c-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="3555c-110">若要參考其類型和覆寫設計階段服務，請更新專案檔中的 PackageReference 專案中繼資料。</span><span class="sxs-lookup"><span data-stu-id="3555c-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="3555c-111">如果透過 Microsoft.entityframeworkcore 將封裝參考為可轉移，則您必須將明確的 PackageReference 新增至套件，並變更其中繼資料。</span><span class="sxs-lookup"><span data-stu-id="3555c-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="3555c-112">服務清單</span><span class="sxs-lookup"><span data-stu-id="3555c-112">List of services</span></span>

<span data-ttu-id="3555c-113">以下是設計階段服務的清單。</span><span class="sxs-lookup"><span data-stu-id="3555c-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="3555c-114">服務</span><span class="sxs-lookup"><span data-stu-id="3555c-114">Service</span></span>                                                                              | <span data-ttu-id="3555c-115">描述</span><span class="sxs-lookup"><span data-stu-id="3555c-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="3555c-116">為對應的模型批註產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="3555c-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="3555c-117">有助於產生 c # 程式碼。</span><span class="sxs-lookup"><span data-stu-id="3555c-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="3555c-118">Pluralizes 和 singularizes 字組。</span><span class="sxs-lookup"><span data-stu-id="3555c-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="3555c-119">產生遷移的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3555c-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="3555c-120">用於管理遷移檔的主要類別。</span><span class="sxs-lookup"><span data-stu-id="3555c-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="3555c-121">從資料庫建立資料庫模型。</span><span class="sxs-lookup"><span data-stu-id="3555c-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="3555c-122">產生模型的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3555c-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="3555c-123">產生 OnConfiguring 程式碼。</span><span class="sxs-lookup"><span data-stu-id="3555c-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="3555c-124">樣板反轉工程模型的主要類別。</span><span class="sxs-lookup"><span data-stu-id="3555c-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="3555c-125">使用服務</span><span class="sxs-lookup"><span data-stu-id="3555c-125">Using services</span></span>

<span data-ttu-id="3555c-126">這些服務也有助於建立您自己的工具。</span><span class="sxs-lookup"><span data-stu-id="3555c-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="3555c-127">例如，當您想要自動化設計階段工作流程的一部分時。</span><span class="sxs-lookup"><span data-stu-id="3555c-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="3555c-128">您可以使用 AddEntityFrameworkDesignTimeServices 和 AddDbCoNtextDesignTimeServices 擴充方法來建立包含這些服務的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="3555c-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
