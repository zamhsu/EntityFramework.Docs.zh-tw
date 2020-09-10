---
title: 升級至 Entity Framework 6-EF6
description: 升級至 Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: b27504e3b18c668a1c41142b9cd5e697944cb5fa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619702"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="9838c-103">升級至 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="9838c-103">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="9838c-104">在舊版的 EF 中，程式碼是在核心程式庫 (，主要是在 .NET Framework 和頻外 (OOB) 程式庫中隨附的 System.Data.Entity.dll) ， (主要 EntityFramework.dll NuGet 套件中所附的) 。</span><span class="sxs-lookup"><span data-stu-id="9838c-104">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="9838c-105">EF6 會從核心程式庫取得程式碼，並將其併入 OOB 程式庫中。</span><span class="sxs-lookup"><span data-stu-id="9838c-105">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="9838c-106">這是必要的，以便讓 EF 成為開放原始碼，讓它能夠以不同于 .NET Framework 的步調演進。</span><span class="sxs-lookup"><span data-stu-id="9838c-106">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="9838c-107">結果是必須針對已移動的類型重建應用程式。</span><span class="sxs-lookup"><span data-stu-id="9838c-107">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="9838c-108">如果應用程式使用 EF 4.1 和更新版本隨附的 DbCoNtext，這應該是很簡單的。</span><span class="sxs-lookup"><span data-stu-id="9838c-108">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="9838c-109">使用 ObjectCoNtext 的應用程式需要更多工作，但它仍然不難進行。</span><span class="sxs-lookup"><span data-stu-id="9838c-109">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="9838c-110">以下是將現有應用程式升級至 EF6 所需的事項檢查清單。</span><span class="sxs-lookup"><span data-stu-id="9838c-110">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="9838c-111">1. 安裝 EF6 NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="9838c-111">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="9838c-112">您必須升級到新的 Entity Framework 6 執行時間。</span><span class="sxs-lookup"><span data-stu-id="9838c-112">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="9838c-113">以滑鼠右鍵按一下您的專案，然後選取 [**管理 NuGet 套件 ...** ]</span><span class="sxs-lookup"><span data-stu-id="9838c-113">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="9838c-114">在 [**線上**] 索引標籤下選取**EntityFramework** ，然後按一下 [**安裝**]。</span><span class="sxs-lookup"><span data-stu-id="9838c-114">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="9838c-115">如果已安裝舊版的 EntityFramework NuGet 套件，則會將它升級至 EF6。</span><span class="sxs-lookup"><span data-stu-id="9838c-115">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="9838c-116">或者，您也可以從封裝管理員主控台執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9838c-116">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="9838c-117">2. 確定已移除 System.Data.Entity.dll 的元件參考</span><span class="sxs-lookup"><span data-stu-id="9838c-117">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="9838c-118">安裝 EF6 NuGet 套件應該會自動從您的專案移除任何對 system.string 的參考。</span><span class="sxs-lookup"><span data-stu-id="9838c-118">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="9838c-119">3. 將任何 EF 設計工具 (EDMX) 模型交換為使用 EF 6.x 程式碼產生</span><span class="sxs-lookup"><span data-stu-id="9838c-119">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="9838c-120">如果您有使用 EF 設計工具建立的任何模型，您將需要更新程式碼產生範本，以產生 EF6 相容的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9838c-120">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="9838c-121">目前只有 EF 6.x DbCoNtext 產生器範本適用于 Visual Studio 2012 和2013。</span><span class="sxs-lookup"><span data-stu-id="9838c-121">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="9838c-122">刪除現有的程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="9838c-122">Delete existing code-generation templates.</span></span> <span data-ttu-id="9838c-123">這些檔案通常會命名為\*\* \<edmx_file_name\> tt**和** \<edmx_file_name\> 。CoNtext.tt\*\* ，並將其內嵌在方案總管的 edmx 檔案底下。</span><span class="sxs-lookup"><span data-stu-id="9838c-123">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="9838c-124">您可以在方案總管中選取範本，然後按下 **Del** 鍵將它們刪除。</span><span class="sxs-lookup"><span data-stu-id="9838c-124">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="9838c-125">在網站專案中，範本不會在您的 edmx 檔案下進行嵌套，而是在方案總管中與它一起列出。</span><span class="sxs-lookup"><span data-stu-id="9838c-125">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="9838c-126">在 VB.NET 專案中，您必須啟用 [顯示所有檔案]，才能看到嵌套的範本檔案。</span><span class="sxs-lookup"><span data-stu-id="9838c-126">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="9838c-127">新增適當的 EF 6.x 程式碼產生範本。</span><span class="sxs-lookup"><span data-stu-id="9838c-127">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="9838c-128">在 EF 設計工具中開啟您的模型，以滑鼠右鍵按一下設計介面，然後選取 [**加入程式碼產生專案 ...** ]</span><span class="sxs-lookup"><span data-stu-id="9838c-128">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="9838c-129">如果您使用 DbCoNtext API (建議) 則會在 [**資料**] 索引標籤下提供**EF 6.x DbCoNtext**產生器。</span><span class="sxs-lookup"><span data-stu-id="9838c-129">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="9838c-130">如果您使用 Visual Studio 2012，則必須安裝 EF 6 工具才能使用此範本。</span><span class="sxs-lookup"><span data-stu-id="9838c-130">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="9838c-131">如需詳細資訊，請參閱 [Get Entity Framework](xref:ef6/fundamentals/install) 。</span><span class="sxs-lookup"><span data-stu-id="9838c-131">See [Get Entity Framework](xref:ef6/fundamentals/install) for details.</span></span>  

    - <span data-ttu-id="9838c-132">如果您使用 ObjectCoNtext API，則必須選取 [ **線上** ] 索引標籤，並搜尋 **EF 6.x EntityObject**產生器。</span><span class="sxs-lookup"><span data-stu-id="9838c-132">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="9838c-133">如果您將任何自訂套用至程式碼產生範本，則必須將它們重新套用至更新的範本。</span><span class="sxs-lookup"><span data-stu-id="9838c-133">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="9838c-134">4. 更新所使用之任何核心 EF 類型的命名空間</span><span class="sxs-lookup"><span data-stu-id="9838c-134">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="9838c-135">DbCoNtext 和 Code First 類型的命名空間尚未變更。</span><span class="sxs-lookup"><span data-stu-id="9838c-135">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="9838c-136">這表示使用 EF 4.1 或更新版本的許多應用程式都不需要變更任何變更。</span><span class="sxs-lookup"><span data-stu-id="9838c-136">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="9838c-137">先前在 System.Data.Entity.dll 中的類型（例如 ObjectCoNtext）已移至新的命名空間。</span><span class="sxs-lookup"><span data-stu-id="9838c-137">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="9838c-138">這表示您可能需要更新 *使用* 或匯 *入* 指示詞，以針對 EF6 進行建立。</span><span class="sxs-lookup"><span data-stu-id="9838c-138">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="9838c-139">命名空間變更的一般規則是，系統中的任何類型都會移至 system.string. \*。</span><span class="sxs-lookup"><span data-stu-id="9838c-139">The general rule for namespace changes is that any type in System.Data.\* is moved to System.Data.Entity.Core.\*.</span></span> <span data-ttu-id="9838c-140">換句話說，只需要插入**Entity Core** 。</span><span class="sxs-lookup"><span data-stu-id="9838c-140">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="9838c-141">在 System. 資料之後。</span><span class="sxs-lookup"><span data-stu-id="9838c-141">after System.Data.</span></span> <span data-ttu-id="9838c-142">例如：</span><span class="sxs-lookup"><span data-stu-id="9838c-142">For example:</span></span>

