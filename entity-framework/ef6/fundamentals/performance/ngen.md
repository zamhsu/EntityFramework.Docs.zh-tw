---
title: 使用 NGen 來改善啟動效能-EF6
description: 在 Entity Framework 6 中使用 NGen 改善啟動效能
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: a58ffc1a4da1cdb0ae12366ab1ec0139141b3a7f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065416"
---
# <a name="improving-startup-performance-with-ngen"></a><span data-ttu-id="f36ed-103">使用 NGen 改善啟動效能</span><span class="sxs-lookup"><span data-stu-id="f36ed-103">Improving startup performance with NGen</span></span>
> [!NOTE]
> <span data-ttu-id="f36ed-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="f36ed-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="f36ed-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="f36ed-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="f36ed-106">.NET Framework 支援產生受控應用程式和程式庫的原生映射，以協助應用程式更快啟動，而且在某些情況下會使用較少的記憶體。</span><span class="sxs-lookup"><span data-stu-id="f36ed-106">The .NET Framework supports the generation of native images for managed applications and libraries as a way to help applications start faster and also in some cases use less memory.</span></span> <span data-ttu-id="f36ed-107">原生映射的建立方式是將 managed 程式碼元件轉譯為包含原生電腦指令的檔案，然後再執行應用程式，以避免 .NET JIT (及時) 編譯器必須在應用程式執行時間產生原生指令。</span><span class="sxs-lookup"><span data-stu-id="f36ed-107">Native images are created by translating managed code assemblies into files containing native machine instructions before the application is executed, relieving the .NET JIT (Just-In-Time) compiler from having to generate the native instructions at application runtime.</span></span>  

<span data-ttu-id="f36ed-108">在第6版之前，EF runtime 的核心程式庫是 .NET Framework 的一部分，並且會自動產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-108">Prior to version 6, the EF runtime’s core libraries were part of the .NET Framework and native images were generated automatically for them.</span></span> <span data-ttu-id="f36ed-109">從第6版開始，整個 EF runtime 已合併至 EntityFramework NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="f36ed-109">Starting with version 6 the whole EF runtime has been combined into the EntityFramework NuGet package.</span></span> <span data-ttu-id="f36ed-110">原生映射現在必須使用 NGen.exe 命令列工具產生，才能取得類似的結果。</span><span class="sxs-lookup"><span data-stu-id="f36ed-110">Native images have to now be generated using the NGen.exe command line tool to obtain similar results.</span></span>  

<span data-ttu-id="f36ed-111">經驗觀察顯示 EF runtime 元件的原生映射可以在應用程式啟動時間的1到3秒之間剪下。</span><span class="sxs-lookup"><span data-stu-id="f36ed-111">Empirical observations show that native images of the EF runtime assemblies can cut between 1 and 3 seconds of application startup time.</span></span>  

## <a name="how-to-use-ngenexe"></a><span data-ttu-id="f36ed-112">如何使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="f36ed-112">How to use NGen.exe</span></span>  

<span data-ttu-id="f36ed-113">NGen.exe 工具最基本的功能是「安裝」， (也就是建立並保存到磁片) 元件的原生映射及其所有直接相依性。</span><span class="sxs-lookup"><span data-stu-id="f36ed-113">The most basic function of the NGen.exe tool is to “install” (that is, to create and persist to disk) native images for an assembly and all its direct dependencies.</span></span> <span data-ttu-id="f36ed-114">以下是您可以達成此目的的方法：</span><span class="sxs-lookup"><span data-stu-id="f36ed-114">Here is how you can achieve that:</span></span>  

1. <span data-ttu-id="f36ed-115">以系統管理員身分開啟 [命令提示字元] 視窗。</span><span class="sxs-lookup"><span data-stu-id="f36ed-115">Open a Command Prompt window as an administrator.</span></span>
2. <span data-ttu-id="f36ed-116">將目前的工作目錄變更為您想要為其產生原生映射的元件位置：</span><span class="sxs-lookup"><span data-stu-id="f36ed-116">Change the current working directory to the location of the assemblies you want to generate native images for:</span></span>

   ``` console
   cd <*Assemblies location*>  
   ```

