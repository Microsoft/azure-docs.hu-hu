---
title: Indexelési szabályzatok kezelése az Azure Cosmos DB-ben
description: Ismerje meg, hogyan kezelheti az indexelési szabályzatokat, hogyan vehet fel vagy zárhat ki egy tulajdonságot az indexelésből, hogyan definiálhat indexelést különböző Azure Cosmos DB SDK
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/02/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d52f8c59e83a4aae8724100770965f756a439fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015691"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Indexelési szabályzatok kezelése az Azure Cosmos DB-ben
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB a rendszer az egyes tárolók számára definiált [indexelési házirendek](index-policy.md) alapján indexeli az adatkészleteket. Az újonnan létrehozott tárolók alapértelmezett indexelési szabályzata minden sztring vagy szám esetében tartományindexeket kényszerít. Ez a szabályzat saját egyéni indexelési szabályzattal felülbírálható.

> [!NOTE]
> A cikkben ismertetett indexelési szabályzatok frissítésének módszere csak a Azure Cosmos DB SQL (Core) API-ra vonatkozik. Ismerkedjen meg a [Azure Cosmos db API-MongoDB](mongodb-indexing.md) és a [másodlagos indexeléshez Azure Cosmos db Cassandra APIban.](cassandra-secondary-index.md)

## <a name="indexing-policy-examples"></a>Indexelési szabályzat példái

