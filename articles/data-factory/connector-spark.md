---
title: Adatok másolása a Sparkból
description: Megtudhatja, hogyan másolhat adatokból a Spark-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 0a31ed7a8df080c0e1186ed75f325e36aff32920
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388821"
---
# <a name="copy-data-from-spark-using-azure-data-factory"></a>Adatok másolása a Sparkból Azure Data Factory használatával 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory a Spark-adatok másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Spark-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Sparkból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a Spark-összekötőhöz tartozó Data Factory-entitások definiálására használnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Spark társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Spark** | Yes |
| gazda | A Spark-kiszolgáló IP-címe vagy állomásneve  | Yes |
| port | A Spark-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. Ha csatlakozik az Azure Hdinsight-hoz, a 443-as portot kell megadnia. | Yes |
| serverType | A Spark-kiszolgáló típusa. <br/>Az engedélyezett értékek a következők: **SharkServer**, **SharkServer2**, **SparkThriftServer** | No |
| thriftTransportProtocol | A takarékossági rétegben használandó átviteli protokoll. <br/>Az engedélyezett értékek: **Binary**, **SASL**, **http** | No |
| authenticationType | A Spark-kiszolgáló eléréséhez használt hitelesítési módszer. <br/>Az engedélyezett értékek: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Yes |
| username | A Spark Server eléréséhez használt Felhasználónév.  | No |
| jelszó | A felhasználóhoz tartozó jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | No |
| httpPath | A Spark-kiszolgálónak megfelelő részleges URL-cím.  | No |
| enableSsl | Megadja, hogy a kiszolgálóval létesített kapcsolatok titkosítva vannak-e a TLS protokollal. Az alapértelmezett érték a hamis.  | No |
| trustedCertPath | A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazó. PEM fájl teljes elérési útja a kiszolgáló TLS-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha a TLS-t saját üzemeltetésű IR-vel használja. Az alapértelmezett érték az IR-vel telepített hitesítésszolgáltatói. PEM fájl.  | No |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték a hamis.  | No |
| allowHostNameCNMismatch | Megadja, hogy szükséges-e a CA által kiállított TLS/SSL-tanúsítvány neve ahhoz, hogy a kiszolgáló állomásneve megegyezzen a TLS-kapcsolaton keresztül. Az alapértelmezett érték a hamis.  | No |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | No |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

**Példa**

```json
{
    "name": "SparkLinkedService",
    "properties": {
        "type": "Spark",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Spark-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Sparkból, állítsa az adatkészlet Type (típus) tulajdonságát **SparkObject** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SparkObject** | Yes |
| schema | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. `schema`A és `table` az új számítási feladatok használata. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "SparkDataset",
    "properties": {
        "type": "SparkObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Spark linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Spark-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="spark-as-source"></a>Spark forrásként

Az adatok Sparkból való másolásához állítsa a forrás típusát a másolás tevékenység **SparkSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SparkSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSpark",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Spark input dataset name>",
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
                "type": "SparkSource",
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
