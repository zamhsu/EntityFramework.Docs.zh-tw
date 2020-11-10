---
title: 測試使用 EF Core 的程式碼 - EF Core
description: 針對使用 Entity Framework Core 的應用程式進行測試的不同方法
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/index
ms.openlocfilehash: db25a51ec83bff15ff8c8a959a5f1707dbcf7f0f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431498"
---
# <a name="testing-code-that-uses-ef-core"></a>測試使用 EF Core 的程式碼

測試存取資料庫的程式碼需要下列其中一項：

* 對生產環境中所使用的相同資料庫系統執行查詢和更新。
* 對其他一些較容易管理的資料庫系統執行查詢和更新。
* 使用 test double 或其他一些機制來完全避免使用資料庫。

本文件概述上述每種選擇所牽涉到的取捨，並示範 EF Core 如何搭配每種方法使用。  

> [!TIP]
> 請參閱 [EF Core 測試範例](xref:core/testing/testing-sample)來取得示範這裡所介紹之概念的程式碼。

## <a name="all-database-providers-are-not-equal"></a>所有資料庫提供者都不相同

請務必了解，EF Core 的設計不是將基礎資料庫系統的每個層面抽象化。
相反地，EF Core 是一組通用的模式和概念，可搭配任何資料庫系統使用。
EF Core 資料庫提供者接著會在此通用架構上分層資料庫特定的行為和功能。
這讓每個資料庫系統得以發揮所長，同時仍可在適當的情況下，與其他資料庫系統保持共通性。

基本上，這表示交換資料庫提供者將會變更 EF Core 行為，且應用程式可能無法正常運作，除非其明確地將任何行為上的差異納入考量。
話雖如此，在許多情況下，由於關聯式資料庫之間有高度的共通性，因此這樣做沒有問題。
這有好處也有壞處。
好處是在資料庫系統之間移動可能相當容易。
壞處是如果應用程式未針對新的資料庫系統進行完整測試，可能會對安全性有所誤判。  

## <a name="approach-1-production-database-system"></a>方法 1：生產資料庫系統

如上一節所述，確定測試就是生產環境中執行內容的唯一方式，就是使用相同的資料庫系統。
例如，如果部署的應用程式使用 SQL Azure，則也應該對 SQL Azure 進行測試。

不過，讓所有開發人員對 SQL Azure 執行測試，同時積極地開發程式碼，不僅速度緩慢，也很耗費成本。
這說明這些方法所牽涉到的主要取捨：何時適合偏離生產資料庫系統，以提升測試效率？

幸運的是，在此情況下，答案相當簡單：使用本機或內部部署 SQL Server 進行開發人員測試。
SQL Azure 和 SQL Server 非常類似，因此對 SQL Server 進行測試通常是合理的取捨。
話雖如此，最好還是在進入生產環境之前，先對 SQL Azure 本身執行測試。

### <a name="localdb"></a>LocalDB

所有主要資料庫系統都有某種形式的「開發人員版本」，可用於本機測試。
SQL Server 也有稱為 [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) 的功能。
LocalDB 的主要優點在於會視需要啟動資料庫執行個體。
這可避免在電腦上執行資料庫服務，即使未正在執行測試也一樣。

但 LocalDB 也有自身的問題：

* 它無法提供 [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) 的所有支援。
* 不適用於 Linux。
* 啟動服務時，可能會造成第一個測試回合延遲。

我個人從不認為在開發電腦上執行資料庫服務有何問題，因此通常會建議改用 Developer Edition。
不過，LocalDB 可能適合某些人，特別是在功能不強大的開發電腦上。

在 Docker 容器 (或類似容器) 中[執行 SQL Server](/sql/linux/quickstart-install-connect-docker) (或任何其他資料庫系統)，是另一種能夠避免直接在開發電腦上執行資料庫系統的方式。  

## <a name="approach-2-sqlite"></a>方法 2：SQLite

EF Core 測試 SQL Server 提供者的方式，主要是透過對本機 SQL Server 執行個體執行測試。
在快速的電腦上，這些測試會在幾分鐘內執行數十萬個查詢。
這說明使用實際資料庫系統可能是高效能的解決方案。
這是個迷思，認為使用較輕量型資料庫是快速執行測試的唯一方式。

話雖如此，萬一無法對接近生產資料庫系統的系統執行測試，那該怎麼辦？
次佳選擇是使用具有類似功能的產品。
這通常表示另一個關聯式資料庫，而 [SQLite](https://sqlite.org/index.html) 是明顯的選擇。

SQLite 是不錯的選擇，因為：

* 它與應用程式同處理序執行，因此額外負荷很低。
* 使用簡單且自動為資料庫建立的檔案，因此不需要資料庫管理。
* 具有記憶體內部模式，可避免甚至是建立檔案。

不過，請記住：

* SQLite 必然無法提供生產資料庫系統的所有支援。
* 對於某些查詢，SQLite 的運作方式會不同於生產資料庫系統。

因此，如果使用 SQLite 進行一些測試，請務必同時對實際資料庫系統進行測試。

如需 EF Core 特定指導方針，請參閱[使用 SQLite 進行測試](xref:core/testing/sqlite)。

## <a name="approach-3-the-ef-core-in-memory-database"></a>方法 3：EF Core 記憶體內部資料庫

EF Core 隨附記憶體內部資料庫，可用於 EF Core 本身的內部測試。
此資料庫一般 **不適合用於測試使用 EF Core 的應用程式** 。 尤其是：

* 這不是關聯式資料庫。
* 不支援異動。
* 其無法執行原始 SQL 查詢。
* 未針對效能最佳化。

當測試 EF Core 內部時，上述幾點並不重要，因為我們會將其特別用於資料庫與測試無關的情況下。
另一方面，當測試使用 EF Core 的應用程式時，上述幾點則通常非常重要。

## <a name="unit-testing"></a>單元測試

請考慮測試可能需要使用資料庫中某些資料，但不會因此測試資料庫互動的商務邏輯。
其中一個選項是使用 [test double](https://en.wikipedia.org/wiki/Test_double)，例如 mock 或 fake。

我們使用 test double 進行 EF Core 的內部測試。
不過，我們絕對不會嘗試 mock DbContext 或 IQueryable。
這麼做很困難、繁瑣且容易出錯。
**請不要這麼做。**

相反地，對使用 DbCoNtext 的項目進行單元測試時，我們會使用 EF 記憶體內部資料庫。
在此情況下，由於測試不會受到資料庫行為影響，因此適合使用 EF 記憶體內部資料庫。
只要別用來測試實際資料庫查詢或更新即可。

[EF Core 測試範例](xref:core/testing/testing-sample)會示範使用 EF 記憶體內部資料庫，以及 SQL Server 和 SQLite 的測試。
