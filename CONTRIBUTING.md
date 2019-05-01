---
ms.openlocfilehash: 79a2a10cae9f8a5541bca132e407d4abbe95e093
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929884"
---
# <a name="contributing-to-the-entity-framework-documentation"></a>貢獻 Entity Framework 文件

以下說明對 Entity Framework 文件貢獻文章和程式碼範例的程序。 貢獻可以簡單到像錯字更正，或是複雜到像是新的文章。

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>如何提出簡單的修正或建議

文章會以 Markdown 檔案的形式儲存在此存放庫中。 若要對 Markdown 檔案內容進行簡易變更，請按一下瀏覽器視窗右上角的 [編輯] 連結。 您可能需要展開 [選項] 列，才可看到 [編輯] 連結。 請遵循指示，建立提取要求 (PR)。 EF 小組將會於檢閱 PR 後接受，或建議變更。

## <a name="how-to-make-a-more-complex-submission"></a>如何進行更複雜的提交

您需要對 [Git 與 GitHub.com](https://guides.github.com/activities/hello-world/) 有基本了解。

* 建立一項[議題](https://github.com/aspnet/EntityFramework.Docs/issues/new)，描述您要執行的動作，例如變更現有文章或建立新的文章。 在您投入更多時間之前，請先等候 EF 小組的核准。
* 分支 [aspnet/EntityFramework.Docs](https://github.com/aspnet/EntityFramework.Docs/) 存放庫，並針對您的變更建立分支。
* 將內含變更的提取要求 (PR) 提交給管理員。
* 回應 PR 意見。

## <a name="markdown-syntax"></a>Markdown 語法

文章是以 [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html) 撰寫，其涵蓋 [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/)。 如需 EF 文件中常用 UI 功能的 DFM 語法和中繼資料範例，請參閱 .NET Core 存放庫樣式指南中的[中繼資料與 Markdown 範本](https://github.com/dotnet/docs/blob/master/styleguide/template.md)。

## <a name="folder-structure-conventions"></a>資料夾結構慣例

影像及其他靜態內容，會儲存在網站每個區域/資料夾內的 `_static` 資料夾中。

程式碼範例則會儲存在 `samples` 根資料夾中。 它們會整理為模擬文件結構 (位於 `entity-framework` 根資料夾下) 的資料夾結構。

## <a name="code-snippets"></a>程式碼片段

文章通常會包含程式碼片段，作為重點說明之用。 DFM 可讓您將程式碼複製到 Markdown 檔案中，或是參考不同的程式碼檔案。 盡可能使用不同的程式碼檔案，將程式碼中的錯誤機率降到最低。 程式碼檔案應使用上述為範例專案所述的資料夾結構，儲存在存放庫中。

以下是一些 [DFM 程式碼片段語法](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet)的範例。

若要將完整的程式碼檔案轉譯為程式碼片段：

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

若要使用行號，將一部分檔案轉譯為程式碼片段：

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

若是 C# 程式碼片段，可參考 [C# 區域](https://msdn.microsoft.com/library/9a1ybwek.aspx)。 使用區域，而不是行號。 程式碼檔案中的行號通常會變更，而變得與 Markdown 中的行號參考不同步。 C# 區域可以是巢狀的。 如果您參考外部區域，則內部 `#region` 與 `#endregion` 指示詞就不會轉譯在程式碼片段中。

若要轉譯名為 "snippet_Example" 的 C# 區域：

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

若要醒目提示轉譯程式碼片段中選取的行 (通常會以黃色背景色彩呈現)：

``` none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a>使用 DocFX 測試變更

使用 [DocFX 命令列工具](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)測試您的變更，這會建立本機裝載的網站版本。 DocFX 不會轉譯為 docs.microsoft.com 所建立的樣式與網站延伸模組。

DocFX 需要 .NET Framework (Windows) 或 Mono (Linux 或 macOS)。

### <a name="windows-instructions"></a>Windows 指示

* 請從 [DocFX 版本](https://github.com/dotnet/docfx/releases)下載並解壓縮 *docfx.zip*。
* 將 DocFX 新增至您的 PATH。
* 在命令列視窗中，巡覽至複製的存放庫 (其中包含 *docfx.json* 檔案)，然後執行下列命令：

   ``` console
   docfx -t default --serve
   ```

* 在瀏覽器中，巡覽至 `http://localhost:8080`。

### <a name="mono-instructions"></a>Mono 指示

* 請透過 Homebrew 安裝 Mono - `brew install mono`。
* 下載[最新版的 DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2)。
* 解壓縮至 `\bin\docfx`。
* 建立 **docfx** 的別名：

  ``` console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* 執行複製的存放庫中之 **docfx**，來建置網站，並執行 **docfx-serve** 於 `http://localhost:8080` 檢視網站。

## <a name="voice-and-tone"></a>語態和語氣

我們撰寫文件的目標是盡可能讓愈多使用者輕易即可了解愈好。 為此，我們制定了書寫樣式導方針，並要求參與者務必遵循。 如需詳細資訊，請參閱 .NET Core 存放庫中的[語態和語氣指導方針](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md)。
