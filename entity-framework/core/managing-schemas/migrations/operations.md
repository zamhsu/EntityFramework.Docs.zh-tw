---
title: 自訂遷移作業-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: bd2bfdc24977a47eaf7a6756a88b758b563d818a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416874"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="acd17-102">自訂的遷移作業</span><span class="sxs-lookup"><span data-stu-id="acd17-102">Custom Migrations Operations</span></span>

<span data-ttu-id="acd17-103">MigrationBuilder API 可讓您在遷移期間執行許多不同種類的作業，但不是很徹底。</span><span class="sxs-lookup"><span data-stu-id="acd17-103">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="acd17-104">不過，此 API 也可延伸，讓您定義自己的作業。</span><span class="sxs-lookup"><span data-stu-id="acd17-104">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="acd17-105">有兩種方式可以擴充 API：使用 `Sql()` 方法，或藉由定義自訂 `MigrationOperation` 物件。</span><span class="sxs-lookup"><span data-stu-id="acd17-105">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="acd17-106">為了說明，讓我們看看如何使用每種方法來建立資料庫使用者。</span><span class="sxs-lookup"><span data-stu-id="acd17-106">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="acd17-107">在我們的遷移中，我們想要啟用撰寫下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="acd17-107">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="acd17-108">使用 MigrationBuilder （）</span><span class="sxs-lookup"><span data-stu-id="acd17-108">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="acd17-109">執行自訂作業最簡單的方式，就是定義會呼叫 `MigrationBuilder.Sql()`的擴充方法。</span><span class="sxs-lookup"><span data-stu-id="acd17-109">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="acd17-110">以下範例會產生適當的 Transact-sql。</span><span class="sxs-lookup"><span data-stu-id="acd17-110">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="acd17-111">如果您的遷移需要支援多個資料庫提供者，您可以使用 [`MigrationBuilder.ActiveProvider`] 屬性。</span><span class="sxs-lookup"><span data-stu-id="acd17-111">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="acd17-112">以下是同時支援 Microsoft SQL Server 和于 postgresql 的範例。</span><span class="sxs-lookup"><span data-stu-id="acd17-112">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

<span data-ttu-id="acd17-113">只有在您知道將套用自訂作業的每個提供者時，這個方法才有效。</span><span class="sxs-lookup"><span data-stu-id="acd17-113">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="acd17-114">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="acd17-114">Using a MigrationOperation</span></span>

<span data-ttu-id="acd17-115">若要將自訂作業與 SQL 分離，您可以定義自己的 `MigrationOperation` 來代表它。</span><span class="sxs-lookup"><span data-stu-id="acd17-115">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="acd17-116">作業接著會傳遞至提供者，讓它可以判斷要產生的適當 SQL。</span><span class="sxs-lookup"><span data-stu-id="acd17-116">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="acd17-117">使用此方法時，擴充方法只需要將其中一項作業新增至 `MigrationBuilder.Operations`。</span><span class="sxs-lookup"><span data-stu-id="acd17-117">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

<span data-ttu-id="acd17-118">這種方法需要每個提供者知道如何在其 `IMigrationsSqlGenerator` 服務中產生這項作業的 SQL。</span><span class="sxs-lookup"><span data-stu-id="acd17-118">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="acd17-119">以下是覆寫 SQL Server 的產生器以處理新作業的範例。</span><span class="sxs-lookup"><span data-stu-id="acd17-119">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

<span data-ttu-id="acd17-120">以更新的 sql 產生器服務取代預設的遷移。</span><span class="sxs-lookup"><span data-stu-id="acd17-120">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
