---
title: Ngen-EF6 的改善啟動效能
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
ms.openlocfilehash: 2e9a93c7741046da25b890fed084a6b280bf5643
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489956"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="fd816-102">使用 NGen 的改善啟動效能</span><span class="sxs-lookup"><span data-stu-id="fd816-102">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="fd816-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="fd816-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="fd816-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="fd816-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="fd816-105">.NET Framework 支援的原生映像產生受管理的應用程式和程式庫做為協助更快速地啟動的應用程式的方式以及在某些情況下使用較少的記憶體。</span><span class="sxs-lookup"><span data-stu-id="fd816-105">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="fd816-106">原生映像建立的轉譯減輕.NET JIT (Just In Time) 編譯器產生的原生指示的 managed 程式碼組件包含原生機器指令，才能執行應用程式時，檔案應用程式執行階段。</span><span class="sxs-lookup"><span data-stu-id="fd816-106">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="fd816-107">之前第 6 版，EF 執行階段的核心程式庫是.NET Framework 的一部分，並為其自動產生原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-107">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="fd816-108">從版本 6 開始的整個 EF 執行階段已經結合到 EntityFramework NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="fd816-108">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="fd816-109">原生映像現在會產生使用 NGen.exe 命令列工具，以取得類似的結果。</span><span class="sxs-lookup"><span data-stu-id="fd816-109">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="fd816-110">經驗的觀察值顯示的 EF 執行階段組件的原生映像可以剪下的應用程式啟動時間的 1 到 3 秒之間。</span><span class="sxs-lookup"><span data-stu-id="fd816-110">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="fd816-111">如何使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="fd816-111">How to use NGen.exe</span></span>  

<span data-ttu-id="fd816-112">最基本的函式的 NGen.exe 工具是 「 安裝 」 (也就是建立來保存到磁碟) 組件及其所有直接相依性的原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-112">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="fd816-113">以下是達成的：</span><span class="sxs-lookup"><span data-stu-id="fd816-113">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="fd816-114">系統管理員身分開啟命令提示字元 視窗</span><span class="sxs-lookup"><span data-stu-id="fd816-114">Open a Command Prompt window as an administrator</span></span>  
2. <span data-ttu-id="fd816-115">將目前的工作目錄變更至您想要產生原生映像的組件的位置：</span><span class="sxs-lookup"><span data-stu-id="fd816-115">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>  

  ``` console
    cd <*Assemblies location*>  
  ```
3. <span data-ttu-id="fd816-116">取決於您的作業系統和應用程式的組態，您可能需要產生原生映像適用於 32 位元架構，64 位元架構或兩者。</span><span class="sxs-lookup"><span data-stu-id="fd816-116">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>  

    <span data-ttu-id="fd816-117">執行的 32 位元：</span><span class="sxs-lookup"><span data-stu-id="fd816-117">For 32 bit run:</span></span>  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    <span data-ttu-id="fd816-118">對於執行的 64 位元：</span><span class="sxs-lookup"><span data-stu-id="fd816-118">For 64 bit run:</span></span>
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> <span data-ttu-id="fd816-119">產生錯誤的架構的原生映像，是很常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="fd816-119">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="fd816-120">當不確定您只可以產生適用於安裝在電腦的作業系統的所有架構的原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-120">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="fd816-121">NGen.exe 也支援其他功能，例如解除安裝，並顯示已安裝的原生映像，佇列產生多個映像等等。如需使用方式的詳細資訊，請參閱[NGen.exe 文件](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fd816-121">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="fd816-122">使用 NGen.exe 的時機</span><span class="sxs-lookup"><span data-stu-id="fd816-122">When to use NGen.exe</span></span>  

