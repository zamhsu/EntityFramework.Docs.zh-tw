---
title: 使用 NGen 來改善啟動效能-EF6
description: 在 Entity Framework 6 中使用 NGen 改善啟動效能
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: 53b9c2147c95fe096d459ad195a0549b32b67155
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616098"
---
# <a name="improving-startup-performance-with-ngen"></a>使用 NGen 改善啟動效能
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

.NET Framework 支援產生受控應用程式和程式庫的原生映射，以協助應用程式更快啟動，而且在某些情況下會使用較少的記憶體。 原生映射的建立方式是將 managed 程式碼元件轉譯為包含原生電腦指令的檔案，然後再執行應用程式，以避免 .NET JIT (及時) 編譯器必須在應用程式執行時間產生原生指令。  

在第6版之前，EF runtime 的核心程式庫是 .NET Framework 的一部分，並且會自動產生原生映射。 從第6版開始，整個 EF runtime 已合併至 EntityFramework NuGet 套件。 原生映射現在必須使用 NGen.exe 命令列工具產生，才能取得類似的結果。  

經驗觀察顯示 EF runtime 元件的原生映射可以在應用程式啟動時間的1到3秒之間剪下。  

## <a name="how-to-use-ngenexe"></a>如何使用 NGen.exe  

NGen.exe 工具最基本的功能是「安裝」， (也就是建立並保存到磁片) 元件的原生映射及其所有直接相依性。 以下是您可以達成此目的的方法：  

1. 以系統管理員身分開啟 [命令提示字元] 視窗。
2. 將目前的工作目錄變更為您想要為其產生原生映射的元件位置：

   ``` console
   cd <*Assemblies location*>  
   ```

3. 根據您的作業系統和應用程式的設定，您可能需要為32位架構、64位架構或兩者產生原生映射。

   若為32位執行：

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   若為64位執行：
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> 產生錯誤架構的原生映射是很常見的錯誤。 如果不確定，您可以只針對套用至電腦上所安裝作業系統的所有架構產生原生映射。  

NGen.exe 也支援其他功能，例如卸載和顯示已安裝的原生映射、將多個映射的產生排入佇列等等。如需使用方式的詳細資訊，請參閱 [NGen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。  

## <a name="when-to-use-ngenexe"></a>使用時機 NGen.exe  

當您決定要在以 EF 6 版或更新版本為基礎的應用程式中產生原生映射的元件時，您應該考慮下列選項：  

- **主要 ef runtime 元件 EntityFramework.dll**：一般 ef 應用程式會在啟動時，或在第一次存取資料庫時，從此元件執行大量的程式碼。 因此，建立此元件的原生映射將會產生最大的啟動效能提升。  
- **您的應用程式所使用的任何 EF 提供者元件**：啟動時間也會稍微受益于產生這些的原生映射。 例如，如果應用程式使用 EF provider for SQL Server 您會想要產生 EntityFramework.SqlServer.dll 的原生映射。  
- **您應用程式的元件和其他**相依性： [NGen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx) 涵蓋了選擇要產生原生影像的元件，以及原生映射對安全性的影響、先進的選項（例如「硬系結」）、像是在偵錯工具和分析案例中使用原生映射等案例的一般準則。  

> [!TIP]
> 請務必仔細測量在啟動效能和應用程式的整體效能上使用原生映射所造成的影響，並將它們與實際需求進行比較。 雖然原生映射通常有助於改善啟動效能，但在某些情況下會減少記憶體使用量，但並非所有案例都會同樣受益。 例如，在穩定狀態執行 (也就是，一旦至少叫用應用程式所使用的所有方法一次) 之後，JIT 編譯程式所產生的所有方法，實際上會比原生映射產生稍微更好的效能。  

## <a name="using-ngenexe-in-a-development-machine"></a>在開發電腦中使用 NGen.exe  

在開發期間，.NET JIT 編譯程式會針對經常變更的程式碼，提供最佳的整體折衷。 為編譯的相依性產生原生映射（例如 EF runtime 元件）有助於加速開發和測試，方法是在每次執行的開頭剪下幾秒鐘。  

尋找 EF runtime 元件的理想位置是解決方案的 NuGet 套件位置。 例如，若是使用 EF 6.0.2 搭配 SQL Server 並以 .NET 4.5 或更新版本為目標的應用程式，您可以在命令提示字元視窗中輸入下列命令， (記得以系統管理員的身分來開啟它) ：  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> 這樣做的優點是，安裝適用于 EF provider for SQL Server 的原生映射也會預設安裝主要 EF runtime 元件的原生映射。 這是因為 NGen.exe 可以偵測到 EntityFramework.dll 是位於相同目錄中 EntityFramework.SqlServer.dll 元件的直接相依性。  

## <a name="creating-native-images-during-setup"></a>在安裝期間建立原生映射  

在安裝期間，WiX 工具組支援將 managed 元件的原生映射產生佇列，如本操作 [指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。 另一個替代方式是建立執行 NGen.exe 命令的自訂安裝工作。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>確認原生映射正在用於 EF  

您可以藉由尋找副檔名為 ".ni.dll" 或 ".ni.exe" 的已載入元件，確認特定的應用程式正在使用原生元件。 例如，EF 的主要執行時間元件的原生映射將被呼叫 EntityFramework.ni.dll。 檢查載入之 .NET 元件的簡單方法是使用 [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。  

## <a name="other-things-to-be-aware-of"></a>其他需要注意的事項  

**建立元件的原生映射不應與在 GAC 中註冊元件[ (全域組件快取) ](https://msdn.microsoft.com/library/yf1d93sz.aspx)混淆**。 NGen.exe 可以建立不在 GAC 中的元件映射，事實上，使用特定 EF 版本的多個應用程式可以共用相同的原生映射。 雖然 Windows 8 可以自動為放在 GAC 中的元件建立原生映射，但是 EF 執行時間已優化，可與您的應用程式一起部署，我們不建議您在 GAC 中註冊它，因為這會對元件解析產生負面影響，並在其他方面提供應用程式的服務。  
