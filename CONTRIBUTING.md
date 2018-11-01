# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="00e86-101">貢獻 Entity Framework 文件</span><span class="sxs-lookup"><span data-stu-id="00e86-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="00e86-102">本文件涵蓋參與 [Entity Framework 文件網站](https://docs.microsoft.com/ef)中所裝載文章與程式碼範例的程序。</span><span class="sxs-lookup"><span data-stu-id="00e86-102">The document covers the process for contributing to the articles and code samples that are hosted on the [Entity Framework documentation site](https://docs.microsoft.com/ef).</span></span> <span data-ttu-id="00e86-103">貢獻可以簡單到像錯字更正，或是複雜到像是新的文章。</span><span class="sxs-lookup"><span data-stu-id="00e86-103">Contributions may be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="00e86-104">如何提出簡單的修正或建議</span><span class="sxs-lookup"><span data-stu-id="00e86-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="00e86-105">文章會以 Markdown 檔案的形式儲存在存放庫中。</span><span class="sxs-lookup"><span data-stu-id="00e86-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="00e86-106">您可以在瀏覽器中，點選瀏覽器視窗右上角的 [編輯] 連結，對 Markdown 檔案內容進行簡易變更。</span><span class="sxs-lookup"><span data-stu-id="00e86-106">Simple changes to the content of a Markdown file can be made in the browser by tapping the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="00e86-107">(如果瀏覽器視窗很窄，建議展開 [選項] 列，才可看到 [編輯] 連結)。請遵循指示，建立提取要求 (PR)。</span><span class="sxs-lookup"><span data-stu-id="00e86-107">(In narrow browser windows you might need to expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="00e86-108">EF 小組將會於檢閱 PR 後接受，或建議變更。</span><span class="sxs-lookup"><span data-stu-id="00e86-108">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="00e86-109">如何進行更複雜的提交</span><span class="sxs-lookup"><span data-stu-id="00e86-109">How to make a more complex submission</span></span>

