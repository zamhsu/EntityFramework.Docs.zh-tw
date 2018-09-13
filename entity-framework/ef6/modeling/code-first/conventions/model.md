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
# <a name="model-based-conventions"></a>以模型為基礎的慣例
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

基礎模型慣例是慣例為基礎的模型組態的進階的方法。 在大部分情況下[自訂 Code First 慣例 API 上 DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md)應該使用。 使用基礎模型慣例之前，建議使用 DbModelBuilder API 慣例的了解。  

基礎模型慣例允許建立的屬性和資料表不是可透過標準慣例設定會影響的慣例。 這些範例會每個階層模型的資料表中的鑑別子資料行和獨立關聯中的資料行。  

## <a name="creating-a-convention"></a>建立一種命名慣例   

在管線中的慣例需要套用至模型時，會選擇建立基礎模型慣例的第一個步驟。 有兩種類型的模型慣例，概念 （C-工作區） 和存放區 （S-工作區）。 C 間距慣例會套用至應用程式建置，而 S 空間慣例會套用至代表資料庫模型的版本，以及如何自動產生的資料行等的控制項項目命名的模型。  

模型慣例是從 IConceptualModelConvention 或 IStoreModelConvention 延伸的類別。  這些介面，同時接受可以是泛型型別輸入 MetadataItem 用來篩選慣例適用於資料類型。  

## <a name="adding-a-convention"></a>加入一種命名慣例   

模型慣例會加入做為一般慣例類別相同的方式。 在  **OnModelCreating**方法，將慣例新增至模型的慣例的清單。  

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

一種命名慣例，也可以加入相對於另一個慣例使用 Conventions.AddBefore\< \>或 Conventions.AddAfter\< \>方法。 如需適用於 Entity Framework 之慣例的詳細資訊請參閱提示 > 一節。  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>範例： 鑑別子模型慣例  

重新命名 EF 所產生的資料行是無法使用 Api 的其他慣例的某項目的範例。  這是一種情況其中使用模型慣例是您唯一的選項。  

若要使用的基礎模型慣例設定產生的資料行的範例自訂命名鑑別子資料行的方式。  以下是會在名為"鑑別子 」 之模型中的每個資料行重新命名 「 entitytype 」 為基礎的簡單模型慣例的範例。  這包括開發人員只是名為"鑑別子 」 的資料行。 由於 「 鑑別子 」 資料行是產生的資料行，這需要在 S 空間中執行。  

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

## <a name="example-general-ia-renaming-convention"></a>範例： 重新命名慣例的一般 IA   

基礎模型動作慣例的另一個更複雜的範例是設定為獨立的關聯 (IAs) 的方式。  這是模型慣例會適用，因為 IAs EF 所產生，而不 DbModelBuilder API 可以存取的模型中存在的情況。  

當 EF 產生 IA 時，它會建立名為 EntityType_KeyName 的資料行。 比方說，名為 Customer 與索引鍵資料行的關聯指定為 CustomerId 就會產生名為 Customer_CustomerId 的資料行。 下列慣例階梯狀 '\_' 字元不會針對 IA.產生的資料行名稱  

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

## <a name="extending-existing-conventions"></a>擴充現有的慣例   

如果您要撰寫的慣例是類似於其中的慣例，Entity Framework 已套用到您的模型，您一律可以擴充該慣例，以避免從頭重寫程式。  這個範例是要取代現有的識別碼符合與自訂的慣例。   若要覆寫索引鍵的慣例的另一個好處是，覆寫的方法會取得已偵測到或明確設定沒有索引鍵時，才呼叫。 一份慣例，Entity Framework 使用，請參閱這裡： [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx ](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。  

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

然後，我們需要加入我們新的慣例之前的現有索引鍵的慣例。 我們將新增 CustomKeyDiscoveryConvention 之後，我們可以移除 IdKeyDiscoveryConvention。  如果我們不會移除現有的 IdKeyDiscoveryConvention 這個慣例會仍優先於識別碼探索慣例，因為它會執行第一次，但在找到任何 「 金鑰 」 屬性的情況下，將會執行 「 識別碼 」 慣例。  我們會看到此行為，因為每個慣例會更新先前的慣例 （而非獨立操作它及其所有組合在一起），讓先前的慣例如果比方說，更新資料行名稱相符的項目，會看到模型感興趣，您自訂的慣例 （當之前的名稱不是感興趣的），然後它會套用至該資料行。  

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

## <a name="notes"></a>注意  

MSDN 文件中有一份由 Entity Framework 目前套用的慣例： [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx ](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。  這份清單會直接提取自我們的原始程式碼。  Entity Framework 6 的原始程式碼位於[GitHub](https://github.com/aspnet/entityframework6/)和許多 Entity Framework 所使用的慣例都是不錯起點的自訂模型基礎的慣例。  
