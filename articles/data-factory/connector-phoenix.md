---
title: Adatok másolása a Phoenixből Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a Phoenixből egy Azure Data Factory-folyamat másolási tevékenységének használatával a támogatott fogadó adattárakba.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 128946562fb3976bcb98de6c0911b7f89805b149
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680432"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Adatok másolása a Phoenixből Azure Data Factory használatával 

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Phoenixből való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Phoenix-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Phoenixből bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Phoenix-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Phoenixhez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Phoenix** | Igen |
| gazdagép | A Phoenix-kiszolgáló IP-címe vagy állomásneve. (192.168.222.160)  | Igen |
| port | A Phoenix-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. Az alapértelmezett érték a 8765. Ha csatlakozik az Azure Hdinsight-hoz, a 443-as portot kell megadnia. | Nem |
| httpPath | A Phoenix-kiszolgálónak megfelelő részleges URL-cím. (azaz/Gateway/sandbox/Phoenix/Version). Hdinsight-fürt használata esetén `/hbasephoenix0`t kell megadni.  | Nem |
| authenticationType | A Phoenix-kiszolgálóhoz való kapcsolódáshoz használt hitelesítési módszer. <br/>Az engedélyezett értékek: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Igen |
| felhasználónév | A Phoenix-kiszolgálóhoz való kapcsolódáshoz használt Felhasználónév.  | Nem |
| jelszó | A felhasználónévnek megfelelő jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| enableSsl | Meghatározza, hogy a kiszolgálóval létesített kapcsolatok SSL használatával legyenek titkosítva. Az alapértelmezett érték false (hamis).  | Nem |
| trustedCertPath | A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazó. PEM fájl teljes elérési útja a kiszolgáló SSL-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, amikor SSL-t használ a saját üzemeltetésű IR-ben. Az alapértelmezett érték az IR-vel telepített hitesítésszolgáltatói. PEM fájl.  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték false (hamis).  | Nem |
| allowHostNameCNMismatch | Megadja, hogy szükséges-e a HITELESÍTÉSSZOLGÁLTATÓ által kiadott SSL-tanúsítvány neve ahhoz, hogy a kiszolgáló állomásneve egyezzen az SSL-kapcsolaton keresztül. Az alapértelmezett érték false (hamis).  | Nem |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték false (hamis).  | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

>[!NOTE]
>Ha a fürt nem támogatja a ragadós munkamenetet (pl. HDInsight), explicit módon adja hozzá a csomópont-indexet a http-elérésiút-beállítás végén, például adja meg a `/hbasephoenix0`t a `/hbasephoenix`helyett.

**Példa**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Phoenix-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Phoenixből való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **PhoenixObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **PhoenixObject** | Igen |
| Séma | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Új számítási feladatokhoz használjon `schema` és `table`. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Phoenix-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="phoenix-as-source"></a>Phoenix forrásként

Az adatok Phoenixből való másolásához állítsa a forrás típusát a másolás tevékenység **PhoenixSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **PhoenixSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
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

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
