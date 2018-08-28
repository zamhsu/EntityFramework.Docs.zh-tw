---
title: 自訂的移轉作業-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/7/2017
ms.openlocfilehash: 33f4fb6dca7c0d2e29abd196194a453f2b390b14
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997090"
---
<a name="custom-migrations-operations"></a>自訂的移轉作業
============================
MigrationBuilder API 可讓您在移轉時，執行許多不同種類的作業，但不能完全詳盡。 不過，此 API 也是可延伸可讓您定義您自己的作業。 有兩種方式可延伸 API： 使用`Sql()`方法，或藉由定義自訂`MigrationOperation`物件。

為了說明，讓我們看看實作建立資料庫使用者，使用每一種方法的作業。 在我們的移轉，我們想要啟用寫入下列程式碼：

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

<a name="using-migrationbuildersql"></a>使用 MigrationBuilder.Sql()
----------------------------
實作自訂作業的最簡單方式是定義擴充方法呼叫`MigrationBuilder.Sql()`。
以下是範例會產生適當的 Transact SQL。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

如果您的移轉需要支援多個資料庫提供者，您可以使用`MigrationBuilder.ActiveProvider`屬性。 以下是支援 Microsoft SQL Server 和 PostgreSQL 的範例。

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

這個方法僅適用於您知道每個提供者將會套用您的自訂作業。

<a name="using-a-migrationoperation"></a>使用 MigrationOperation
---------------------------
將它們分開自訂的作業，從 SQL，您可以定義您自己`MigrationOperation`來代表它。 作業接著會傳遞至提供者，因此可以判斷適當的 SQL 來產生。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

使用此方法時，擴充方法只需要新增至這些作業的其中一個`MigrationBuilder.Operations`。

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

此方法需要知道如何產生 SQL，這項作業中的每個提供者其`IMigrationsSqlGenerator`服務。 以下是覆寫可處理新作業的 SQL Server 的產生器的範例。

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
