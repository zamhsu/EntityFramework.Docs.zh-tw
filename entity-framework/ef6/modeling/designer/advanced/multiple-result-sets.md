---
title: 預存程序包含多個結果集-EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
ms.openlocfilehash: 56c28f05bd7efe1b54d6cadd32afe0e9c6cf38b5
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251007"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>多個結果集的預存程序
有時候在使用預存程序，您將需要傳回多個結果集。 此案例中常用來減少資料庫的撰寫單一畫面所需的往返。 之前 EF5，Entity Framework 可讓呼叫的預存程序，但只會傳回第一個結果集，以呼叫程式碼。

本文將說明您可用來存取 Entity Framework 中的預存程序的多個結果集的兩種方式。 其中使用了只是程式碼，並適用於這兩個程式碼第一次和 EF 設計工具，另一個僅適用於使用 EF 設計工具。 工具和 API 支援，這應該會在未來改善 Entity Framework 的版本。

## <a name="model"></a>型號

這篇文章中的範例會使用基本的部落格，其中一篇部落格有許多文章和張貼的文章模型屬於單一的部落格。 我們將使用預存程序中的資料庫，傳回所有的部落格和文章，如下所示：

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>使用程式碼存取多個結果集

我們可以執行使用程式碼，以發出原始的 SQL 命令，以執行我們的預存程序。 這種方法的優點是，它適用於這兩個程式碼第一次和 EF 設計工具。

若要取得多個結果設定我們要卸除 ObjectContext api，藉由使用 IObjectContextAdapter 介面的工作。

一旦我們擁有 ObjectContext，然後我們可以使用轉譯方法，將我們的預存程序的結果轉譯成可追蹤和平常一樣使用 EF 中的實體。 下列程式碼範例會示範這個作用中。

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

Translate 方法會接受我們收到我們執行此程序、 EntitySet 名稱，以及 MergeOption 時讀取器。 EntitySet 名稱會與您的衍生內容上的 DbSet 屬性相同。 MergeOption 列舉控制如果相同的實體已經存在於記憶體中，如何處理結果。

這裡我們逐一查看集合的部落格前 NextResult，這點很重要假設上述的程式碼因為移至下一個結果集之前，必須使用第一個結果集。

一旦將轉譯這兩個則與任何其他實體相同的方式追蹤的部落格和文章實體的 EF，因此可修改或刪除和儲存如往常一樣，會呼叫方法。

>[!NOTE]
> 建立使用轉譯方法的實體時，EF 就不會考量任何對應。 它只會比對結果集中的屬性名稱，在您的類別上的資料行名稱。

>[!NOTE]
> 如果您有存取文章屬性上的部落格實體的其中一個消極式載入已啟用，EF 會連線至資料庫，延遲載入所有文章，即使我們已經載入它們全部。 這是因為 EF 不知道您已載入所有貼文，或是否有多個資料庫中。 如果您想要避免這個問題將會需要停用消極式載入。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>使用 設定在 EDMX 的多個結果集

>[!NOTE]
> 您必須要能夠在 EDMX 設定多個結果集的.NET Framework 4.5 為目標。 如果您的目標.NET 4.0，您可以使用上一節所示的程式碼為基礎的方法。

如果您使用 EF 設計工具，您也可以修改您的模型，讓它知道將會傳回不同結果集。 因此您必須手動編輯 edmx 檔案，設定感知，手動為這項工具不是多個結果之前要知道一件事。 像這將會運作，但它也會中斷模型的驗證，在 VS 中，請編輯 edmx 檔。 因此如果您驗證您的模型就會看到錯誤。

-   若要這樣做，您需要將預存程序加入至模型，您需要單一結果集查詢。
-   一旦您擁有這則需要以滑鼠右鍵按一下您的模型，然後選取**開啟方式...** 然後**Xml**

    ![開啟為](~/ef6/media/openas.png)

在您具備模型開啟為 XML，則您需要執行下列步驟：

-   在您的模型中找到複雜型別和函式匯入：

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

 

-   移除的複雜型別
-   更新函式匯入，讓它對應到您的實體，在本例中它看起來如下所示：

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

這會告知模型的預存程序會傳回兩個的集合，其中一個部落格文章和張貼的項目之一。

-   尋找函式的對應項目：

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

-   結果對應取代其中的每個實體傳回，如下所示：

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

它也可將結果集對應至複雜型別，例如依預設，建立一個。 若要這樣做您建立新的複雜類型，而不是移除它們，以及使用複雜型別的所有位置，您有上述範例中使用的實體名稱。

之後您就可以將模型儲存並執行下列的程式碼，以使用預存程序已變更這些對應：

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
> 如果您手動編輯模型的 edmx 檔案，如果您曾經重新產生資料庫中的模型，它將會覆寫。

## <a name="summary"></a>總結

這裡我們已示範使用 Entity Framework 的兩種不同方法存取多個結果集。 兩者都同樣有效，視您的情況而定，喜好設定，而且您應該選擇似乎適合您情況的一個。 已規劃的支援多個結果集就會在未來改善 Entity Framework 的版本和執行這份文件中的步驟將不再會需要。