Az alábbiakban néhány példát láthat a [JSON-formátumában](index-policy.md#include-exclude-paths)látható indexelési házirendekre, így azok elérhetővé válnak a Azure Portal. Ugyanezen paraméterek állíthatók be az Azure CLI-n vagy bármely SDK-n keresztül.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a><a id="range-index"></a>Letiltási szabályzat a tulajdonságok egyes elérési útjai szelektív kizárásához

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Ez az indexelési házirend egyenértékű a manuálisan beállított ```kind``` , ```dataType``` és az ```precision``` alapértelmezett értékekkel. Ezek a tulajdonságok már nem szükségesek explicit módon beállítani, és az indexelési szabályzatból teljesen ki kell hagyni őket (a fenti példában látható módon).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Bizonyos tulajdonságok elérési útjainak szelektív kiválasztására szolgáló szabályzat

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Ez az indexelési házirend egyenértékű a manuálisan beállított ```kind``` , ```dataType``` és az ```precision``` alapértelmezett értékekkel. Ezek a tulajdonságok már nem szükségesek explicit módon beállítani, és az indexelési szabályzatból teljesen ki kell hagyni őket (a fenti példában látható módon).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> Általában ajánlott egy **kijelentkezési** indexelési házirend használata, amellyel Azure Cosmos db proaktív módon indexelheti az adatmodellbe felvehető új tulajdonságokat.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a><a id="spatial-index"></a>Térbeli index használata csak adott tulajdonság elérési útján

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a><a id="composite-index"></a>Összetett indexelési házirend – példák

Az egyéni tulajdonságok elérési útjának belefoglalása vagy kizárása mellett összetett index is megadható. Ha olyan lekérdezést szeretne végrehajtani `ORDER BY` , amely több tulajdonságra vonatkozó záradékot tartalmaz, az ezen tulajdonságok [összetett indexét](index-policy.md#composite-indexes) kell megadni. Az összetett indexek emellett a több szűrővel, vagy egy szűrővel és ORDER BY záradékkal rendelkező lekérdezések esetében is teljesítménybeli előnyökkel járnak.

> [!NOTE]
> Az összetett elérési utak implicitek, `/?` mert csak az adott elérési úton található skaláris érték van indexelve. Az `/*` összetett elérési utak nem támogatják a helyettesítő karaktert. Nem adhat meg `/?` vagy `/*` összetett elérési utat.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Összetett index definiálva (név ASC, Age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

A lekérdezési #1 és a lekérdezési #2 a fenti összetett index szükséges a Name és a Age kifejezéshez:

Lekérdezés #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Lekérdezés #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Ez az összetett index kihasználja a lekérdezési #3 és a lekérdezési #4 és optimalizálja a szűrőket:

Lekérdezés #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Lekérdezés #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Összetett index definiálva (név ASC, Age ASC) és (név ASC, Age DESC):

Több különböző összetett index is definiálható ugyanabban az indexelési házirendben.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Összetett index definiálva (név ASC, Age ASC):

A sorrend megadása nem kötelező. Ha nincs megadva, a sorrend növekvő.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Az összes tulajdonság elérési útjának kizárása, de az indexelés aktív marad

Ez a szabályzat olyan helyzetekben használható, ahol az élettartam [(TTL) funkció](time-to-live.md) aktív, de nincs szükség további indexekre (a Azure Cosmos db tiszta kulcs-érték tárolóként való használatára).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nincs indexelés

Ez a szabályzat kikapcsolja az indexelést. Ha a `indexingMode` értéke `none` , akkor nem állítható be TTL a tárolón.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Indexelési házirend frissítése

Azure Cosmos DB az indexelési házirend az alábbi módszerek bármelyikével frissíthető:

- a Azure Portal
- Az Azure CLI használata
- a PowerShell használata
- Az SDK-k egyikének használata

Az [indexelési házirend frissítése](index-policy.md#modifying-the-indexing-policy) elindítja az index átalakítását. Az átalakítás előrehaladása az SDK-k által is nyomon követhető.

> [!NOTE]
> Az indexelési szabályzat frissítésekor a Azure Cosmos DB írások megszakítás nélkül lesznek megszakítva. További tudnivalók a [transzformációk indexeléséről](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure Cosmos-tárolók az indexelési szabályzatot JSON-dokumentumként tárolják, amelyet a Azure Portal közvetlenül szerkeszthet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

1. Nyissa meg a **adatkezelő** ablaktáblát, és válassza ki a használni kívánt tárolót.

1. Kattintson a **méretezési & beállítások** elemre.

1. Az indexelési szabályzat JSON-dokumentumának módosítása (lásd az [alábbi](#indexing-policy-examples)példákat)

1. Amikor elkészült, kattintson a **Mentés** elemre.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Indexelés kezelése Azure Portal használatával":::

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Egyéni indexelési házirenddel rendelkező tároló létrehozásához lásd: [tároló létrehozása egyéni index-házirenddel a parancssori felület használatával](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>A PowerShell használata

Egyéni indexelési házirenddel rendelkező tároló létrehozásához lásd: [tároló létrehozása egyéni index-házirenddel a PowerShell használatával](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> A .NET SDK használata

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

A `DocumentCollection` [.net SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) objektuma egy olyan tulajdonságot tesz elérhetővé, `IndexingPolicy` amely lehetővé teszi a módosítását, illetve a `IndexingMode` hozzáadását és eltávolítását `IncludedPaths` `ExcludedPaths` .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Az index átalakítási folyamatának nyomon követéséhez adjon át egy `RequestOptions` objektumot, amely a `PopulateQuotaInfo` tulajdonságot állítja be `true` .

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

A `ContainerProperties` [.net SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) objektuma (lásd a [](create-sql-api-dotnet.md) használatról szóló rövid útmutatót) egy olyan tulajdonságot tesz elérhetővé `IndexingPolicy` , amely lehetővé teszi a módosítását, illetve a `IndexingMode` hozzáadását és eltávolítását `IncludedPaths` `ExcludedPaths` .

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Az index átalakítási folyamatának nyomon követéséhez adjon `RequestOptions` meg egy objektumot `PopulateQuotaInfo` , amely a tulajdonságot állítja be `true` , majd kérje le az értéket a `x-ms-documentdb-collection-index-transformation-progress` Válasz fejlécből.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Amikor új tároló létrehozásakor egyéni indexelési szabályzatot határoz meg, az SDK-V3's fluent API lehetővé teszi a definíció tömör és hatékony módon történő megírását:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>A Java SDK használata

A `DocumentCollection` [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) -ból származó objektum ( [](create-sql-api-java.md) lásd a használatról szóló rövid útmutatót) teszi elérhetővé `getIndexingPolicy()` és `setIndexingPolicy()` metódusokat. Az `IndexingPolicy` általuk manipulált objektum lehetővé teszi az indexelési mód módosítását, valamint a felvett és kizárt elérési utak hozzáadását és eltávolítását.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Ha nyomon szeretné követni az index átalakításának folyamatát egy tárolón, adjon meg egy olyan `RequestOptions` objektumot, amely kéri a kvóta-információ feltöltését, majd kérje le az értéket a `x-ms-documentdb-collection-index-transformation-progress` Válasz fejlécből.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Az Node.js SDK használata

Az `ContainerDefinition` [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) felülete (lásd [a](create-sql-api-nodejs.md) használatáról szóló rövid útmutatót) egy olyan `indexingPolicy` tulajdonságot tesz elérhetővé, amely lehetővé teszi a módosítását, illetve a `indexingMode` és a hozzáadását, illetve eltávolítását `includedPaths` `excludedPaths` .

A tároló részleteinek beolvasása

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Az indexelési mód beállítása konzisztens értékre

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Belefoglalt elérési út hozzáadása térbeli indexhez

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Kizárt elérési út hozzáadása

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

A tároló frissítése a módosításokkal

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Ha nyomon szeretné követni az index átalakításának folyamatát egy tárolón, adjon meg egy `RequestOptions` objektumot, amely a `populateQuotaInfo` tulajdonságot állítja be `true` , majd kérje le az értéket a `x-ms-documentdb-collection-index-transformation-progress` Válasz fejlécből.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>A Python SDK használata

# <a name="python-sdk-v3"></a>[Python SDK v3](#tab/pythonv3)

A [PYTHON SDK v3](https://pypi.org/project/azure-cosmos/) használatakor [(lásd a](create-sql-api-python.md) használatról szóló rövid útmutatót) a tároló konfigurációja szótárként van kezelve. Ebből a szótárból elérhető az indexelési házirend és annak összes attribútuma.

A tároló részleteinek beolvasása

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Az indexelési mód beállítása konzisztens értékre

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Egy befoglalt elérési úttal és egy térbeli indexszel rendelkező indexelési szabályzat definiálása

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Az indexelési szabályzat definiálása kizárt elérési úttal

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Összetett index hozzáadása

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

A tároló frissítése a módosításokkal

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK v4](#tab/pythonv4)

A [PYTHON SDK v4](https://pypi.org/project/azure-cosmos/)használatakor a tároló konfigurációja szótárként van kezelve. Ebből a szótárból elérhető az indexelési házirend és annak összes attribútuma.

A tároló részleteinek beolvasása

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Az indexelési mód beállítása konzisztens értékre

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Egy befoglalt elérési úttal és egy térbeli indexszel rendelkező indexelési szabályzat definiálása

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Az indexelési szabályzat definiálása kizárt elérési úttal

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Összetett index hozzáadása

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

A tároló frissítése a módosításokkal

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Az index átalakítási folyamatának beolvasása a válasz fejlécből
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Következő lépések

Az indexeléssel kapcsolatban az alábbi cikkekben olvashat bővebben:

- [Indexelés áttekintése](index-overview.md)
- [Indexelési szabályzat](index-policy.md)
