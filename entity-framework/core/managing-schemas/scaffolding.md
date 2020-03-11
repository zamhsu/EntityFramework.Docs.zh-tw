---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416760"
---
# <a name="reverse-engineering"></a><span data-ttu-id="d4245-102">反向工程</span><span class="sxs-lookup"><span data-stu-id="d4245-102">Reverse Engineering</span></span>

<span data-ttu-id="d4245-103">反向工程是基類庫實體類型類別的程式，以及以資料庫架構為基礎的 DbCoNtext 類別。</span><span class="sxs-lookup"><span data-stu-id="d4245-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="d4245-104">您可以使用 EF Core 套件管理員主控台（PMC）工具的 `Scaffold-DbContext` 命令，或 .NET 命令列介面（CLI）工具的 `dotnet ef dbcontext scaffold` 命令來執行此作業。</span><span class="sxs-lookup"><span data-stu-id="d4245-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="d4245-105">安裝</span><span class="sxs-lookup"><span data-stu-id="d4245-105">Installing</span></span>

<span data-ttu-id="d4245-106">在反向工程之前，您必須安裝[PMC 工具](xref:core/miscellaneous/cli/powershell)（僅限 Visual Studio）或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="d4245-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="d4245-107">如需詳細資訊，請參閱連結。</span><span class="sxs-lookup"><span data-stu-id="d4245-107">See links for details.</span></span>

