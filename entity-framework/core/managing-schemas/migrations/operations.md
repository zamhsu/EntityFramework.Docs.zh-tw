---
title: 自訂遷移作業-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: bd2bfdc24977a47eaf7a6756a88b758b563d818a
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812053"
---
# <a name="custom-migrations-operations"></a>自訂的遷移作業

MigrationBuilder API 可讓您在遷移期間執行許多不同種類的作業，但不是很徹底。 不過，此 API 也可延伸，讓您定義自己的作業。 有兩種方式可以擴充 API：使用 `Sql()` 方法，或藉由定義自訂 `MigrationOperation` 物件。

為了說明，讓我們看看如何使用每種方法來建立資料庫使用者。 在我們的遷移中，我們想要啟用撰寫下列程式碼：

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>使用 MigrationBuilder （）

執行自訂作業最簡單的方式，就是定義會呼叫 `MigrationBuilder.Sql()`的擴充方法。 以下範例會產生適當的 Transact-sql。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

如果您的遷移需要支援多個資料庫提供者，您可以使用 [`MigrationBuilder.ActiveProvider`] 屬性。 以下是同時支援 Microsoft SQL Server 和于 postgresql 的範例。

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

只有在您知道將套用自訂作業的每個提供者時，這個方法才有效。

## <a name="using-a-migrationoperation"></a>使用 MigrationOperation

若要將自訂作業與 SQL 分離，您可以定義自己的 `MigrationOperation` 來代表它。 作業接著會傳遞至提供者，讓它可以判斷要產生的適當 SQL。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

使用此方法時，擴充方法只需要將其中一項作業新增至 `MigrationBuilder.Operations`。

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

這種方法需要每個提供者知道如何在其 `IMigrationsSqlGenerator` 服務中產生這項作業的 SQL。 以下是覆寫 SQL Server 的產生器以處理新作業的範例。

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

以更新的 sql 產生器服務取代預設的遷移。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
