---
title: 以模型為基礎的慣例-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: fb79164f71cb3afff705a83f5078a13d043abca8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490931"
---
# <a name="model-based-conventions"></a><span data-ttu-id="a811d-102">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="a811d-102">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="a811d-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="a811d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a811d-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="a811d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a811d-105">基礎模型慣例是慣例為基礎的模型組態的進階的方法。</span><span class="sxs-lookup"><span data-stu-id="a811d-105">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="a811d-106">在大部分情況下[自訂 Code First 慣例 API 上 DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md)應該使用。</span><span class="sxs-lookup"><span data-stu-id="a811d-106">For most scenarios the [Custom Code First Convention API on DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) should be used.</span></span> <span data-ttu-id="a811d-107">使用基礎模型慣例之前，建議使用 DbModelBuilder API 慣例的了解。</span><span class="sxs-lookup"><span data-stu-id="a811d-107">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="a811d-108">基礎模型慣例允許建立的屬性和資料表不是可透過標準慣例設定會影響的慣例。</span><span class="sxs-lookup"><span data-stu-id="a811d-108">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="a811d-109">這些範例會每個階層模型的資料表中的鑑別子資料行和獨立關聯中的資料行。</span><span class="sxs-lookup"><span data-stu-id="a811d-109">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="a811d-110">建立一種命名慣例</span><span class="sxs-lookup"><span data-stu-id="a811d-110">Creating a Convention</span></span>   

<span data-ttu-id="a811d-111">在管線中的慣例需要套用至模型時，會選擇建立基礎模型慣例的第一個步驟。</span><span class="sxs-lookup"><span data-stu-id="a811d-111">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="a811d-112">有兩種類型的模型慣例，概念 （C-工作區） 和存放區 （S-工作區）。</span><span class="sxs-lookup"><span data-stu-id="a811d-112">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="a811d-113">C 間距慣例會套用至應用程式建置，而 S 空間慣例會套用至代表資料庫模型的版本，以及如何自動產生的資料行等的控制項項目命名的模型。</span><span class="sxs-lookup"><span data-stu-id="a811d-113">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="a811d-114">模型慣例是從 IConceptualModelConvention 或 IStoreModelConvention 延伸的類別。</span><span class="sxs-lookup"><span data-stu-id="a811d-114">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="a811d-115">這些介面，同時接受可以是泛型型別輸入 MetadataItem 用來篩選慣例適用於資料類型。</span><span class="sxs-lookup"><span data-stu-id="a811d-115">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="a811d-116">加入一種命名慣例</span><span class="sxs-lookup"><span data-stu-id="a811d-116">Adding a Convention</span></span>   

<span data-ttu-id="a811d-117">模型慣例會加入做為一般慣例類別相同的方式。</span><span class="sxs-lookup"><span data-stu-id="a811d-117">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="a811d-118">在  **OnModelCreating**方法，將慣例新增至模型的慣例的清單。</span><span class="sxs-lookup"><span data-stu-id="a811d-118">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

