---
title: Adatok másolása Azure Cosmos DB API-MongoDB
description: Ismerje meg, hogy miként másolhatók adatok a támogatott forrás-adattárakból vagy a Azure Cosmos DB API-MongoDB a támogatott fogadó üzletekbe Data Factory használatával.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b13920252b3a5626fd192c6e899154efd31a3de
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893248"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Adatok másolása Azure Cosmos DB API-MongoDB az Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure Cosmos DB API-MongoDB való másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

>[!NOTE]
>Ez az összekötő csak a Azure Cosmos DB API-MongoDB való Adatmásolást támogatja. Az SQL API esetében tekintse meg az [Cosmos db SQL API-összekötőt](connector-azure-cosmos-db.md). Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Cosmos DB API-MongoDB bármely támogatott fogadó adattárba másolhatja az adatait, vagy bármely támogatott forrás adattárból átmásolhatja az adatAzure Cosmos DB API-ját a MongoDB. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

A MongoDB-összekötő Azure Cosmos DB API-ját a következőre használhatja:

- Adatok másolása a és a rendszerből a [Azure Cosmos db API-MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Írás a Azure Cosmos DB **Insert** vagy **upsert**.
- JSON-dokumentumok importálása és exportálása, illetve adatok másolása táblázatos adatkészletbe vagy másolással. Ilyenek például az SQL Database és a CSV-fájlok. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a Azure Cosmos DB API-MongoDB tartozó entitások definiálásához használhat Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB társított szolgáltatáshoz tartozó Azure Cosmos DB API-k esetében a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **CosmosDbMongoDbApi**értékre kell beállítani. | Igen |
| connectionString |Adja meg a Azure Cosmos DB API-MongoDB tartozó kapcsolatok karakterláncát. A Azure Portal > a Cosmos DB > elsődleges vagy másodlagos kapcsolatok karakterláncát, `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`mintázatával. <br/><br />A Azure Key Vaultban jelszót is beállíthat, és lekérheti a `password` konfigurációját a kapcsolatok karakterláncán kívülre. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vault](store-credentials-in-key-vault.md) ban című témakört.|Igen |
| adatbázis | Az elérni kívánt adatbázis neve. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhatja a Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |Nem |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). A MongoDB-adatkészlet Azure Cosmos DB API-jával a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **CosmosDbMongoDbApiCollection**értékre kell állítani. |Igen |
| collectionName |A Azure Cosmos DB gyűjtemény neve. |Igen |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB API-ját a MongoDB forrás-és fogadó támogatásához.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB MongoDB API-ját forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **CosmosDbMongoDbApiSource**értékre kell állítani. |Igen |
| szűrő | Meghatározza a kiválasztási szűrőt a lekérdezési operátorok használatával. Ha egy gyűjteményben lévő összes dokumentumot vissza szeretné adni, hagyja ki ezt a paramétert, vagy adjon át üres dokumentumot ({}). | Nem |
| cursorMethods. Project | Meghatározza a dokumentumokban a kivetítéshez visszaadni kívánt mezőket. Ha a megfelelő dokumentumokban lévő összes mezőt vissza szeretné adni, hagyja ki ezt a paramétert. | Nem |
| cursorMethods. sort | Meghatározza, hogy a lekérdezés milyen sorrendben adja vissza a megfelelő dokumentumokat. Tekintse meg a [kurzor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nem |
| cursorMethods. limit | A kiszolgáló által visszaadott dokumentumok maximális számát adja meg. Lásd: [kurzor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nem | 
| cursorMethods. skip | Meghatározza a kihagyni kívánt dokumentumok számát, valamint a MongoDB az eredmények visszaadásának helyét. Lásd: [kurzor. skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nem |
| batchSize | Meghatározza a MongoDB-példány válaszának egyes kötegében visszaadni kívánt dokumentumok számát. A legtöbb esetben a Batch méretének módosítása nem érinti a felhasználót vagy az alkalmazást. Cosmos DB korlátozza, hogy az egyes kötegek ne lépjék túl a 40MB méretét, ami a dokumentumok méretének batchSize összege, ezért csökkentse ezt az értéket, ha a dokumentum mérete nagy. | Nem<br/>(az alapértelmezett érték **100**) |

>[!TIP]
>Az ADF támogatja a BSON-dokumentumok **szigorú módban**történő felhasználását. Győződjön meg arról, hogy a szűrő lekérdezése a rendszerhéj mód helyett szigorú módban van. További Leírás a következő helyen található: [MongoDB Manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB API a MongoDB as fogadóként

A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **CosmosDbMongoDbApiSink**értékre kell állítani. |Igen |
| writeBehavior |Ismerteti, hogyan lehet az Azure Cosmos DBba írni az adatbevitelt. Megengedett értékek: **Insert** és **upsert**.<br/><br/>A **upsert** viselkedése a dokumentum cseréje, ha már létezik azonos `_id`ú dokumentum. Ha nem, szúrja be a dokumentumot.<br /><br />**Megjegyzés**: a Data Factory automatikusan létrehoz egy `_id` a dokumentumhoz, ha az eredeti dokumentumban vagy az oszlop-hozzárendelésben nincs megadva `_id`. Ez azt jelenti, hogy meg kell győződnie arról, hogy a **upsert** a várt módon működnek, a dokumentum azonosítója. |Nem<br />(az alapértelmezett érték a **Beszúrás**) |
| writeBatchSize | A **writeBatchSize** tulajdonság az egyes kötegekben írandó dokumentumok méretét határozza meg. A **writeBatchSize** értékének növelésével növelheti a teljesítményt és csökkentheti az értéket, ha a dokumentum mérete nagy. |Nem<br />(az alapértelmezett érték **10 000**) |
| writeBatchTimeout | Az a várakozási idő, ameddig a kötegelt beszúrási művelet befejezi az időtúllépést. Az engedélyezett érték a TimeSpan. | Nem<br/>(az alapértelmezett érték **00:30:00** – 30 perc) |

>[!TIP]
>Ha JSON-dokumentumokat szeretne importálni, tekintse meg a [JSON-dokumentumok importálása vagy exportálása](#import-and-export-json-documents) szakaszt; a táblázatos adatokból történő másoláshoz tekintse meg a [séma-hozzárendelést](#schema-mapping).

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Ezt a Azure Cosmos DB összekötőt egyszerűen elvégezheti:

* Dokumentumok másolása két Azure Cosmos DB gyűjtemény között.
* A különböző forrásokból származó JSON-dokumentumokat importálhatja Azure Cosmos DBba, többek között a MongoDB, az Azure Blob Storage-ból, a Azure Data Lake Storeból és más, a Azure Data Factory által támogatott fájl-alapú áruházakból.
* JSON-dokumentumok exportálása Azure Cosmos DB gyűjteményből különböző file-alapú áruházakba.

Séma – agnosztikus másolás:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás másként lehetőséget a JSON-fájlok vagy a Cosmos db-gyűjtemény** lehetőségre.
* Ha tevékenység-létrehozást használ, válassza a JSON formátum elemet a forrás vagy a fogadó megfelelő fájljával.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha Azure Cosmos DB API-ból szeretne adatokat másolni táblázatos MongoDB vagy fordított értékre, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

Kifejezetten a Cosmos DBba való íráshoz, hogy megbizonyosodjon róla, hogy feltölti Cosmos DB a forrásadatok jobb oldali azonosítójával, például az SQL Database tábla "id" oszlopát használja, és azt szeretné használni, hogy az INSERT/upsert MongoDB tartozó dokumentum-AZONOSÍTÓként adja meg a megfelelő`_id.$oid`MongoDB a következő módon:

![Térkép azonosítója a MongoDB-fogadóban](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

A másolási tevékenység végrehajtása után a BSON ObjectId a fogadóban jön létre:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintheti meg. lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
