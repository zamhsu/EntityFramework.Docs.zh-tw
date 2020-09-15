---
title: 自動 Code First 移轉-EF6
description: Entity Framework 6 中的自動 Code First 移轉
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: e7bd9ff7d9dcecb7fecf213306047a53fc04135c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072560"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="b9d4b-103">自動 Code First 移轉</span><span class="sxs-lookup"><span data-stu-id="b9d4b-103">Automatic Code First Migrations</span></span>
<span data-ttu-id="b9d4b-104">自動遷移可讓您使用 Code First 移轉，而不需要在專案中為您所做的每項變更建立程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-104">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="b9d4b-105">並非所有變更都可以自動套用-例如，資料行重新命名需要使用以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-105">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="b9d4b-106">本文假設您知道如何在基本案例中使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="b9d4b-107">如果您沒有這麼做，就必須先閱讀 [Code First 移轉](xref:ef6/modeling/code-first/migrations/index) ，再繼續進行操作。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="b9d4b-108">小組環境的建議</span><span class="sxs-lookup"><span data-stu-id="b9d4b-108">Recommendation for Team Environments</span></span>

<span data-ttu-id="b9d4b-109">您可以散置自動和以程式碼為基礎的遷移，但不建議在小組開發案例中使用。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-109">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="b9d4b-110">如果您是使用原始檔控制之開發人員小組的一部分，您應該使用純粹自動遷移或單純以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-110">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="b9d4b-111">由於自動遷移的限制，我們建議您在小組環境中使用以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-111">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="b9d4b-112">建置初始模型與資料庫</span><span class="sxs-lookup"><span data-stu-id="b9d4b-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="b9d4b-113">在開始使用移轉之前，我們需要一個專案和一個 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="b9d4b-114">此逐步解說中我們將使用標準 **Blog** 和 **Post** 模型。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="b9d4b-115">建立新的 **MigrationsAutomaticDemo** 主控台應用程式</span><span class="sxs-lookup"><span data-stu-id="b9d4b-115">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="b9d4b-116">將最新版本 **EntityFramework** NuGet 套件新增至專案</span><span class="sxs-lookup"><span data-stu-id="b9d4b-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="b9d4b-117">[工具] –&gt; [程式庫套件管理員] –&gt; [套件管理員主控台] </span><span class="sxs-lookup"><span data-stu-id="b9d4b-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="b9d4b-118">執行 **Install-package EntityFramework** 命令</span><span class="sxs-lookup"><span data-stu-id="b9d4b-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="b9d4b-119">使用如下所示的程式碼新增 **Model.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="b9d4b-120">此程式碼會定義構成我們網域模型的單一 **Blog** 類別，以及作為 EF Code First 內容的 **BlogContext** 類別</span><span class="sxs-lookup"><span data-stu-id="b9d4b-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

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

-   <span data-ttu-id="b9d4b-121">現在我們有了一個模型，即可用來執行資料存取。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="b9d4b-122">使用如下所示的程式碼更新 **Program.cs** 檔案。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-122">Update the **Program.cs** file with the code shown below.</span></span>

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

-   <span data-ttu-id="b9d4b-123">執行您的應用程式，您會看到為您建立了 **MigrationsAutomaticCodeDemo BlogCoNtext** 資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-123">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![資料庫 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="b9d4b-125">啟用移轉</span><span class="sxs-lookup"><span data-stu-id="b9d4b-125">Enabling Migrations</span></span>

