---
title: Egyedi kulcsok definiálása egy Azure Cosmos-tárolóhoz
description: Megtudhatja, hogyan határozhat meg egy Azure Cosmos-tároló egyedi kulcsait a Azure Portal, a PowerShell, a .NET, a Java és számos más SDK használatával.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336622"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Egyedi kulcsok definiálása egy Azure Cosmos-tárolóhoz
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk bemutatja az [egyedi kulcsok](unique-keys.md) definiálásának különböző módjait az Azure Cosmos-tárolók létrehozásakor. Ezt a műveletet jelenleg a Azure Portal vagy az egyik SDK használatával lehet elvégezni.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) , vagy válasszon ki egy meglévőt.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza ki a használni kívánt tárolót.

1. Kattintson az **új tároló** elemre.

1. A **tároló hozzáadása** párbeszédpanelen kattintson az **+ egyedi kulcs hozzáadása** lehetőségre egy egyedi kulcs hozzáadásához.

1. Adja meg az egyedi kulcs megkötésének elérési útját (ke) t

1. Ha szükséges, vegyen fel további egyedi kulcs-bejegyzéseket a **+ egyedi kulcs hozzáadása** lehetőségre kattintva.

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Képernyőkép a Azure Portal egyedi kulcs megkötésének bejegyzéséről":::

## <a name="use-powershell"></a>A PowerShell használata

Egyedi kulcsokkal rendelkező tároló létrehozásához lásd: [Azure Cosmos-tároló létrehozása egyedi kulccsal és TTL-vel](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>A .NET SDK használata

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Amikor új tárolót hoz létre a [.net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)használatával, egy `UniqueKeyPolicy` objektum használható egyedi kulcsokra vonatkozó megkötések definiálásához.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Amikor új tárolót hoz létre a [.net SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)használatával, az SDK Fluent API segítségével tömör és olvasható módon deklarálhatja az egyedi kulcsokat.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>A Java SDK használata

Amikor a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)-val új tárolót hoz létre, egy `UniqueKeyPolicy` objektum használható egyedi kulcsokra vonatkozó megkötések definiálásához.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Az Node.js SDK használata

Amikor új tárolót hoz létre az [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)-val, egy `UniqueKeyPolicy` objektum használható egyedi kulcsokra vonatkozó megkötések definiálásához.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>A Python SDK használata

Amikor új tárolót hoz létre a [PYTHON SDK](https://pypi.org/project/azure-cosmos/)használatával, az egyedi kulcsokra vonatkozó megkötések a paraméterként átadott szótár részeként adhatók meg.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Következő lépések

- További információ a [particionálásról](partitioning-overview.md)
- Az [indexelés működésének](index-overview.md) megismerése
