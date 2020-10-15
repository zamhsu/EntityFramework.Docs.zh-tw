---
title: 具有多個結果集的預存程式-EF6
description: Entity Framework 6 中具有多個結果集的預存程式
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 8c80e2b8c861a763b7f63ea2523194028498dace
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066274"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>具有多個結果集的預存程式
有時候，使用預存程式時，您必須傳回一個以上的結果集。 此案例通常用來減少撰寫單一畫面所需的資料庫往返次數。在 EF5 之前，Entity Framework 可讓您呼叫預存程式，但只會將第一個結果集傳回給呼叫程式碼。

本文將為您示範兩種方式，可讓您用來從 Entity Framework 中的預存程式存取多個結果集。 其中一個只使用程式碼，並使用程式碼 first 和 EF 設計工具，另一個則適用于 EF 設計工具。 這項工具和 API 支援在未來的 Entity Framework 版本中應該會改善。

## <a name="model"></a>模型

本文中的範例使用基本的 Blog 和貼文模型，其中的 blog 有許多貼文，而貼文屬於單一的 blog。 我們會使用資料庫中的預存程式，傳回所有的 blog 和 post，如下所示：

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>使用程式碼存取多個結果集

我們可以執行使用程式碼來發出原始 SQL 命令，以執行預存程式。 這種方法的優點是它可以搭配 Code first 和 EF Designer 一起使用。

若要取得多個結果集，我們必須使用 IObjectCoNtextAdapter 介面來捨棄 ObjectCoNtext API。

一旦有 ObjectCoNtext 之後，我們就可以使用「轉譯」方法，將預存程式的結果轉譯成可在 EF 中追蹤和使用的實體。 下列程式碼範例將示範這項操作。

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

轉譯方法會接受我們執行程式時所收到的讀取器、EntitySet 名稱和 MergeOption。 EntitySet 名稱將與您衍生內容上的 DbSet 屬性相同。 如果記憶體中已有相同的實體，MergeOption 列舉會控制如何處理結果。

在這裡，我們會在呼叫 NextResult 之前逐一查看 blog 集合，因為上述程式碼必須先取用第一個結果集，才能移至下一個結果集。

一旦呼叫這兩個轉譯方法，EF 就會以與其他任何實體相同的方式來追蹤 Blog 和 Post 實體，因此可以像平常一樣修改或刪除和儲存。

>[!NOTE]
> 當 EF 使用平移方法建立實體時，不會將任何對應納入考慮。 它只會將結果集中的資料行名稱與類別上的屬性名稱相符。

>[!NOTE]
> 如果您已啟用消極式載入，在其中一個 blog 實體上存取 post 屬性，EF 將會連接到資料庫，以延遲載入所有貼文，即使我們已經載入全部貼文也一樣。 這是因為 EF 無法得知您是否已載入所有貼文，或資料庫中是否有更多的資料。 如果您想要避免這種情況，就必須停用消極式載入。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>在 EDMX 中設定的多個結果集

>[!NOTE]
> 您必須以 .NET Framework 4.5 為目標，才能在 EDMX 中設定多個結果集。 如果您的目標是 .NET 4.0，您可以使用上一節所示的程式碼型方法。

如果您使用 EF Designer，也可以修改模型，讓它知道將傳回的不同結果集。 事先要知道的是，這項工具不會有多個結果集感知，因此您必須手動編輯 edmx 檔案。 像這樣編輯 edmx 檔案可以正常運作，但它也會在 VS 中中斷模型的驗證。 因此，如果您驗證您的模型，您一律會收到錯誤。

-   若要這樣做，您必須將預存程式加入至您的模型，就像單一結果集查詢一樣。
-   完成之後，您必須以滑鼠右鍵按一下您的模型，然後選取 [**開啟方式**]。 then **Xml**

    ![開啟為](~/ef6/media/openas.png)

當您將模型開啟為 XML 之後，您必須執行下列步驟：

-   在您的模型中尋找複雜型別和函式匯入：

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   移除複雜類型
-   更新函式匯入，使其對應至您的實體，在我們的案例中，它看起來會如下所示：

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

這會告訴模型，預存程式將會傳回兩個集合，其中一個是 blog 專案和一個張貼專案。

-   尋找函式對應元素：

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   將傳回的每個實體的結果對應取代為一個，如下所示：

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

您也可以將結果集對應至複雜類型，例如預設建立的類型。 若要這樣做，您可以建立新的複雜型別，而不是移除它們，並使用您在上述範例中使用機構名稱的任何地方的複雜類型。

變更這些對應之後，您就可以儲存模型並執行下列程式碼，以使用預存程式：

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> 如果您手動編輯模型的 edmx 檔案，如果您從資料庫重新產生模型，將會覆寫此檔案。

## <a name="summary"></a>摘要

在這裡，我們示範了兩種使用 Entity Framework 來存取多個結果集的不同方法。 這兩個都同樣有效，取決於您的情況和喜好設定，而且您應該選擇最適合您情況的選項。 計畫在未來的 Entity Framework 版本中，將會改善多個結果集的支援，而且將不再需要執行本檔中的步驟。