3. <span data-ttu-id="f36ed-117">根據您的作業系統和應用程式的設定，您可能需要為32位架構、64位架構或兩者產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-117">Depending on your operating system and the application’s configuration you might need to generate native images for 32 bit architecture, 64 bit architecture or for both.</span></span>

   <span data-ttu-id="f36ed-118">若為32位執行：</span><span class="sxs-lookup"><span data-stu-id="f36ed-118">For 32 bit run:</span></span>

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   <span data-ttu-id="f36ed-119">若為64位執行：</span><span class="sxs-lookup"><span data-stu-id="f36ed-119">For 64 bit run:</span></span>
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> <span data-ttu-id="f36ed-120">產生錯誤架構的原生映射是很常見的錯誤。</span><span class="sxs-lookup"><span data-stu-id="f36ed-120">Generating native images for the wrong architecture is a very common mistake.</span></span> <span data-ttu-id="f36ed-121">如果不確定，您可以只針對套用至電腦上所安裝作業系統的所有架構產生原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-121">When in doubt you can simply generate native images for all the architectures that apply to the operating system installed in the machine.</span></span>  

<span data-ttu-id="f36ed-122">NGen.exe 也支援其他功能，例如卸載和顯示已安裝的原生映射、將多個映射的產生排入佇列等等。如需使用方式的詳細資訊，請參閱 [NGen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f36ed-122">NGen.exe also supports other functions such as uninstalling and displaying the installed native images, queuing the generation of multiple images, etc. For more details of usage read the [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx).</span></span>  

## <a name="when-to-use-ngenexe"></a><span data-ttu-id="f36ed-123">使用時機 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="f36ed-123">When to use NGen.exe</span></span>  

<span data-ttu-id="f36ed-124">當您決定要在以 EF 6 版或更新版本為基礎的應用程式中產生原生映射的元件時，您應該考慮下列選項：</span><span class="sxs-lookup"><span data-stu-id="f36ed-124">When it comes to decide which assemblies to generate native images for in an application based on EF version 6 or greater, you should consider the following options:</span></span>  

- <span data-ttu-id="f36ed-125">**主要 ef runtime 元件 EntityFramework.dll**：一般 ef 應用程式會在啟動時，或在第一次存取資料庫時，從此元件執行大量的程式碼。</span><span class="sxs-lookup"><span data-stu-id="f36ed-125">**The main EF runtime assembly, EntityFramework.dll**: A typical EF based application executes a significant amount of code from this assembly on startup or on its first access to the database.</span></span> <span data-ttu-id="f36ed-126">因此，建立此元件的原生映射將會產生最大的啟動效能提升。</span><span class="sxs-lookup"><span data-stu-id="f36ed-126">Consequently, creating native images of this assembly will produce the biggest gains in startup performance.</span></span>  
- <span data-ttu-id="f36ed-127">**您的應用程式所使用的任何 EF 提供者元件**：啟動時間也會稍微受益于產生這些的原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-127">**Any EF provider assembly used by your application**: Startup time can also benefit slightly from generating native images of these.</span></span> <span data-ttu-id="f36ed-128">例如，如果應用程式使用 EF provider for SQL Server 您會想要產生 EntityFramework.SqlServer.dll 的原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-128">For example, if the application uses the EF provider for SQL Server you will want to generate a native image for EntityFramework.SqlServer.dll.</span></span>  
- <span data-ttu-id="f36ed-129">**您應用程式的元件和其他**相依性： [NGen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx) 涵蓋了選擇要產生原生影像的元件，以及原生映射對安全性的影響、先進的選項（例如「硬系結」）、像是在偵錯工具和分析案例中使用原生映射等案例的一般準則。</span><span class="sxs-lookup"><span data-stu-id="f36ed-129">**Your application’s assemblies and other dependencies**: The [NGen.exe documentation](https://msdn.microsoft.com/library/6t9t5wcf.aspx) covers general criteria for choosing which assemblies to generate native images for and the impact of native images on security, advanced options such as “hard binding”, scenarios such as using native images in debugging and profiling scenarios, etc.</span></span>  

> [!TIP]
> <span data-ttu-id="f36ed-130">請務必仔細測量在啟動效能和應用程式的整體效能上使用原生映射所造成的影響，並將它們與實際需求進行比較。</span><span class="sxs-lookup"><span data-stu-id="f36ed-130">Make sure you carefully measure the impact of using native images on both the startup performance and the overall performance of your application and compare them against actual requirements.</span></span> <span data-ttu-id="f36ed-131">雖然原生映射通常有助於改善啟動效能，但在某些情況下會減少記憶體使用量，但並非所有案例都會同樣受益。</span><span class="sxs-lookup"><span data-stu-id="f36ed-131">While native images will generally help improve startup up performance and in some cases reduce memory usage, not all scenarios will benefit equally.</span></span> <span data-ttu-id="f36ed-132">例如，在穩定狀態執行 (也就是，一旦至少叫用應用程式所使用的所有方法一次) 之後，JIT 編譯程式所產生的所有方法，實際上會比原生映射產生稍微更好的效能。</span><span class="sxs-lookup"><span data-stu-id="f36ed-132">For instance, on steady state execution (that is, once all the methods being used by the application have been invoked at least once) code generated by the JIT compiler can in fact yield slightly better performance than native images.</span></span>  

## <a name="using-ngenexe-in-a-development-machine"></a><span data-ttu-id="f36ed-133">在開發電腦中使用 NGen.exe</span><span class="sxs-lookup"><span data-stu-id="f36ed-133">Using NGen.exe in a development machine</span></span>  

<span data-ttu-id="f36ed-134">在開發期間，.NET JIT 編譯程式會針對經常變更的程式碼，提供最佳的整體折衷。</span><span class="sxs-lookup"><span data-stu-id="f36ed-134">During development the .NET JIT compiler will offer the best overall tradeoff for code that is changing frequently.</span></span> <span data-ttu-id="f36ed-135">為編譯的相依性產生原生映射（例如 EF runtime 元件）有助於加速開發和測試，方法是在每次執行的開頭剪下幾秒鐘。</span><span class="sxs-lookup"><span data-stu-id="f36ed-135">Generating native images for compiled dependencies such as the EF runtime assemblies can help accelerate development and testing by cutting a few seconds out at the beginning of each execution.</span></span>  

<span data-ttu-id="f36ed-136">尋找 EF runtime 元件的理想位置是解決方案的 NuGet 套件位置。</span><span class="sxs-lookup"><span data-stu-id="f36ed-136">A good place to find the EF runtime assemblies is the NuGet package location for the solution.</span></span> <span data-ttu-id="f36ed-137">例如，若是使用 EF 6.0.2 搭配 SQL Server 並以 .NET 4.5 或更新版本為目標的應用程式，您可以在命令提示字元視窗中輸入下列命令， (記得以系統管理員的身分來開啟它) ：</span><span class="sxs-lookup"><span data-stu-id="f36ed-137">For example, for an application using EF 6.0.2 with SQL Server and targeting .NET 4.5 or greater you can type the following in a Command Prompt window (remember to open it as an administrator):</span></span>  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> <span data-ttu-id="f36ed-138">這樣做的優點是，安裝適用于 EF provider for SQL Server 的原生映射也會預設安裝主要 EF runtime 元件的原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-138">This takes advantage of the fact that installing the native images for EF provider for SQL Server will also by default install the native images for the main EF runtime assembly.</span></span> <span data-ttu-id="f36ed-139">這是因為 NGen.exe 可以偵測到 EntityFramework.dll 是位於相同目錄中 EntityFramework.SqlServer.dll 元件的直接相依性。</span><span class="sxs-lookup"><span data-stu-id="f36ed-139">This works because NGen.exe can detect that EntityFramework.dll is a direct dependency of the EntityFramework.SqlServer.dll assembly located in the same directory.</span></span>  

## <a name="creating-native-images-during-setup"></a><span data-ttu-id="f36ed-140">在安裝期間建立原生映射</span><span class="sxs-lookup"><span data-stu-id="f36ed-140">Creating native images during setup</span></span>  

<span data-ttu-id="f36ed-141">在安裝期間，WiX 工具組支援將 managed 元件的原生映射產生佇列，如本操作 [指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。</span><span class="sxs-lookup"><span data-stu-id="f36ed-141">The WiX Toolkit supports queuing the generation of native images for managed assemblies during setup, as explained in this [how-to guide](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html).</span></span> <span data-ttu-id="f36ed-142">另一個替代方式是建立執行 NGen.exe 命令的自訂安裝工作。</span><span class="sxs-lookup"><span data-stu-id="f36ed-142">Another alternative is to create a custom setup task that execute the NGen.exe command.</span></span>  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a><span data-ttu-id="f36ed-143">確認原生映射正在用於 EF</span><span class="sxs-lookup"><span data-stu-id="f36ed-143">Verifying that native images are being used for EF</span></span>  

<span data-ttu-id="f36ed-144">您可以藉由尋找副檔名為 ".ni.dll" 或 ".ni.exe" 的已載入元件，確認特定的應用程式正在使用原生元件。</span><span class="sxs-lookup"><span data-stu-id="f36ed-144">You can verify that a specific application is using a native assembly by looking for loaded assemblies that have the extension “.ni.dll” or “.ni.exe”.</span></span> <span data-ttu-id="f36ed-145">例如，EF 的主要執行時間元件的原生映射將被呼叫 EntityFramework.ni.dll。</span><span class="sxs-lookup"><span data-stu-id="f36ed-145">For example, a native image for the EF’s main runtime assembly will be called EntityFramework.ni.dll.</span></span> <span data-ttu-id="f36ed-146">檢查載入之 .NET 元件的簡單方法是使用 [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。</span><span class="sxs-lookup"><span data-stu-id="f36ed-146">An easy way to inspect the loaded .NET assemblies of a process is to use [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653).</span></span>  

## <a name="other-things-to-be-aware-of"></a><span data-ttu-id="f36ed-147">其他需要注意的事項</span><span class="sxs-lookup"><span data-stu-id="f36ed-147">Other things to be aware of</span></span>  

<span data-ttu-id="f36ed-148">**建立元件的原生映射不應與在 GAC 中註冊元件[ (全域組件快取) ](https://msdn.microsoft.com/library/yf1d93sz.aspx)混淆**。</span><span class="sxs-lookup"><span data-stu-id="f36ed-148">**Creating a native image of an assembly should not be confused with registering the assembly in the [GAC (Global Assembly Cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**.</span></span> <span data-ttu-id="f36ed-149">NGen.exe 可以建立不在 GAC 中的元件映射，事實上，使用特定 EF 版本的多個應用程式可以共用相同的原生映射。</span><span class="sxs-lookup"><span data-stu-id="f36ed-149">NGen.exe allows creating images of assemblies that are not in the GAC, and in fact, multiple applications that use a specific version of EF can share the same native image.</span></span> <span data-ttu-id="f36ed-150">雖然 Windows 8 可以自動為放在 GAC 中的元件建立原生映射，但是 EF 執行時間已優化，可與您的應用程式一起部署，我們不建議您在 GAC 中註冊它，因為這會對元件解析產生負面影響，並在其他方面提供應用程式的服務。</span><span class="sxs-lookup"><span data-stu-id="f36ed-150">While Windows 8 can automatically create native images for assemblies placed in the GAC, the EF runtime is optimized to be deployed alongside your application and we do not recommend registering it in the GAC as this has a negative impact on assembly resolution and servicing your applications among other aspects.</span></span>  
