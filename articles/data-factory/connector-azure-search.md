---
title: Az Adatmásolás a keresési indexbe
description: Útmutató az Azure Search-indexek adatainak leküldéséhez vagy másolásához egy Azure Data Factory folyamat másolási tevékenységének használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597504"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Adatmásolás Azure Cognitive Search-indexbe Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-search-connector.md)
> * [Aktuális verzió](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatmásoláshoz az Azure Cognitive Search indexbe. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A keresési indexbe bármilyen támogatott forrás-adattárból másolhat adatok. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Azure Cognitive Search connectorhoz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Cognitive Search társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzureSearch** | Yes |
| url | A keresési szolgáltatás URL-címe. | Yes |
| kulcs | A keresési szolgáltatáshoz tartozó rendszergazdai kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

> [!IMPORTANT]
> Amikor az Azure Cognitive Search társított szolgáltatásban az adatok Felhőbeli adattárból történő másolását végzi, a connactVia-ben egy explicit régióval rendelkező Azure Integration Runtime kell hivatkoznia. Állítsa be a régiót, ahol a keresési szolgáltatás található. További információ: [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Példa**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Azure Cognitive Search adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Azure Cognitive Searchba való másoláshoz a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AzureSearchIndex** | Yes |
| indexName | A keresési index neve. Data Factory nem hozza létre az indexet. Az indexnek léteznie kell az Azure Cognitive Searchban. | Yes |

**Példa**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Azure Cognitive Search Source által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-cognitive-search-as-sink"></a>Azure-Cognitive Search fogadóként

Az Azure Cognitive Searchba való másoláshoz állítsa a forrás típusát a másolás tevékenység **AzureSearchIndexSink**. A másolási tevékenység fogadója szakasz a következő  tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AzureSearchIndexSink** | Yes |
| writeBehavior | Meghatározza, hogy a rendszer egyesítse vagy lecserélje, ha már létezik dokumentum az indexben. Tekintse meg a [WriteBehavior tulajdonságot](#writebehavior-property).<br/><br/>Az engedélyezett értékek: **merge** (default) és **upload**. | No |
| writeBatchSize | Adatok feltöltése a keresési indexbe, ha a puffer mérete eléri a writeBatchSize. A részletekért tekintse meg a [WriteBatchSize tulajdonságot](#writebatchsize-property) .<br/><br/>Az engedélyezett értékek: integer 1 – 1 000; az alapértelmezett érték a 1000. | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

### <a name="writebehavior-property"></a>WriteBehavior tulajdonság

AzureSearchSink upsert az adatírás során. Más szóval, amikor egy dokumentum írásakor a dokumentum kulcsa már létezik a keresési indexben, az Azure Cognitive Search frissíti a meglévő dokumentumot ahelyett, hogy ütközést kellene eldobnia.

A AzureSearchSink a következő két upsert-viselkedést biztosítja (a AzureSearch SDK használatával):

- **Egyesítés**: az új dokumentum összes oszlopát egyesítheti a meglévővel. Az új dokumentumban null értékű oszlopok esetén a meglévő érték megmarad.
- **Feltöltés**: az új dokumentum lecseréli a meglévőt. Az új dokumentumban nem megadott oszlopok esetében az érték null, ha a meglévő dokumentumban nem null érték van megadva, vagy nem.

Az alapértelmezett viselkedés az **Egyesítés**.

### <a name="writebatchsize-property"></a>WriteBatchSize tulajdonság

Az Azure Cognitive Search szolgáltatás támogatja a dokumentumok kötegként való írását. Egy köteg 1 – 1 000 műveletet tartalmazhat. Egy művelet kezeli az egyik dokumentumot a feltöltési/egyesítési művelet végrehajtásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Adattípusok támogatása

A következő táblázat meghatározza, hogy az Azure Cognitive Search adattípusa támogatott-e vagy sem.

| Az Azure Cognitive Search adattípusa | Az Azure Cognitive Search fogadóban támogatott |
| ---------------------- | ------------------------------ |
| Sztring | Y |
| Int32 | Y |
| Int64 | Y |
| Dupla | Y |
| Logikai | Y |
| DataTimeOffset | Y |
| Karakterlánc-tömb | N |
| Geographypoint adattípuson | N |

Jelenleg más adattípusok, például a ComplexType nem támogatottak. Az Azure Cognitive Search támogatott adattípusok teljes listájáért lásd: [támogatott adattípusok (azure Cognitive Search)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).