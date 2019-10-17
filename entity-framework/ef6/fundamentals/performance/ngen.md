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
# <a name="improving-startup-performance-with-ngen"></a>使用 NGen 改善啟動效能
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

.NET Framework 支援針對受控應用程式和程式庫產生原生映射，以協助應用程式的啟動速度更快，而且在某些情況下會使用較少的記憶體。 原生映射的建立方式是將 managed 程式碼元件轉譯為包含原生電腦指令的檔案，然後再執行應用程式，以避免 .NET JIT （及時）編譯器必須產生原生指令應用程式執行時間。  

在第6版之前，EF 執行時間的核心程式庫是 .NET Framework 的一部分，而且會為它們自動產生原生映射。 從第6版開始，已將整個 EF 執行時間合併為 EntityFramework NuGet 套件。 現在必須使用 Ngen.exe 命令列工具產生原生映射，以取得類似的結果。  

經驗觀察顯示 EF runtime 元件的原生映射可以在應用程式啟動時間的1到3秒之間剪下。  

## <a name="how-to-use-ngenexe"></a>如何使用 Ngen.exe  

Ngen.exe 工具的最基本功能是針對元件及其所有直接相依性，「安裝」（也就是建立和保存至磁片）原生映射。 以下是您可以達成此目標的方式：  

1. 以系統管理員身分開啟 [命令提示字元] 視窗。
2. 將目前的工作目錄變更為您想要產生原生映射的元件位置：

   ``` console
   cd <*Assemblies location*>  
   ```

3. 視您的作業系統和應用程式的設定而定，您可能需要產生32位架構、64位架構或兩者的原生映射。

   若為32位執行：

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   若為64位執行：
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> 產生錯誤架構的原生映射是很常見的錯誤。 若不確定，您可以只針對所有適用于電腦上所安裝作業系統的架構產生原生映射。  

Ngen.exe 也支援其他功能，例如卸載和顯示已安裝的原生映射、將多個映射的產生排入佇列等等。如需使用方式的詳細資訊，請參閱[ngen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。  

## <a name="when-to-use-ngenexe"></a>使用 Ngen.exe 的時機  

當決定要在以 EF 6 版或更新版本為基礎的應用程式中產生原生映射的元件時，您應該考慮下列選項：  

- **主要 ef 執行時間元件（EntityFramework）** ：一般 ef 型應用程式會在啟動時或其第一次存取資料庫時，從這個元件執行大量的程式碼。 因此，建立此元件的原生映射將會產生最大的啟動效能提升。  
- **您的應用程式所使用的任何 EF 提供者元件**：啟動時間也會稍微受益于產生這些的原生映射。 例如，如果應用程式使用 SQL Server 的 EF 提供者，您會想要為 EntityFramework 產生原生映射。  
- **您應用程式的元件和其他**相依性： [ngen.exe 檔](https://msdn.microsoft.com/library/6t9t5wcf.aspx)涵蓋選擇要產生原生映射之元件的一般準則，以及原生映射對安全性的影響，例如「hard系結」，例如在偵錯工具和分析案例中使用原生影像等案例。  

> [!TIP]
> 請確定您仔細測量在應用程式的啟動效能和整體效能上使用原生映射的影響，並將它們與實際需求做比較。 雖然原生映射通常有助於改善啟動效能，而且在某些情況下會減少記憶體使用量，並非所有案例都能同樣受益。 比方說，在穩定狀態執行時（亦即，一旦應用程式使用的所有方法都叫用至少一次），JIT 編譯程式所產生的程式碼實際上可能會比原生映射產生稍微好一點的效能。  

## <a name="using-ngenexe-in-a-development-machine"></a>在開發電腦中使用 Ngen.exe  

在開發期間，.NET JIT 編譯程式會針對經常變更的程式碼提供最佳的整體取捨。 針對編譯的相依性（例如 EF 執行時間元件）產生原生映射有助於加速開發和測試，方法是在每次執行開始時減少幾秒鐘。  

尋找 EF 執行時間元件的好地方是解決方案的 NuGet 套件位置。 例如，針對使用 EF 6.0.2 搭配 SQL Server 並以 .NET 4.5 或更高版本為目標的應用程式，您可以在 [命令提示字元] 視窗中輸入下列內容（請務必以系統管理員的身分開啟）：  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> 這會利用安裝 EF provider for SQL Server 原生映射的事實，也會預設安裝主要 EF 執行時間元件的原生映射。 這是因為 Ngen.exe 可以偵測到 EntityFramework 是位於相同目錄中的 EntityFramework 的直接相依性。  

## <a name="creating-native-images-during-setup"></a>在安裝期間建立原生映射  

WiX 工具組支援在安裝期間，針對 managed 元件的原生映射產生佇列，如本操作[指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。 另一個替代方式是建立執行 Ngen.exe 命令的自訂安裝工作。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>確認原生映射正用於 EF  

您可以藉由尋找副檔名為 ". ni .dll" 或 ".exe" 的載入元件，驗證特定應用程式是否使用原生元件。 例如，EF 主要執行時間元件的原生映射將稱為 EntityFramework。 檢查處理程式載入的 .NET 元件的簡單方法是使用[Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。  

## <a name="other-things-to-be-aware-of"></a>其他要注意的事項  

**建立元件的原生映射時，不應該與在 GAC 中註冊元件[（全域組件快取）](https://msdn.microsoft.com/library/yf1d93sz.aspx)混淆**。 Ngen.exe 允許建立不在 GAC 中的元件影像，事實上，使用特定 EF 版本的多個應用程式可以共用相同的原生映射。 雖然 Windows 8 可以自動建立放在 GAC 中之元件的原生映射，但 EF 執行時間已優化，可與您的應用程式一起部署，而且我們不建議在 GAC 中註冊它，因為這對元件解析有負面影響，而且在其他方面提供您的應用程式。  
