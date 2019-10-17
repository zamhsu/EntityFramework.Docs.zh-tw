---
title: 使用 NGen 改善啟動效能-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
ms.openlocfilehash: 841aec645abdb2a56076d0b70bfb2614b0acafb4
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72446004"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="92308-102">使用 NGen 改善啟動效能</span><span class="sxs-lookup"><span data-stu-id="92308-102">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="92308-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="92308-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="92308-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="92308-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="92308-105">.NET Framework 支援針對受控應用程式和程式庫產生原生映射，以協助應用程式的啟動速度更快，而且在某些情況下會使用較少的記憶體。</span><span class="sxs-lookup"><span data-stu-id="92308-105">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="92308-106">原生映射的建立方式是將 managed 程式碼元件轉譯為包含原生電腦指令的檔案，然後再執行應用程式，以避免 .NET JIT （及時）編譯器必須產生原生指令應用程式執行時間。</span><span class="sxs-lookup"><span data-stu-id="92308-106">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="92308-107">在第6版之前，EF 執行時間的核心程式庫是 .NET Framework 的一部分，而且會為它們自動產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-107">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="92308-108">從第6版開始，已將整個 EF 執行時間合併為 EntityFramework NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="92308-108">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="92308-109">現在必須使用 Ngen.exe 命令列工具產生原生映射，以取得類似的結果。</span><span class="sxs-lookup"><span data-stu-id="92308-109">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="92308-110">經驗觀察顯示 EF runtime 元件的原生映射可以在應用程式啟動時間的1到3秒之間剪下。</span><span class="sxs-lookup"><span data-stu-id="92308-110">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="92308-111">如何使用 Ngen.exe</span><span class="sxs-lookup"><span data-stu-id="92308-111">How to use NGen.exe</span></span>  

<span data-ttu-id="92308-112">Ngen.exe 工具的最基本功能是針對元件及其所有直接相依性，「安裝」（也就是建立和保存至磁片）原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-112">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="92308-113">以下是您可以達成此目標的方式：</span><span class="sxs-lookup"><span data-stu-id="92308-113">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="92308-114">以系統管理員身分開啟 [命令提示字元] 視窗。</span><span class="sxs-lookup"><span data-stu-id="92308-114">Open a Command Prompt window as an administrator.</span></span>
2. <span data-ttu-id="92308-115">將目前的工作目錄變更為您想要產生原生映射的元件位置：</span><span class="sxs-lookup"><span data-stu-id="92308-115">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>

   ``` console
   cd <*Assemblies location*>  
   ```

3. <span data-ttu-id="92308-116">視您的作業系統和應用程式的設定而定，您可能需要產生32位架構、64位架構或兩者的原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-116">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>

   <span data-ttu-id="92308-117">若為32位執行：</span><span class="sxs-lookup"><span data-stu-id="92308-117">For 32 bit run:</span></span>

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   <span data-ttu-id="92308-118">若為64位執行：</span><span class="sxs-lookup"><span data-stu-id="92308-118">For 64 bit run:</span></span>
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> <span data-ttu-id="92308-119">產生錯誤架構的原生映射是很常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="92308-119">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="92308-120">若不確定，您可以只針對所有適用于電腦上所安裝作業系統的架構產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-120">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="92308-121">Ngen.exe 也支援其他功能，例如卸載和顯示已安裝的原生映射、將多個映射的產生排入佇列等等。如需使用方式的詳細資訊，請參閱[ngen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。</span><span class="sxs-lookup"><span data-stu-id="92308-121">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="92308-122">使用 Ngen.exe 的時機</span><span class="sxs-lookup"><span data-stu-id="92308-122">When to use NGen.exe</span></span>  

