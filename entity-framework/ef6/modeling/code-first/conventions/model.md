---
title: 以模型為基礎的慣例-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: c873e9a216bd9bd1934f2149ae6af602072f3608
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656160"
---
# <a name="model-based-conventions"></a><span data-ttu-id="ca336-102">以模型為基礎的慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-102">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="ca336-103">**僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。</span><span class="sxs-lookup"><span data-stu-id="ca336-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ca336-104">如果您使用的是較早版本，則不適用部分或全部的資訊。</span><span class="sxs-lookup"><span data-stu-id="ca336-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ca336-105">以模型為基礎的慣例是以慣例為基礎的模型設定的先進方法。</span><span class="sxs-lookup"><span data-stu-id="ca336-105">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="ca336-106">在大部分情況下，應該使用[DbModelBuilder 上的自訂 Code First 慣例 API](~/ef6/modeling/code-first/conventions/custom.md) 。</span><span class="sxs-lookup"><span data-stu-id="ca336-106">For most scenarios the [Custom Code First Convention API on DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) should be used.</span></span> <span data-ttu-id="ca336-107">使用以模型為基礎的慣例之前，建議您先瞭解適用于慣例的 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="ca336-107">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="ca336-108">以模型為基礎的慣例允許建立會影響無法透過標準慣例設定之屬性和資料表的慣例。</span><span class="sxs-lookup"><span data-stu-id="ca336-108">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="ca336-109">這些範例是資料表中每個階層模型和獨立關聯資料行的鑒別子資料行。</span><span class="sxs-lookup"><span data-stu-id="ca336-109">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="ca336-110">建立慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-110">Creating a Convention</span></span>   

<span data-ttu-id="ca336-111">建立以模型為基礎的慣例的第一個步驟，是選擇管線中的慣例必須套用至模型的時機。</span><span class="sxs-lookup"><span data-stu-id="ca336-111">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="ca336-112">模型慣例有兩種類型：概念（C-空格）和存放區（S-Space）。</span><span class="sxs-lookup"><span data-stu-id="ca336-112">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="ca336-113">C-Space 慣例會套用至應用程式所建立的模型，而 S-Space 慣例會套用至代表資料庫的模型版本，並控制如何命名自動產生的資料行等專案。</span><span class="sxs-lookup"><span data-stu-id="ca336-113">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="ca336-114">模型慣例是一種從 IConceptualModelConvention 或 IStoreModelConvention 延伸的類別。</span><span class="sxs-lookup"><span data-stu-id="ca336-114">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="ca336-115">這些介面都接受可以是 MetadataItem 類型的泛型型別，用來篩選套用慣例的資料類型。</span><span class="sxs-lookup"><span data-stu-id="ca336-115">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="ca336-116">新增慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-116">Adding a Convention</span></span>   

<span data-ttu-id="ca336-117">模型慣例的加入方式與一般慣例類別相同。</span><span class="sxs-lookup"><span data-stu-id="ca336-117">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="ca336-118">在**OnModelCreating**方法中，將慣例加入至模型的慣例清單。</span><span class="sxs-lookup"><span data-stu-id="ca336-118">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

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

