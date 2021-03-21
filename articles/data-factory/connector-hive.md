---
title: Adatok másolása a kaptárból Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a kaptárból egy Azure Data Factory-folyamat másolási tevékenységével támogatott fogadó adattárakba.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367027"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Adatok másolása és átalakítása a kaptárból a Azure Data Factory használatával 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok struktúrából való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a kaptár-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a kaptárból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a kaptár-összekötőhöz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A kaptár társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **struktúra** | Yes |
| gazda | A kaptár-kiszolgáló IP-címe vagy állomásneve, amely több gazdagép esetében ";" karakterrel elválasztva (csak akkor, ha a serviceDiscoveryMode engedélyezve van).  | Yes |
| port | A kaptár-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. Ha csatlakozik az Azure Hdinsight-hoz, a 443-as portot kell megadnia. | Yes |
| serverType | A kaptár-kiszolgáló típusa. <br/>Az engedélyezett értékek a következők: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | No |
| thriftTransportProtocol | A takarékossági rétegben használandó átviteli protokoll. <br/>Az engedélyezett értékek: **Binary**, **SASL**, **http** | No |
| authenticationType | A kaptár-kiszolgáló eléréséhez használt hitelesítési módszer. <br/>Az engedélyezett értékek: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. A Kerberos-hitelesítés jelenleg nem támogatott. | Yes |
| serviceDiscoveryMode | igaz, ha a ZooKeeper szolgáltatás használatát jelzi, a hamis értéket nem.  | No |
| zooKeeperNameSpace | A ZooKeeper névtér, amely alatt a kaptár-kiszolgáló 2 csomópontja hozzá van adva.  | No |
| useNativeQuery | Megadja, hogy az illesztőprogram natív HiveQL-lekérdezéseket használ-e, vagy a HiveQL-ben egy egyenértékű űrlapra konvertálja őket.  | No |
| username | A kaptár-kiszolgáló eléréséhez használt Felhasználónév.  | No |
| jelszó | A felhasználóhoz tartozó jelszó. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | No |
| httpPath | A kaptár-kiszolgálónak megfelelő részleges URL-cím.  | No |
| enableSsl | Megadja, hogy a kiszolgálóval létesített kapcsolatok titkosítva vannak-e a TLS protokollal. Az alapértelmezett érték a hamis.  | No |
| trustedCertPath | A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazó. PEM fájl teljes elérési útja a kiszolgáló TLS-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha a TLS-t saját üzemeltetésű IR-vel használja. Az alapértelmezett érték az IR-vel telepített hitesítésszolgáltatói. PEM fájl.  | No |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték a hamis.  | No |
| allowHostNameCNMismatch | Megadja, hogy szükséges-e a CA által kiállított TLS/SSL-tanúsítvány neve ahhoz, hogy a kiszolgáló állomásneve megegyezzen a TLS-kapcsolaton keresztül. Az alapértelmezett érték a hamis.  | No |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | No |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |
| storageReference | A Storage-fióknak a leképezési adatfolyamatban való előkészítéséhez használt társított szolgáltatására mutató hivatkozás. Erre csak akkor van szükség, ha a struktúra társított szolgáltatását használja a leképezési folyamat során. | No |

**Példa**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a kaptár-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a kaptárból, állítsa az adatkészlet Type (típus) tulajdonságát **HiveObject** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **HiveObject** | Yes |
| schema | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A tábla neve, beleértve a séma részét. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a kaptár forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="hivesource-as-source"></a>HiveSource forrásként

Az adatok struktúrából való másolásához állítsa a forrás típusát a másolás tevékenység **HiveSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **HiveSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A kaptár-összekötő [beágyazott adatkészlet](data-flow-source.md#inline-datasets) -forrásként támogatott a leképezési adatfolyamatokban. Olvasás lekérdezéssel vagy közvetlenül a HDInsight-ben található struktúra-táblából. A kaptárak az adatfolyamatok részeként történő átalakítás előtt egy Storage-fiókba kerülnek. 

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a kaptár forrása által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tárolás | A tárolónak `hive` | igen |  `hive` | áruház | 
| Formátum | Azt jelzi, hogy egy táblából vagy lekérdezésből olvas-e | igen | `table` vagy `query` | formátumban |
| Séma neve | Ha egy táblából olvas be, a forrástábla sémája |  igen, ha a formátum `table` | Sztring | schemaName |
| Table name (Táblázat neve) | Ha egy táblából olvas be, a tábla neve |   igen, ha a formátum `table` | Sztring | tableName |
| Lekérdezés | Ha a Format `query` , a struktúra társított szolgáltatásának forrás-lekérdezése | igen, ha a formátum `query` | Sztring | lekérdezés |
| Előkészített | A struktúra táblázata mindig előkészítés alatt áll. | igen | `true` | előkészített |
| Storage-tároló | Az adatok a kaptárból való beolvasása vagy a kaptárba való írás előtt használt tároló tároló. A kaptár-fürtnek hozzáféréssel kell rendelkeznie ehhez a tárolóhoz. | igen | Sztring | storageContainer |
| Átmeneti adatbázis | Az a séma/adatbázis, amelyben a társított szolgáltatásban megadott felhasználói fiók hozzáfér. Külső táblák létrehozásához használatos az előkészítés során, majd eldobás után | nem | `true` vagy `false` | stagingDatabaseName |
| Előzetes SQL-parancsfájlok | Az adatolvasás előtt a kaptár táblában futtatandó SQL-kód | nem | Sztring | preSQLs |

#### <a name="source-example"></a>Forrás példa

Az alábbi példa egy struktúra-forrás konfigurációját mutatja be:

![Példa a kaptár forrására](media/data-flow/hive-source.png "[Példa a kaptár forrására")

Ezek a beállítások a következő adatfolyam-szkriptre fordíthatók le:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Ismert korlátozások

* Az összetett típusok, például a tömbök, a térképek, a struktúrák és a szakszervezetek nem támogatottak az olvasáshoz. 
* A kaptár-összekötő csak a 4,0-es vagy újabb verziójú Azure HDInsight támogatja a kaptár-táblákat (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
