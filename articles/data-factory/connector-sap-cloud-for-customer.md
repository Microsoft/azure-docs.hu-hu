---
title: Adatok másolása az SAP-felhőbe vagy az ügyfél számára
description: Megtudhatja, hogyan másolhat adatok az SAP-felhőből az ügyfelek által támogatott fogadó adattárakba (vagy) a támogatott forrás-adattárakból az SAP-felhőbe az ügyfelek számára a Data Factory használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: 9578b87e16f418a7923cd71aa0638fa4e9279cfd
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430882"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Adatok másolása az SAP Cloud for Customer (C4C) szolgáltatásból Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok az SAP-felhőbe való másolásához az ügyfél számára (C4C). A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) című témakört, amely részletesen ismerteti az egyes SAP-összekötőket, a comparsion és

## <a name="supported-capabilities"></a>Támogatott képességek

Az ügyfél-összekötőhöz tartozó SAP-felhő a következő tevékenységeknél támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az SAP-felhőből származó adatok az ügyfélnek bármilyen támogatott fogadó adattárba másolhatók, vagy bármely támogatott forrás-adattárból másolhatók adatok az SAP Cloud for Customer szolgáltatásba. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az összekötő lehetővé teszi, hogy Azure Data Factory az adatok másolását az SAP-felhőbe, az SAP Cloud for Sales, az SAP Cloud for Service és az SAP Cloud for Service szolgáltatással, valamint a közösségi szerepvállalási megoldásokhoz.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket az SAP Cloud for Customer Connector szolgáltatáshoz tartozó entitások definiálásához használtak Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SAP Cloud for Customer társított szolgáltatáshoz a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomer**. | Igen |
| url | Az SAP-C4C OData szolgáltatásának URL-címe. | Igen |
| username | Adja meg az SAP-C4C való kapcsolódáshoz használandó felhasználónevet. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem |

**Példa**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az SAP Cloud for Customer adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha az SAP-felhőből szeretne adatokat másolni az ügyfél számára, állítsa az adatkészlet Type (típus) tulajdonságát **SapCloudForCustomerResource** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SapCloudForCustomerResource** |Igen |
| path | Az SAP C4C OData entitás elérési útjának megadása. |Igen |

**Példa**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az SAP Cloud által az ügyfél forrásaként támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-c4c-as-source"></a>SAP-C4C forrásként

Az SAP felhőből az ügyfélnek történő adatmásoláshoz állítsa a forrás típusát a másolás tevékenység **SapCloudForCustomerSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomerSource**  | Igen |
| lekérdezés | Az adatolvasáshoz válassza az egyéni OData-lekérdezést. | Nem |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Ha nincs megadva, az alapértelmezett érték **00:30:00** (30 perc). | Nem |

Példa lekérdezésre egy adott napra vonatkozó adat lekéréséhez: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP-C4C fogadóként

Ha az ügyfél számára szeretne Adatmásolást készíteni az SAP-felhőbe, a másolási tevékenységben állítsa be a fogadó típusát a **SapCloudForCustomerSink** értékre. A másolási tevékenység fogadója szakasz a következő  tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **SapCloudForCustomerSink**  | Igen |
| writeBehavior | A művelet írási viselkedése. Lehet "Insert", "Update". | Nem. Alapértelmezett "Beszúrás". |
| writeBatchSize | Az írási művelet kötegének mérete. A legjobb teljesítmény eléréséhez használt köteg mérete eltérő lehet a különböző táblák vagy kiszolgálók esetében. | Nem. Alapértelmezett 10. |

**Példa**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Adattípusok leképezése az SAP Cloud ügyfél számára

Az SAP-felhőből az ügyfélnek történő adatmásoláskor a következő leképezések használhatók az SAP Cloud for Customer adattípusokhoz az ideiglenes adattípusok Azure Data Factoryához. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| Az SAP C4C OData adattípusa | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| EDM. Binary | Bájt [] |
| Edm.Boolean | Logikai |
| EDM. byte | Bájt [] |
| EDM. DateTime | DateTime |
| EDM. decimális | Tizedesjegy |
| Edm.Double | Dupla |
| EDM. Single | Egyirányú |
| EDM. GUID | Guid |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. sbyte érték | Int16 |
| Edm.String | Sztring |
| EDM. Time | időtartam |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
