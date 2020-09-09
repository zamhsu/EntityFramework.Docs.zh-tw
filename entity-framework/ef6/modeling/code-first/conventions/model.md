---
title: 以模型為基礎的慣例-EF6
description: Entity Framework 6 中以模型為基礎的慣例
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: 30a79f505939220b3d4040778397eab972e6a712
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617181"
---
# <a name="model-based-conventions"></a><span data-ttu-id="a68ea-103">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-103">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="a68ea-104">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="a68ea-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a68ea-105">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="a68ea-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a68ea-106">以模型為基礎的慣例是以慣例為基礎的模型設定的先進方法。</span><span class="sxs-lookup"><span data-stu-id="a68ea-106">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="a68ea-107">在大部分的情況下，應該使用 [DbModelBuilder 上的自訂 Code First 慣例 API](xref:ef6/modeling/code-first/conventions/custom) 。</span><span class="sxs-lookup"><span data-stu-id="a68ea-107">For most scenarios the [Custom Code First Convention API on DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) should be used.</span></span> <span data-ttu-id="a68ea-108">建議您先瞭解慣例的 DbModelBuilder API，再使用以模型為基礎的慣例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-108">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="a68ea-109">以模型為基礎的慣例可讓您建立會影響無法透過標準慣例設定之屬性和資料表的慣例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-109">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="a68ea-110">這些範例為數據表中每個階層模型和獨立關聯資料行的鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-110">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="a68ea-111">建立慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-111">Creating a Convention</span></span>   

<span data-ttu-id="a68ea-112">建立以模型為基礎的慣例的第一個步驟，是選擇管線中的慣例必須套用至模型。</span><span class="sxs-lookup"><span data-stu-id="a68ea-112">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="a68ea-113">模型慣例有兩種類型：概念 (C 空間) 和儲存 (S 空間) 。</span><span class="sxs-lookup"><span data-stu-id="a68ea-113">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="a68ea-114">C 空間慣例會套用至應用程式所建立的模型，而會將 S 空間慣例套用至代表資料庫的模型版本，並控制如何命名自動產生的資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-114">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="a68ea-115">模型慣例是從 IConceptualModelConvention 或 IStoreModelConvention 延伸的類別。</span><span class="sxs-lookup"><span data-stu-id="a68ea-115">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="a68ea-116">這些介面都會接受型別為 MetadataItem 的泛型型別，而該型別會用來篩選套用慣例的資料型別。</span><span class="sxs-lookup"><span data-stu-id="a68ea-116">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="a68ea-117">加入慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-117">Adding a Convention</span></span>   

<span data-ttu-id="a68ea-118">模型慣例的新增方式與一般慣例類別相同。</span><span class="sxs-lookup"><span data-stu-id="a68ea-118">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="a68ea-119">在 **OnModelCreating** 方法中，將慣例新增至模型的慣例清單。</span><span class="sxs-lookup"><span data-stu-id="a68ea-119">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

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

