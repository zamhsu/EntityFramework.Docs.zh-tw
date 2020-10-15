---
title: 在小組環境中進行遷移-EF Core
description: 在 team 環境中使用 Entity Framework Core 管理遷移和解決衝突的最佳作法
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062499"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="726ef-103">在小組環境中移轉</span><span class="sxs-lookup"><span data-stu-id="726ef-103">Migrations in Team Environments</span></span>

<span data-ttu-id="726ef-104">在小組環境中使用遷移時，請特別注意模型快照集檔案。</span><span class="sxs-lookup"><span data-stu-id="726ef-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="726ef-105">這個檔案可以告訴您，您的組員遷移是否與您的遷移完全相同，或者，如果您需要在共用之前重新建立您的遷移，以解決衝突。</span><span class="sxs-lookup"><span data-stu-id="726ef-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="726ef-106">合併</span><span class="sxs-lookup"><span data-stu-id="726ef-106">Merging</span></span>

<span data-ttu-id="726ef-107">當您合併小組成員時，您可能會在模型快照集檔案中發生衝突。</span><span class="sxs-lookup"><span data-stu-id="726ef-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="726ef-108">如果這兩個變更都不相關，則合併是很簡單的，而且這兩個遷移可以並存。</span><span class="sxs-lookup"><span data-stu-id="726ef-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="726ef-109">例如，您可能會在如下所示的 customer 實體類型設定中出現合併衝突：</span><span class="sxs-lookup"><span data-stu-id="726ef-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="726ef-110">因為這兩個屬性都必須存在於最終模型中，所以請加入這兩個屬性來完成合併。</span><span class="sxs-lookup"><span data-stu-id="726ef-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="726ef-111">在許多情況下，您的版本控制系統可能會自動為您合併這類變更。</span><span class="sxs-lookup"><span data-stu-id="726ef-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="726ef-112">在這些情況下，您的遷移和組員的遷移彼此獨立。</span><span class="sxs-lookup"><span data-stu-id="726ef-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="726ef-113">由於您可以先套用其中一種，因此在與小組共用之前，您不需要對您的遷移進行任何額外的變更。</span><span class="sxs-lookup"><span data-stu-id="726ef-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="726ef-114">解決衝突</span><span class="sxs-lookup"><span data-stu-id="726ef-114">Resolving conflicts</span></span>

<span data-ttu-id="726ef-115">當您合併模型快照模型時，有時會遇到真正的衝突。</span><span class="sxs-lookup"><span data-stu-id="726ef-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="726ef-116">例如，您和您的組員可能會將相同的屬性重新命名。</span><span class="sxs-lookup"><span data-stu-id="726ef-116">For example, you and your teammate may each have renamed the same property.</span></span>

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="726ef-117">如果您遇到這類衝突，請重新建立您的遷移來解決此問題。</span><span class="sxs-lookup"><span data-stu-id="726ef-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="726ef-118">請遵循下列步驟：</span><span class="sxs-lookup"><span data-stu-id="726ef-118">Follow these steps:</span></span>

1. <span data-ttu-id="726ef-119">在合併之前中止合併並回復至您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="726ef-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="726ef-120">移除您的遷移 (但保持您的模型變更) </span><span class="sxs-lookup"><span data-stu-id="726ef-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="726ef-121">將您的組員變更合併到您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="726ef-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="726ef-122">重新新增您的遷移</span><span class="sxs-lookup"><span data-stu-id="726ef-122">Re-add your migration</span></span>

<span data-ttu-id="726ef-123">這麼做之後，就可以依正確順序套用這兩個遷移。</span><span class="sxs-lookup"><span data-stu-id="726ef-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="726ef-124">首先會套用其遷移，並將資料行重新命名為 *別名*，之後您的遷移就會將它重新命名為使用者 *名稱*。</span><span class="sxs-lookup"><span data-stu-id="726ef-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="726ef-125">您可以安全地與小組的其他人共用您的遷移。</span><span class="sxs-lookup"><span data-stu-id="726ef-125">Your migration can safely be shared with the rest of the team.</span></span>
