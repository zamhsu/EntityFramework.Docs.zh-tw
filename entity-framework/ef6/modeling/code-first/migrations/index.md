---
title: Code First 移轉 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
ms.openlocfilehash: 6c530545968a26d13051d1e682557092bde13bb0
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251033"
---
# <a name="code-first-migrations"></a><span data-ttu-id="66432-102">Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="66432-102">Code First Migrations</span></span>
<span data-ttu-id="66432-103">如果您使用 Code First 工作流程，則建議使用 Code First 移轉來改善應用程式的資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="66432-103">Code First Migrations is the recommended way to evolve you application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="66432-104">移轉會提供一組工具，該工具允許下列項目：</span><span class="sxs-lookup"><span data-stu-id="66432-104">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="66432-105">建立使用 EF 模型的初始資料庫</span><span class="sxs-lookup"><span data-stu-id="66432-105">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="66432-106">產生移轉來追蹤您對 EF 模型進行的變更</span><span class="sxs-lookup"><span data-stu-id="66432-106">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="66432-107">使用這些變更來將您的資料庫保持最新</span><span class="sxs-lookup"><span data-stu-id="66432-107">Keep your database up to date with those changes</span></span>

<span data-ttu-id="66432-108">下列逐步解說將提供 Entity Framework 中的 Code First 移轉概觀。</span><span class="sxs-lookup"><span data-stu-id="66432-108">The following walkthrough will provide an overview Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="66432-109">您可以完成整個逐步解說，或跳至您感興趣的主題。</span><span class="sxs-lookup"><span data-stu-id="66432-109">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="66432-110">其中包含下列主題：</span><span class="sxs-lookup"><span data-stu-id="66432-110">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="66432-111">建置初始模型與資料庫</span><span class="sxs-lookup"><span data-stu-id="66432-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="66432-112">在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="66432-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="66432-113">此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。</span><span class="sxs-lookup"><span data-stu-id="66432-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="66432-114">建立新 **MigrationsDemo** 主控台應用程式</span><span class="sxs-lookup"><span data-stu-id="66432-114">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="66432-115">將最新版本 **EntityFramework** NuGet 套件新增至專案</span><span class="sxs-lookup"><span data-stu-id="66432-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="66432-116">[工具] –&gt; [程式庫套件管理員] –&gt; [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="66432-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="66432-117">執行 **Install-package EntityFramework** 命令</span><span class="sxs-lookup"><span data-stu-id="66432-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="66432-118">使用如下所示的程式碼新增 **Model.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="66432-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="66432-119">此程式碼會定義構成我們網域模型的單一 **Blog** 類別，以及作為 EF Code First 內容的 **BlogContext** 類別</span><span class="sxs-lookup"><span data-stu-id="66432-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="66432-120">現在我們有了一個模型，即可用來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="66432-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="66432-121">使用如下所示的程式碼更新 **Program.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="66432-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="66432-122">執行您的應用程式，而且您將會看到 **MigrationsCodeDemo.BlogContext** 資料庫已建立。</span><span class="sxs-lookup"><span data-stu-id="66432-122">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![資料庫  LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="66432-124">啟用移轉</span><span class="sxs-lookup"><span data-stu-id="66432-124">Enabling Migrations</span></span>

<span data-ttu-id="66432-125">現在可以對我們的模型做更多變更。</span><span class="sxs-lookup"><span data-stu-id="66432-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="66432-126">請將一個 URL 屬性引進 Blog 類別。</span><span class="sxs-lookup"><span data-stu-id="66432-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="66432-127">如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉來更新資料庫」(* [*http://go.microsoft.com/fwlink/?LinkId=238269*](http://go.microsoft.com/fwlink/?LinkId=238269)*)。*</span><span class="sxs-lookup"><span data-stu-id="66432-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](http://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="66432-128">如例外狀況所示，現在可以開始使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="66432-129">第一個步驟是為內容啟用移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-129">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="66432-130">在套件管理員主控台中執行 **Enable-migrations** 命令</span><span class="sxs-lookup"><span data-stu-id="66432-130">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="66432-131">此命令會將 **Migrations** 資料夾新增至專案。</span><span class="sxs-lookup"><span data-stu-id="66432-131">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="66432-132">這個新資料夾包含兩個檔案：</span><span class="sxs-lookup"><span data-stu-id="66432-132">This new folder contains two files:</span></span>

-   <span data-ttu-id="66432-133">**組態類別**。</span><span class="sxs-lookup"><span data-stu-id="66432-133">**The Configuration class.**</span></span> <span data-ttu-id="66432-134">這個類別可讓您設定移轉對內容的運作方式。</span><span class="sxs-lookup"><span data-stu-id="66432-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="66432-135">在此逐步解說中，我們將只使用預設組態。</span><span class="sxs-lookup"><span data-stu-id="66432-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="66432-136">「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」</span><span class="sxs-lookup"><span data-stu-id="66432-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="66432-137">**InitialCreate 移轉**。</span><span class="sxs-lookup"><span data-stu-id="66432-137">**An InitialCreate migration**.</span></span> <span data-ttu-id="66432-138">產生此移轉是因為在我們啟用移轉之前，Code First 已為我們建立了資料庫。</span><span class="sxs-lookup"><span data-stu-id="66432-138">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="66432-139">這個包含 scaffold 移轉中的程式碼，表示已在資料庫中建立的物件。</span><span class="sxs-lookup"><span data-stu-id="66432-139">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="66432-140">在本案例中，即是包含 **BlogId** 和 **Name** 資料行的 **Blog** 資料表。</span><span class="sxs-lookup"><span data-stu-id="66432-140">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="66432-141">檔案名稱包含時間戳記，以協助排序。</span><span class="sxs-lookup"><span data-stu-id="66432-141">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="66432-142">「如果尚未建立資料庫，則不會將此 InitialCreate 移轉新增至專案中。反之，在我們第一次呼叫 Add-Migration 時，建立這些資料表的程式碼將會建立至新的移轉中。」</span><span class="sxs-lookup"><span data-stu-id="66432-142">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="66432-143">針對相同資料庫的多個模型</span><span class="sxs-lookup"><span data-stu-id="66432-143">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="66432-144">使用 EF6 之前的版本時，只能使用一個 Code First 模型來產生和管理資料庫結構描述。</span><span class="sxs-lookup"><span data-stu-id="66432-144">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="66432-145">這是每個資料庫單一 **\_\_MigrationsHistory** 資料表的結果，無法識別哪些項目屬於哪一個模型。</span><span class="sxs-lookup"><span data-stu-id="66432-145">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="66432-146">從 EF6 開始，**Configuration** 類別包含 **ContextKey** 屬性。</span><span class="sxs-lookup"><span data-stu-id="66432-146">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="66432-147">這可以作為每個 Code First 模型的唯一識別碼。</span><span class="sxs-lookup"><span data-stu-id="66432-147">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="66432-148">**\_\_MigrationsHistory** 資料表中的對應資料行，允許來自多個模型的項目共用該資料表。</span><span class="sxs-lookup"><span data-stu-id="66432-148">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="66432-149">根據預設，此屬性設定為內容的完整名稱。</span><span class="sxs-lookup"><span data-stu-id="66432-149">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="66432-150">產生及執行移轉</span><span class="sxs-lookup"><span data-stu-id="66432-150">Generating & Running Migrations</span></span>

<span data-ttu-id="66432-151">Code First 移轉有兩個您將先熟悉的主要命令。</span><span class="sxs-lookup"><span data-stu-id="66432-151">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="66432-152">**Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉</span><span class="sxs-lookup"><span data-stu-id="66432-152">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="66432-153">**Update-Database** 會將任何擱置的移轉套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="66432-153">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="66432-154">我們需要建立移轉以處理我們新增的新 URL 屬性。</span><span class="sxs-lookup"><span data-stu-id="66432-154">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="66432-155">**Add-migration** 命令可讓我們為這些移轉命名，現在我們將其命名為 **AddBlogUrl**。</span><span class="sxs-lookup"><span data-stu-id="66432-155">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="66432-156">在套件管理員主控台中執行 **Add-Migration AddBlogUrl** 命令</span><span class="sxs-lookup"><span data-stu-id="66432-156">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="66432-157">現在，我們在 **Migrations** 資料夾中已經具有 **AddBlogUrl** 移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-157">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="66432-158">移轉檔案名稱已前置時間戳記，以協助排序</span><span class="sxs-lookup"><span data-stu-id="66432-158">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Blogs", "Url", c => c.String());
            }

            public override void Down()
            {
                DropColumn("dbo.Blogs", "Url");
            }
        }
    }