<span data-ttu-id="a68ea-120">您也可以使用 AddBefore 或 AddAfter 方法，加入慣例與另一個慣例的關聯 \<\> \<\> 。</span><span class="sxs-lookup"><span data-stu-id="a68ea-120">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="a68ea-121">如需 Entity Framework 適用之慣例的詳細資訊，請參閱附注一節。</span><span class="sxs-lookup"><span data-stu-id="a68ea-121">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="a68ea-122">範例：鑒別子模型慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-122">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="a68ea-123">重新命名 EF 產生的資料行，就是您無法使用其他慣例 Api 來進行的一些範例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-123">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="a68ea-124">這種情況下，使用模型慣例是您唯一的選項。</span><span class="sxs-lookup"><span data-stu-id="a68ea-124">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="a68ea-125">如何使用以模型為基礎的慣例來設定產生的資料行的範例，是自訂鑒別子資料行的命名方式。</span><span class="sxs-lookup"><span data-stu-id="a68ea-125">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="a68ea-126">以下是以模型為基礎的簡單慣例範例，會將名為「鑒別子」之模型中的每個資料行重新命名為「EntityType」。</span><span class="sxs-lookup"><span data-stu-id="a68ea-126">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="a68ea-127">這包括開發人員單純命名為 "鑒別子" 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-127">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="a68ea-128">因為「鑒別子」資料行是產生的資料行，所以必須在 S 空間中執行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-128">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

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

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="a68ea-129">範例：一般 IA 重新命名慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-129">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="a68ea-130">另一個更複雜的模型架構慣例範例，是設定獨立關聯 (IAs) 的命名方式。</span><span class="sxs-lookup"><span data-stu-id="a68ea-130">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="a68ea-131">這是適用于模型慣例的情況，因為 IAs 是由 EF 產生，而且不存在於 DbModelBuilder API 可以存取的模型中。</span><span class="sxs-lookup"><span data-stu-id="a68ea-131">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="a68ea-132">當 EF 產生 IA 時，它會建立名為 EntityType_KeyName 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-132">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="a68ea-133">例如，針對名稱為 CustomerId 的索引鍵資料行的名稱為 Customer 的關聯，會產生名為 Customer_CustomerId 的資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-133">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="a68ea-134">下列慣例 \_ 會從針對 IA 產生的資料行名稱中去除 ' ' 字元。</span><span class="sxs-lookup"><span data-stu-id="a68ea-134">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

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
        for (int i = 0; i < properties.Count; ++i)
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

## <a name="extending-existing-conventions"></a><span data-ttu-id="a68ea-135">擴充現有的慣例</span><span class="sxs-lookup"><span data-stu-id="a68ea-135">Extending Existing Conventions</span></span>   

<span data-ttu-id="a68ea-136">如果您需要撰寫的慣例類似于 Entity Framework 已套用至模型的其中一種慣例，您一律可以擴充該慣例，以避免必須從頭重寫它。</span><span class="sxs-lookup"><span data-stu-id="a68ea-136">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="a68ea-137">其中一個範例是將現有的識別碼比對慣例取代為自訂的比對慣例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-137">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="a68ea-138">覆寫金鑰慣例的額外好處是，只有在沒有偵測到或明確設定金鑰時，才會呼叫覆寫的方法。</span><span class="sxs-lookup"><span data-stu-id="a68ea-138">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="a68ea-139">您可以在這裡找到 Entity Framework 所使用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="a68ea-139">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

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

<span data-ttu-id="a68ea-140">接著，我們需要在現有的金鑰慣例之前加入新慣例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-140">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="a68ea-141">新增 CustomKeyDiscoveryConvention 之後，我們可以移除 IdKeyDiscoveryConvention。</span><span class="sxs-lookup"><span data-stu-id="a68ea-141">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="a68ea-142">如果我們未移除現有的 IdKeyDiscoveryConvention，此慣例仍會優先于識別碼探索慣例，因為它會先執行，但是如果找不到任何 "key" 屬性，則會執行 "Id" 慣例。</span><span class="sxs-lookup"><span data-stu-id="a68ea-142">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="a68ea-143">我們會看到此行為，因為每個慣例都會看到先前的慣例所更新的模型 (而不是單獨操作，而且會合並) ，如此一來，如果先前的慣例已更新資料行名稱，以符合您的自訂)  (慣例，則會將其套用至該資料行。</span><span class="sxs-lookup"><span data-stu-id="a68ea-143">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

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

## <a name="notes"></a><span data-ttu-id="a68ea-144">注意</span><span class="sxs-lookup"><span data-stu-id="a68ea-144">Notes</span></span>  

<span data-ttu-id="a68ea-145">您可以在 MSDN 檔中取得 Entity Framework 目前套用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="a68ea-145">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="a68ea-146">這份清單會直接從我們的原始程式碼提取。</span><span class="sxs-lookup"><span data-stu-id="a68ea-146">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="a68ea-147">Entity Framework 6 的原始程式碼可在 [GitHub](https://github.com/aspnet/entityframework6/) 上取得，而 Entity Framework 所使用的許多慣例是以自訂模型為基礎之慣例的良好起點。</span><span class="sxs-lookup"><span data-stu-id="a68ea-147">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
