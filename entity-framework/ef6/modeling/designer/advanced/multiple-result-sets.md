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
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="6a02c-102">具有多個結果集的預存程式</span><span class="sxs-lookup"><span data-stu-id="6a02c-102">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="6a02c-103">有時候使用預存程式時，您必須傳回一個以上的結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-103">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="6a02c-104">此案例通常用來減少撰寫單一畫面所需的資料庫往返次數。</span><span class="sxs-lookup"><span data-stu-id="6a02c-104">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="6a02c-105"> 在 EF5 之前，Entity Framework 會允許呼叫預存程式，但只會將第一個結果集傳回給呼叫程式碼。</span><span class="sxs-lookup"><span data-stu-id="6a02c-105"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="6a02c-106">本文將示範兩種方式，讓您用來從 Entity Framework 中的預存程式存取一個以上的結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-106">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="6a02c-107">其中一個只使用程式碼，並搭配使用程式碼 first 和 EF 設計工具，另一種則適用于 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="6a02c-107">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="6a02c-108">在未來的 Entity Framework 版本中，這項工具和 API 支援應該會改善。</span><span class="sxs-lookup"><span data-stu-id="6a02c-108">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="6a02c-109">模型</span><span class="sxs-lookup"><span data-stu-id="6a02c-109">Model</span></span>

<span data-ttu-id="6a02c-110">本文中的範例使用基本的 Blog 和文章模型，其中的 blog 有許多文章，而文章則屬於單一的 blog。</span><span class="sxs-lookup"><span data-stu-id="6a02c-110">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="6a02c-111">我們將在資料庫中使用預存程式，以傳回所有的 blog 和文章，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6a02c-111">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="6a02c-112">使用程式碼存取多個結果集</span><span class="sxs-lookup"><span data-stu-id="6a02c-112">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="6a02c-113">我們可以執行使用程式碼來發出原始 SQL 命令，以執行我們的預存程式。</span><span class="sxs-lookup"><span data-stu-id="6a02c-113">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="6a02c-114">這種方法的優點是它可以與 Code first 和 EF Designer 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="6a02c-114">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="6a02c-115">為了讓多個結果集運作，我們必須使用 IObjectCoNtextAdapter 介面來卸載 ObjectCoNtext API。</span><span class="sxs-lookup"><span data-stu-id="6a02c-115">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="6a02c-116">一旦有了 ObjectCoNtext 之後，我們就可以使用「轉譯」方法，將預存程式的結果轉譯成可在 EF 中以一般方式追蹤和使用的實體。</span><span class="sxs-lookup"><span data-stu-id="6a02c-116">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="6a02c-117">下列程式碼範例將示範此動作。</span><span class="sxs-lookup"><span data-stu-id="6a02c-117">The following code sample demonstrates this in action.</span></span>

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

<span data-ttu-id="6a02c-118">「轉譯」方法會接受我們在執行程式、EntitySet 名稱和 MergeOption 時收到的讀取器。</span><span class="sxs-lookup"><span data-stu-id="6a02c-118">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="6a02c-119">EntitySet 名稱會與衍生內容上的 DbSet 屬性相同。</span><span class="sxs-lookup"><span data-stu-id="6a02c-119">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="6a02c-120">如果記憶體中已有相同的實體存在，MergeOption 列舉會控制如何處理結果。</span><span class="sxs-lookup"><span data-stu-id="6a02c-120">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="6a02c-121">在此我們呼叫 NextResult 之前，我們會逐一查看 blog 的集合，因為上述程式碼必須先取用第一個結果集，才能移到下一個結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-121">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="6a02c-122">一旦呼叫這兩個轉譯方法，EF 就會追蹤 Blog 和 Post 實體，就像任何其他實體一樣，也可以修改或刪除並儲存為正常。</span><span class="sxs-lookup"><span data-stu-id="6a02c-122">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="6a02c-123">當 EF 使用「轉譯」方法建立實體時，不會將任何對應納入考慮。</span><span class="sxs-lookup"><span data-stu-id="6a02c-123">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="6a02c-124">它只會比對結果集中的資料行名稱與類別上的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="6a02c-124">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="6a02c-125">如果您已啟用消極式載入，存取其中一個 blog 實體的 post 屬性，則 EF 會連接到資料庫以延遲載入所有貼文，即使我們已全部載入也一樣。</span><span class="sxs-lookup"><span data-stu-id="6a02c-125">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="6a02c-126">這是因為 EF 無法得知您是否已載入所有貼文，或資料庫中是否有其他資料。</span><span class="sxs-lookup"><span data-stu-id="6a02c-126">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="6a02c-127">如果您想要避免這種情況，就必須停用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="6a02c-127">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="6a02c-128">在 EDMX 中設定的多個結果集</span><span class="sxs-lookup"><span data-stu-id="6a02c-128">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="6a02c-129">您必須以 .NET Framework 4.5 為目標，才能夠在 EDMX 中設定多個結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-129">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="6a02c-130">如果您的目標是 .NET 4.0，您可以使用上一節所示的程式碼型方法。</span><span class="sxs-lookup"><span data-stu-id="6a02c-130">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="6a02c-131">如果您使用的是 EF Designer，您也可以修改您的模型，讓它知道將傳回的不同結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-131">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="6a02c-132">請先知道，這項工具並不是多個結果集感知，因此您必須手動編輯 edmx 檔案。</span><span class="sxs-lookup"><span data-stu-id="6a02c-132">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="6a02c-133">像這樣編輯 .edmx 檔案可以正常執行，但它也會在 VS 中中斷模型的驗證。</span><span class="sxs-lookup"><span data-stu-id="6a02c-133">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="6a02c-134">因此，如果您驗證模型，一律會收到錯誤。</span><span class="sxs-lookup"><span data-stu-id="6a02c-134">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="6a02c-135">若要這麼做，您必須將預存程式加入至您的模型，就像單一結果集查詢一樣。</span><span class="sxs-lookup"><span data-stu-id="6a02c-135">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="6a02c-136">這麼做之後，您必須以滑鼠右鍵按一下模型，然後選取 [**開啟方式]。**</span><span class="sxs-lookup"><span data-stu-id="6a02c-136">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="6a02c-137">then **Xml**</span><span class="sxs-lookup"><span data-stu-id="6a02c-137">then **Xml**</span></span>

    ![開啟身分](~/ef6/media/openas.png)