<span data-ttu-id="92308-123">當決定要在以 EF 6 版或更新版本為基礎的應用程式中產生原生映射的元件時，您應該考慮下列選項：</span><span class="sxs-lookup"><span data-stu-id="92308-123">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="92308-124">**主要 ef 執行時間元件（EntityFramework）** ：一般 ef 型應用程式會在啟動時或其第一次存取資料庫時，從這個元件執行大量的程式碼。</span><span class="sxs-lookup"><span data-stu-id="92308-124">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="92308-125">因此，建立此元件的原生映射將會產生最大的啟動效能提升。</span><span class="sxs-lookup"><span data-stu-id="92308-125">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="92308-126">**您的應用程式所使用的任何 EF 提供者元件**：啟動時間也會稍微受益于產生這些的原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-126">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="92308-127">例如，如果應用程式使用 SQL Server 的 EF 提供者，您會想要為 EntityFramework 產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-127">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="92308-128">**您應用程式的元件和其他**相依性： [ngen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)涵蓋選擇要產生原生映射之元件的一般準則，以及原生映射對安全性的影響，例如「hard系結」，例如在偵錯工具和分析案例中使用原生影像等案例。</span><span class="sxs-lookup"><span data-stu-id="92308-128">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="92308-129">請確定您仔細測量在應用程式的啟動效能和整體效能上使用原生映射的影響，並將它們與實際需求做比較。</span><span class="sxs-lookup"><span data-stu-id="92308-129">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="92308-130">雖然原生映射通常有助於改善啟動效能，而且在某些情況下會減少記憶體使用量，並非所有案例都能同樣受益。</span><span class="sxs-lookup"><span data-stu-id="92308-130">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="92308-131">比方說，在穩定狀態執行時（亦即，一旦應用程式使用的所有方法都叫用至少一次），JIT 編譯程式所產生的程式碼實際上可能會比原生映射產生稍微好一點的效能。</span><span class="sxs-lookup"><span data-stu-id="92308-131">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="92308-132">在開發電腦中使用 Ngen.exe</span><span class="sxs-lookup"><span data-stu-id="92308-132">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="92308-133">在開發期間，.NET JIT 編譯程式會針對經常變更的程式碼提供最佳的整體取捨。</span><span class="sxs-lookup"><span data-stu-id="92308-133">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="92308-134">針對編譯的相依性（例如 EF 執行時間元件）產生原生映射有助於加速開發和測試，方法是在每次執行開始時減少幾秒鐘。</span><span class="sxs-lookup"><span data-stu-id="92308-134">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="92308-135">尋找 EF 執行時間元件的好地方是解決方案的 NuGet 套件位置。</span><span class="sxs-lookup"><span data-stu-id="92308-135">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="92308-136">例如，針對使用 EF 6.0.2 搭配 SQL Server 並以 .NET 4.5 或更高版本為目標的應用程式，您可以在 [命令提示字元] 視窗中輸入下列內容（請務必以系統管理員的身分開啟）：</span><span class="sxs-lookup"><span data-stu-id="92308-136">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="92308-137">這會利用安裝 EF provider for SQL Server 原生映射的事實，也會預設安裝主要 EF 執行時間元件的原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-137">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="92308-138">這是因為 Ngen.exe 可以偵測到 EntityFramework 是位於相同目錄中的 EntityFramework 的直接相依性。</span><span class="sxs-lookup"><span data-stu-id="92308-138">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="92308-139">在安裝期間建立原生映射</span><span class="sxs-lookup"><span data-stu-id="92308-139">Creating native images during setup</span></span>  

<span data-ttu-id="92308-140">WiX 工具組支援在安裝期間，針對 managed 元件的原生映射產生佇列，如本操作[指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。</span><span class="sxs-lookup"><span data-stu-id="92308-140">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="92308-141">另一個替代方式是建立執行 Ngen.exe 命令的自訂安裝工作。</span><span class="sxs-lookup"><span data-stu-id="92308-141">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="92308-142">確認原生映射正用於 EF</span><span class="sxs-lookup"><span data-stu-id="92308-142">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="92308-143">您可以藉由尋找副檔名為 ". ni .dll" 或 ".exe" 的載入元件，驗證特定應用程式是否使用原生元件。</span><span class="sxs-lookup"><span data-stu-id="92308-143">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="92308-144">例如，EF 主要執行時間元件的原生映射將稱為 EntityFramework。</span><span class="sxs-lookup"><span data-stu-id="92308-144">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="92308-145">檢查處理程式載入的 .NET 元件的簡單方法是使用[Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。</span><span class="sxs-lookup"><span data-stu-id="92308-145">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="92308-146">其他要注意的事項</span><span class="sxs-lookup"><span data-stu-id="92308-146">Other things to be aware of</span></span>  

<span data-ttu-id="92308-147">**建立元件的原生映射時，不應該與在 GAC 中註冊元件[（全域組件快取）](https://msdn.microsoft.com/library/yf1d93sz.aspx)混淆**。</span><span class="sxs-lookup"><span data-stu-id="92308-147">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="92308-148">Ngen.exe 允許建立不在 GAC 中的元件影像，事實上，使用特定 EF 版本的多個應用程式可以共用相同的原生映射。</span><span class="sxs-lookup"><span data-stu-id="92308-148">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="92308-149">雖然 Windows 8 可以自動建立放在 GAC 中之元件的原生映射，但 EF 執行時間已優化，可與您的應用程式一起部署，而且我們不建議在 GAC 中註冊它，因為這對元件解析有負面影響，而且在其他方面提供您的應用程式。</span><span class="sxs-lookup"><span data-stu-id="92308-149">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  
