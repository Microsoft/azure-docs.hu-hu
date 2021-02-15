---
title: Adatok másolása a MongoDB Atlasból
description: Megtudhatja, hogyan másolhat adatmásolási tevékenységet a MongoDB Atlas szolgáltatásból egy Azure Data Factory folyamat másolási tevékenységével.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368795"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Adatok másolása a MongoDB Atlasból a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok MongoDB Atlas-adatbázisból történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB Atlas adatbázisából származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a MongoDB Atlas-összekötő **a 4,2-ig támogatja a verziókat**.

## <a name="prerequisites"></a>Előfeltételek

Ha Azure Integration Runtimet használ a másoláshoz, akkor ügyeljen arra, hogy hozzáadja a hatályos régió [Azure Integration Runtime IP](azure-integration-runtime-ip-addresses.md) -címeit a MongoDB Atlas IP-hozzáférési listájához.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a MongoDB Atlas connectorhoz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB Atlas társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **MongoDbAtlas** |Yes |
| connectionString |Határozza meg a MongoDB Atlas-kapcsolatok karakterláncát, például: `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` . <br/><br /> Azure Key Vaultban is elhelyezheti a kapcsolatok karakterláncát. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört. |Yes |
| adatbázis | Az elérni kívánt adatbázis neve. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

**Példa**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). A MongoDB Atlas-adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MongoDbAtlasCollection** | Yes |
| collectionName |A gyűjtemény neve a MongoDB Atlas adatbázisban. |Yes |

**Példa**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a MongoDB Atlas-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MongoDbAtlasSource** | Yes |
| filter (szűrő) | Meghatározza a kiválasztási szűrőt a lekérdezési operátorok használatával. Ha egy gyűjteményben lévő összes dokumentumot vissza szeretné adni, hagyja ki ezt a paramétert, vagy adjon meg egy üres dokumentumot ( {} ). | No |
| cursorMethods. Project | Meghatározza a dokumentumokban a kivetítéshez visszaadni kívánt mezőket. Ha a megfelelő dokumentumokban lévő összes mezőt vissza szeretné adni, hagyja ki ezt a paramétert. | No |
| cursorMethods. sort | Meghatározza, hogy a lekérdezés milyen sorrendben adja vissza a megfelelő dokumentumokat. Tekintse meg a [kurzor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | No |
| cursorMethods. limit | A kiszolgáló által visszaadott dokumentumok maximális számát adja meg. Lásd: [kurzor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | No |
| cursorMethods. skip | Meghatározza a kihagyni kívánt dokumentumok számát, illetve a MongoDB Atlas által az eredmények visszaadásának helyétől kezdődően. Lásd: [kurzor. skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | No |
| batchSize | Meghatározza a MongoDB Atlas-példány válaszának minden egyes kötegében visszaadni kívánt dokumentumok számát. A legtöbb esetben a Batch méretének módosítása nem érinti a felhasználót vagy az alkalmazást. Cosmos DB korlátozza, hogy az egyes kötegek ne lépjék túl a 40MB méretét, ami a dokumentumok méretének batchSize összege, ezért csökkentse ezt az értéket, ha a dokumentum mérete nagy. | No<br/>(az alapértelmezett érték **100**) |

>[!TIP]
>Az ADF támogatja a BSON-dokumentumok **szigorú módban** történő felhasználását. Győződjön meg arról, hogy a szűrő lekérdezése a rendszerhéj mód helyett szigorú módban van. További Leírás a következő helyen található: [MongoDB Manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

## <a name="export-json-documents-as-is"></a>JSON-dokumentumok exportálása a következőképpen:

Ezt a MongoDB Atlas-összekötőt használhatja a JSON-dokumentumok exportálására egy MongoDB Atlas-gyűjteményből különböző file-alapú áruházakba vagy Azure Cosmos DBba. Az ilyen sémák és sémák közötti másoláshoz ugorjon a másolási tevékenységben a "Structure" (más néven *séma*) szakaszra.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha adatokat szeretne másolni a MongoDB Atlasból a táblázatos fogadóba, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
