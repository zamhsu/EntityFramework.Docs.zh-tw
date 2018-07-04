---
title: 自訂的移轉作業-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 84d80175e719c950844b13688e1a4992614f25d8
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163138"
---
<a name="custom-migrations-operations"></a><span data-ttu-id="27eda-102">自訂的移轉作業</span><span class="sxs-lookup"><span data-stu-id="27eda-102">Custom Migrations Operations</span></span>
============================
<span data-ttu-id="27eda-103">MigrationBuilder API 可讓您在移轉時，執行許多不同種類的作業，但很遠低於詳盡。</span><span class="sxs-lookup"><span data-stu-id="27eda-103">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="27eda-104">不過，應用程式開發介面也是可延伸可讓您定義您自己的作業。</span><span class="sxs-lookup"><span data-stu-id="27eda-104">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="27eda-105">有兩種方式來擴充應用程式開發介面： 使用`Sql()`方法，或藉由定義自訂`MigrationOperation`物件。</span><span class="sxs-lookup"><span data-stu-id="27eda-105">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="27eda-106">為了說明，讓我們看看實作會建立資料庫使用者，使用每一種方法的作業。</span><span class="sxs-lookup"><span data-stu-id="27eda-106">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="27eda-107">在我們的移轉中，我們想要啟用撰寫下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="27eda-107">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a><span data-ttu-id="27eda-108">使用 MigrationBuilder.Sql()</span><span class="sxs-lookup"><span data-stu-id="27eda-108">Using MigrationBuilder.Sql()</span></span>
----------------------------
<span data-ttu-id="27eda-109">若要實作自訂作業最簡單的方式是定義的擴充方法，呼叫`MigrationBuilder.Sql()`。</span><span class="sxs-lookup"><span data-stu-id="27eda-109">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span>
<span data-ttu-id="27eda-110">以下是範例會產生適當的 Transact SQL。</span><span class="sxs-lookup"><span data-stu-id="27eda-110">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="27eda-111">如果您移轉需要支援多個資料庫提供者，您可以使用`MigrationBuilder.ActiveProvider`屬性。</span><span class="sxs-lookup"><span data-stu-id="27eda-111">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="27eda-112">以下是支援 Microsoft SQL Server 和 PostgreSQL 範例。</span><span class="sxs-lookup"><span data-stu-id="27eda-112">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

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
}
```

<span data-ttu-id="27eda-113">這個方法只有您知道每個提供者將會套用您的自訂作業。</span><span class="sxs-lookup"><span data-stu-id="27eda-113">This approach only works if you know every provider where your custom operation will be applied.</span></span>

<a name="using-a-migrationoperation"></a><span data-ttu-id="27eda-114">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="27eda-114">Using a MigrationOperation</span></span>
---------------------------
<span data-ttu-id="27eda-115">若要分離 SQL 的自訂作業，您可以定義您自己`MigrationOperation`來代表它。</span><span class="sxs-lookup"><span data-stu-id="27eda-115">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="27eda-116">作業接著會傳遞給提供者，因此它可以判斷適當的 SQL 產生。</span><span class="sxs-lookup"><span data-stu-id="27eda-116">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="27eda-117">使用這個方法，擴充方法只需要將其加入至這些作業`MigrationBuilder.Operations`。</span><span class="sxs-lookup"><span data-stu-id="27eda-117">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

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

<span data-ttu-id="27eda-118">這個方法需要知道如何產生 SQL 這項作業中的每個提供者及其`IMigrationsSqlGenerator`服務。</span><span class="sxs-lookup"><span data-stu-id="27eda-118">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="27eda-119">以下是覆寫以處理新作業的 SQL Server 的產生器的範例。</span><span class="sxs-lookup"><span data-stu-id="27eda-119">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

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
        var stringMapping = Dependencies.TypeMapper.GetMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

<span data-ttu-id="27eda-120">取代更新的一個預設移轉 sql 產生器服務。</span><span class="sxs-lookup"><span data-stu-id="27eda-120">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