- <span data-ttu-id="9838c-143">EntityException => System.object。**Core**。EntityException</span><span class="sxs-lookup"><span data-stu-id="9838c-143">System.Data.EntityException => System.Data.**Entity.Core**.EntityException</span></span>  
- <span data-ttu-id="9838c-144">System.string => 的 System.object。**Core**。物件. ObjectCoNtext</span><span class="sxs-lookup"><span data-stu-id="9838c-144">System.Data.Objects.ObjectContext => System.Data.**Entity.Core**.Objects.ObjectContext</span></span>  
- <span data-ttu-id="9838c-145">Dataclasses.dll. RelationshipManager => 的 System.object。**Core**。Dataclasses.dll. RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="9838c-145">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="9838c-146">這些類型是在 *核心* 命名空間中，因為它們不會直接用於大部分以 DbCoNtext 為基礎的應用程式。</span><span class="sxs-lookup"><span data-stu-id="9838c-146">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="9838c-147">某些屬於 System.Data.Entity.dll 一部分的型別仍然經常用於 DbCoNtext 型應用程式，所以尚未移至 *核心* 命名空間。</span><span class="sxs-lookup"><span data-stu-id="9838c-147">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="9838c-148">這些警告是：</span><span class="sxs-lookup"><span data-stu-id="9838c-148">These are:</span></span>

- <span data-ttu-id="9838c-149">EntityState => System.object。**實體**。EntityState</span><span class="sxs-lookup"><span data-stu-id="9838c-149">System.Data.EntityState => System.Data.**Entity**.EntityState</span></span>  
- <span data-ttu-id="9838c-150">Dataclasses.dll. EdmFunctionAttribute => 的 System.object。**DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="9838c-150">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="9838c-151">此類別已重新命名;舊名稱的類別仍然存在且正常運作，但現在已標示為過時。</span><span class="sxs-lookup"><span data-stu-id="9838c-151">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="9838c-152">EntityFunctions => 的 System.object。**DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="9838c-152">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="9838c-153">此類別已重新命名;舊名稱的類別仍然存在且正常運作，但現在已標示為過時。 ) </span><span class="sxs-lookup"><span data-stu-id="9838c-153">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="9838c-154">空間類別 (例如，DbGeography、DbGeometry) 已從移動。空間 => 的資料。**實體**。空間</span><span class="sxs-lookup"><span data-stu-id="9838c-154">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity**.Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="9838c-155">System. Data 命名空間中的某些類型不是 EF 元件 System.Data.dll。</span><span class="sxs-lookup"><span data-stu-id="9838c-155">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="9838c-156">這些類型尚未移動，因此其命名空間會保持不變。</span><span class="sxs-lookup"><span data-stu-id="9838c-156">These types have not moved and so their namespaces remain unchanged.</span></span>