<span data-ttu-id="00e86-110">您需要對 [Git 與 GitHub.com](https://guides.github.com/activities/hello-world/) 有基本了解。</span><span class="sxs-lookup"><span data-stu-id="00e86-110">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="00e86-111">建立一項[議題](https://github.com/aspnet/EntityFramework.Docs/issues/new)，描述您要執行的動作，例如變更現有文章或建立新的文章。</span><span class="sxs-lookup"><span data-stu-id="00e86-111">Open an [issue](https://github.com/aspnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="00e86-112">在您投入更多時間之前，請先等候 EF 小組的核准。</span><span class="sxs-lookup"><span data-stu-id="00e86-112">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="00e86-113">分支 [aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) 存放庫，並針對您的變更建立分支。</span><span class="sxs-lookup"><span data-stu-id="00e86-113">Fork the [aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="00e86-114">將內含變更的提取要求 (PR) 提交給管理員。</span><span class="sxs-lookup"><span data-stu-id="00e86-114">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="00e86-115">回應 PR 意見。</span><span class="sxs-lookup"><span data-stu-id="00e86-115">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="00e86-116">Markdown 語法</span><span class="sxs-lookup"><span data-stu-id="00e86-116">Markdown syntax</span></span>

<span data-ttu-id="00e86-117">文章是以 [DocFx-flavored Markdown](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) 撰寫，其涵蓋 [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/)。</span><span class="sxs-lookup"><span data-stu-id="00e86-117">Articles are written in [DocFx-flavored Markdown](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="00e86-118">如需 EF 文件中常用 UI 功能的 DFM 語法範例，請參閱 .NET Core 存放庫樣式指南中的[中繼資料與 Markdown 範本](https://github.com/dotnet/docs/blob/master/styleguide/template.md)。</span><span class="sxs-lookup"><span data-stu-id="00e86-118">For examples of DFM syntax for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="00e86-119">資料夾結構慣例</span><span class="sxs-lookup"><span data-stu-id="00e86-119">Folder structure conventions</span></span>

<span data-ttu-id="00e86-120">影像及其他靜態內容，會儲存在網站每個區域/資料夾內之 `_static` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="00e86-120">Images, and other static content, are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="00e86-121">程式碼範例則會儲存在 `samples` 根資料夾中。</span><span class="sxs-lookup"><span data-stu-id="00e86-121">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="00e86-122">它們會整理為模擬文件結構 (位於 `entity-framework` 根資料夾下) 的資料夾結構。</span><span class="sxs-lookup"><span data-stu-id="00e86-122">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="00e86-123">程式碼片段</span><span class="sxs-lookup"><span data-stu-id="00e86-123">Code snippets</span></span>

<span data-ttu-id="00e86-124">文章通常會包含程式碼片段，作為重點說明之用。</span><span class="sxs-lookup"><span data-stu-id="00e86-124">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="00e86-125">DFM 可讓您將程式碼複製到 Markdown 檔案中，或是參考不同的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="00e86-125">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="00e86-126">建議盡可能使用不同的程式碼檔案，將程式碼中的錯誤機率降到最低。</span><span class="sxs-lookup"><span data-stu-id="00e86-126">We prefer to use separate code files whenever possible, to minimize the chance of errors in the code.</span></span> <span data-ttu-id="00e86-127">程式碼檔案應使用上述為範例專案所述的資料夾結構，儲存在存放庫中。</span><span class="sxs-lookup"><span data-stu-id="00e86-127">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="00e86-128">以下是一些 [DFM 程式碼片段語法](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)的範例。</span><span class="sxs-lookup"><span data-stu-id="00e86-128">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="00e86-129">若要將完整的程式碼檔案轉譯為程式碼片段：</span><span class="sxs-lookup"><span data-stu-id="00e86-129">To render an entire code file as a snippet:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="00e86-130">若要使用行號，將一部分檔案轉譯為程式碼片段：</span><span class="sxs-lookup"><span data-stu-id="00e86-130">To render a portion of a file as a snippet by using line numbers:</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="00e86-131">若是 C# 程式碼片段，可參考 [C# 區域](https://msdn.microsoft.com/library/9a1ybwek.aspx)。</span><span class="sxs-lookup"><span data-stu-id="00e86-131">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="00e86-132">請盡可能使用區域而不是行號，因為程式碼檔案中的行號通常會變更，而變得與 Markdown 中的行號參考不同步。</span><span class="sxs-lookup"><span data-stu-id="00e86-132">Whenever possible, use regions rather than line numbers, because line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="00e86-133">C# 區域可為巢狀結構，而您若參考外部區域，則內部 `#region` 與 `#endregion` 指示詞就不會轉譯在程式碼片段中。</span><span class="sxs-lookup"><span data-stu-id="00e86-133">C# regions can be nested, and if you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="00e86-134">若要轉譯名為 "snippet_Example" 的 C# 區域：</span><span class="sxs-lookup"><span data-stu-id="00e86-134">To render a C# region named "snippet_Example":</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="00e86-135">若要醒目提示轉譯程式碼片段中選取的行 (通常會以黃色背景色彩呈現)：</span><span class="sxs-lookup"><span data-stu-id="00e86-135">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="00e86-136">使用 DocFX 測試變更</span><span class="sxs-lookup"><span data-stu-id="00e86-136">Test your changes with DocFX</span></span>

<span data-ttu-id="00e86-137">使用 [DocFX 命令列工具](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)測試您的變更，如此會建立本機裝載的網站版本。</span><span class="sxs-lookup"><span data-stu-id="00e86-137">Test your changes with the [DocFX command line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="00e86-138">DocFX 不會轉譯為 docs.microsoft.com 所建立的樣式與網站延伸模組。</span><span class="sxs-lookup"><span data-stu-id="00e86-138">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="00e86-139">DocFX 需要 .NET Framework (Windows) 或 Mono (Linux 或 macOS)。</span><span class="sxs-lookup"><span data-stu-id="00e86-139">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="00e86-140">Windows 指示</span><span class="sxs-lookup"><span data-stu-id="00e86-140">Windows instructions</span></span>

* <span data-ttu-id="00e86-141">請從 [DocFX 版本](https://github.com/dotnet/docfx/releases)下載並解壓縮 *docfx.zip*。</span><span class="sxs-lookup"><span data-stu-id="00e86-141">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="00e86-142">將 DocFX 新增至您的 PATH。</span><span class="sxs-lookup"><span data-stu-id="00e86-142">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="00e86-143">在命令列視窗中，瀏覽至複製的存放庫 (其中包含 *docfx.json* 檔案)，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="00e86-143">In a command line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ``` console
   docfx -t default --serve
   ```

* <span data-ttu-id="00e86-144">在瀏覽器中，巡覽至 `http://localhost:8080`。</span><span class="sxs-lookup"><span data-stu-id="00e86-144">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="00e86-145">Mono 指示</span><span class="sxs-lookup"><span data-stu-id="00e86-145">Mono instructions</span></span>

* <span data-ttu-id="00e86-146">請透過 Homebrew 安裝 Mono - `brew install mono`。</span><span class="sxs-lookup"><span data-stu-id="00e86-146">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="00e86-147">下載[最新版的 DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2)。</span><span class="sxs-lookup"><span data-stu-id="00e86-147">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="00e86-148">解壓縮至 `\bin\docfx`。</span><span class="sxs-lookup"><span data-stu-id="00e86-148">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="00e86-149">建立 **docfx** 的別名：</span><span class="sxs-lookup"><span data-stu-id="00e86-149">Create an alias for **docfx**:</span></span>

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="00e86-150">執行複製的存放庫中之 **docfx**，來建置網站，並執行 **docfx-serve** 於 `http://localhost:8080` 檢視網站。</span><span class="sxs-lookup"><span data-stu-id="00e86-150">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="00e86-151">語態和語氣</span><span class="sxs-lookup"><span data-stu-id="00e86-151">Voice and tone</span></span>

<span data-ttu-id="00e86-152">我們撰寫文件的目標是盡可能讓愈多使用者輕易即可了解愈好。</span><span class="sxs-lookup"><span data-stu-id="00e86-152">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="00e86-153">為此，我們制定了書寫樣式導方針，並要求參與者務必遵循。</span><span class="sxs-lookup"><span data-stu-id="00e86-153">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="00e86-154">如需詳細資訊，請參閱 .NET Core 存放庫中的[語態和語氣指導方針](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md)。</span><span class="sxs-lookup"><span data-stu-id="00e86-154">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
