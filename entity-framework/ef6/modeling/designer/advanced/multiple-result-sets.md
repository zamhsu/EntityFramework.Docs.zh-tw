---
title: 預存程序包含多個結果集-EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 1b3797f9-cd3d-4752-a55e-47b84b399dc1
caps.latest.revision: 3
ms.openlocfilehash: 68d544b0c553868ad1ff36cd24db19cff08db073
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120374"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="d8340-102">多個結果集的預存程序</span><span class="sxs-lookup"><span data-stu-id="d8340-102">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="d8340-103">有時候在使用預存程序，您將需要傳回多個結果集。</span><span class="sxs-lookup"><span data-stu-id="d8340-103">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="d8340-104">此案例中常用來減少資料庫的撰寫單一畫面所需的往返。</span><span class="sxs-lookup"><span data-stu-id="d8340-104">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span> <span data-ttu-id="d8340-105">之前 EF5，Entity Framework 可讓呼叫的預存程序，但只會傳回第一個結果集，以呼叫程式碼。</span><span class="sxs-lookup"><span data-stu-id="d8340-105">Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="d8340-106">本文將說明您可用來存取 Entity Framework 中的預存程序的多個結果集的兩種方式。</span><span class="sxs-lookup"><span data-stu-id="d8340-106">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="d8340-107">其中使用了只是程式碼，並適用於這兩個程式碼第一次和 EF 設計工具，另一個僅適用於使用 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="d8340-107">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="d8340-108">工具和 API 支援，這應該會在未來改善 Entity Framework 的版本。</span><span class="sxs-lookup"><span data-stu-id="d8340-108">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="d8340-109">型號</span><span class="sxs-lookup"><span data-stu-id="d8340-109">Model</span></span>

<span data-ttu-id="d8340-110">這篇文章中的範例會使用基本的部落格，其中一篇部落格有許多文章和張貼的文章模型屬於單一的部落格。</span><span class="sxs-lookup"><span data-stu-id="d8340-110">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="d8340-111">我們將使用預存程序中的資料庫，傳回所有的部落格和文章，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8340-111">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="d8340-112">使用程式碼存取多個結果集</span><span class="sxs-lookup"><span data-stu-id="d8340-112">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="d8340-113">我們可以執行使用程式碼，以發出原始的 SQL 命令，以執行我們的預存程序。</span><span class="sxs-lookup"><span data-stu-id="d8340-113">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="d8340-114">這種方法的優點是，它適用於這兩個程式碼第一次和 EF 設計工具。</span><span class="sxs-lookup"><span data-stu-id="d8340-114">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="d8340-115">若要取得多個結果設定我們要卸除 ObjectContext api，藉由使用 IObjectContextAdapter 介面的工作。</span><span class="sxs-lookup"><span data-stu-id="d8340-115">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="d8340-116">一旦我們擁有 ObjectContext，然後我們可以使用轉譯方法，將我們的預存程序的結果轉譯成可追蹤和平常一樣使用 EF 中的實體。</span><span class="sxs-lookup"><span data-stu-id="d8340-116">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="d8340-117">下列程式碼範例會示範這個作用中。</span><span class="sxs-lookup"><span data-stu-id="d8340-117">The following code sample demonstrates this in action.</span></span>

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

