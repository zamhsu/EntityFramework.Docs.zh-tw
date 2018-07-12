---
title: 設計工具程式碼產生範本 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 56e00fa2-f9f0-48b3-8006-f8266ca7e74b
caps.latest.revision: 3
ms.openlocfilehash: e06dc1c35f8d74772e5c7d69b29553288fd652d0
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2018
ms.locfileid: "37911722"
---
# <a name="designer-code-generation-templates"></a>設計工具程式碼產生範本
當您使用 Entity Framework Designer 建立模型時，會自動為您產生類別和衍生的內容。 除了預設程式碼產生之外，我們也提供許多範本，可用於自訂產生的程式碼。 這些範本會以 T4 文字範本的形式提供，讓您可根據需要來自訂範本。

預設產生的程式碼，取決於您在哪個版本的 Visual Studio 中建立模型：

-   在 Visual Studio 2012 和 2013 中建立的模型，將產生簡單的 POCO 實體類別，以及簡化的 DbContext 衍生內容。
-   在 Visual Studio 2010 中建立的模型，將產生從 EntityObject 衍生的實體類別，以及從 ObjectContext 衍生的內容。
  > [!NOTE]    
  > 在您新增模型後，我們建議切換至 DbContext 產生器範本。

本頁面包含可用的範本，並提供將範本新增至模型的指示。

## <a name="available-templates"></a>可用的範本

Entity Framework 小組提供下列範本：

### <a name="dbcontext-generator"></a>DbContext 產生器

此範本將產生簡單的 POCO 實體類別，以及使用 EF6 從 DbContext 衍生的內容。
這是建議的範本，除非您有理由使用下面列出的其他範本之一。
如果您使用的是 Visual Studio 最新版本 (Visual Studio 2013 以上版本)，則此範本也是您預設會取得的程式碼產生範本：建立新模型時，預設會使用此範本，並且 T4 檔案 (.tt) 會巢狀於您的 .edmx 檔案下。

#### <a name="older-versions-of-visual-studio"></a>Visual Studio 舊版
- **Visual Studio 2012：** 若要取得 **EF 6.x DbContextGenerator** 範本，您必須安裝最新的 **Entity Framework Tools for Visual Studio** - 請參閱[取得Entity Framework](~/ef6/fundamentals/install.md) 頁面以取得詳細資訊。
- **Visual Studio 2010：****EF 6.x DbContextGenerator** 範本不適用於 Visual Studio 2010。

#### <a name="dbcontext-generator-for-ef-5x"></a>適用於 EF 5.x 的 DbContext 產生器

如果您使用較舊版本的 EntityFramework NuGet 套件 (主要版本為 5)，則必須使用 **EF 5.x DbContext 產生器**範本。

如果您使用 Visual Studio 2013 或 2012，則此範本已安裝。

如果您使用 Visual Studio 2010，則在新增範本時需要選取 [線上] 索引標籤以從 Visual Studio 組件庫下載。 或者，您可以直接從 Visual Studio 組件庫中預先安裝此範本。 由於更新版本的 Visual Studio 中已包含範本，因此組件庫中的版本只能安裝於 Visual Studio 2010。