<span data-ttu-id="a811d-119">一種命名慣例，也可以加入相對於另一個慣例使用 Conventions.AddBefore\< \>或 Conventions.AddAfter\< \>方法。</span><span class="sxs-lookup"><span data-stu-id="a811d-119">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="a811d-120">如需適用於 Entity Framework 之慣例的詳細資訊請參閱提示 > 一節。</span><span class="sxs-lookup"><span data-stu-id="a811d-120">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="a811d-121">範例： 鑑別子模型慣例</span><span class="sxs-lookup"><span data-stu-id="a811d-121">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="a811d-122">重新命名 EF 所產生的資料行是無法使用 Api 的其他慣例的某項目的範例。</span><span class="sxs-lookup"><span data-stu-id="a811d-122">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="a811d-123">這是一種情況其中使用模型慣例是您唯一的選項。</span><span class="sxs-lookup"><span data-stu-id="a811d-123">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="a811d-124">若要使用的基礎模型慣例設定產生的資料行的範例自訂命名鑑別子資料行的方式。</span><span class="sxs-lookup"><span data-stu-id="a811d-124">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="a811d-125">以下是會在名為"鑑別子 」 之模型中的每個資料行重新命名 「 entitytype 」 為基礎的簡單模型慣例的範例。</span><span class="sxs-lookup"><span data-stu-id="a811d-125">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="a811d-126">這包括開發人員只是名為"鑑別子 」 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a811d-126">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="a811d-127">由於 「 鑑別子 」 資料行是產生的資料行，這需要在 S 空間中執行。</span><span class="sxs-lookup"><span data-stu-id="a811d-127">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="a811d-128">範例： 重新命名慣例的一般 IA</span><span class="sxs-lookup"><span data-stu-id="a811d-128">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="a811d-129">基礎模型動作慣例的另一個更複雜的範例是設定為獨立的關聯 (IAs) 的方式。</span><span class="sxs-lookup"><span data-stu-id="a811d-129">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="a811d-130">這是模型慣例會適用，因為 IAs EF 所產生，而不 DbModelBuilder API 可以存取的模型中存在的情況。</span><span class="sxs-lookup"><span data-stu-id="a811d-130">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="a811d-131">當 EF 產生 IA 時，它會建立名為 EntityType_KeyName 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a811d-131">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="a811d-132">比方說，名為 Customer 與索引鍵資料行的關聯指定為 CustomerId 就會產生名為 Customer_CustomerId 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a811d-132">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="a811d-133">下列慣例階梯狀 '\_' 字元不會針對 IA.產生的資料行名稱</span><span class="sxs-lookup"><span data-stu-id="a811d-133">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i \< properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a><span data-ttu-id="a811d-134">擴充現有的慣例</span><span class="sxs-lookup"><span data-stu-id="a811d-134">Extending Existing Conventions</span></span>   

<span data-ttu-id="a811d-135">如果您要撰寫的慣例是類似於其中的慣例，Entity Framework 已套用到您的模型，您一律可以擴充該慣例，以避免從頭重寫程式。</span><span class="sxs-lookup"><span data-stu-id="a811d-135">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="a811d-136">這個範例是要取代現有的識別碼符合與自訂的慣例。</span><span class="sxs-lookup"><span data-stu-id="a811d-136">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="a811d-137">若要覆寫索引鍵的慣例的另一個好處是，覆寫的方法會取得已偵測到或明確設定沒有索引鍵時，才呼叫。</span><span class="sxs-lookup"><span data-stu-id="a811d-137">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="a811d-138">一份慣例，Entity Framework 使用，請參閱這裡： [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx ](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="a811d-138">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

<span data-ttu-id="a811d-139">然後，我們需要加入我們新的慣例之前的現有索引鍵的慣例。</span><span class="sxs-lookup"><span data-stu-id="a811d-139">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="a811d-140">我們將新增 CustomKeyDiscoveryConvention 之後，我們可以移除 IdKeyDiscoveryConvention。</span><span class="sxs-lookup"><span data-stu-id="a811d-140">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="a811d-141">如果我們不會移除現有的 IdKeyDiscoveryConvention 這個慣例會仍優先於識別碼探索慣例，因為它會執行第一次，但在找到任何 「 金鑰 」 屬性的情況下，將會執行 「 識別碼 」 慣例。</span><span class="sxs-lookup"><span data-stu-id="a811d-141">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="a811d-142">我們會看到此行為，因為每個慣例會更新先前的慣例 （而非獨立操作它及其所有組合在一起），讓先前的慣例如果比方說，更新資料行名稱相符的項目，會看到模型感興趣，您自訂的慣例 （當之前的名稱不是感興趣的），然後它會套用至該資料行。</span><span class="sxs-lookup"><span data-stu-id="a811d-142">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a><span data-ttu-id="a811d-143">注意</span><span class="sxs-lookup"><span data-stu-id="a811d-143">Notes</span></span>  

<span data-ttu-id="a811d-144">MSDN 文件中有一份由 Entity Framework 目前套用的慣例： [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx ](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="a811d-144">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="a811d-145">這份清單會直接提取自我們的原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="a811d-145">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="a811d-146">Entity Framework 6 的原始程式碼位於[GitHub](https://github.com/aspnet/entityframework6/)和許多 Entity Framework 所使用的慣例都是不錯起點的自訂模型基礎的慣例。</span><span class="sxs-lookup"><span data-stu-id="a811d-146">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
