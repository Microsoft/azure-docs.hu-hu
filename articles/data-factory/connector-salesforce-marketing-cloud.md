---
title: Adatok másolása a Salesforce marketing-felhőből
description: Megtudhatja, hogyan másolhat adatokat a Salesforce marketing-felhőből a fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 161b81b196a1e178c7244845b25594440e6d6e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369747"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Adatok másolása a Salesforce marketing-felhőből Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához a Salesforce marketing-felhőből. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A Salesforce marketing Cloud Connector a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Salesforce marketing-felhőből származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Salesforce marketing Cloud Connector támogatja a OAuth 2 hitelesítést, és támogatja az örökölt és a bővített csomagok típusát is. Az összekötő a [Salesforce marketing Cloud Rest APIra](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)épül.

>[!NOTE]
>Ez az összekötő nem támogatja az egyéni objektumok vagy az egyéni adatbővítmények beolvasását.

## <a name="getting-started"></a>Első lépések

A másolási tevékenységgel rendelkező folyamatokat .NET SDK, Python SDK, Azure PowerShell, REST API vagy Azure Resource Manager sablon használatával is létrehozhatja. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](quickstart-create-data-factory-dot-net.md) talál.

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Salesforce marketing Cloud connectorhoz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Salesforce marketing Cloud társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **SalesforceMarketingCloud** | Yes |
| connectionProperties | A Salesforce marketing-felhőhöz való kapcsolódást meghatározó tulajdonságok csoportja. | Yes |
| ***Alatt `connectionProperties` :*** | | |
| authenticationType | Megadja a használandó hitelesítési módszert. Az engedélyezett értékek a következők: `Enhanced sts OAuth 2.0` vagy `OAuth_2.0` .<br><br>A Salesforce marketing-felhő örökölt csomagja csak `OAuth_2.0` a továbbfejlesztett csomagok igényét támogatja `Enhanced sts OAuth 2.0` . <br>2019. augusztus 1-től a Salesforce marketing Cloud eltávolította a régi csomagok létrehozására való képességet. Minden új csomag továbbfejlesztett csomag. | Yes |
| gazda | A továbbfejlesztett csomag esetében a gazdagépnek a "MC" betűvel kezdődő, 28 karakterből álló karakterláncnak kell lennie az [altartománynak](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) , például `mc563885gzs27c5t9-63k636ttgm` :. <br>Örökölt csomag esetén válassza a következőt: `www.exacttargetapis.com` . | Yes |
| ügyfél-azonosító | A Salesforce marketing Cloud-alkalmazáshoz társított ügyfél-azonosító.  | Yes |
| clientSecret | A Salesforce marketing Cloud-alkalmazáshoz társított ügyfél-titok. Kiválaszthatja, hogy ezt a mezőt SecureString szeretné-e tárolni az ADF-ben, vagy tárolja a titkot a Azure Key Vaultban, és az ADF másolási tevékenység lekérése az adatok másolásával – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Yes |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | No |
| useHostVerification | Megadja, hogy szükséges-e az állomásnév a kiszolgáló tanúsítványában, hogy egyezzen a kiszolgáló állomásneve a TLS-kapcsolaton keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | No |
| usePeerVerification | Megadja, hogy a rendszer ellenőrizze-e a kiszolgáló identitását TLS-kapcsolaton keresztül. Az alapértelmezett érték az igaz.  | No |

**Példa: bővített STS OAuth 2 hitelesítés használata a továbbfejlesztett csomaghoz** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Példa: OAuth 2 hitelesítés használata örökölt csomaghoz** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Ha a Salesforce marketing Cloud társított szolgáltatást használta a következő hasznos adatokkal, akkor továbbra is támogatott, míg a rendszer azt javasolja, hogy az új továbbítási szolgáltatással bővítse a csomag támogatását.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Salesforce marketing-Felhőbeli adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Salesforce marketing-felhőből, állítsa az adatkészlet Type (típus) tulajdonságát **SalesforceMarketingCloudObject** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SalesforceMarketingCloudObject** | Yes |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Salesforce marketing Cloud Source által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce marketing-felhő forrásként

Az adatok Salesforce marketing-felhőből történő másolásához állítsa a másolási tevékenység forrás típusát **SalesforceMarketingCloudSource** értékre. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SalesforceMarketingCloudSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
