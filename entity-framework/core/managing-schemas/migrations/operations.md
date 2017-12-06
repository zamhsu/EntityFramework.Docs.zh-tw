---
title: "自訂的移轉作業-EF 核心"
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d41409dee034e84d22092a5f9111dd79c87dcec3
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
<a name="custom-migrations-operations"></a>自訂的移轉作業
============================
MigrationBuilder API 可讓您在移轉時，執行許多不同種類的作業，但很遠低於詳盡。 不過，應用程式開發介面也是可延伸可讓您定義您自己的作業。 有兩種方式來擴充應用程式開發介面： 使用`Sql()`方法，或藉由定義自訂`MigrationOperation`物件。

為了說明，讓我們看看實作會建立資料庫使用者，使用每一種方法的作業。 在我們的移轉中，我們想要啟用撰寫下列程式碼：

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a>使用 MigrationBuilder.Sql()
----------------------------
若要實作自訂作業最簡單的方式是定義的擴充方法，呼叫`MigrationBuilder.Sql()`。
以下是範例會產生適當的 Transact SQL。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

如果您移轉需要支援多個資料庫提供者，您可以使用`MigrationBuilder.ActiveProvider`屬性。 以下是支援 Microsoft SQL Server 和 PostreSQL 範例。

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

這個方法只有您知道每個提供者將會套用您的自訂作業。

<a name="using-a-migrationoperation"></a>使用 MigrationOperation
---------------------------
若要分離 SQL 的自訂作業，您可以定義您自己`MigrationOperation`來代表它。 作業接著會傳遞給提供者，因此它可以判斷適當的 SQL 產生。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

使用這個方法，擴充方法只需要將其加入至這些作業`MigrationBuilder.Operations`。

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

這個方法需要知道如何產生 SQL 這項作業中的每個提供者及其`IMigrationsSqlGenerator`服務。 以下是覆寫以處理新作業的 SQL Server 的產生器的範例。

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

取代更新的一個預設移轉 sql 產生器服務。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
