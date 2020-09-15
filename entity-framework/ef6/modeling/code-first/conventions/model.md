---
title: 以模型為基礎的慣例-EF6
description: Entity Framework 6 中以模型為基礎的慣例
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: bcb7a88f4d9fc994b51300ac30ec58671be370c8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073999"
---
# <a name="model-based-conventions"></a>以模型為基礎的慣例
> [!NOTE]
> **僅限 EF6 及更新版本** - Entity Framework 6 已引進此頁面中所討論的功能及 API 等等。 如果您使用的是較早版本，則不適用部分或全部的資訊。  

以模型為基礎的慣例是以慣例為基礎的模型設定的先進方法。 在大部分的情況下，應該使用 [DbModelBuilder 上的自訂 Code First 慣例 API](xref:ef6/modeling/code-first/conventions/custom) 。 建議您先瞭解慣例的 DbModelBuilder API，再使用以模型為基礎的慣例。  

以模型為基礎的慣例可讓您建立會影響無法透過標準慣例設定之屬性和資料表的慣例。 這些範例為數據表中每個階層模型和獨立關聯資料行的鑒別子資料行。  

## <a name="creating-a-convention"></a>建立慣例   

建立以模型為基礎的慣例的第一個步驟，是選擇管線中的慣例必須套用至模型。 模型慣例有兩種類型：概念 (C 空間) 和儲存 (S 空間) 。 C 空間慣例會套用至應用程式所建立的模型，而會將 S 空間慣例套用至代表資料庫的模型版本，並控制如何命名自動產生的資料行。  

模型慣例是從 IConceptualModelConvention 或 IStoreModelConvention 延伸的類別。  這些介面都會接受型別為 MetadataItem 的泛型型別，而該型別會用來篩選套用慣例的資料型別。  

## <a name="adding-a-convention"></a>加入慣例   

模型慣例的新增方式與一般慣例類別相同。 在 **OnModelCreating** 方法中，將慣例新增至模型的慣例清單。  

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

您也可以使用 AddBefore 或 AddAfter 方法，加入慣例與另一個慣例的關聯 \<\> \<\> 。 如需 Entity Framework 適用之慣例的詳細資訊，請參閱附注一節。  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>範例：鑒別子模型慣例  

重新命名 EF 產生的資料行，就是您無法使用其他慣例 Api 來進行的一些範例。  這種情況下，使用模型慣例是您唯一的選項。  

如何使用以模型為基礎的慣例來設定產生的資料行的範例，是自訂鑒別子資料行的命名方式。  以下是以模型為基礎的簡單慣例範例，會將名為「鑒別子」之模型中的每個資料行重新命名為「EntityType」。  這包括開發人員單純命名為 "鑒別子" 的資料行。 因為「鑒別子」資料行是產生的資料行，所以必須在 S 空間中執行。  

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

## <a name="example-general-ia-renaming-convention"></a>範例：一般 IA 重新命名慣例   

另一個更複雜的模型架構慣例範例，是設定獨立關聯 (IAs) 的命名方式。  這是適用于模型慣例的情況，因為 IAs 是由 EF 產生，而且不存在於 DbModelBuilder API 可以存取的模型中。  

當 EF 產生 IA 時，它會建立名為 EntityType_KeyName 的資料行。 例如，針對名稱為 CustomerId 的索引鍵資料行的名稱為 Customer 的關聯，會產生名為 Customer_CustomerId 的資料行。 下列慣例 \_ 會從針對 IA 產生的資料行名稱中去除 ' ' 字元。  

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

## <a name="extending-existing-conventions"></a>擴充現有的慣例   

如果您需要撰寫的慣例類似于 Entity Framework 已套用至模型的其中一種慣例，您一律可以擴充該慣例，以避免必須從頭重寫它。  其中一個範例是將現有的識別碼比對慣例取代為自訂的比對慣例。   覆寫金鑰慣例的額外好處是，只有在沒有偵測到或明確設定金鑰時，才會呼叫覆寫的方法。 您可以在這裡找到 Entity Framework 所使用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。  

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

接著，我們需要在現有的金鑰慣例之前加入新慣例。 新增 CustomKeyDiscoveryConvention 之後，我們可以移除 IdKeyDiscoveryConvention。  如果我們未移除現有的 IdKeyDiscoveryConvention，此慣例仍會優先于識別碼探索慣例，因為它會先執行，但是如果找不到任何 "key" 屬性，則會執行 "Id" 慣例。  我們會看到此行為，因為每個慣例都會看到先前的慣例所更新的模型 (而不是單獨操作，而且會合並) ，如此一來，如果先前的慣例已更新資料行名稱，以符合您的自訂)  (慣例，則會將其套用至該資料行。  

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

## <a name="notes"></a>備註  

您可以在 MSDN 檔中取得 Entity Framework 目前套用的慣例清單： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。  這份清單會直接從我們的原始程式碼提取。  Entity Framework 6 的原始程式碼可在 [GitHub](https://github.com/aspnet/entityframework6/) 上取得，而 Entity Framework 所使用的許多慣例是以自訂模型為基礎之慣例的良好起點。  
