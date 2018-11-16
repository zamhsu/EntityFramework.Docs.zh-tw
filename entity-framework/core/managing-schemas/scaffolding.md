---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: ef729c0c26d5a1f57099f339eb51cda7e83289df
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688676"
---
# <a name="reverse-engineering"></a><span data-ttu-id="e6435-102">反向工程</span><span class="sxs-lookup"><span data-stu-id="e6435-102">Reverse Engineering</span></span>

<span data-ttu-id="e6435-103">反向工程是 scaffolding 實體類型類別和資料庫結構描述為基礎的 DbContext 類別的程序。</span><span class="sxs-lookup"><span data-stu-id="e6435-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="e6435-104">可以使用來執行`Scaffold-DbContext`EF Core 套件管理員主控台 (PMC) 工具的命令或`dotnet ef dbcontext scaffold`.NET 命令列介面 (CLI) 工具的命令。</span><span class="sxs-lookup"><span data-stu-id="e6435-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="e6435-105">安裝</span><span class="sxs-lookup"><span data-stu-id="e6435-105">Installing</span></span>

<span data-ttu-id="e6435-106">反向工程、 之前您必須安裝[PMC 工具](xref:core/miscellaneous/cli/powershell)(僅限 Visual Studio) 或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e6435-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="e6435-107">請參閱詳細資料的連結。</span><span class="sxs-lookup"><span data-stu-id="e6435-107">See links for details.</span></span>

<span data-ttu-id="e6435-108">您也需要安裝適當[資料庫提供者](xref:core/providers/index)資料庫結構描述您想要反向工程。</span><span class="sxs-lookup"><span data-stu-id="e6435-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="e6435-109">連接字串</span><span class="sxs-lookup"><span data-stu-id="e6435-109">Connection string</span></span>