- [EF 5.x DbContext Generator for C#](http://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2) (適用於 C# 的 EF 5.x DbContext 產生器)
- [EF 5.x DbContext Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03) (適用於 C# 網站的 EF 5.x DbContext 產生器)
- [EF 5.x DbContext Generator for VB.NET](http://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured) (適用於 VB.NET 的 EF 5.x DbContext 產生器)
- [EF 5.x DbContext Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614) (適用於 VB.NET 網站的 EF 5.x DbContext 產生器)

#### <a name="dbcontext-generator-for-ef-4x"></a>適用於 EF 4.x 的 DbContext 產生器

如果您使用較舊版本的 EntityFramework NuGet 套件 (主要版本為 4)，則必須使用 **EF 4.x DbContext 產生器**範本。 新增範本時，您可在 [線上] 索引標籤中找到此範本，或者您也可以直接從 Visual Studio 組件庫中預先安裝此範本。

- [EF 4.x DbContext Generator for C#](http://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2) (適用於 C# 的 EF 4.x DbContext 產生器)
- [EF 4.x DbContext Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e) (適用於 C# 網站的 EF 4.x DbContext 產生器)
- [EF 4.x DbContext Generator for VB.NET](http://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073) (適用於 VB.NET 的 EF 4.x DbContext 產生器)
- [EF 4.x DbContext Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92) (適用於 VB.NET 網站的 EF 4.x DbContext 產生器)

### <a name="entityobject-generator"></a>EntityObject 產生器

此範本將產生從 EntityObject 衍生的實體類別，以及從 ObjectContext 衍生的內容。

> [!NOTE]
> 考慮使用 DbContext 產生器

DbContext 產生器現在是新應用程式的建議範本。 DbContext 產生器會利用更簡單的 DbContext API。 EntityObject 產生器仍然可用來支援現有應用程式。

**Visual Studio 2010, 2012 &amp; 2013**

在您新增範本時需要選取 [線上] 索引標籤以從 Visual Studio 組件庫下載。 或者，您可以直接從 Visual Studio 組件庫中預先安裝此範本。

- [EF 6.x EntityObject Generator for C#](http://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a) (適用於 C# 的 EF 6.x EntityObject 產生器)
- [EF 6.x EntityObject Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996) (適用於 C# 網站的 EF 6.x EntityObject 產生器)
- [EF 6.x EntityObject Generator for VB.NET](http://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea) (適用於 VB.NET 的 EF 6.x EntityObject 產生器)
- [EF 6.x EntityObject Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a) (適用於 VB.NET 網站的 EF 6.x EntityObject 產生器)

**EntityObject Generator for EF 5.x** (適用於 EF 5.x 的 EntityObject 產生器)


如果您使用 Visual Studio 2012 或 2013，則在新增範本時需要選取 [線上] 索引標籤以從 Visual Studio 組件庫下載。 或者，您可以直接從 Visual Studio 組件庫中預先安裝此範本。 由於 Visual Studio 2010 中已包含範本，因此組件庫中的版本只能安裝於 Visual Studio 2012 &amp; 2013。

- [EF 5.x EntityObject Generator for C#](http://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339) (適用於 C# 的 EF 5.x EntityObject 產生器)
- [EF 5.x EntityObject Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [適用於 VB.NET 的 EF 5.x EntityObject 產生器](http://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30) (適用於 C# 網站的 EF 5.x EntityObject 產生器)
- [EF 5.x EntityObject Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41) (適用於 VB.NET 網站的 EF 5.x EntityObject 產生器)

如果您只希望產生 ObjectContext 程式碼而無需編輯範本，則可以[還原為 EntityObject 程式碼產生](~/ef6/modeling/designer/codegen/legacy-objectcontext.md)。

如果您使用 Visual Studio 2010，則此範本已安裝。 如果您在 Visual Studio 2010 中建立新模型，則預設會使用此範本，但 .tt 檔案不包含在您的專案中。 如果您希望自訂範本，則您必須將其新增至您的專案。

### <a name="self-tracking-entities-ste-generator"></a>自我追蹤實體 (STE) 產生器

此範本將產生自我追蹤實體類別，以及從 ObjectContext 衍生的內容。 在 EF 應用程式中，內容負責追蹤實體中的變更。 不過，在多層式架構 (N-Tier) 案例中，內容可能無法在修改實體的層上使用。 自我追蹤實體可協助您追蹤任何層中的變更。 如需詳細資訊，請參閱[自我追蹤實體](~/ef6/fundamentals/disconnected-entities/self-tracking-entities/index.md)。

> [!NOTE]
> 不建議使用 STE 範本

我們不再建議在新應用程式中使用 STE 範本，但此範本會繼續支援現有應用程式。 請瀏覽[已中斷連線的實體文章](~/ef6/fundamentals/disconnected-entities/index.md)，了解我們為多層式架構 (N-Tier) 案例推薦的其他選項。

> [!NOTE]
> 沒有 EF 6.x 版本的 STE 範本。


> [!NOTE]
> 沒有 Visual Studio 2013 版本的 STE 範本。

### <a name="visual-studio-2012"></a>Visual Studio 2012

如果您使用 Visual Studio 2012，則在新增範本時需要選取 [線上] 索引標籤以從 Visual Studio 組件庫下載。 或者，您可以直接從 Visual Studio 組件庫中預先安裝此範本。 由於 Visual Studio 2010 中已包含範本，因此組件庫中的版本只能安裝於 Visual Studio 2012。

- [EF 5.x STE Generator for C#](http://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f) (適用於 C# 的 EF 5.x STE 產生器)
- [EF 5.x STE Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c) (適用於 C# 網站的 EF 5.x STE 產生器)
- [EF 5.x STE Generator for VB.NET](http://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b) (適用於 VB.NET 的 EF 5.x STE 產生器)
- [EF 5.x STE Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e) (適用於 VB.NET 網站的 EF 5.x STE 產生器)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

如果您使用 Visual Studio 2010，則此範本已安裝。

### <a name="poco-entity-generator"></a>POCO 實體產生器

此範本將產生 POCO 實體類別，以及從 ObjectContext 衍生的內容

> [!NOTE]
> 考慮使用 DbContext 產生器

DbContext 產生器現在是用於在新應用程式中產生 POCO 類別的建議範本。 DbContext 產生器會利用新的 DbContext API，並可產生更簡單的 POCO 類別。 POCO 實體產生器仍然可用來支援現有應用程式。

> [!NOTE]
> 沒有 EF 5.x 或 EF 6.x 版本的 STE 範本。

> [!NOTE]
> 沒有 Visual Studio 2013 版本的 POCO 範本。

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 &amp; Visual Studio 2010

在您新增範本時需要選取 [線上] 索引標籤以從 Visual Studio 組件庫下載。 或者，您可以直接從 Visual Studio 組件庫中預先安裝此範本。

- [EF 4.x POCO Generator for C#](http://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313) (適用於 C# 的 EF 4.x POCO 產生器)
- [EF 4.x POCO Generator for C# Web Sites](http://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f) (適用於 C# 網站的 EF 4.x POCO 產生器)
- [EF 4.x POCO Generator for VB.NET](http://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752) (適用於 VB.NET 的 EF 4.x POCO 產生器)
- [EF 4.x POCO Generator for VB.NET Web Sites](http://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34) (適用於 VB.NET 網站的 EF 4.x POCO 產生器)

### <a name="what-are-the-web-sites-templates"></a>什麼是「網站」範本

「網站」範本 (即 **適用於 C\# 網站的 EF 5.x DbContext 產生器**) 用於透過 [檔案] -&gt; [新增] -&gt; [網站...] 建立的網站專案中。這些與透過 [檔案] -&gt; [新增] -&gt; [專案...] 建立的 Web 應用程式不同，後者使用標準範本。 我們提供不同的範本，因為 Visual Studio 中的項目範本系統需要。

## <a name="using-a-template"></a>使用範本

若要開始使用程式碼產生範本，請以滑鼠右鍵按一下 EF Designer 中設計介面上的空白點，然後選取 [新增程式碼產生項目...]。

![Add_Code_Gen_Item](~/ef6/media/add-code-gen-item.png)

如果您已經安裝要使用的範本 (或者已包含在 Visual Studio 中)，則範本將在左側功能表的 [程式碼] 或 [資料] 區段下可用。

![已安裝](~/ef6/media/installed.png)

如果您尚未安裝範本，請從左側功能表中選取 [線上]，然後搜尋所需的範本。

![搜尋](~/ef6/media/search.png) 

如果您使用 Visual Studio 2012，則新的 .tt 檔案將巢狀於 .edmx 檔案下。*

> [!NOTE]
> 對於在 Visual Studio 2012 中建立的模型，您將需要刪除用於預設程式碼產生的範本，否則您將產生重複的類別和內容。 預設檔案為 **&lt;模型名稱&gt;.tt** 和 **&lt;模型名稱&gt;.context.tt**。 

![VS2012_Templates](~/ef6/media/vs2012-templates.png)

如果您使用 Visual Studio 2010，tt 檔案會直接新增至您的專案。  

![VS2010_Templates](~/ef6/media/vs2010-templates.png)
