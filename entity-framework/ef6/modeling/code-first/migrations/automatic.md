---
title: 自動的 Code First 移轉 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283910"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="b4bff-102">自動的 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-102">Automatic Code First Migrations</span></span>
<span data-ttu-id="b4bff-103">自動移轉，可讓您使用 Code First 移轉，而不需要每個您所做的變更在您的專案中的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="b4bff-103">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="b4bff-104">並非所有的變更可以自動套用-資料行重新命名，例如需要使用程式碼為基礎的移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-104">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="b4bff-105">本文假設您知道如何在基本案例中使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="b4bff-106">如果不這麼做，則您將需要閱讀[Code First 移轉](~/ef6/modeling/code-first/migrations/index.md)再繼續進行。</span><span class="sxs-lookup"><span data-stu-id="b4bff-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="b4bff-107">適用於小組環境建議</span><span class="sxs-lookup"><span data-stu-id="b4bff-107">Recommendation for Team Environments</span></span>

<span data-ttu-id="b4bff-108">您可以顛倒自動和程式碼為基礎的移轉，但建議您不要在小組開發案例中。</span><span class="sxs-lookup"><span data-stu-id="b4bff-108">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="b4bff-109">如果您是使用原始檔控制的開發人員小組的一部分您應該使用完全自動移轉或純程式碼型的移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-109">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="b4bff-110">指定的自動移轉的限制，我們建議在小組環境中使用程式碼為基礎的移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-110">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="b4bff-111">建置初始模型與資料庫</span><span class="sxs-lookup"><span data-stu-id="b4bff-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="b4bff-112">在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="b4bff-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="b4bff-113">此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。</span><span class="sxs-lookup"><span data-stu-id="b4bff-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="b4bff-114">建立新**MigrationsAutomaticDemo**主控台應用程式</span><span class="sxs-lookup"><span data-stu-id="b4bff-114">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="b4bff-115">將最新版本 **EntityFramework** NuGet 套件新增至專案</span><span class="sxs-lookup"><span data-stu-id="b4bff-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="b4bff-116">[工具] –&gt; [程式庫套件管理員] –&gt; [套件管理員主控台]</span><span class="sxs-lookup"><span data-stu-id="b4bff-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="b4bff-117">執行 **Install-package EntityFramework** 命令</span><span class="sxs-lookup"><span data-stu-id="b4bff-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="b4bff-118">使用如下所示的程式碼新增 **Model.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="b4bff-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="b4bff-119">此程式碼會定義構成我們網域模型的單一 **Blog** 類別，以及作為 EF Code First 內容的 **BlogContext** 類別</span><span class="sxs-lookup"><span data-stu-id="b4bff-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="b4bff-120">現在我們有了一個模型，即可用來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="b4bff-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="b4bff-121">使用如下所示的程式碼更新 **Program.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="b4bff-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="b4bff-122">執行您的應用程式，您會看到**MigrationsAutomaticCodeDemo.BlogContext**為您建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="b4bff-122">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![資料庫 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="b4bff-124">啟用移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-124">Enabling Migrations</span></span>

<span data-ttu-id="b4bff-125">現在可以對我們的模型做更多變更。</span><span class="sxs-lookup"><span data-stu-id="b4bff-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="b4bff-126">請將一個 URL 屬性引進 Blog 類別。</span><span class="sxs-lookup"><span data-stu-id="b4bff-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="b4bff-127">如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉來更新資料庫」(* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*)。*</span><span class="sxs-lookup"><span data-stu-id="b4bff-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="b4bff-128">如例外狀況所示，現在可以開始使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="b4bff-129">因為我們想要使用自動移轉，所以我們要指定 **– EnableAutomaticMigrations**切換。</span><span class="sxs-lookup"><span data-stu-id="b4bff-129">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="b4bff-130">執行**Enable-migrations – EnableAutomaticMigrations**已新增在套件管理員主控台這個命令的命令**移轉**專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="b4bff-130">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="b4bff-131">這個新的資料夾包含一個檔案：</span><span class="sxs-lookup"><span data-stu-id="b4bff-131">This new folder contains one file:</span></span>

-   <span data-ttu-id="b4bff-132">**組態類別**。</span><span class="sxs-lookup"><span data-stu-id="b4bff-132">**The Configuration class.**</span></span> <span data-ttu-id="b4bff-133">這個類別可讓您設定移轉對內容的運作方式。</span><span class="sxs-lookup"><span data-stu-id="b4bff-133">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="b4bff-134">在此逐步解說中，我們將只使用預設組態。</span><span class="sxs-lookup"><span data-stu-id="b4bff-134">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="b4bff-135">「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」</span><span class="sxs-lookup"><span data-stu-id="b4bff-135">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="b4bff-136">您第一次的自動移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-136">Your First Automatic Migration</span></span>

<span data-ttu-id="b4bff-137">Code First 移轉有兩個您將先熟悉的主要命令。</span><span class="sxs-lookup"><span data-stu-id="b4bff-137">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="b4bff-138">**Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-138">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="b4bff-139">**Update-Database** 會將任何擱置的移轉套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="b4bff-139">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="b4bff-140">我們要避免使用 Add-migration （除非我們真的需要），並著重於讓 Code First 移轉自動計算，並套用變更。</span><span class="sxs-lookup"><span data-stu-id="b4bff-140">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="b4bff-141">讓我們使用**Update-database**若要取得 Code First 移轉，將變更推送至我們的模型 (新**Blog.Ur**l 屬性) 到資料庫。</span><span class="sxs-lookup"><span data-stu-id="b4bff-141">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="b4bff-142">執行**Update-database**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="b4bff-142">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="b4bff-143">**MigrationsAutomaticDemo.BlogContext**資料庫現在會更新以包含**Url**中的資料行**部落格**資料表。</span><span class="sxs-lookup"><span data-stu-id="b4bff-143">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="b4bff-144">第二個的自動移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-144">Your Second Automatic Migration</span></span>

<span data-ttu-id="b4bff-145">讓我們進行另一次變更，並讓我們，自動將變更推送至資料庫的 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-145">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="b4bff-146">同時也新增新的 **Post** 類別</span><span class="sxs-lookup"><span data-stu-id="b4bff-146">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="b4bff-147">我們也會將 **Post** 集合新增至 **Blog** 類別，以構成 **Blog** 和 **Post** 之間關係的另一端</span><span class="sxs-lookup"><span data-stu-id="b4bff-147">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="b4bff-148">現在，使用**Update-database**使資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="b4bff-148">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="b4bff-149">這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="b4bff-149">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="b4bff-150">在套件管理員主控台中執行 **-Verbose** 命令。</span><span class="sxs-lookup"><span data-stu-id="b4bff-150">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="b4bff-151">加入程式碼基礎的移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-151">Adding a Code Based Migration</span></span>

<span data-ttu-id="b4bff-152">現在讓我們看看我們可能會想要使用的程式碼為基礎的移轉的項目。</span><span class="sxs-lookup"><span data-stu-id="b4bff-152">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="b4bff-153">讓我們新增**分級**屬性設**部落格**類別</span><span class="sxs-lookup"><span data-stu-id="b4bff-153">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="b4bff-154">我們可以執行**Update-database**將這些變更推送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b4bff-154">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="b4bff-155">不過，我們將新增一個不可為 null **Blogs.Rating**資料行中，如果資料表中沒有任何現有的資料將會取得指派給它的 CLR 預設值是新的資料行的資料類型 (評等是整數，就是**0**).</span><span class="sxs-lookup"><span data-stu-id="b4bff-155">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="b4bff-156">但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。</span><span class="sxs-lookup"><span data-stu-id="b4bff-156">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="b4bff-157">讓我們使用新增移轉命令，來撰寫出這項變更與程式碼為基礎的移轉，讓我們可以進行編輯。</span><span class="sxs-lookup"><span data-stu-id="b4bff-157">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="b4bff-158">**Add-migration**命令可讓我們為這些移轉的名稱，讓我們只要呼叫我們**AddBlogRating**。</span><span class="sxs-lookup"><span data-stu-id="b4bff-158">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="b4bff-159">執行**Add-migration AddBlogRating**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="b4bff-159">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="b4bff-160">在 **移轉**資料夾，我們現在新增了**AddBlogRating**移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-160">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="b4bff-161">預先移轉檔案名稱被固定時間戳記的來協助進行排序。</span><span class="sxs-lookup"><span data-stu-id="b4bff-161">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="b4bff-162">讓我們編輯產生的程式碼，以指定的預設值為 3 Blog.Rating (在下列程式碼中的行 10)</span><span class="sxs-lookup"><span data-stu-id="b4bff-162">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="b4bff-163">*移轉也都擷取一些中繼資料的程式碼後置檔案。此中繼資料可複寫我們執行此程式碼為基礎的移轉之前，先自動移轉的 Code First 移轉。如果另一個開發人員想要執行我們的移轉或部署我們的應用程式的時候，這很重要。*</span><span class="sxs-lookup"><span data-stu-id="b4bff-163">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

<span data-ttu-id="b4bff-164">已編輯的移轉看起來一切良好，現在請使用 **Update-Database** 將資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="b4bff-164">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="b4bff-165">執行**Update-database**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="b4bff-165">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="b4bff-166">回到自動移轉</span><span class="sxs-lookup"><span data-stu-id="b4bff-166">Back to Automatic Migrations</span></span>

<span data-ttu-id="b4bff-167">現在，我們可自行切換回到自動移轉，我們更簡單的變更。</span><span class="sxs-lookup"><span data-stu-id="b4bff-167">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="b4bff-168">Code First 移轉會負責執行正確的順序，根據它會將儲存在每個程式碼為基礎的移轉的程式碼後置檔案的中繼資料的自動與程式碼為基礎的移轉。</span><span class="sxs-lookup"><span data-stu-id="b4bff-168">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="b4bff-169">讓我們將 Post.Abstract 屬性新增至我們的模型</span><span class="sxs-lookup"><span data-stu-id="b4bff-169">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="b4bff-170">現在我們可以使用**Update-database**取得 Code First 移轉，將這項變更推送到使用自動移轉的資料庫。</span><span class="sxs-lookup"><span data-stu-id="b4bff-170">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="b4bff-171">執行**Update-database**命令在套件管理員主控台。</span><span class="sxs-lookup"><span data-stu-id="b4bff-171">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="b4bff-172">總結</span><span class="sxs-lookup"><span data-stu-id="b4bff-172">Summary</span></span>

<span data-ttu-id="b4bff-173">在本逐步解說，您可了解如何使用自動移轉，推送模型變更至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b4bff-173">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="b4bff-174">您也看到了如何建立和執行程式碼型的移轉，之間自動移轉，當您需要更多的控制。</span><span class="sxs-lookup"><span data-stu-id="b4bff-174">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
