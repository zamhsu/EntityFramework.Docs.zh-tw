# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="3c140-101">貢獻 Entity Framework 文件</span><span class="sxs-lookup"><span data-stu-id="3c140-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="3c140-102">以下說明對 Entity Framework 文件貢獻文章和程式碼範例的程序。</span><span class="sxs-lookup"><span data-stu-id="3c140-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="3c140-103">貢獻可以簡單到像錯字更正，或是複雜到像是新的文章。</span><span class="sxs-lookup"><span data-stu-id="3c140-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="3c140-104">如何提出簡單的修正或建議</span><span class="sxs-lookup"><span data-stu-id="3c140-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="3c140-105">文章會以 Markdown 檔案的形式儲存在此存放庫中。</span><span class="sxs-lookup"><span data-stu-id="3c140-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="3c140-106">若要對 Markdown 檔案內容進行簡易變更，請按一下瀏覽器視窗右上角的 [編輯] 連結。</span><span class="sxs-lookup"><span data-stu-id="3c140-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="3c140-107">您可能需要展開 [選項] 列，才可看到 [編輯] 連結。</span><span class="sxs-lookup"><span data-stu-id="3c140-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="3c140-108">請依照指示來建立提取要求 (PR)。</span><span class="sxs-lookup"><span data-stu-id="3c140-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="3c140-109">EF 小組將會於檢閱 PR 後接受，或建議變更。</span><span class="sxs-lookup"><span data-stu-id="3c140-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="3c140-110">如何進行更複雜的提交</span><span class="sxs-lookup"><span data-stu-id="3c140-110">How to make a more complex submission</span></span>