<span data-ttu-id="b9d4b-126">現在可以對我們的模型做更多變更。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="b9d4b-127">請將一個 URL 屬性引進 Blog 類別。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="b9d4b-128">如果您再次執行該應用程式，您將會收到 InvalidOperationException，指出*自資料庫建立以來，支援 'BlogContext' 內容的模型已變更。請考慮使用 Code First 移轉更新資料庫 (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*</span><span class="sxs-lookup"><span data-stu-id="b9d4b-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="b9d4b-129">如例外狀況所示，現在可以開始使用 Code First 移轉。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="b9d4b-130">因為我們想要使用自動遷移，所以會指定 **– EnableAutomaticMigrations** 參數。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-130">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="b9d4b-131">在封裝管理員主控台中執行 [ **啟用-遷移-EnableAutomaticMigrations** ] 命令，此命令已將 [ **遷移** ] 資料夾新增至我們的專案。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-131">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="b9d4b-132">這個新資料夾包含一個檔案：</span><span class="sxs-lookup"><span data-stu-id="b9d4b-132">This new folder contains one file:</span></span>

-   <span data-ttu-id="b9d4b-133">**組態類別**。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-133">**The Configuration class.**</span></span> <span data-ttu-id="b9d4b-134">這個類別可讓您設定移轉對內容的運作方式。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="b9d4b-135">在此逐步解說中，我們將只使用預設組態。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="b9d4b-136">「由於專案中只有一個單一 Code First 內容，因此 Enable-Migrations 已自動填入此組態適用的內容類型。」 </span><span class="sxs-lookup"><span data-stu-id="b9d4b-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="b9d4b-137">您的第一個自動遷移</span><span class="sxs-lookup"><span data-stu-id="b9d4b-137">Your First Automatic Migration</span></span>

<span data-ttu-id="b9d4b-138">Code First 移轉有兩個您將先熟悉的主要命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-138">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="b9d4b-139">**Add-Migration** 將根據自上次建立移轉以來您對模型所做的變更，來建立下一次移轉</span><span class="sxs-lookup"><span data-stu-id="b9d4b-139">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="b9d4b-140">**Update-Database** 會將任何擱置的移轉套用至資料庫</span><span class="sxs-lookup"><span data-stu-id="b9d4b-140">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="b9d4b-141">除非我們真的需要) ，並將焦點放在讓 Code First 移轉自動計算和套用變更，否則我們將會避免使用「新增-遷移 (」。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-141">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="b9d4b-142">讓我們使用 **Update-database** 來取得 Code First 移轉將變更推送至我們的模型 (新的 **Blog**l 屬性) 至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-142">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="b9d4b-143">在封裝管理員主控台中執行 **更新資料庫** 命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-143">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="b9d4b-144">**MigrationsAutomaticDemo. BlogCoNtext**資料庫現在已更新為包含 [ **blog** ] 資料表中的 [ **Url** ] 資料行。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-144">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="b9d4b-145">第二個自動遷移</span><span class="sxs-lookup"><span data-stu-id="b9d4b-145">Your Second Automatic Migration</span></span>

<span data-ttu-id="b9d4b-146">讓我們進行其他變更，讓 Code First 移轉自動將變更推送到資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-146">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="b9d4b-147">同時也新增新的 **Post** 類別</span><span class="sxs-lookup"><span data-stu-id="b9d4b-147">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="b9d4b-148">我們也會將 **Post** 集合新增至 **Blog** 類別，以構成 **Blog** 和 **Post** 之間關係的另一端</span><span class="sxs-lookup"><span data-stu-id="b9d4b-148">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="b9d4b-149">現在使用 **更新資料庫** ，讓資料庫保持在最新狀態。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-149">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="b9d4b-150">這次讓我們指定 **–Verbose** 旗標，讓您可以看到 Code First 移轉正在執行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-150">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="b9d4b-151">在套件管理員主控台中執行 **-Verbose** 命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-151">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="b9d4b-152">新增以程式碼為基礎的遷移</span><span class="sxs-lookup"><span data-stu-id="b9d4b-152">Adding a Code Based Migration</span></span>

<span data-ttu-id="b9d4b-153">現在讓我們看看，我們可能想要使用以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-153">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="b9d4b-154">讓我們將 **評** 等屬性新增至 **Blog** 類別</span><span class="sxs-lookup"><span data-stu-id="b9d4b-154">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="b9d4b-155">我們可以直接執行 **更新資料庫** ，將這些變更推送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-155">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="b9d4b-156">但是，我們要新增一個不可為 null 的 **blog** 資料行，如果資料表中有任何現有的資料，則會將新資料行的 CLR 預設資料類型指派給資料類型 (評等為整數，因此為 **0**) 。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-156">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="b9d4b-157">但我們應指定預設值為 **3**，以便 **Blog** 資料表中的現有資料列以適當的評等開頭。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-157">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="b9d4b-158">讓我們使用 [新增-遷移] 命令將這種變更寫入至以程式碼為基礎的遷移，讓我們可以進行編輯。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-158">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="b9d4b-159">[ **新增-遷移** ] 命令可讓我們將名稱提供給這些遷移，讓我們直接呼叫我們的 **AddBlogRating**。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-159">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="b9d4b-160">在封裝管理員主控台中執行 [ **新增-遷移 AddBlogRating** ] 命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-160">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="b9d4b-161">在 [ **遷移** ] 資料夾中，我們現在有新的 **AddBlogRating** 遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-161">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="b9d4b-162">使用時間戳預先修正遷移檔案名，以協助進行排序。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-162">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="b9d4b-163">讓我們編輯產生的程式碼，為 Blog (第10行的程式碼片段指定預設值 3) </span><span class="sxs-lookup"><span data-stu-id="b9d4b-163">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="b9d4b-164">*遷移也具有可捕獲某些中繼資料的程式碼後端檔案。此中繼資料可讓 Code First 移轉複寫我們在這個以程式碼為基礎的遷移之前所執行的自動遷移。如果另一位開發人員想要執行我們的遷移或部署應用程式的時候，這點很重要。*</span><span class="sxs-lookup"><span data-stu-id="b9d4b-164">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

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

<span data-ttu-id="b9d4b-165">已編輯的移轉看起來一切良好，現在請使用 **Update-Database** 將資料庫保持最新狀態。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-165">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="b9d4b-166">在封裝管理員主控台中執行 **更新資料庫** 命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-166">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="b9d4b-167">返回自動遷移</span><span class="sxs-lookup"><span data-stu-id="b9d4b-167">Back to Automatic Migrations</span></span>

<span data-ttu-id="b9d4b-168">我們現在可以切換回自動遷移，以進行更簡單的變更。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-168">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="b9d4b-169">Code First 移轉會根據每個以程式碼為基礎之遷移的程式碼後端檔案中儲存的中繼資料，以正確的順序來執行自動和以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-169">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="b9d4b-170">讓我們將 Post. Abstract 屬性新增至我們的模型</span><span class="sxs-lookup"><span data-stu-id="b9d4b-170">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="b9d4b-171">現在我們可以使用 **更新資料庫** 來取得 Code First 移轉使用自動遷移將這種變更推送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-171">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="b9d4b-172">在封裝管理員主控台中執行 **更新資料庫** 命令。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-172">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="b9d4b-173">摘要</span><span class="sxs-lookup"><span data-stu-id="b9d4b-173">Summary</span></span>

<span data-ttu-id="b9d4b-174">在這個逐步解說中，您已瞭解如何使用自動遷移將模型變更推送至資料庫。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-174">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="b9d4b-175">您也已瞭解如何在需要更多控制時，于自動遷移之間 scaffold 和執行以程式碼為基礎的遷移。</span><span class="sxs-lookup"><span data-stu-id="b9d4b-175">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
