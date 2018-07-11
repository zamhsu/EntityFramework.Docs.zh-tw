---
title: 在小組環境-EF Core 移轉
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: cf76df32099c25f33d5d94edf6bccec099cf714a
ms.sourcegitcommit: de491b0988eab91b84dcbd941b7551e597e9c051
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38228376"
---
<a name="migrations-in-team-environments"></a><span data-ttu-id="e9db8-102">在小組環境中的移轉</span><span class="sxs-lookup"><span data-stu-id="e9db8-102">Migrations in Team Environments</span></span>
===============================
<span data-ttu-id="e9db8-103">在小組環境中使用移轉，請特別注意模型快照集檔案。</span><span class="sxs-lookup"><span data-stu-id="e9db8-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="e9db8-104">此檔案會告訴您，如果您小組成員的移轉合併完全與您的記錄，或如果您需要重新建立您的移轉，才能共用它所解決的衝突。</span><span class="sxs-lookup"><span data-stu-id="e9db8-104">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

<a name="merging"></a><span data-ttu-id="e9db8-105">合併</span><span class="sxs-lookup"><span data-stu-id="e9db8-105">Merging</span></span>
-------
<span data-ttu-id="e9db8-106">當您合併移轉從您的小組成員時，您可能會收到您模型的快照集檔案中衝突。</span><span class="sxs-lookup"><span data-stu-id="e9db8-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="e9db8-107">如果這兩個變更無關，只是一般規則合併，並可同時存在兩個移轉。</span><span class="sxs-lookup"><span data-stu-id="e9db8-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="e9db8-108">例如，可能看起來像這樣的客戶實體型別組態中會出現合併衝突：</span><span class="sxs-lookup"><span data-stu-id="e9db8-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

    <<<<<<< Mine
    b.Property<bool>("Deactivated");
    =======
    b.Property<int>("LoyaltyPoints");
    >>>>>>> Theirs

<span data-ttu-id="e9db8-109">因為這兩個屬性都必須存在於最終模型中，請加入這兩個屬性以完成合併。</span><span class="sxs-lookup"><span data-stu-id="e9db8-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="e9db8-110">在許多情況下，您的版本控制系統可能會自動為您合併這類變更。</span><span class="sxs-lookup"><span data-stu-id="e9db8-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="e9db8-111">在這些情況下，您的移轉和移轉您的小組成員會彼此獨立。</span><span class="sxs-lookup"><span data-stu-id="e9db8-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="e9db8-112">由於其中之一可以先套用，您不需要對您的移轉，才能分享您小組中的任何其他變更。</span><span class="sxs-lookup"><span data-stu-id="e9db8-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

<a name="resolving-conflicts"></a><span data-ttu-id="e9db8-113">解決衝突</span><span class="sxs-lookup"><span data-stu-id="e9db8-113">Resolving conflicts</span></span>
-------------------
<span data-ttu-id="e9db8-114">有時候您會遇到，則為 true 的衝突合併 model 快照集時。</span><span class="sxs-lookup"><span data-stu-id="e9db8-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="e9db8-115">例如，您和您的小組可能每個已重新命名相同的屬性。</span><span class="sxs-lookup"><span data-stu-id="e9db8-115">For example, you and your teammate may each have renamed the same property.</span></span>

    <<<<<<< Mine
    b.Property<string>("Username");
    =======
    b.Property<string>("Alias");
    >>>>>>> Theirs

<span data-ttu-id="e9db8-116">如果您遇到這類衝突，則會解析重新建立您的移轉。</span><span class="sxs-lookup"><span data-stu-id="e9db8-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="e9db8-117">請依照下列步驟：</span><span class="sxs-lookup"><span data-stu-id="e9db8-117">Follow these steps:</span></span>

1. <span data-ttu-id="e9db8-118">中止 「 合併 」 和 「 回復到您在合併之前的工作目錄</span><span class="sxs-lookup"><span data-stu-id="e9db8-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="e9db8-119">移除您的移轉 （但保留模型的變更）</span><span class="sxs-lookup"><span data-stu-id="e9db8-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="e9db8-120">您的小組成員變更合併至您的工作目錄</span><span class="sxs-lookup"><span data-stu-id="e9db8-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="e9db8-121">重新加入您的移轉</span><span class="sxs-lookup"><span data-stu-id="e9db8-121">Re-add your migration</span></span>

<span data-ttu-id="e9db8-122">完成之後，兩個移轉可以套用正確的順序。</span><span class="sxs-lookup"><span data-stu-id="e9db8-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="e9db8-123">移轉會先套用，重新命名的資料行*別名*，之後您的移轉重新命名它*Username*。</span><span class="sxs-lookup"><span data-stu-id="e9db8-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="e9db8-124">與小組的其餘部分，可以安全地共用您的移轉。</span><span class="sxs-lookup"><span data-stu-id="e9db8-124">Your migration can safely be shared with the rest of the team.</span></span>
