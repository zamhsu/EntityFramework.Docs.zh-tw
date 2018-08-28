---
title: 升級至 Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 8317fc0dcd5a7b176f3100e449bc6ff708bd310f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997367"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="e66f0-102">升級至 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="e66f0-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="e66f0-103">在舊版的 EF 的程式碼是核心程式庫 (主要 System.Data.Entity.dll 中) 隨附於.NET Framework 與頻外 (OOB) 程式庫 (主要 EntityFramework.dll) 隨附於 NuGet 套件之間有分割。</span><span class="sxs-lookup"><span data-stu-id="e66f0-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="e66f0-104">EF6 會從核心程式庫的程式碼，並納入 OOB 程式庫。</span><span class="sxs-lookup"><span data-stu-id="e66f0-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="e66f0-105">若要允許進行開放原始碼的 EF 必須進行此動作，讓它成為能夠發展以不同的步調，從.NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e66f0-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="e66f0-106">結果就是應用程式必須重建針對移動的型別。</span><span class="sxs-lookup"><span data-stu-id="e66f0-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="e66f0-107">這應該是直接使用 DbContext 為隨附於 EF 4.1 和更新版本的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e66f0-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="e66f0-108">多一點的工作是為了利用 ObjectContext 的應用程式，但它仍不難做到。</span><span class="sxs-lookup"><span data-stu-id="e66f0-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="e66f0-109">以下是您要升級現有 ef6 應用程式所做的事情的檢查清單。</span><span class="sxs-lookup"><span data-stu-id="e66f0-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="e66f0-110">1.安裝 EF6 的 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="e66f0-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="e66f0-111">您要升級至新的 Entity Framework 6 執行階段。</span><span class="sxs-lookup"><span data-stu-id="e66f0-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="e66f0-112">以滑鼠右鍵按一下專案，然後選取**管理 NuGet 套件...**</span><span class="sxs-lookup"><span data-stu-id="e66f0-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="e66f0-113">底下**線上**索引標籤上選取**EntityFramework**按一下**安裝**</span><span class="sxs-lookup"><span data-stu-id="e66f0-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="e66f0-114">如果舊版 EntityFramework NuGet 套件已安裝這會將它升級到 EF6。</span><span class="sxs-lookup"><span data-stu-id="e66f0-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="e66f0-115">或者，您也可以從套件管理員主控台中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="e66f0-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="e66f0-116">2.請確定已移除 System.Data.Entity.dll 的組件參考</span><span class="sxs-lookup"><span data-stu-id="e66f0-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="e66f0-117">安裝 EF6 的 NuGet 套件應該會自動會為您移除任何參考 System.Data.Entity 從您的專案。</span><span class="sxs-lookup"><span data-stu-id="e66f0-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="e66f0-118">3.交換使用 EF 6.x 的程式碼產生的任何 EF 設計工具 (EDMX) 模型</span><span class="sxs-lookup"><span data-stu-id="e66f0-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="e66f0-119">如果您有使用 EF 設計工具建立的任何模型，您必須更新程式碼產生範本，來產生 EF6 相容的程式碼。</span><span class="sxs-lookup"><span data-stu-id="e66f0-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="e66f0-120">目前沒有只有 EF 6.x DbContext 產生器的範本適用於 Visual Studio 2012 和 2013年。</span><span class="sxs-lookup"><span data-stu-id="e66f0-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="e66f0-121">刪除現有的程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="e66f0-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="e66f0-122">這些檔案通常會命名為 **\<edmx_file_name\>.tt**並 **\<edmx_file_name\>。Context.tt**和您的 edmx 檔案，在 [方案總管] 下進行巢狀。</span><span class="sxs-lookup"><span data-stu-id="e66f0-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="e66f0-123">您可以選取範本方案總管，然後按下**Del**鍵來刪除它們。</span><span class="sxs-lookup"><span data-stu-id="e66f0-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="e66f0-124">在網站專案範本將不下您的 edmx 檔案，巢狀但與它一起列在 [方案總管]。</span><span class="sxs-lookup"><span data-stu-id="e66f0-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="e66f0-125">在 VB.NET 專案中，您必須啟用 顯示所有檔案 ' 能夠查看巢狀的範本的檔案。</span><span class="sxs-lookup"><span data-stu-id="e66f0-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="e66f0-126">新增適當的 EF 6.x 的程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="e66f0-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="e66f0-127">開啟您的模型，在 EF 設計工具中，以滑鼠右鍵按一下設計介面，並選取**加入的程式碼產生項目...**</span><span class="sxs-lookup"><span data-stu-id="e66f0-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="e66f0-128">如果您使用 DbContext API （建議選項） 再**EF 6.x DbContext Generator**會位在**資料** 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="e66f0-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="e66f0-129">如果您使用 Visual Studio 2012，您必須安裝 EF 6 工具，將此範本。</span><span class="sxs-lookup"><span data-stu-id="e66f0-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="e66f0-130">請參閱[取得 Entity Framework](~/ef6/fundamentals/install.md)如需詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="e66f0-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="e66f0-131">如果您使用 ObjectContext API，則您必須選取**線上**索引標籤，然後搜尋**EF 6.x EntityObject Generator**。</span><span class="sxs-lookup"><span data-stu-id="e66f0-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="e66f0-132">如果您的程式碼產生範本，您必須將它們重新套用至已更新範本以套用任何自訂項目。</span><span class="sxs-lookup"><span data-stu-id="e66f0-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="e66f0-133">4.更新正在使用的任何 EF core 型別命名空間</span><span class="sxs-lookup"><span data-stu-id="e66f0-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="e66f0-134">DbContext 和第一個程式碼類型的命名空間都沒有變更。</span><span class="sxs-lookup"><span data-stu-id="e66f0-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="e66f0-135">這表示對於許多應用程式使用 EF 4.1 或更新版本您不需要變更任何項目。</span><span class="sxs-lookup"><span data-stu-id="e66f0-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="e66f0-136">型別 ObjectContext 如先前在 System.Data.Entity.dll 中都已移至新的命名空間中。</span><span class="sxs-lookup"><span data-stu-id="e66f0-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="e66f0-137">這表示您可能需要更新您*使用*或是*匯入*指示詞，針對 EF6 進行建置。</span><span class="sxs-lookup"><span data-stu-id="e66f0-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="e66f0-138">命名空間變更的一般規則是 System.Data.* 中的任何型別會移至 System.Data.Entity.Core.*。</span><span class="sxs-lookup"><span data-stu-id="e66f0-138">The general rule for namespace changes is that any type in System.Data.* is moved to System.Data.Entity.Core.*.</span></span> <span data-ttu-id="e66f0-139">換句話說，只需要插入**Entity.Core。**</span><span class="sxs-lookup"><span data-stu-id="e66f0-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="e66f0-140">之後 System.Data。</span><span class="sxs-lookup"><span data-stu-id="e66f0-140">after System.Data.</span></span> <span data-ttu-id="e66f0-141">例如: </span><span class="sxs-lookup"><span data-stu-id="e66f0-141">For example:</span></span>

