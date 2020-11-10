---
title: 反向工程-EF Core
description: 使用 Entity Framework Core 從現有的資料庫反轉模型的工程
author: bricelam
ms.date: 11/13/2018
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 11ffa2e62136e47959ebbfd54ccb55c2b9e23e04
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429763"
---
# <a name="reverse-engineering"></a><span data-ttu-id="1a8d6-103">反向工程</span><span class="sxs-lookup"><span data-stu-id="1a8d6-103">Reverse Engineering</span></span>

<span data-ttu-id="1a8d6-104">反向工程是以資料庫架構為基礎的基礎結構實體型別類別和 DbCoNtext 類別的處理常式。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-104">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="1a8d6-105">您可以使用 `Scaffold-DbContext` EF Core 封裝管理員主控台的命令 (PMC) 工具或 `dotnet ef dbcontext scaffold` .net 命令列介面的命令 (CLI) 工具來執行此命令。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-105">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="1a8d6-106">安裝</span><span class="sxs-lookup"><span data-stu-id="1a8d6-106">Installing</span></span>

<span data-ttu-id="1a8d6-107">在進行反向工程之前，您必須安裝 [PMC 工具](xref:core/cli/powershell) (只 Visual Studio) 或 [CLI 工具](xref:core/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-107">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/cli/dotnet).</span></span> <span data-ttu-id="1a8d6-108">如需詳細資訊，請參閱連結。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-108">See links for details.</span></span>

<span data-ttu-id="1a8d6-109">您也必須為想要還原工程的資料庫架構安裝適當的 [資料庫提供者](xref:core/providers/index) 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-109">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="1a8d6-110">連接字串</span><span class="sxs-lookup"><span data-stu-id="1a8d6-110">Connection string</span></span>

<span data-ttu-id="1a8d6-111">命令的第一個引數是資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-111">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="1a8d6-112">這些工具會使用此連接字串來讀取資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-112">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="1a8d6-113">您如何加上引號和 escape 連接字串，取決於您用來執行命令的 shell。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-113">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="1a8d6-114">請參閱您的 shell 檔以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-114">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="1a8d6-115">例如，PowerShell 要求您必須將字元換用 `$` ，而不是 `\` 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-115">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1a8d6-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1a8d6-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="1a8d6-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a8d6-117">Visual Studio</span></span>](#tab/vs)

```powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="1a8d6-118">設定和使用者秘密</span><span class="sxs-lookup"><span data-stu-id="1a8d6-118">Configuration and User Secrets</span></span>

<span data-ttu-id="1a8d6-119">如果您有 ASP.NET Core 專案，可以使用 `Name=<connection-string>` 語法從設定讀取連接字串。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-119">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="1a8d6-120">這與 [秘密管理員工具](/aspnet/core/security/app-secrets#secret-manager) 搭配使用，可讓您的資料庫密碼與程式碼基底分開。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-120">This works well with the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="1a8d6-121">提供者名稱</span><span class="sxs-lookup"><span data-stu-id="1a8d6-121">Provider name</span></span>

<span data-ttu-id="1a8d6-122">第二個引數是提供者名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-122">The second argument is the provider name.</span></span> <span data-ttu-id="1a8d6-123">提供者名稱通常與提供者的 NuGet 套件名稱相同。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-123">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="1a8d6-124">指定資料表</span><span class="sxs-lookup"><span data-stu-id="1a8d6-124">Specifying tables</span></span>

<span data-ttu-id="1a8d6-125">依預設，資料庫架構中的所有資料表都會依預設為實體類型進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-125">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="1a8d6-126">您可以藉由指定架構和資料表來限制哪些資料表會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-126">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1a8d6-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1a8d6-127">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="1a8d6-128">`--schema`選項可用來包含架構內的每個資料表，但 `--table` 可以用來包含特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-128">The `--schema` option can be used to include every table within a schema, while `--table` can be used to include specific tables.</span></span>

<span data-ttu-id="1a8d6-129">若要包含多個資料表，請多次指定選項：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-129">To include multiple tables, specify the option multiple times:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[<span data-ttu-id="1a8d6-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a8d6-130">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="1a8d6-131">`-Schemas`選項可用來包含架構內的每個資料表，但 `-Tables` 可以用來包含特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-131">The `-Schemas` option can be used to include every table within a schema, while `-Tables` can be used to include specific tables.</span></span>

<span data-ttu-id="1a8d6-132">若要包含多個資料表，請使用陣列：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-132">To include multiple tables, use an array:</span></span>

```powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="1a8d6-133">\*\*_</span><span class="sxs-lookup"><span data-stu-id="1a8d6-133">\*\*_</span></span>

## <a name="preserving-names"></a><span data-ttu-id="1a8d6-134">保留名稱</span><span class="sxs-lookup"><span data-stu-id="1a8d6-134">Preserving names</span></span>

<span data-ttu-id="1a8d6-135">根據預設，已修正資料表和資料行名稱，以更符合 .NET 命名慣例的類型和屬性。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-135">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="1a8d6-136">`-UseDatabaseNames`在 PMC 中指定參數或 `--use-database-names` .NET Core CLI 中的選項，將會停用此行為，盡可能保留原始資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-136">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the .NET Core CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="1a8d6-137">不正確 .NET 識別碼仍會固定，且合成名稱（如導覽屬性）仍會符合 .NET 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-137">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="1a8d6-138">流暢的 API 或資料批註</span><span class="sxs-lookup"><span data-stu-id="1a8d6-138">Fluent API or Data Annotations</span></span>

<span data-ttu-id="1a8d6-139">實體類型預設會使用流暢的 API 進行設定。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-139">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="1a8d6-140">指定 `-DataAnnotations` (PMC) 或 `--data-annotations` ( .NET Core CLI) ，以改為在可能的情況下使用資料批註。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-140">Specify `-DataAnnotations` (PMC) or `--data-annotations` (.NET Core CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="1a8d6-141">例如，使用流暢的 API 將會 scaffold：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-141">For example, using the Fluent API will scaffold this:</span></span>

```csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="1a8d6-142">使用資料批註時，會 scaffold：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-142">While using Data Annotations will scaffold this:</span></span>

```csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="1a8d6-143">DbCoNtext 名稱</span><span class="sxs-lookup"><span data-stu-id="1a8d6-143">DbContext name</span></span>

<span data-ttu-id="1a8d6-144">根據預設，scaffold DbCoNtext 類別名稱將會是 _CoNtext \* 尾碼的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-144">The scaffolded DbContext class name will be the name of the database suffixed with _Context\* by default.</span></span> <span data-ttu-id="1a8d6-145">若要指定不同的，請 `-Context` 在 PMC 和 `--context` .NET Core CLI 中使用。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-145">To specify a different one, use `-Context` in PMC and `--context` in the .NET Core CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="1a8d6-146">目錄和命名空間</span><span class="sxs-lookup"><span data-stu-id="1a8d6-146">Directories and namespaces</span></span>

<span data-ttu-id="1a8d6-147">實體類別和 DbCoNtext 類別會 scaffold 至專案的根目錄，並使用專案的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-147">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1a8d6-148">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1a8d6-148">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="1a8d6-149">您可以指定使用 scaffold 類別的目錄 `--output-dir` ，而且 `--context-dir` 可以用來將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-149">You can specify the directory where classes are scaffolded using `--output-dir`, and `--context-dir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

<span data-ttu-id="1a8d6-150">根據預設，命名空間會是根命名空間加上專案根目錄下任何子目錄的名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-150">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="1a8d6-151">不過，從 EFCore 5.0 開始，您可以使用來覆寫所有輸出類別的命名空間 `--namespace` 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-151">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `--namespace`.</span></span> <span data-ttu-id="1a8d6-152">您也可以使用下列方式，只覆寫 DbCoNtext 類別的命名空間 `--context-namespace` ：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-152">You can also override the namespace for just the DbContext class using `--context-namespace`:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="1a8d6-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1a8d6-153">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="1a8d6-154">您可以指定使用 scaffold 類別的目錄 `-OutputDir` ，而且 `-ContextDir` 可以用來將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中：</span><span class="sxs-lookup"><span data-stu-id="1a8d6-154">You can specify the directory where classes are scaffolded using `-OutputDir`, and `-ContextDir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

<span data-ttu-id="1a8d6-155">根據預設，命名空間會是根命名空間加上專案根目錄下任何子目錄的名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-155">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="1a8d6-156">不過，從 EFCore 5.0 開始，您可以使用來覆寫所有輸出類別的命名空間 `-Namespace` 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-156">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `-Namespace`.</span></span> <span data-ttu-id="1a8d6-157">您也可以使用，只覆寫 DbCoNtext 類別的命名空間 `-ContextNamespace` 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-157">You can also override the namespace for just the DbContext class using `-ContextNamespace`.</span></span>

```powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a><span data-ttu-id="1a8d6-158">運作方式</span><span class="sxs-lookup"><span data-stu-id="1a8d6-158">How it works</span></span>

<span data-ttu-id="1a8d6-159">反向工程從讀取資料庫架構開始。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-159">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="1a8d6-160">它會讀取資料表、資料行、條件約束和索引的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-160">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="1a8d6-161">接著，它會使用架構資訊來建立 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-161">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="1a8d6-162">資料表是用來建立實體類型;資料行是用來建立屬性;和外鍵可用來建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-162">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="1a8d6-163">最後，此模型會用來產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-163">Finally, the model is used to generate code.</span></span> <span data-ttu-id="1a8d6-164">系統會 scaffold 對應的實體類型類別、流暢的 API 和資料批註，以便從您的應用程式重新建立相同的模型。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-164">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="1a8d6-165">限制</span><span class="sxs-lookup"><span data-stu-id="1a8d6-165">Limitations</span></span>

* <span data-ttu-id="1a8d6-166">您無法使用資料庫架構來表示模型的所有內容。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-166">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="1a8d6-167">例如，資料庫架構中不會有 [**繼承**](xref:core/modeling/inheritance)階層、 [**擁有類型**](xref:core/modeling/owned-entities)和 [**資料表分割**](xref:core/modeling/table-splitting) 的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-167">For example, information about [**inheritance hierarchies**](xref:core/modeling/inheritance), [**owned types**](xref:core/modeling/owned-entities), and [**table splitting**](xref:core/modeling/table-splitting) are not present in the database schema.</span></span> <span data-ttu-id="1a8d6-168">因此，這些結構將永遠不會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-168">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="1a8d6-169">此外，EF Core 提供者可能不支援 **某些資料行類型** 。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-169">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="1a8d6-170">這些資料行不會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-170">These columns won't be included in the model.</span></span>
* <span data-ttu-id="1a8d6-171">您可以在 EF Core 模型中定義 [**並行權杖**](xref:core/modeling/concurrency)，以防止兩位使用者同時更新相同的實體。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-171">You can define [**concurrency tokens**](xref:core/modeling/concurrency), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="1a8d6-172">某些資料庫具有特殊類型來表示這種類型的資料行 (例如，在 SQL Server) 中的 rowversion，在此情況下，我們可以將此資訊進行反向工程。不過，其他並行存取權杖將不會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-172">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="1a8d6-173">反向工程目前不支援[c # 8 可為 Null 的參考型別功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律會產生假設功能已停用的 c # 程式碼。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-173">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="1a8d6-174">例如，可為 null 的文字資料行將 scaffold 為類型的屬性，而不是使用 `string` `string?` 流暢的 API 或資料批註來設定是否需要屬性。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-174">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="1a8d6-175">您可以編輯 scaffold 程式碼，並以 c # 可 null 性注釋取代這些程式碼。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-175">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="1a8d6-176">問題 [#15520](https://github.com/dotnet/efcore/issues/15520)會追蹤可為 null 之參考型別的型別支援。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-176">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/dotnet/efcore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="1a8d6-177">自訂模型</span><span class="sxs-lookup"><span data-stu-id="1a8d6-177">Customizing the model</span></span>

<span data-ttu-id="1a8d6-178">EF Core 產生的程式碼就是您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-178">The code generated by EF Core is your code.</span></span> <span data-ttu-id="1a8d6-179">您可以隨意變更它。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-179">Feel free to change it.</span></span> <span data-ttu-id="1a8d6-180">如果您再次對相同的模型進行反向工程，它只會重新產生。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-180">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="1a8d6-181">Scaffold 程式碼代表 *一個* 可以用來存取資料庫的模型，但它當然不是 *唯一* 可以使用的模型。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-181">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="1a8d6-182">自訂實體類型類別和 DbCoNtext 類別，以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-182">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="1a8d6-183">例如，您可以選擇重新命名類型和屬性、引進繼承階層，或將資料表分割成多個實體。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-183">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="1a8d6-184">您也可以從模型中移除非唯一的索引、未使用的序列和導覽屬性、選擇性的純量屬性和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-184">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="1a8d6-185">您也可以新增其他的函式、方法、屬性等。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-185">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="1a8d6-186">在不同的檔案中使用另一個部分類別。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-186">using another partial class in a separate file.</span></span> <span data-ttu-id="1a8d6-187">即使您想要再次對模型進行反向工程，此方法仍可運作。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-187">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="1a8d6-188">更新模型</span><span class="sxs-lookup"><span data-stu-id="1a8d6-188">Updating the model</span></span>

<span data-ttu-id="1a8d6-189">對資料庫進行變更之後，您可能需要更新 EF Core 模型以反映這些變更。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-189">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="1a8d6-190">如果資料庫變更很簡單，則只需要手動對 EF Core 模型進行變更，可能會是最簡單的做法。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-190">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="1a8d6-191">例如，重新命名資料表或資料行、移除資料行或更新資料行的類型，是在程式碼中進行的簡單變更。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-191">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="1a8d6-192">不過，更重要的變更並不容易以手動方式進行。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-192">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="1a8d6-193">其中一個常見的工作流程是使用 `-Force` (PMC) 或 `--force` (CLI) ，使用更新的模型來覆寫現有的模型，以從資料庫反向工程模型。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-193">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="1a8d6-194">另一個經常要求的功能是能夠從資料庫更新模型，同時保留像是重新命名、類型階層等自訂。使用 [問題 [#831](https://github.com/dotnet/efcore/issues/831) ] 來追蹤這項功能的進度。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-194">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/dotnet/efcore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="1a8d6-195">如果您再次從資料庫反向工程模型，您對檔案所做的任何變更都將遺失。</span><span class="sxs-lookup"><span data-stu-id="1a8d6-195">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
