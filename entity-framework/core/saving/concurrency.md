---
title: "處理並行的 EF 核心"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bce0539d-b0cd-457d-be71-f7ca16f3baea
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: bbd3e154c1b27b16c7d8f8fbf9ed51df0849795c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="handling-concurrency"></a><span data-ttu-id="7f37d-102">處理並行</span><span class="sxs-lookup"><span data-stu-id="7f37d-102">Handling Concurrency</span></span>

<span data-ttu-id="7f37d-103">如果屬性設定為並行語彙基元 EF 會檢查沒有其他使用者在儲存變更至該記錄時，已修改該資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="7f37d-103">If a property is configured as a concurrency token then EF will check that no other user has modified that value in the database when saving changes to that record.</span></span>

> [!TIP]  
> <span data-ttu-id="7f37d-104">您可以檢視這篇文章[範例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)GitHub 上。</span><span class="sxs-lookup"><span data-stu-id="7f37d-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/) on GitHub.</span></span>

## <a name="how-concurrency-handling-works-in-ef-core"></a><span data-ttu-id="7f37d-105">在 EF 核心中的並行處理如何運作</span><span class="sxs-lookup"><span data-stu-id="7f37d-105">How concurrency handling works in EF Core</span></span>

<span data-ttu-id="7f37d-106">如需 Entity Framework Core 中並行處理的運作方式的詳細說明，請參閱[並行語彙基元](../modeling/concurrency.md)。</span><span class="sxs-lookup"><span data-stu-id="7f37d-106">For a detailed description of how concurrency handling works in Entity Framework Core, see [Concurrency Tokens](../modeling/concurrency.md).</span></span>

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="7f37d-107">解決並行衝突</span><span class="sxs-lookup"><span data-stu-id="7f37d-107">Resolving concurrency conflicts</span></span>

<span data-ttu-id="7f37d-108">解決並行衝突，包括使用演算法來合併資料庫中所作的變更與目前使用者的暫止變更。</span><span class="sxs-lookup"><span data-stu-id="7f37d-108">Resolving a concurrency conflict involves using an algorithm to merge the pending changes from the current user with the changes made in the database.</span></span> <span data-ttu-id="7f37d-109">確切方式會因您的應用程式，但常見的作法是將值顯示給使用者，並請他們決定正確的值儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="7f37d-109">The exact approach will vary based on your application, but a common approach is to display the values to the user and have them decide the correct values to be stored in the database.</span></span>

<span data-ttu-id="7f37d-110">**有三個設定的值可用來協助解決並行衝突。**</span><span class="sxs-lookup"><span data-stu-id="7f37d-110">**There are three sets of values available to help resolve a concurrency conflict.**</span></span>

* <span data-ttu-id="7f37d-111">**目前的值**是應用程式嘗試寫入資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="7f37d-111">**Current values** are the values that the application was attempting to write to the database.</span></span>

* <span data-ttu-id="7f37d-112">**原始值**原先擷取從資料庫中，進行任何編輯之前的值。</span><span class="sxs-lookup"><span data-stu-id="7f37d-112">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>

* <span data-ttu-id="7f37d-113">**資料庫值**是目前資料庫中儲存的值。</span><span class="sxs-lookup"><span data-stu-id="7f37d-113">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="7f37d-114">若要處理並行衝突，攔截`DbUpdateConcurrencyException`期間`SaveChanges()`，使用`DbUpdateConcurrencyException.Entries`準備受影響的實體，一組新的變更，然後重試`SaveChanges()`作業。</span><span class="sxs-lookup"><span data-stu-id="7f37d-114">To handle a concurrency conflict, catch a `DbUpdateConcurrencyException` during `SaveChanges()`, use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities, and then retry the `SaveChanges()` operation.</span></span>

<span data-ttu-id="7f37d-115">在下列範例中，`Person.FirstName`和`Person.LastName`已設定為並行語彙基元。</span><span class="sxs-lookup"><span data-stu-id="7f37d-115">In the following example, `Person.FirstName` and `Person.LastName` are setup as concurrency token.</span></span> <span data-ttu-id="7f37d-116">沒有`// TODO:`註解中的位置，您會在其中加入應用程式特定的邏輯，以選擇要儲存到資料庫的值。</span><span class="sxs-lookup"><span data-stu-id="7f37d-116">There is a `// TODO:` comment in the location where you would include application specific logic to choose the value to be saved to the database.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Concurrency/Sample.cs?highlight=53,54)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System;
using System.ComponentModel.DataAnnotations;
using System.Linq;

namespace EFSaving.Concurrency
{
    public class Sample
    {
        public static void Run()
        {
            // Ensure database is created and has a person in it
            using (var context = new PersonContext())
            {
                context.Database.EnsureDeleted();
                context.Database.EnsureCreated();

                context.People.Add(new Person { FirstName = "John", LastName = "Doe" });
                context.SaveChanges();
            }

            using (var context = new PersonContext())
            {
                // Fetch a person from database and change phone number
                var person = context.People.Single(p => p.PersonId == 1);
                person.PhoneNumber = "555-555-5555";

                // Change the persons name in the database (will cause a concurrency conflict)
                context.Database.ExecuteSqlCommand("UPDATE dbo.People SET FirstName = 'Jane' WHERE PersonId = 1");

                try
                {
                    // Attempt to save changes to the database
                    context.SaveChanges();
                }
                catch (DbUpdateConcurrencyException ex)
                {
                    foreach (var entry in ex.Entries)
                    {
                        if (entry.Entity is Person)
                        {
                            // Using a NoTracking query means we get the entity but it is not tracked by the context
                            // and will not be merged with existing entities in the context.
                            var databaseEntity = context.People.AsNoTracking().Single(p => p.PersonId == ((Person)entry.Entity).PersonId);
                            var databaseEntry = context.Entry(databaseEntity);

                            foreach (var property in entry.Metadata.GetProperties())
                            {
                                var proposedValue = entry.Property(property.Name).CurrentValue;
                                var originalValue = entry.Property(property.Name).OriginalValue;
                                var databaseValue = databaseEntry.Property(property.Name).CurrentValue;

                                // TODO: Logic to decide which value should be written to database
                                // entry.Property(property.Name).CurrentValue = <value to be saved>;

                                // Update original values to
                                entry.Property(property.Name).OriginalValue = databaseEntry.Property(property.Name).CurrentValue;
                            }
                        }
                        else
                        {
                            throw new NotSupportedException("Don't know how to handle concurrency conflicts for " + entry.Metadata.Name);
                        }
                    }

                    // Retry the save operation
                    context.SaveChanges();
                }
            }
        }

        public class PersonContext : DbContext
        {
            public DbSet<Person> People { get; set; }

            protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            {
                optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFSaving.Concurrency;Trusted_Connection=True;");
            }
        }

        public class Person
        {
            public int PersonId { get; set; }

            [ConcurrencyCheck]
            public string FirstName { get; set; }

            [ConcurrencyCheck]
            public string LastName { get; set; }

            public string PhoneNumber { get; set; }
        }

    }
}
```