- <span data-ttu-id="e66f0-142">System.Data.EntityException = > System.Data。**Entity.Core。** EntityException</span><span class="sxs-lookup"><span data-stu-id="e66f0-142">System.Data.EntityException => System.Data.**Entity.Core.** EntityException</span></span>  
- <span data-ttu-id="e66f0-143">System.Data.Objects.ObjectContext = > System.Data。**Entity.Core。** Objects.ObjectContext</span><span class="sxs-lookup"><span data-stu-id="e66f0-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core.** Objects.ObjectContext</span></span>  
- <span data-ttu-id="e66f0-144">System.Data.Objects.DataClasses.RelationshipManager = > System.Data。**Entity.Core。** Objects.DataClasses.RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="e66f0-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core.** Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="e66f0-145">這些型別是在*Core*命名空間因為不會針對大部分的 DbContext 應用程式的直接用。</span><span class="sxs-lookup"><span data-stu-id="e66f0-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="e66f0-146">屬於 System.Data.Entity.dll 某些類型仍用於通常和直接 DbContext 為基礎的應用程式，並因此不已移到*Core*命名空間。</span><span class="sxs-lookup"><span data-stu-id="e66f0-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="e66f0-147">這些是：</span><span class="sxs-lookup"><span data-stu-id="e66f0-147">These are:</span></span>

- <span data-ttu-id="e66f0-148">System.Data.EntityState = > System.Data。**實體。** EntityState</span><span class="sxs-lookup"><span data-stu-id="e66f0-148">System.Data.EntityState => System.Data.**Entity.** EntityState</span></span>  
- <span data-ttu-id="e66f0-149">System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data。**Entity.DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="e66f0-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="e66f0-150">這個類別已重新命名;使用舊名稱的類別仍然存在，且可運作，但它現在會標示為過時。</span><span class="sxs-lookup"><span data-stu-id="e66f0-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="e66f0-151">System.Data.Objects.EntityFunctions = > System.Data。**Entity.DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="e66f0-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="e66f0-152">這個類別已重新命名;使用舊名稱的類別仍然存在，且可運作，但它現在會標示為已過時）。</span><span class="sxs-lookup"><span data-stu-id="e66f0-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="e66f0-153">空間的類別 （例如 DbGeography、 DbGeometry） 已從 System.Data.Spatial = > System.Data。**實體。** 空間</span><span class="sxs-lookup"><span data-stu-id="e66f0-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity.** Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="e66f0-154">System.Data 命名空間中的某些類型是在 System.Data.dll 中不是 EF 組件。</span><span class="sxs-lookup"><span data-stu-id="e66f0-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="e66f0-155">未將這些型別，因此其命名空間保持不變。</span><span class="sxs-lookup"><span data-stu-id="e66f0-155">These types have not moved and so their namespaces remain unchanged.</span></span>