<span data-ttu-id="d4245-108">您也需要為您想要反向工程的資料庫架構安裝適當的[資料庫提供者](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="d4245-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="d4245-109">連接字串</span><span class="sxs-lookup"><span data-stu-id="d4245-109">Connection string</span></span>

<span data-ttu-id="d4245-110">命令的第一個引數是資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="d4245-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="d4245-111">這些工具會使用此連接字串來讀取資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="d4245-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="d4245-112">您如何加上引號和 escape 連接字串的方式，取決於您用來執行命令的 shell。</span><span class="sxs-lookup"><span data-stu-id="d4245-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="d4245-113">如需詳細資訊，請參閱您的命令介面檔。</span><span class="sxs-lookup"><span data-stu-id="d4245-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="d4245-114">例如，PowerShell 會要求您將 `$` 字元，但不能 `\`。</span><span class="sxs-lookup"><span data-stu-id="d4245-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="d4245-115">設定和使用者秘密</span><span class="sxs-lookup"><span data-stu-id="d4245-115">Configuration and User Secrets</span></span>

<span data-ttu-id="d4245-116">如果您有 ASP.NET Core 專案，您可以使用 `Name=<connection-string>` 語法來讀取設定中的連接字串。</span><span class="sxs-lookup"><span data-stu-id="d4245-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="d4245-117">這適用于[密碼管理員工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)，可讓您的資料庫密碼與程式碼基底分開。</span><span class="sxs-lookup"><span data-stu-id="d4245-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="d4245-118">提供者名稱</span><span class="sxs-lookup"><span data-stu-id="d4245-118">Provider name</span></span>

<span data-ttu-id="d4245-119">第二個引數是提供者名稱。</span><span class="sxs-lookup"><span data-stu-id="d4245-119">The second argument is the provider name.</span></span> <span data-ttu-id="d4245-120">提供者名稱通常與提供者的 NuGet 套件名稱相同。</span><span class="sxs-lookup"><span data-stu-id="d4245-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="d4245-121">指定資料表</span><span class="sxs-lookup"><span data-stu-id="d4245-121">Specifying tables</span></span>

<span data-ttu-id="d4245-122">根據預設，資料庫架構中的所有資料表都會反向工程成實體類型。</span><span class="sxs-lookup"><span data-stu-id="d4245-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="d4245-123">您可以藉由指定架構和資料表來限制哪些資料表會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="d4245-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="d4245-124">PMC 中的 `-Schemas` 參數和 CLI 中的 [`--schema`] 選項，可以用來包含架構中的每個資料表。</span><span class="sxs-lookup"><span data-stu-id="d4245-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="d4245-125">`-Tables` （PMC）和 `--table` （CLI）可以用來包含特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="d4245-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="d4245-126">若要在 PMC 中包含多個資料表，請使用陣列。</span><span class="sxs-lookup"><span data-stu-id="d4245-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="d4245-127">若要在 CLI 中包含多個資料表，請多次指定選項。</span><span class="sxs-lookup"><span data-stu-id="d4245-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="d4245-128">保留名稱</span><span class="sxs-lookup"><span data-stu-id="d4245-128">Preserving names</span></span>

<span data-ttu-id="d4245-129">根據預設，資料表和資料行名稱已修正，以便更符合類型和屬性的 .NET 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="d4245-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="d4245-130">在 PMC 中指定 `-UseDatabaseNames` 參數或在 CLI 中的 [`--use-database-names`] 選項，將會停用此行為，盡可能保留原始的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="d4245-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="d4245-131">不正確 .NET 識別碼仍然會固定，而合成名稱（例如導覽屬性）仍會符合 .NET 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="d4245-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="d4245-132">流暢的 API 或資料批註</span><span class="sxs-lookup"><span data-stu-id="d4245-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="d4245-133">實體類型預設會使用流暢的 API 來設定。</span><span class="sxs-lookup"><span data-stu-id="d4245-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="d4245-134">請盡可能指定 `-DataAnnotations` （PMC）或 `--data-annotations` （CLI），以改為使用資料批註。</span><span class="sxs-lookup"><span data-stu-id="d4245-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="d4245-135">例如，使用流暢的 API 將會 scaffold：</span><span class="sxs-lookup"><span data-stu-id="d4245-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="d4245-136">使用資料批註時，會 scaffold 下列內容：</span><span class="sxs-lookup"><span data-stu-id="d4245-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="d4245-137">DbCoNtext 名稱</span><span class="sxs-lookup"><span data-stu-id="d4245-137">DbContext name</span></span>

<span data-ttu-id="d4245-138">根據預設，scaffold DbCoNtext 類別名稱會是以*內容*尾碼的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="d4245-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="d4245-139">若要指定不同的帳戶，請在 CLI 中使用 PMC 和 `--context` 中的 `-Context`。</span><span class="sxs-lookup"><span data-stu-id="d4245-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="d4245-140">目錄和命名空間</span><span class="sxs-lookup"><span data-stu-id="d4245-140">Directories and namespaces</span></span>

<span data-ttu-id="d4245-141">實體類別和 DbCoNtext 類別會 scaffold 至專案的根目錄，並使用專案的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="d4245-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="d4245-142">您可以使用 `-OutputDir` （PMC）或 `--output-dir` （CLI）來指定 scaffold 類別的目錄。</span><span class="sxs-lookup"><span data-stu-id="d4245-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="d4245-143">命名空間會是根命名空間，加上專案根目錄下任何子目錄的名稱。</span><span class="sxs-lookup"><span data-stu-id="d4245-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="d4245-144">您也可以使用 `-ContextDir` （PMC）和 `--context-dir` （CLI），將 DbCoNtext 類別 scaffold 至實體類型類別的不同目錄中。</span><span class="sxs-lookup"><span data-stu-id="d4245-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="d4245-145">運作方式</span><span class="sxs-lookup"><span data-stu-id="d4245-145">How it works</span></span>

<span data-ttu-id="d4245-146">反向工程一開始會先閱讀資料庫架構。</span><span class="sxs-lookup"><span data-stu-id="d4245-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="d4245-147">它會讀取資料表、資料行、條件約束和索引的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="d4245-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="d4245-148">接下來，它會使用架構資訊來建立 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="d4245-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="d4245-149">資料表是用來建立實體類型;資料行是用來建立屬性;和外鍵是用來建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="d4245-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="d4245-150">最後，模型是用來產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="d4245-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="d4245-151">對應的實體類型類別、流暢的 API 和資料批註會 scaffold，以便從您的應用程式重新建立相同的模型。</span><span class="sxs-lookup"><span data-stu-id="d4245-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="d4245-152">限制</span><span class="sxs-lookup"><span data-stu-id="d4245-152">Limitations</span></span>

* <span data-ttu-id="d4245-153">並非有關模型的所有專案都可以使用資料庫架構來表示。</span><span class="sxs-lookup"><span data-stu-id="d4245-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="d4245-154">例如，有關[**繼承**](../modeling/inheritance.md)階層、[**擁有類型**](../modeling/owned-entities.md)和[**資料表分割**](../modeling/table-splitting.md)的資訊並不會出現在資料庫架構中。</span><span class="sxs-lookup"><span data-stu-id="d4245-154">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="d4245-155">因此，這些結構永遠不會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="d4245-155">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="d4245-156">此外，EF Core 提供者可能不支援**某些資料行類型**。</span><span class="sxs-lookup"><span data-stu-id="d4245-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="d4245-157">這些資料行不會包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="d4245-157">These columns won't be included in the model.</span></span>
* <span data-ttu-id="d4245-158">您可以在 EF Core 模型中定義[**並行標記**](../modeling/concurrency.md)，以防止兩個使用者同時更新相同的實體。</span><span class="sxs-lookup"><span data-stu-id="d4245-158">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="d4245-159">某些資料庫具有特殊類型來表示這種類型的資料行（例如，SQL Server 中的 rowversion），在此情況下，我們可以對這項資訊進行反向工程。不過，其他並行存取權杖將不會進行反向工程。</span><span class="sxs-lookup"><span data-stu-id="d4245-159">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="d4245-160">[反向C#工程目前不支援8可為 null 的參考型別功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 一律C#會產生假設功能已停用的程式碼。</span><span class="sxs-lookup"><span data-stu-id="d4245-160">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="d4245-161">例如，可為 null 的文字資料行將會 scaffold 為具有類型 `string` 的屬性，而不是 `string?`，其中包含用來設定是否需要屬性的流暢 API 或資料批註。</span><span class="sxs-lookup"><span data-stu-id="d4245-161">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="d4245-162">您可以編輯 scaffold 程式碼，並將其C#取代為 null 屬性注釋。</span><span class="sxs-lookup"><span data-stu-id="d4245-162">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="d4245-163">由問題[#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)追蹤可為 null 之參考型別的樣板支援。</span><span class="sxs-lookup"><span data-stu-id="d4245-163">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="d4245-164">自訂模型</span><span class="sxs-lookup"><span data-stu-id="d4245-164">Customizing the model</span></span>

<span data-ttu-id="d4245-165">EF Core 所產生的程式碼就是您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="d4245-165">The code generated by EF Core is your code.</span></span> <span data-ttu-id="d4245-166">您可以隨意變更它。</span><span class="sxs-lookup"><span data-stu-id="d4245-166">Feel free to change it.</span></span> <span data-ttu-id="d4245-167">只有當您再次對相同的模型進行反向工程時，才會重新產生。</span><span class="sxs-lookup"><span data-stu-id="d4245-167">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="d4245-168">Scaffold 程式碼代表*一個*可用於存取資料庫的模型，但它不是*唯一*可以使用的模型。</span><span class="sxs-lookup"><span data-stu-id="d4245-168">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="d4245-169">自訂實體類型類別和 DbCoNtext 類別，以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="d4245-169">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="d4245-170">例如，您可以選擇重新命名類型和屬性、引進繼承階層，或將資料表分割成多個實體。</span><span class="sxs-lookup"><span data-stu-id="d4245-170">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="d4245-171">您也可以從模型中移除非唯一的索引、未使用的序列和導覽屬性、選擇性純量屬性和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="d4245-171">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="d4245-172">您也可以加入其他的函數、方法、屬性等。</span><span class="sxs-lookup"><span data-stu-id="d4245-172">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="d4245-173">在不同的檔案中使用另一個部分類別。</span><span class="sxs-lookup"><span data-stu-id="d4245-173">using another partial class in a separate file.</span></span> <span data-ttu-id="d4245-174">即使您想要再次對模型進行反向工程，此方法仍可運作。</span><span class="sxs-lookup"><span data-stu-id="d4245-174">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="d4245-175">更新模型</span><span class="sxs-lookup"><span data-stu-id="d4245-175">Updating the model</span></span>

<span data-ttu-id="d4245-176">對資料庫進行變更之後，您可能需要更新 EF Core 模型以反映這些變更。</span><span class="sxs-lookup"><span data-stu-id="d4245-176">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="d4245-177">如果資料庫變更很簡單，只需要手動對 EF Core 模型進行變更，可能是最簡單的方法。</span><span class="sxs-lookup"><span data-stu-id="d4245-177">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="d4245-178">例如，重新命名資料表或資料行、移除資料行或更新資料行的類型，都是在程式碼中進行的簡單變更。</span><span class="sxs-lookup"><span data-stu-id="d4245-178">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="d4245-179">不過，更重要的變更並不是手動進行。</span><span class="sxs-lookup"><span data-stu-id="d4245-179">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="d4245-180">其中一個常見的工作流程是使用 `-Force` （PMC）或 `--force` （CLI），再次從資料庫反向工程模型，以覆寫現有的模型。</span><span class="sxs-lookup"><span data-stu-id="d4245-180">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="d4245-181">另一個常見的要求功能是能夠從資料庫更新模型，同時保留自訂，例如重新命名、類型階層等等。使用 [問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) ] 追蹤此功能的進度。</span><span class="sxs-lookup"><span data-stu-id="d4245-181">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="d4245-182">如果您再次從資料庫對模型進行反向工程，您對檔案所做的任何變更都將遺失。</span><span class="sxs-lookup"><span data-stu-id="d4245-182">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