<span data-ttu-id="3c140-111">您需要對 [Git 與 GitHub.com](https://guides.github.com/activities/hello-world/) 有基本了解。</span><span class="sxs-lookup"><span data-stu-id="3c140-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="3c140-112">建立一項[議題](https://github.com/dotnet/EntityFramework.Docs/issues/new)，描述您要執行的動作，例如變更現有文章或建立新的文章。</span><span class="sxs-lookup"><span data-stu-id="3c140-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="3c140-113">在您投入更多時間之前，請先等候 EF 小組的核准。</span><span class="sxs-lookup"><span data-stu-id="3c140-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="3c140-114">將 [dotnet/EntityFramework.Doc](https://github.com/dotnet/EntityFramework.Docs/) 的存放庫分叉，並為您的變更建立分支。</span><span class="sxs-lookup"><span data-stu-id="3c140-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="3c140-115">使用您的變更，將提取要求 (PR) 提交至 main。</span><span class="sxs-lookup"><span data-stu-id="3c140-115">Submit a pull request (PR) to main with your changes.</span></span>
* <span data-ttu-id="3c140-116">回應 PR 意見。</span><span class="sxs-lookup"><span data-stu-id="3c140-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="3c140-117">Markdown 語法</span><span class="sxs-lookup"><span data-stu-id="3c140-117">Markdown syntax</span></span>

<span data-ttu-id="3c140-118">文章是以 [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) 撰寫，其涵蓋 [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/)。</span><span class="sxs-lookup"><span data-stu-id="3c140-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="3c140-119">如需 EF 檔中常用 UI 功能的 DFM 語法和中繼資料的範例，請參閱 [中繼資料和 Markdown 範本](https://docs.microsoft.com/contribute/dotnet/dotnet-style-guide)。</span><span class="sxs-lookup"><span data-stu-id="3c140-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://docs.microsoft.com/contribute/dotnet/dotnet-style-guide).</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="3c140-120">資料夾結構慣例</span><span class="sxs-lookup"><span data-stu-id="3c140-120">Folder structure conventions</span></span>

<span data-ttu-id="3c140-121">影像及其他靜態內容，會儲存在網站每個區域/資料夾內的 `_static` 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="3c140-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="3c140-122">程式碼範例則會儲存在 `samples` 根資料夾中。</span><span class="sxs-lookup"><span data-stu-id="3c140-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="3c140-123">它們會整理為模擬文件結構 (位於 `entity-framework` 根資料夾下) 的資料夾結構。</span><span class="sxs-lookup"><span data-stu-id="3c140-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="3c140-124">程式碼片段</span><span class="sxs-lookup"><span data-stu-id="3c140-124">Code snippets</span></span>

<span data-ttu-id="3c140-125">文章通常會包含程式碼片段，作重點說明之用。</span><span class="sxs-lookup"><span data-stu-id="3c140-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="3c140-126">DFM 可讓您將程式碼複製到 Markdown 檔案中，或是參考不同的程式碼檔案。</span><span class="sxs-lookup"><span data-stu-id="3c140-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="3c140-127">盡可能使用不同的程式碼檔案，將程式碼中的錯誤機率降到最低。</span><span class="sxs-lookup"><span data-stu-id="3c140-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="3c140-128">程式碼檔案應使用上述為範例專案所述的資料夾結構，儲存在存放庫中。</span><span class="sxs-lookup"><span data-stu-id="3c140-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="3c140-129">以下是一些 [DFM 程式碼片段語法](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)的範例。</span><span class="sxs-lookup"><span data-stu-id="3c140-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="3c140-130">若要將完整程式碼檔案轉譯為程式碼片段：</span><span class="sxs-lookup"><span data-stu-id="3c140-130">To render an entire code file as a snippet:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="3c140-131">若要使用行號，將一部分檔案轉譯為程式碼片段：</span><span class="sxs-lookup"><span data-stu-id="3c140-131">To render a portion of a file as a snippet by using line numbers:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="3c140-132">若是 C# 程式碼片段，可參考 [C# 區域](https://msdn.microsoft.com/library/9a1ybwek.aspx)。</span><span class="sxs-lookup"><span data-stu-id="3c140-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="3c140-133">使用區域，而不是行號。</span><span class="sxs-lookup"><span data-stu-id="3c140-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="3c140-134">程式碼檔案中的行號通常會變更，而變得與 Markdown 中的行號參考不同步。</span><span class="sxs-lookup"><span data-stu-id="3c140-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="3c140-135">C# 區域可以是巢狀的。</span><span class="sxs-lookup"><span data-stu-id="3c140-135">C# regions can be nested.</span></span> <span data-ttu-id="3c140-136">如果您參考外部區域，則內部 `#region` 與 `#endregion` 指示詞就不會轉譯在程式碼片段中。</span><span class="sxs-lookup"><span data-stu-id="3c140-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="3c140-137">若要轉譯名為 "snippet_Example" 的 C# 區域：</span><span class="sxs-lookup"><span data-stu-id="3c140-137">To render a C# region named "snippet_Example":</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="3c140-138">若要醒目提示轉譯程式碼片段中所選取的行 (通常會以黃色背景色彩呈現)：</span><span class="sxs-lookup"><span data-stu-id="3c140-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="3c140-139">使用 DocFX 測試變更</span><span class="sxs-lookup"><span data-stu-id="3c140-139">Test your changes with DocFX</span></span>

<span data-ttu-id="3c140-140">使用 [DocFX 命令列工具](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)測試您的變更，這會建立本機裝載的網站版本。</span><span class="sxs-lookup"><span data-stu-id="3c140-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="3c140-141">DocFX 不會轉譯 docs.microsoft.com 建立的樣式和網站延伸模組。</span><span class="sxs-lookup"><span data-stu-id="3c140-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="3c140-142">DocFX 需要 .NET Framework (Windows) 或 Mono (Linux 或 macOS)。</span><span class="sxs-lookup"><span data-stu-id="3c140-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="3c140-143">Windows 指示</span><span class="sxs-lookup"><span data-stu-id="3c140-143">Windows instructions</span></span>

* <span data-ttu-id="3c140-144">請從 [DocFX 版本](https://github.com/dotnet/docfx/releases)下載並解壓縮 *docfx.zip*。</span><span class="sxs-lookup"><span data-stu-id="3c140-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="3c140-145">將 DocFX 新增至您的 PATH。</span><span class="sxs-lookup"><span data-stu-id="3c140-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="3c140-146">在命令列視窗中，巡覽至複製的存放庫 (其中包含 *docfx.json* 檔案)，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3c140-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ```console
   docfx -t default --serve
   ```

* <span data-ttu-id="3c140-147">在瀏覽器中，巡覽至 `http://localhost:8080`。</span><span class="sxs-lookup"><span data-stu-id="3c140-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="3c140-148">Mono 指示</span><span class="sxs-lookup"><span data-stu-id="3c140-148">Mono instructions</span></span>

* <span data-ttu-id="3c140-149">請透過 Homebrew 安裝 Mono - `brew install mono`。</span><span class="sxs-lookup"><span data-stu-id="3c140-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="3c140-150">下載[最新版本的 DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2)。</span><span class="sxs-lookup"><span data-stu-id="3c140-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="3c140-151">解壓縮至 `\bin\docfx`。</span><span class="sxs-lookup"><span data-stu-id="3c140-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="3c140-152">建立 **docfx** 的別名：</span><span class="sxs-lookup"><span data-stu-id="3c140-152">Create an alias for **docfx**:</span></span>

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="3c140-153">執行複製的存放庫中之 **docfx**，來建置網站，並執行 **docfx-serve** 於 `http://localhost:8080` 檢視網站。</span><span class="sxs-lookup"><span data-stu-id="3c140-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="3c140-154">語氣和語調</span><span class="sxs-lookup"><span data-stu-id="3c140-154">Voice and tone</span></span>

<span data-ttu-id="3c140-155">我們撰寫文件的目標是盡可能讓越多使用者輕鬆了解越好。</span><span class="sxs-lookup"><span data-stu-id="3c140-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="3c140-156">為此，我們制定了書寫樣式導方針，並要求參與者務必遵循。</span><span class="sxs-lookup"><span data-stu-id="3c140-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="3c140-157">如需詳細資訊，請參閱 [語音和語氣指導方針](https://docs.microsoft.com/contribute/dotnet/dotnet-voice-tone)。</span><span class="sxs-lookup"><span data-stu-id="3c140-157">For more information, see [Voice and tone guidelines](https://docs.microsoft.com/contribute/dotnet/dotnet-voice-tone).</span></span>
