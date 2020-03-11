---
title: 具有多個結果集的預存程式-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
ms.openlocfilehash: 098ed88ba52e211965baf3660f0e51bd74c71efd
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78418701"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>具有多個結果集的預存程式
有時候使用預存程式時，您必須傳回一個以上的結果集。 此案例通常用來減少撰寫單一畫面所需的資料庫往返次數。 在 EF5 之前，Entity Framework 會允許呼叫預存程式，但只會將第一個結果集傳回給呼叫程式碼。

本文將示範兩種方式，讓您用來從 Entity Framework 中的預存程式存取一個以上的結果集。 其中一個只使用程式碼，並搭配使用程式碼 first 和 EF 設計工具，另一種則適用于 EF 設計工具。 在未來的 Entity Framework 版本中，這項工具和 API 支援應該會改善。

## <a name="model"></a>模型

本文中的範例使用基本的 Blog 和文章模型，其中的 blog 有許多文章，而文章則屬於單一的 blog。 我們將在資料庫中使用預存程式，以傳回所有的 blog 和文章，如下所示：

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>使用程式碼存取多個結果集

我們可以執行使用程式碼來發出原始 SQL 命令，以執行我們的預存程式。 這種方法的優點是它可以與 Code first 和 EF Designer 搭配使用。

為了讓多個結果集運作，我們必須使用 IObjectCoNtextAdapter 介面來卸載 ObjectCoNtext API。

一旦有了 ObjectCoNtext 之後，我們就可以使用「轉譯」方法，將預存程式的結果轉譯成可在 EF 中以一般方式追蹤和使用的實體。 下列程式碼範例將示範此動作。

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

「轉譯」方法會接受我們在執行程式、EntitySet 名稱和 MergeOption 時收到的讀取器。 EntitySet 名稱會與衍生內容上的 DbSet 屬性相同。 如果記憶體中已有相同的實體存在，MergeOption 列舉會控制如何處理結果。

在此我們呼叫 NextResult 之前，我們會逐一查看 blog 的集合，因為上述程式碼必須先取用第一個結果集，才能移到下一個結果集。

一旦呼叫這兩個轉譯方法，EF 就會追蹤 Blog 和 Post 實體，就像任何其他實體一樣，也可以修改或刪除並儲存為正常。

>[!NOTE]
> 當 EF 使用「轉譯」方法建立實體時，不會將任何對應納入考慮。 它只會比對結果集中的資料行名稱與類別上的屬性名稱。

>[!NOTE]
> 如果您已啟用消極式載入，存取其中一個 blog 實體的 post 屬性，則 EF 會連接到資料庫以延遲載入所有貼文，即使我們已全部載入也一樣。 這是因為 EF 無法得知您是否已載入所有貼文，或資料庫中是否有其他資料。 如果您想要避免這種情況，就必須停用消極式載入。

## <a name="multiple-result-sets-with-configured-in-edmx"></a>在 EDMX 中設定的多個結果集

>[!NOTE]
> 您必須以 .NET Framework 4.5 為目標，才能夠在 EDMX 中設定多個結果集。 如果您的目標是 .NET 4.0，您可以使用上一節所示的程式碼型方法。

如果您使用的是 EF Designer，您也可以修改您的模型，讓它知道將傳回的不同結果集。 請先知道，這項工具並不是多個結果集感知，因此您必須手動編輯 edmx 檔案。 像這樣編輯 .edmx 檔案可以正常執行，但它也會在 VS 中中斷模型的驗證。 因此，如果您驗證模型，一律會收到錯誤。

-   若要這麼做，您必須將預存程式加入至您的模型，就像單一結果集查詢一樣。
-   這麼做之後，您必須以滑鼠右鍵按一下模型，然後選取 [**開啟方式]。** then **Xml**

    ![開啟身分](~/ef6/media/openas.png)

當您以 XML 形式開啟模型之後，您需要執行下列步驟：

-   在您的模型中尋找複雜型別和函數匯入：

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

 

-   移除複雜型別
-   更新函式匯入，使其對應至您的實體，在我們的案例中看起來會像下面這樣：

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

這會告知模型，預存程式會傳回兩個集合，其中一個是 blog 專案和一個 post 專案。

-   尋找函數對應元素：

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

-   針對每個傳回的實體，將結果對應取代為一個，如下所示：

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

您也可以將結果集對應至複雜類型，例如預設建立的型別。 若要這麼做，您可以建立新的複雜型別，而不是移除它們，並使用您在上述範例中使用機構名稱的任何地方的複雜類型。

這些對應一旦變更之後，您就可以儲存模型並執行下列程式碼，以使用預存程式：

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
> 如果您手動編輯模型的 edmx 檔案，如果您從資料庫重新產生模型，就會覆寫該檔案。

## <a name="summary"></a>摘要

我們在此示範了兩種不同的方法，可以使用 Entity Framework 來存取多個結果集。 這兩種方法都同樣有效，視您的情況和喜好設定而定，您應該選擇最適合您情況的選項。 計畫在未來的 Entity Framework 版本中，將會改善多個結果集的支援，而且不再需要執行本檔中的步驟。
