---
title: A Table Storage használatának első lépései a Visual Studióval (Cloud Services)
description: Az Azure Table Storage használatának első lépései a Cloud Service-projektekben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545779"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Ismerkedés az Azure Table Storage-hez és a Visual Studióhoz kapcsolódó szolgáltatásokkal (felhőszolgáltatás-projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk azt ismerteti, hogyan kezdheti el az Azure Table Storage használatát a Visual Studióban, miután létrehozta vagy hivatkozott egy Azure Storage-fiókot a Cloud Services-projektben a Visual Studio **csatlakoztatott szolgáltatások hozzáadása** párbeszédpanel használatával. A **csatlakoztatott szolgáltatások hozzáadása** művelet telepíti a megfelelő NuGet-csomagokat az Azure Storage-ba a projektben, és hozzáadja a Storage-fiókhoz tartozó kapcsolati karakterláncot a projekt konfigurációs fájljaihoz.

Az Azure Table Storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adattárolás tárolását. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.

A kezdéshez először létre kell hoznia egy táblát a Storage-fiókban. Bemutatjuk, hogyan hozhat létre egy Azure-táblázatot a kódban, valamint hogyan végezhet el alapszintű táblákat és entitásokkal kapcsolatos műveleteket, például tábla entitások hozzáadását, módosítását, olvasását és olvasását. A minták C kódban vannak megírva \# , és a [.net-hez készült Microsoft Azure Storage ügyféloldali kódtárat](/previous-versions/azure/dn261237(v=azure.100))használják.

**Megjegyzés:** Az Azure Storage-hívásokat végrehajtó API-k némelyike aszinkron módon történik. Lásd: [aszinkron programozás aszinkron módon, és](/previous-versions/hh191443(v=vs.140)) további információra számíthat. Az alábbi kód az aszinkron programozási módszerek használatát feltételezi.

* További információ a táblázatok programozással történő kezeléséről: az [Azure Table Storage használatának első lépései a .NET használatával](../cosmos-db/tutorial-develop-table-dotnet.md) .
* Az Azure Storage szolgáltatással kapcsolatos általános információkért lásd a [Storage dokumentációját](https://azure.microsoft.com/documentation/services/storage/) .
* Az Azure Cloud Services általános információit [Cloud Services dokumentációban](https://azure.microsoft.com/documentation/services/cloud-services/) találja.
* A ASP.NET-alkalmazások programozásával kapcsolatos további információkért lásd: [ASP.net](https://www.asp.net) .

## <a name="access-tables-in-code"></a>Hozzáférési táblázatok a kódban
A Cloud Service-projektek tábláinak eléréséhez minden olyan C#-forrásfájl esetében meg kell adnia a következő elemeket, amelyek hozzáférnek az Azure Table Storage szolgáltatáshoz.

1. Győződjön meg arról, hogy a C#-fájl tetején található névtér-deklarációk tartalmazzák **az alábbi utasításokat** .
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Szerezzen be egy **CloudStorageAccount** -objektumot, amely a Storage-fiók adatait jelöli. A következő kód használatával szerezheti be a Storage-kapcsolódási karakterlánc és a Storage-fiók adatait az Azure szolgáltatás konfigurációjában.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Használja a fenti kódot a kód elé a következő mintákban.
   > 
   > 
3. Szerezzen be egy **CloudTableClient** -objektumot, amely a Storage-fiókban található táblázat-objektumokra hivatkozik.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Egy **CloudTable** hivatkozási objektum beszerzése egy adott táblára és entitásokra való hivatkozáshoz.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Tábla létrehozása kódban
Az Azure-tábla létrehozásához egyszerűen vegyen fel egy hívást a **CreateIfNotExistsAsync** -be a **CloudTable** -objektum lekérése után, a "hozzáférési táblázatok a kódban" szakaszban leírtak szerint.

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. A következő kód egy **CustomerEntity** nevű Entity osztályt határoz meg, amely az ügyfél utónevét és a vezetéknevét használja a partíciós kulcsként.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Az entitásokat érintő tábla-műveletek a korábban a "hozzáférési táblázatok a kódban" című részében létrehozott **CloudTable** objektum használatával hajthatók végre. A **TableOperation** objektum a végrehajtani kívánt műveletet jelöli. A következő mintakód bemutatja, hogyan hozhat létre egy **CloudTable** objektumot és egy **CustomerEntity** objektumot. A művelet előkészítéséhez létrejön egy **TableOperation** , amely beszúrja az ügyfél entitást a táblába. Végezetül **CloudTable.ExecuteAsync** meghívásával hajtja végre a műveletet.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```


## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási művelettel több entitást is beszúrhat egy táblába. A következő mintakód két entitást hoz létre ("Jeff Smith" és "ben Smith"), hozzáadja őket egy **tablebatchoperation művelethez** objektumhoz az Insert metódus használatával, majd elindítja a műveletet **CloudTable.ExecuteBatchAsync** meghívásával.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Egy partíció összes entitásának beolvasása
Egy partíció összes entitásához tartozó tábla lekérdezéséhez használjon **TableQuery** objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);

return View();
```


## <a name="get-a-single-entity"></a>Egyetlen entitás beolvasása
Megírhat egy lekérdezést, amely egyetlen, adott entitást kap. A következő kód egy **TableOperation** objektumot használ a "ben Smith" nevű ügyfél megadásához. Ez a metódus csak egyetlen entitást ad vissza, nem egy gyűjteményt, és a visszaadott értéket a **ableresult. result** egy **CustomerEntity** objektum. A partíciók és a sorok kulcsának a lekérdezésben való megadásával a leggyorsabb módszer egyetlen entitás beolvasására a **Table** szolgáltatásból.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Entitás törlése
A keresés után törölheti az entitásokat. A következő kód egy "ben Smith" nevű ügyfél-entitást keres, és ha megtalálta, akkor törli.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    await peopleTable.ExecuteAsync(deleteOperation);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]