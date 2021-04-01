---
title: Adatok másolása a ServiceNow
description: Bemutatjuk, hogy miként másolhatók adatok a ServiceNow-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 4e7ebc422a9fd8503c5a3b004e1d06cb5ebfb987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378451"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>Adatok másolása a ServiceNow a Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok ServiceNow való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a ServiceNow-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a ServiceNow bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az ServiceNow-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A ServiceNow társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **ServiceNow** | Yes |
| endpoint | A ServiceNow-kiszolgáló () végpontja `http://<instance>.service-now.com` .  | Yes |
| authenticationType | A használni kívánt hitelesítési típus. <br/>Az engedélyezett értékek a következők: **Alapszintű**, **OAuth2** | Yes |
| username | Az alap-és OAuth2 hitelesítéshez a ServiceNow-kiszolgálóhoz való kapcsolódáshoz használt Felhasználónév.  | Yes |
| jelszó | Az alapszintű és a OAuth2 hitelesítéshez tartozó felhasználónévhez tartozó jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| ügyfél-azonosító | A OAuth2-hitelesítés ügyfél-azonosítója.  | No |
| clientSecret | A OAuth2-hitelesítéshez használt ügyfél titka. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | No |
| useHostVerification | Megadja, hogy szükséges-e az állomásnév a kiszolgáló tanúsítványában, hogy egyezzen a kiszolgáló állomásneve a TLS-kapcsolaton keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | No |
| usePeerVerification | Megadja, hogy a rendszer ellenőrizze-e a kiszolgáló identitását TLS-kapcsolaton keresztül. Az alapértelmezett érték az igaz.  | No |

**Példa**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a ServiceNow adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok ServiceNow való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **ServiceNowObject** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **ServiceNowObject** | Yes |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a ServiceNow forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="servicenow-as-source"></a>ServiceNow forrásként

Az adatok ServiceNow való másolásához állítsa a forrás típusát a másolás tevékenység **ServiceNowSource** értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **ServiceNowSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM Actual.alm_asset"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

A lekérdezésben a ServiceNow sémájának és oszlopának megadásakor vegye figyelembe a következőket, és **tekintse meg a [teljesítményre vonatkozó tippeket](#performance-tips) a másolás teljesítményének következményeiről**.

- **Séma:** határozza meg a sémát a `Actual` `Display` ServiceNow-lekérdezésben vagy a (z) mezőben, amelyet a `sysparm_display_value` [ServiceNow REST API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET)-k hívásakor igaz vagy hamis paraméterként is megtekintheti. 
- **Oszlop:** a séma alatt lévő tényleges érték oszlopának neve `Actual` `[column name]_value` , míg a (z) séma területen a megjelenítési érték `Display` szerepel `[column name]_display_value` . Vegye figyelembe, hogy az oszlop nevének leképezése szükséges a lekérdezésben használt sémához.

**Példa lekérdezésre:** 
 `SELECT col_value FROM Actual.alm_asset` VAGY`SELECT col_display_value FROM Display.alm_asset`

**Példa**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>Teljesítménnyel kapcsolatos tippek

### <a name="schema-to-use"></a>Használandó séma

A ServiceNow 2 különböző sémával rendelkezik, az egyik **"tényleges"** , amely tényleges adatokat ad vissza, a másik pedig a **"Display"** , amely az adatok megjelenítési értékeit adja vissza. 

Ha van egy szűrő a lekérdezésben, használja a "tényleges" sémát, amely jobb másolási teljesítményt tartalmaz. A "tényleges" séma lekérdezése során a ServiceNow natív módon támogatja a szűrést, amikor az adatok beolvasásakor csak a szűrt eredményhalmazt adja vissza, míg a "Display" séma lekérdezése során az ADF beolvassa az összes adatokat, és belsőleg alkalmazza a szűrőt.

### <a name="index"></a>Index

A ServiceNow táblázat indexe segíthet a lekérdezési teljesítmény javításában, lásd: [Table index létrehozása](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html).

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