<span data-ttu-id="ca336-119">您也可以使用 AddBefore\<\> 或 AddAfter\<\> 方法，來新增慣例與另一個慣例的關聯。</span><span class="sxs-lookup"><span data-stu-id="ca336-119">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="ca336-120">如需 Entity Framework 所套用慣例的詳細資訊，請參閱附注一節。</span><span class="sxs-lookup"><span data-stu-id="ca336-120">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="ca336-121">範例：鑒別子模型慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-121">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="ca336-122">重新命名 EF 所產生的資料行，是您無法使用其他慣例 Api 執行的作業範例。</span><span class="sxs-lookup"><span data-stu-id="ca336-122">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="ca336-123">在此情況下，使用模型慣例是您唯一的選擇。</span><span class="sxs-lookup"><span data-stu-id="ca336-123">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="ca336-124">如何使用模型型慣例來設定產生的資料行的範例，就是自訂鑒別子資料行的命名方式。</span><span class="sxs-lookup"><span data-stu-id="ca336-124">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="ca336-125">以下是以模型為基礎的簡單慣例範例，其會將名為「鑒別子」之模型中的每個資料行重新命名為「EntityType」。</span><span class="sxs-lookup"><span data-stu-id="ca336-125">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="ca336-126">這包括開發人員簡單命名為「鑒別子」的資料行。</span><span class="sxs-lookup"><span data-stu-id="ca336-126">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="ca336-127">因為「鑒別子」資料行是產生的資料行，所以這需要在 S 空間中執行。</span><span class="sxs-lookup"><span data-stu-id="ca336-127">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

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

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="ca336-128">範例：一般 IA 重新命名慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-128">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="ca336-129">另一個更複雜的模型型慣例範例是設定獨立關聯（IAs）的命名方式。</span><span class="sxs-lookup"><span data-stu-id="ca336-129">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="ca336-130">這是適用模型慣例的情況，因為 IAs 是由 EF 產生，而且不存在於 DbModelBuilder API 可存取的模型中。</span><span class="sxs-lookup"><span data-stu-id="ca336-130">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="ca336-131">當 EF 產生 IA 時，它會建立一個名為 EntityType_KeyName 的資料行。</span><span class="sxs-lookup"><span data-stu-id="ca336-131">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="ca336-132">例如，如果名為 Customer 的關聯具有名為 CustomerId 的索引鍵資料行，就會產生名為 Customer_CustomerId 的資料行。</span><span class="sxs-lookup"><span data-stu-id="ca336-132">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="ca336-133">下列慣例會從為 IA 產生的資料行名稱中去除 '\_' 字元。</span><span class="sxs-lookup"><span data-stu-id="ca336-133">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

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

## <a name="extending-existing-conventions"></a><span data-ttu-id="ca336-134">擴充現有的慣例</span><span class="sxs-lookup"><span data-stu-id="ca336-134">Extending Existing Conventions</span></span>   

<span data-ttu-id="ca336-135">如果您需要撰寫的慣例與 Entity Framework 已適用于模型的其中一個慣例類似，您一律可以擴充該慣例，以避免必須從頭重新進行重寫。</span><span class="sxs-lookup"><span data-stu-id="ca336-135">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="ca336-136">其中一個範例是以自訂的方式取代現有的識別碼比對慣例。</span><span class="sxs-lookup"><span data-stu-id="ca336-136">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="ca336-137">覆寫金鑰慣例的額外好處是，只有在沒有偵測到或明確設定索引鍵時，才會呼叫覆寫的方法。</span><span class="sxs-lookup"><span data-stu-id="ca336-137">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="ca336-138">這裡提供 Entity Framework 所使用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ca336-138">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

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

<span data-ttu-id="ca336-139">接著，我們需要在現有的金鑰慣例之前加入新的慣例。</span><span class="sxs-lookup"><span data-stu-id="ca336-139">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="ca336-140">新增 CustomKeyDiscoveryConvention 之後，我們就可以移除 IdKeyDiscoveryConvention。</span><span class="sxs-lookup"><span data-stu-id="ca336-140">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="ca336-141">如果我們未移除現有的 IdKeyDiscoveryConvention，此慣例仍然會優先于識別碼探索慣例，因為它會先執行，但在找不到 "key" 屬性的情況下，將會執行 "Id" 慣例。</span><span class="sxs-lookup"><span data-stu-id="ca336-141">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="ca336-142">我們會看到這項行為，因為每個慣例都會看到先前慣例已更新的模型（而不是單獨對其進行操作，並結合在一起），因此，如果先前的慣例已更新資料行名稱以符合下列專案的內容：對您的自訂慣例感關注（如果名稱之前不是相關，則會套用至該資料行）。</span><span class="sxs-lookup"><span data-stu-id="ca336-142">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

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

## <a name="notes"></a><span data-ttu-id="ca336-143">備註</span><span class="sxs-lookup"><span data-stu-id="ca336-143">Notes</span></span>  

<span data-ttu-id="ca336-144">此處的 MSDN 檔提供 Entity Framework 目前套用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。</span><span class="sxs-lookup"><span data-stu-id="ca336-144">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="ca336-145">這份清單會直接從我們的原始程式碼提取。</span><span class="sxs-lookup"><span data-stu-id="ca336-145">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="ca336-146">Entity Framework 6 的原始程式碼可在[GitHub](https://github.com/aspnet/entityframework6/)上取得，而 Entity Framework 使用的許多慣例都是自訂模型型慣例的良好起點。</span><span class="sxs-lookup"><span data-stu-id="ca336-146">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