<span data-ttu-id="e6435-110">命令的第一個引數是資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="e6435-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="e6435-111">讀取資料庫結構描述，工具會使用此連接字串。</span><span class="sxs-lookup"><span data-stu-id="e6435-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="e6435-112">加上引號和逸出的連接字串的方式而定的 shell 您用來執行命令。</span><span class="sxs-lookup"><span data-stu-id="e6435-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="e6435-113">請參閱您的殼層文件，如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="e6435-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="e6435-114">例如，PowerShell 會要求您以逸出`$`字元，而非`\`。</span><span class="sxs-lookup"><span data-stu-id="e6435-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="e6435-115">組態和使用者祕密</span><span class="sxs-lookup"><span data-stu-id="e6435-115">Configuration and User Secrets</span></span>

<span data-ttu-id="e6435-116">如果您有 ASP.NET Core 專案，您可以使用`Name=<connection-string>`從組態讀取連接字串的語法。</span><span class="sxs-lookup"><span data-stu-id="e6435-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="e6435-117">這適用於[Secret Manager 工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)區隔您的資料庫密碼從您的程式碼基底。</span><span class="sxs-lookup"><span data-stu-id="e6435-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="e6435-118">提供者名稱</span><span class="sxs-lookup"><span data-stu-id="e6435-118">Provider name</span></span>

<span data-ttu-id="e6435-119">第二個引數是提供者名稱。</span><span class="sxs-lookup"><span data-stu-id="e6435-119">The second argument is the provider name.</span></span> <span data-ttu-id="e6435-120">提供者名稱通常是提供者的 NuGet 套件名稱相同。</span><span class="sxs-lookup"><span data-stu-id="e6435-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="e6435-121">指定的資料表</span><span class="sxs-lookup"><span data-stu-id="e6435-121">Specifying tables</span></span>

<span data-ttu-id="e6435-122">資料庫結構描述中的所有資料表都都會反向工程到實體類型中，依預設。</span><span class="sxs-lookup"><span data-stu-id="e6435-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="e6435-123">您可以限制哪些資料表進行反向工程藉由指定結構描述和資料表。</span><span class="sxs-lookup"><span data-stu-id="e6435-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="e6435-124">`-Schemas`在 PMC 中的參數和`--schema`CLI 中的選項可以用來包含結構描述中的每個資料表。</span><span class="sxs-lookup"><span data-stu-id="e6435-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="e6435-125">`-Tables` (PMC) 和`--table`(CLI) 可以用來包含特定的資料表。</span><span class="sxs-lookup"><span data-stu-id="e6435-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="e6435-126">若要在 PMC 中包含多個資料表，使用的陣列。</span><span class="sxs-lookup"><span data-stu-id="e6435-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="e6435-127">若要在 CLI 中包含多個資料表，請指定選項多次。</span><span class="sxs-lookup"><span data-stu-id="e6435-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="e6435-128">保留的名稱</span><span class="sxs-lookup"><span data-stu-id="e6435-128">Preserving names</span></span>

<span data-ttu-id="e6435-129">資料表和資料行名稱被固定的以更加符合的型別和屬性的.NET 命名慣例預設。</span><span class="sxs-lookup"><span data-stu-id="e6435-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="e6435-130">指定`-UseDatabaseNames`在 PMC 中切換或`--use-database-names`CLI 中的選項會停用保留原始的資料庫名稱的最大的這種行為。</span><span class="sxs-lookup"><span data-stu-id="e6435-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="e6435-131">無效的.NET 識別項仍將會修正，並合成的名稱，例如導覽屬性將仍會符合.NET 命名慣例。</span><span class="sxs-lookup"><span data-stu-id="e6435-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="e6435-132">Fluent API 或資料註解</span><span class="sxs-lookup"><span data-stu-id="e6435-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="e6435-133">實體類型會使用 Fluent API 預設設定。</span><span class="sxs-lookup"><span data-stu-id="e6435-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="e6435-134">指定`-DataAnnotations`(PMC) 或`--data-annotations`(CLI)，要改為使用資料註解時可能。</span><span class="sxs-lookup"><span data-stu-id="e6435-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="e6435-135">例如，使用 Fluent API 會 scaffold this。</span><span class="sxs-lookup"><span data-stu-id="e6435-135">For example, using the Fluent API will scaffold the this.</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="e6435-136">使用資料註解時將 scaffold 這。</span><span class="sxs-lookup"><span data-stu-id="e6435-136">While using Data Annotations will scaffold this.</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="e6435-137">DbContext 名稱</span><span class="sxs-lookup"><span data-stu-id="e6435-137">DbContext name</span></span>

<span data-ttu-id="e6435-138">包含 scaffold 的 DbContext 類別名稱會加上資料庫的名稱*內容*預設。</span><span class="sxs-lookup"><span data-stu-id="e6435-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="e6435-139">若要指定一個不同，請使用`-Context`在 PMC 中和`--context`CLI 中。</span><span class="sxs-lookup"><span data-stu-id="e6435-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="e6435-140">目錄和命名空間</span><span class="sxs-lookup"><span data-stu-id="e6435-140">Directories and namespaces</span></span>

<span data-ttu-id="e6435-141">實體類別與 DbContext 類別 scaffold 到專案的根目錄，並使用專案的預設命名空間。</span><span class="sxs-lookup"><span data-stu-id="e6435-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="e6435-142">您可以指定的目錄，類別會使用包含 scaffold `-OutputDir` (PMC) 或`--output-dir`(CLI)。</span><span class="sxs-lookup"><span data-stu-id="e6435-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="e6435-143">根命名空間加上專案的根目錄下的任何子目錄的名稱，將會在命名空間。</span><span class="sxs-lookup"><span data-stu-id="e6435-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="e6435-144">您也可以使用`-ContextDir`(PMC) 和`--context-dir`(CLI) 來建立結構的 DbContext 類別，在個別的目錄，從實體型別類別。</span><span class="sxs-lookup"><span data-stu-id="e6435-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="e6435-145">它的運作方式</span><span class="sxs-lookup"><span data-stu-id="e6435-145">How it works</span></span>

<span data-ttu-id="e6435-146">反向工程一開始會讀取資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="e6435-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="e6435-147">它會讀取資料表、 資料行、 條件約束，以及索引的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="e6435-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="e6435-148">接下來，它會建立 EF Core 模型使用的結構描述資訊。</span><span class="sxs-lookup"><span data-stu-id="e6435-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="e6435-149">資料表用來建立實體類型;資料行用來建立 [屬性]，與外部索引鍵用來建立關聯性。</span><span class="sxs-lookup"><span data-stu-id="e6435-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="e6435-150">最後，此模型用來產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="e6435-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="e6435-151">對應的 「 實體類型 」 類別，Fluent API，與資料註解會 scaffold 才能重新建立相同的模型，從您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e6435-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="what-doesnt-work"></a><span data-ttu-id="e6435-152">不可行</span><span class="sxs-lookup"><span data-stu-id="e6435-152">What doesn't work</span></span>

<span data-ttu-id="e6435-153">並非所有相關模型可以使用資料庫結構描述來表示。</span><span class="sxs-lookup"><span data-stu-id="e6435-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="e6435-154">例如，有關的資訊**繼承階層架構**，**擁有的類型**，並**資料表分割**不存在於資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="e6435-154">For example, information about **inheritance hierarchies**, **owned types**, and **table splitting** are not present in the database schema.</span></span> <span data-ttu-id="e6435-155">因為這個緣故，這些建構將永遠不會反向工程。</span><span class="sxs-lookup"><span data-stu-id="e6435-155">Because of this, these constructs will never be reverse engineered.</span></span>

<span data-ttu-id="e6435-156">颾魤 ㄛ**某些資料行類型**EF Core 提供者可能不支援。</span><span class="sxs-lookup"><span data-stu-id="e6435-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="e6435-157">在模型中，將不會包含這些資料行。</span><span class="sxs-lookup"><span data-stu-id="e6435-157">These columns won't be included in the model.</span></span>

<span data-ttu-id="e6435-158">EF Core 需要有索引鍵，每個實體類型。</span><span class="sxs-lookup"><span data-stu-id="e6435-158">EF Core requires every entity type to have a key.</span></span> <span data-ttu-id="e6435-159">資料表，不過，不需要指定主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="e6435-159">Tables, however, aren't required to specify a primary key.</span></span> <span data-ttu-id="e6435-160">**資料表沒有主索引鍵**是目前無法反向工程。</span><span class="sxs-lookup"><span data-stu-id="e6435-160">**Tables without a primary key** are currently not reverse engineered.</span></span>

<span data-ttu-id="e6435-161">您可以定義**並行語彙基元**在 EF Core 模型中，以防止兩位使用者同時更新相同的實體。</span><span class="sxs-lookup"><span data-stu-id="e6435-161">You can define **concurrency tokens** in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="e6435-162">某些資料庫具有一種特殊類型來表示這種類型的資料行 (例如，SQL Server 中的 rowversion) 在這種情況下，我們可以反向工程這項資訊;不過，其他並行語彙基元將不會反向工程。</span><span class="sxs-lookup"><span data-stu-id="e6435-162">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="e6435-163">自訂模型</span><span class="sxs-lookup"><span data-stu-id="e6435-163">Customizing the model</span></span>

<span data-ttu-id="e6435-164">EF Core 產生的程式碼是您的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e6435-164">The code generated by EF Core is your code.</span></span> <span data-ttu-id="e6435-165">請隨意變更。</span><span class="sxs-lookup"><span data-stu-id="e6435-165">Feel free to change it.</span></span> <span data-ttu-id="e6435-166">它將只會重新產生您一次反向工程的相同模型。</span><span class="sxs-lookup"><span data-stu-id="e6435-166">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="e6435-167">Scaffold 程式碼代表*一個*模型，可用來存取資料庫，但它不一定*只*可用的模型。</span><span class="sxs-lookup"><span data-stu-id="e6435-167">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="e6435-168">自訂實體類型類別和 DbContext 類別，以符合您的需求。</span><span class="sxs-lookup"><span data-stu-id="e6435-168">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="e6435-169">例如，您可以選擇重新命名類型和屬性，導入繼承階層架構，或將資料表分割成多個實體。</span><span class="sxs-lookup"><span data-stu-id="e6435-169">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="e6435-170">您也可以從模型移除非唯一索引、 未使用的序列和導覽屬性、 選擇性的純量屬性和條件約束名稱。</span><span class="sxs-lookup"><span data-stu-id="e6435-170">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="e6435-171">您也可以新增額外的建構函式、 方法、 屬性等等。</span><span class="sxs-lookup"><span data-stu-id="e6435-171">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="e6435-172">使用另一個部分類別在不同的檔案。</span><span class="sxs-lookup"><span data-stu-id="e6435-172">using another partial class in a separate file.</span></span> <span data-ttu-id="e6435-173">即使您想要一次反向工程模型，適用於這種方法。</span><span class="sxs-lookup"><span data-stu-id="e6435-173">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="e6435-174">更新模型</span><span class="sxs-lookup"><span data-stu-id="e6435-174">Updating the model</span></span>

<span data-ttu-id="e6435-175">之後對資料庫進行變更，您可能需要更新您的 EF Core 模型，以反映這些變更。</span><span class="sxs-lookup"><span data-stu-id="e6435-175">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="e6435-176">如果資料庫變更很簡單，它可能只是為了您的 EF Core 模型進行手動變更最簡單。</span><span class="sxs-lookup"><span data-stu-id="e6435-176">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="e6435-177">例如，重新命名資料表或資料行、 移除資料行，或更新資料行的類型是要在程式碼中不重要的變更。</span><span class="sxs-lookup"><span data-stu-id="e6435-177">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="e6435-178">更重要的變更，不過，不是以輕鬆進行手動。</span><span class="sxs-lookup"><span data-stu-id="e6435-178">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="e6435-179">其中一個常見的工作流程是將反向工程的模型，一次使用的資料庫`-Force`(PMC) 或`--force`(CLI) 來使用更新的一個覆寫現有的模型。</span><span class="sxs-lookup"><span data-stu-id="e6435-179">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="e6435-180">另一個常被要求的功能是能夠同時保留重新命名、 型別階層架構等的自訂更新資料庫中的模型。使用問題[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)來追蹤這項功能的進度。</span><span class="sxs-lookup"><span data-stu-id="e6435-180">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="e6435-181">如果您反向重新設計資料庫中的模型，您已對檔案進行任何變更將會遺失。</span><span class="sxs-lookup"><span data-stu-id="e6435-181">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