```

<span data-ttu-id="66432-159">現在我們可以編輯或新增項目至此移轉，但目前一切看來良好。</span><span class="sxs-lookup"><span data-stu-id="66432-159">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="66432-160">讓我們使用 **Update-database** 來將此移轉套用至資料庫。</span><span class="sxs-lookup"><span data-stu-id="66432-160">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="66432-161">在套件管理員主控台中執行 **Update-Database** 命令</span><span class="sxs-lookup"><span data-stu-id="66432-161">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="66432-162">Code First 移轉將會比較 **Migrations** 資料夾中與已套用至資料庫的移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-162">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="66432-163">它會發現 **AddBlogUrl** 移轉必須先套用，並執行該移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-163">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="66432-164">**MigrationsDemo.BlogContext** 資料庫現在已經更新，並已包含 **Blog** 資料表中的 **Url** 資料行。</span><span class="sxs-lookup"><span data-stu-id="66432-164">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="66432-165">自訂移轉</span><span class="sxs-lookup"><span data-stu-id="66432-165">Customizing Migrations</span></span>

<span data-ttu-id="66432-166">到目前為止，我們已在不進行任何變更的情況下產生並執行了移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-166">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="66432-167">現在讓我們看看編輯預設產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="66432-167">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="66432-168">現在來對我們的模型進行更多變更，讓我們將新的 **Rating** 屬性新增至 **Blog** 類別</span><span class="sxs-lookup"><span data-stu-id="66432-168">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="66432-169">同時也新增新的 **Post** 類別</span><span class="sxs-lookup"><span data-stu-id="66432-169">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="66432-170">我們也會將 **Post** 集合新增至 **Blog** 類別，以構成 **Blog** 和 **Post** 之間關係的另一端</span><span class="sxs-lookup"><span data-stu-id="66432-170">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="66432-171">我們將使用 **Add-migration** 命令，讓 Code First 移轉為我們建立其認為最適合的移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-171">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="66432-172">我們將這項移轉稱為 **AddPostClass**。</span><span class="sxs-lookup"><span data-stu-id="66432-172">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="66432-173">在套件管理員主控台中執行 **Add-Migration AddPostClass** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-173">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="66432-174">Code First 移轉在建立這些變更方面做得非常好，但我們可能仍想要進行某些變更：</span><span class="sxs-lookup"><span data-stu-id="66432-174">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="66432-175">首先，讓我們為 **Posts.Title** 資料行新增唯一索引 (在下列程式碼中的第 22 和 29 行中新增)。</span><span class="sxs-lookup"><span data-stu-id="66432-175">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="66432-176">我們也將新增一個不可為 null 的 **Blogs.Rating** 資料行。</span><span class="sxs-lookup"><span data-stu-id="66432-176">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="66432-177">如果資料表中沒有任何現有資料，則會為新資料行指派資料類型的 CLR 預設值 (Rating 為整數，因此為 **0**)。</span><span class="sxs-lookup"><span data-stu-id="66432-177">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="66432-178">但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。</span><span class="sxs-lookup"><span data-stu-id="66432-178">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="66432-179">(您可以看到下列程式碼第 24 行指定的預設值)</span><span class="sxs-lookup"><span data-stu-id="66432-179">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="66432-180">已編輯的移轉已準備好，現在請使用 **Update-Database** 將資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="66432-180">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="66432-181">這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="66432-181">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="66432-182">在套件管理員主控台中執行 **-Verbose** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-182">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="66432-183">資料動作/自訂 SQL</span><span class="sxs-lookup"><span data-stu-id="66432-183">Data Motion / Custom SQL</span></span>

<span data-ttu-id="66432-184">到目前為止，我們已經探討了不變更或移動任何資料的移轉作業，現在讓我們看一下需要移動某些資料的部分。</span><span class="sxs-lookup"><span data-stu-id="66432-184">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="66432-185">目前還沒有對資料動作的原生支援，但我們可以在指令碼的任何位置上執行一些任意 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-185">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="66432-186">讓我們將 **Post.Abstract** 屬性新增至模型。</span><span class="sxs-lookup"><span data-stu-id="66432-186">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="66432-187">稍後，我們將使用 **Content** 資料行開頭的一些文字，為現有文章預先填入 **Abstract**。</span><span class="sxs-lookup"><span data-stu-id="66432-187">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="66432-188">我們將使用 **Add-migration** 命令，讓 Code First 移轉為我們建立其認為最適合的移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-188">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="66432-189">在套件管理員主控台中執行 **Add-Migration AddPostAbstract** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-189">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="66432-190">產生的移轉會處理結構描述變更，但我們也希望為每個文章使用其內容的前 100 個字元來預先填入 **Abstract** 資料行。</span><span class="sxs-lookup"><span data-stu-id="66432-190">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="66432-191">我們可以在新增資料行後，透過下拉到 SQL 並執行 **UPDATE** 陳述式來完成此操作。</span><span class="sxs-lookup"><span data-stu-id="66432-191">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="66432-192">(在以下的程式碼第 12 行中新增)</span><span class="sxs-lookup"><span data-stu-id="66432-192">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="66432-193">已編輯的移轉看起來一切良好，現在請使用 **Update-Database** 將資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="66432-193">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="66432-194">我們將會指定 **–Verbose** 旗標，讓我們能夠查看為資料庫執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="66432-194">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="66432-195">在套件管理員主控台中執行 **-Verbose** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-195">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="66432-196">移轉至特定版本 (包括降級)</span><span class="sxs-lookup"><span data-stu-id="66432-196">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="66432-197">到目前為止我們已一律升級至最新的移轉，但有時您可能會希望升級或降級至特定的移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-197">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="66432-198">假設我們想要在執行 **AddBlogUrl** 移轉後，將資料庫移轉到其原先的狀態。</span><span class="sxs-lookup"><span data-stu-id="66432-198">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="66432-199">我們可以使用 **–TargetMigration** 參數來降級至此移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-199">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="66432-200">在套件管理員主控台中執行 **Update-Database –TargetMigration: AddBlogUrl** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-200">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="66432-201">此命令會為我們的  **AddBlogAbstract** 和  **AddPostClass** 移轉執行向下指令碼。</span><span class="sxs-lookup"><span data-stu-id="66432-201">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="66432-202">如果您想要一路向前復原為空白資料庫，則您可以使用 **Update-Database –TargetMigration: $InitialDatabase** 命令。</span><span class="sxs-lookup"><span data-stu-id="66432-202">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="66432-203">取得 SQL 指令碼</span><span class="sxs-lookup"><span data-stu-id="66432-203">Getting a SQL Script</span></span>

<span data-ttu-id="66432-204">如果其他開發人員希望在其電腦上進行這些變更，他們可以在我們檢查對原始檔控制的變更後同步。</span><span class="sxs-lookup"><span data-stu-id="66432-204">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="66432-205">一旦他們擁有了我們的新移轉，他們就可以執行 Update-Database 命令以在本機套用變更。</span><span class="sxs-lookup"><span data-stu-id="66432-205">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="66432-206">但是，如果我們想將這些變更推送至測試伺服器並生產，我們可能需要能夠遞交給 DBA 的 SQL 指令碼。</span><span class="sxs-lookup"><span data-stu-id="66432-206">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="66432-207">請執行 **Update-database** 命令，但這次請指定 **–Script** 旗標，以便將變更寫入至指令碼，而非套用。</span><span class="sxs-lookup"><span data-stu-id="66432-207">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="66432-208">我們也會指定來源和目標移轉以產生指令碼。</span><span class="sxs-lookup"><span data-stu-id="66432-208">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="66432-209">我們希望指令碼從空的資料庫 (**$InitialDatabase**) 移轉至最新版本 (移轉 **AddPostAbstract**)。</span><span class="sxs-lookup"><span data-stu-id="66432-209">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="66432-210">「如果您未指定目標移轉，則移轉會使用最新的移轉作為目標。如果您未指定來源移轉，則移轉會使用資料庫的目前狀態。」</span><span class="sxs-lookup"><span data-stu-id="66432-210">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="66432-211">在套件管理員主控台中執行 **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** 命令</span><span class="sxs-lookup"><span data-stu-id="66432-211">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="66432-212">Code First 移轉將執行移轉管線，但不會實際套用變更，而是將其寫入 .sql 檔案中。</span><span class="sxs-lookup"><span data-stu-id="66432-212">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="66432-213">一旦產生指令碼，即會在 Visual Studio 中開啟，供您檢視或儲存。</span><span class="sxs-lookup"><span data-stu-id="66432-213">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="66432-214">產生等冪指令碼</span><span class="sxs-lookup"><span data-stu-id="66432-214">Generating Idempotent Scripts</span></span>

<span data-ttu-id="66432-215">從 EF6 開始，如果指定 **–SourceMigration $InitialDatabase**，則產生的指令碼	將為「等冪」。</span><span class="sxs-lookup"><span data-stu-id="66432-215">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="66432-216">等冪指令碼可以將目前任何版本的資料庫升級至最新版本 (如果使用 **-TargetMigration**，則可以升級指定的版本)。</span><span class="sxs-lookup"><span data-stu-id="66432-216">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="66432-217">產生的指令碼包括用於檢查 **\_\_MigrationsHistory** 資料表的邏輯，並僅套用先前未套用的變更。</span><span class="sxs-lookup"><span data-stu-id="66432-217">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="66432-218">在應用程式啟動時自動升級 (MigrateDatabaseToLatestVersion 初始設定式)</span><span class="sxs-lookup"><span data-stu-id="66432-218">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="66432-219">如果您要部署應用程式，則可能希望在應用程式啟動時自動升級資料庫 (透過套用任何擱置的移轉)。</span><span class="sxs-lookup"><span data-stu-id="66432-219">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="66432-220">您可以藉由註冊 **MigrateDatabaseToLatestVersion** 資料庫初始設定式來執行此操作。</span><span class="sxs-lookup"><span data-stu-id="66432-220">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="66432-221">資料庫初始設定式只會包含一些用來確定資料庫已正確設定的邏輯。</span><span class="sxs-lookup"><span data-stu-id="66432-221">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="66432-222">第一次在應用程式處理序 (**AppDomain**) 中使用內容時會執行此邏輯。</span><span class="sxs-lookup"><span data-stu-id="66432-222">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="66432-223">我們可以更新 **Program.cs** 檔案 (如下所示) 在我們使用內容 (第 14 行) 之前為 BlogContext 設定 **MigrateDatabaseToLatestVersion** 初始設定式。</span><span class="sxs-lookup"><span data-stu-id="66432-223">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="66432-224">請注意，您也需要為 **System.Data.Entity** 命名空間 (第 5 行) 新增使用陳述式。</span><span class="sxs-lookup"><span data-stu-id="66432-224">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="66432-225">當我們建立此初始設定式的執行個體時，我們需要指定內容類型 ( **BlogContext**) 和移轉組態 (**Configuration**) - 移轉組態是在我們啟用移轉時新增至 **Migrations** 資料夾的類別。</span><span class="sxs-lookup"><span data-stu-id="66432-225">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion\<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

<span data-ttu-id="66432-226">現在每當我們應用程式執行時，將首先檢查其目標資料庫是否為最新，如果不是，則會套用任何擱置的移轉。</span><span class="sxs-lookup"><span data-stu-id="66432-226">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