<span data-ttu-id="6a02c-139">當您以 XML 形式開啟模型之後，您需要執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="6a02c-139">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="6a02c-140">在您的模型中尋找複雜型別和函數匯入：</span><span class="sxs-lookup"><span data-stu-id="6a02c-140">Find the complex type and function import in your model:</span></span>

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

 

-   <span data-ttu-id="6a02c-141">移除複雜型別</span><span class="sxs-lookup"><span data-stu-id="6a02c-141">Remove the complex type</span></span>
-   <span data-ttu-id="6a02c-142">更新函式匯入，使其對應至您的實體，在我們的案例中看起來會像下面這樣：</span><span class="sxs-lookup"><span data-stu-id="6a02c-142">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="6a02c-143">這會告知模型，預存程式會傳回兩個集合，其中一個是 blog 專案和一個 post 專案。</span><span class="sxs-lookup"><span data-stu-id="6a02c-143">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="6a02c-144">尋找函數對應元素：</span><span class="sxs-lookup"><span data-stu-id="6a02c-144">Find the function mapping element:</span></span>

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

-   <span data-ttu-id="6a02c-145">針對每個傳回的實體，將結果對應取代為一個，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6a02c-145">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

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

<span data-ttu-id="6a02c-146">您也可以將結果集對應至複雜類型，例如預設建立的型別。</span><span class="sxs-lookup"><span data-stu-id="6a02c-146">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="6a02c-147">若要這麼做，您可以建立新的複雜型別，而不是移除它們，並使用您在上述範例中使用機構名稱的任何地方的複雜類型。</span><span class="sxs-lookup"><span data-stu-id="6a02c-147">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="6a02c-148">這些對應一旦變更之後，您就可以儲存模型並執行下列程式碼，以使用預存程式：</span><span class="sxs-lookup"><span data-stu-id="6a02c-148">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

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
> <span data-ttu-id="6a02c-149">如果您手動編輯模型的 edmx 檔案，如果您從資料庫重新產生模型，就會覆寫該檔案。</span><span class="sxs-lookup"><span data-stu-id="6a02c-149">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="6a02c-150">摘要</span><span class="sxs-lookup"><span data-stu-id="6a02c-150">Summary</span></span>

<span data-ttu-id="6a02c-151">我們在此示範了兩種不同的方法，可以使用 Entity Framework 來存取多個結果集。</span><span class="sxs-lookup"><span data-stu-id="6a02c-151">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="6a02c-152">這兩種方法都同樣有效，視您的情況和喜好設定而定，您應該選擇最適合您情況的選項。</span><span class="sxs-lookup"><span data-stu-id="6a02c-152">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="6a02c-153">計畫在未來的 Entity Framework 版本中，將會改善多個結果集的支援，而且不再需要執行本檔中的步驟。</span><span class="sxs-lookup"><span data-stu-id="6a02c-153">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