<span data-ttu-id="d8340-118">Translate 方法會接受我們收到我們執行此程序、 EntitySet 名稱，以及 MergeOption 時讀取器。</span><span class="sxs-lookup"><span data-stu-id="d8340-118">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="d8340-119">EntitySet 名稱會與您的衍生內容上的 DbSet 屬性相同。</span><span class="sxs-lookup"><span data-stu-id="d8340-119">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="d8340-120">MergeOption 列舉控制如果相同的實體已經存在於記憶體中，如何處理結果。</span><span class="sxs-lookup"><span data-stu-id="d8340-120">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="d8340-121">這裡我們逐一查看集合的部落格前 NextResult，這點很重要假設上述的程式碼因為移至下一個結果集之前，必須使用第一個結果集。</span><span class="sxs-lookup"><span data-stu-id="d8340-121">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="d8340-122">一旦將轉譯這兩個則與任何其他實體相同的方式追蹤的部落格和文章實體的 EF，因此可修改或刪除和儲存如往常一樣，會呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="d8340-122">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="d8340-123">建立使用轉譯方法的實體時，EF 就不會考量任何對應。</span><span class="sxs-lookup"><span data-stu-id="d8340-123">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="d8340-124">它只會比對結果集中的屬性名稱，在您的類別上的資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="d8340-124">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="d8340-125">如果您有存取文章屬性上的部落格實體的其中一個消極式載入已啟用，EF 會連線至資料庫，延遲載入所有文章，即使我們已經載入它們全部。</span><span class="sxs-lookup"><span data-stu-id="d8340-125">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="d8340-126">這是因為 EF 不知道您已載入所有貼文，或是否有多個資料庫中。</span><span class="sxs-lookup"><span data-stu-id="d8340-126">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="d8340-127">如果您想要避免這個問題將會需要停用消極式載入。</span><span class="sxs-lookup"><span data-stu-id="d8340-127">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="d8340-128">使用 設定在 EDMX 的多個結果集</span><span class="sxs-lookup"><span data-stu-id="d8340-128">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="d8340-129">您必須要能夠在 EDMX 設定多個結果集的.NET Framework 4.5 為目標。</span><span class="sxs-lookup"><span data-stu-id="d8340-129">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="d8340-130">如果您的目標.NET 4.0，您可以使用上一節所示的程式碼為基礎的方法。</span><span class="sxs-lookup"><span data-stu-id="d8340-130">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="d8340-131">如果您使用 EF 設計工具，您也可以修改您的模型，讓它知道將會傳回不同結果集。</span><span class="sxs-lookup"><span data-stu-id="d8340-131">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="d8340-132">因此您必須手動編輯 edmx 檔案，設定感知，手動為這項工具不是多個結果之前要知道一件事。</span><span class="sxs-lookup"><span data-stu-id="d8340-132">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="d8340-133">像這將會運作，但它也會中斷模型的驗證，在 VS 中，請編輯 edmx 檔。</span><span class="sxs-lookup"><span data-stu-id="d8340-133">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="d8340-134">因此如果您驗證您的模型就會看到錯誤。</span><span class="sxs-lookup"><span data-stu-id="d8340-134">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="d8340-135">若要這樣做，您需要將預存程序加入至模型，您需要單一結果集查詢。</span><span class="sxs-lookup"><span data-stu-id="d8340-135">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="d8340-136">一旦您擁有這則需要以滑鼠右鍵按一下您的模型，然後選取**開啟方式...**</span><span class="sxs-lookup"><span data-stu-id="d8340-136">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="d8340-137">然後**Xml**</span><span class="sxs-lookup"><span data-stu-id="d8340-137">then **Xml**</span></span>

    ![OpenAs](~/ef6/media/openas.png)

<span data-ttu-id="d8340-139">在您具備模型開啟為 XML，則您需要執行下列步驟：</span><span class="sxs-lookup"><span data-stu-id="d8340-139">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="d8340-140">在您的模型中找到複雜型別和函式匯入：</span><span class="sxs-lookup"><span data-stu-id="d8340-140">Find the complex type and function import in your model:</span></span>

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

 

-   <span data-ttu-id="d8340-141">移除的複雜型別</span><span class="sxs-lookup"><span data-stu-id="d8340-141">Remove the complex type</span></span>
-   <span data-ttu-id="d8340-142">更新函式匯入，讓它對應到您的實體，在本例中它看起來如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8340-142">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="d8340-143">這會告知模型的預存程序會傳回兩個的集合，其中一個部落格文章和張貼的項目之一。</span><span class="sxs-lookup"><span data-stu-id="d8340-143">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="d8340-144">尋找函式的對應項目：</span><span class="sxs-lookup"><span data-stu-id="d8340-144">Find the function mapping element:</span></span>

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

-   <span data-ttu-id="d8340-145">結果對應取代其中的每個實體傳回，如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8340-145">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

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

<span data-ttu-id="d8340-146">它也可將結果集對應至複雜型別，例如依預設，建立一個。</span><span class="sxs-lookup"><span data-stu-id="d8340-146">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="d8340-147">若要這樣做您建立新的複雜類型，而不是移除它們，以及使用複雜型別的所有位置，您有上述範例中使用的實體名稱。</span><span class="sxs-lookup"><span data-stu-id="d8340-147">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="d8340-148">之後您就可以將模型儲存並執行下列的程式碼，以使用預存程序已變更這些對應：</span><span class="sxs-lookup"><span data-stu-id="d8340-148">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

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
> <span data-ttu-id="d8340-149">如果您手動編輯模型的 edmx 檔案，如果您曾經重新產生資料庫中的模型，它將會覆寫。</span><span class="sxs-lookup"><span data-stu-id="d8340-149">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="d8340-150">總結</span><span class="sxs-lookup"><span data-stu-id="d8340-150">Summary</span></span>

<span data-ttu-id="d8340-151">這裡我們已示範使用 Entity Framework 的兩種不同方法存取多個結果集。</span><span class="sxs-lookup"><span data-stu-id="d8340-151">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="d8340-152">兩者都同樣有效，視您的情況而定，喜好設定，而且您應該選擇似乎適合您情況的一個。</span><span class="sxs-lookup"><span data-stu-id="d8340-152">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="d8340-153">已規劃的支援多個結果集就會在未來改善 Entity Framework 的版本和執行這份文件中的步驟將不再會需要。</span><span class="sxs-lookup"><span data-stu-id="d8340-153">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