<span data-ttu-id="fd816-123">決定要產生原生映像，在 EF 6 或更新版本為基礎的應用程式中的組件說過，您應該考慮下列選項：</span><span class="sxs-lookup"><span data-stu-id="fd816-123">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="fd816-124">**主要 EF 執行階段組件，EntityFramework.dll**： 典型的 EF 基礎應用程式執行大量的程式碼從這個組件會在啟動或其第一次存取資料庫。</span><span class="sxs-lookup"><span data-stu-id="fd816-124">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="fd816-125">因此，建立此組件的原生映像，將會產生的最大的提升，在 啟動效能。</span><span class="sxs-lookup"><span data-stu-id="fd816-125">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="fd816-126">**任何應用程式所使用的提供者組件 EF**： 啟動時間也受益於稍有產生這些原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-126">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="fd816-127">比方說，如果應用程式會使用 EF provider for SQL Server 您會想要為 EntityFramework.SqlServer.dll 產生原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-127">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="fd816-128">**您的應用程式組件和其他相依性**: [NGen.exe 文件](https://msdn.microsoft.com/library/6t9t5wcf.aspx)涵蓋選擇要產生原生映像和原生映像上的安全性，影響的組件的一般準則進階選項，例如 「 硬式繫結 」 案例，例如使用偵錯和分析等案例中的原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-128">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="fd816-129">請確定您仔細衡量使用原生映像上的啟動效能和您的應用程式的整體效能的影響，並比較實際的需求。</span><span class="sxs-lookup"><span data-stu-id="fd816-129">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="fd816-130">原生映像通常有助於改善啟動效能，並在某些情況下減少記憶體使用量，而不是所有實例將同樣都獲益。</span><span class="sxs-lookup"><span data-stu-id="fd816-130">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="fd816-131">比方說，在穩定狀態下執行 （也就是說，一旦應用程式正在使用的所有方法都至少一次叫都用） JIT 編譯器所產生的程式碼可能實際上會產生稍微較佳的效能比原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-131">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="fd816-132">在開發電腦中使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="fd816-132">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="fd816-133">在開發.NET JIT 編譯器會提供經常變更的程式碼的最佳整體權衡取捨。</span><span class="sxs-lookup"><span data-stu-id="fd816-133">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="fd816-134">產生已編譯的相依性，例如 EF 執行階段組件的原生映像，可協助加速開發和測試可藉由減少幾秒鐘的時間，在每次執行的開頭。</span><span class="sxs-lookup"><span data-stu-id="fd816-134">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="fd816-135">若要尋找的 EF 執行階段組件的好地方是方案的 NuGet 封裝位置。</span><span class="sxs-lookup"><span data-stu-id="fd816-135">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="fd816-136">例如，使用 EF 6.0.2 搭配 SQL Server，並以.NET 4.5 或更新版本為目標的應用程式您可以輸入下列命令提示字元視窗 （請記住，若要開啟以系統管理員身分）：</span><span class="sxs-lookup"><span data-stu-id="fd816-136">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

``` console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="fd816-137">這會利用的事實，安裝適用於 SQL Server 的 EF 提供者的原生映像也會依預設安裝原生映像主要 EF 執行階段組件。</span><span class="sxs-lookup"><span data-stu-id="fd816-137">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="fd816-138">這是因為 NGen.exe 可以偵測出 EntityFramework.dll 的直接相依性 EntityFramework.SqlServer.dll 組件位於相同的目錄。</span><span class="sxs-lookup"><span data-stu-id="fd816-138">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="fd816-139">在安裝期間建立原生映像</span><span class="sxs-lookup"><span data-stu-id="fd816-139">Creating native images during setup</span></span>  

<span data-ttu-id="fd816-140">WiX 工具組支援佇列處理產生的原生映像 managed 組件在安裝期間，在此所述[< 如何 > 指南](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)。</span><span class="sxs-lookup"><span data-stu-id="fd816-140">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="fd816-141">另一個替代方式是建立自訂安裝工作執行 NGen.exe 命令。</span><span class="sxs-lookup"><span data-stu-id="fd816-141">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="fd816-142">驗證原生映像會用於 EF</span><span class="sxs-lookup"><span data-stu-id="fd816-142">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="fd816-143">您可以確認特定的應用程式使用原生組件，藉由尋找延伸模組的載入組件 」。 ni.dll 「 或 」。 ni.exe"。</span><span class="sxs-lookup"><span data-stu-id="fd816-143">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="fd816-144">例如，EF 的主要執行階段組件的原生映像將會呼叫 EntityFramework.ni.dll。</span><span class="sxs-lookup"><span data-stu-id="fd816-144">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="fd816-145">檢查處理序載入的.NET 組件的簡單方法是使用[Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。</span><span class="sxs-lookup"><span data-stu-id="fd816-145">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="fd816-146">要注意的其他項目</span><span class="sxs-lookup"><span data-stu-id="fd816-146">Other things to be aware of</span></span>  

<span data-ttu-id="fd816-147">**建立組件的原生映像不應混淆註冊中的組件[GAC （全域組件快取）](https://msdn.microsoft.com/library/yf1d93sz.aspx)**。</span><span class="sxs-lookup"><span data-stu-id="fd816-147">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="fd816-148">NGen.exe 可讓您建立的組件不在 GAC 中的映像，事實上，使用特定版本的 EF 的多個應用程式可以共用相同的原生映像。</span><span class="sxs-lookup"><span data-stu-id="fd816-148">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="fd816-149">Windows 8 可以自動建立原生映像放在 GAC 的組件，而 EF 執行階段會最佳化與您的應用程式一起部署，而我們不建議在 GAC 中註冊，這會有負面的影響，在組件解析和服務應用程式在其他方面。</span><span class="sxs-lookup"><span data-stu-id="fd816-149">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  
