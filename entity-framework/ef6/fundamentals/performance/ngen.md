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
# <a name="improving-startup-performance-with-ngen"></a>使用 NGen 的改善啟動效能
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

.NET Framework 支援的原生映像產生受管理的應用程式和程式庫做為協助更快速地啟動的應用程式的方式以及在某些情況下使用較少的記憶體。 原生映像建立的轉譯減輕.NET JIT (Just In Time) 編譯器產生的原生指示的 managed 程式碼組件包含原生機器指令，才能執行應用程式時，檔案應用程式執行階段。  

之前第 6 版，EF 執行階段的核心程式庫是.NET Framework 的一部分，並為其自動產生原生映像。 從版本 6 開始的整個 EF 執行階段已經結合到 EntityFramework NuGet 套件。 原生映像現在會產生使用 NGen.exe 命令列工具，以取得類似的結果。  

經驗的觀察值顯示的 EF 執行階段組件的原生映像可以剪下的應用程式啟動時間的 1 到 3 秒之間。  

## <a name="how-to-use-ngenexe"></a>如何使用 NGen.exe  

最基本的函式的 NGen.exe 工具是 「 安裝 」 (也就是建立來保存到磁碟) 組件及其所有直接相依性的原生映像。 以下是達成的：  

1. 系統管理員身分開啟命令提示字元 視窗  
2. 將目前的工作目錄變更至您想要產生原生映像的組件的位置：  

  ``` console
    cd <*Assemblies location*>  
  ```
3. 取決於您的作業系統和應用程式的組態，您可能需要產生原生映像適用於 32 位元架構，64 位元架構或兩者。  

    執行的 32 位元：  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    對於執行的 64 位元：
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> 產生錯誤的架構的原生映像，是很常見的錯誤。 當不確定您只可以產生適用於安裝在電腦的作業系統的所有架構的原生映像。  

NGen.exe 也支援其他功能，例如解除安裝，並顯示已安裝的原生映像，佇列產生多個映像等等。如需使用方式的詳細資訊，請參閱[NGen.exe 文件](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。  

## <a name="when-to-use-ngenexe"></a>使用 NGen.exe 的時機  

決定要產生原生映像，在 EF 6 或更新版本為基礎的應用程式中的組件說過，您應該考慮下列選項：  

- **主要 EF 執行階段組件，EntityFramework.dll**： 典型的 EF 基礎應用程式執行大量的程式碼從這個組件會在啟動或其第一次存取資料庫。 因此，建立此組件的原生映像，將會產生的最大的提升，在 啟動效能。  
- **任何應用程式所使用的提供者組件 EF**： 啟動時間也受益於稍有產生這些原生映像。 比方說，如果應用程式會使用 EF provider for SQL Server 您會想要為 EntityFramework.SqlServer.dll 產生原生映像。  
- **您的應用程式組件和其他相依性**: [NGen.exe 文件](https://msdn.microsoft.com/library/6t9t5wcf.aspx)涵蓋選擇要產生原生映像和原生映像上的安全性，影響的組件的一般準則進階選項，例如 「 硬式繫結 」 案例，例如使用偵錯和分析等案例中的原生映像。  

> [!TIP]
> 請確定您仔細衡量使用原生映像上的啟動效能和您的應用程式的整體效能的影響，並比較實際的需求。 原生映像通常有助於改善啟動效能，並在某些情況下減少記憶體使用量，而不是所有實例將同樣都獲益。 比方說，在穩定狀態下執行 （也就是說，一旦應用程式正在使用的所有方法都至少一次叫都用） JIT 編譯器所產生的程式碼可能實際上會產生稍微較佳的效能比原生映像。  

## <a name="using-ngenexe-in-a-development-machine"></a>在開發電腦中使用 NGen.exe  

在開發.NET JIT 編譯器會提供經常變更的程式碼的最佳整體權衡取捨。 產生已編譯的相依性，例如 EF 執行階段組件的原生映像，可協助加速開發和測試可藉由減少幾秒鐘的時間，在每次執行的開頭。  

若要尋找的 EF 執行階段組件的好地方是方案的 NuGet 封裝位置。 例如，使用 EF 6.0.2 搭配 SQL Server，並以.NET 4.5 或更新版本為目標的應用程式您可以輸入下列命令提示字元視窗 （請記住，若要開啟以系統管理員身分）：  

``` console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> 這會利用的事實，安裝適用於 SQL Server 的 EF 提供者的原生映像也會依預設安裝原生映像主要 EF 執行階段組件。 這是因為 NGen.exe 可以偵測出 EntityFramework.dll 的直接相依性 EntityFramework.SqlServer.dll 組件位於相同的目錄。  

## <a name="creating-native-images-during-setup"></a>在安裝期間建立原生映像  

WiX 工具組支援佇列處理產生的原生映像 managed 組件在安裝期間，在此所述[< 如何 > 指南](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)。 另一個替代方式是建立自訂安裝工作執行 NGen.exe 命令。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>驗證原生映像會用於 EF  

您可以確認特定的應用程式使用原生組件，藉由尋找延伸模組的載入組件 」。 ni.dll 「 或 」。 ni.exe"。 例如，EF 的主要執行階段組件的原生映像將會呼叫 EntityFramework.ni.dll。 檢查處理序載入的.NET 組件的簡單方法是使用[Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)。  

## <a name="other-things-to-be-aware-of"></a>要注意的其他項目  

**建立組件的原生映像不應混淆註冊中的組件[GAC （全域組件快取）](https://msdn.microsoft.com/library/yf1d93sz.aspx)**。 NGen.exe 可讓您建立的組件不在 GAC 中的映像，事實上，使用特定版本的 EF 的多個應用程式可以共用相同的原生映像。 Windows 8 可以自動建立原生映像放在 GAC 的組件，而 EF 執行階段會最佳化與您的應用程式一起部署，而我們不建議在 GAC 中註冊，這會有負面的影響，在組件解析和服務應用程式在其他方面。  
