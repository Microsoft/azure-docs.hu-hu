---
title: Adatmásolás és átalakítás Azure Database for MySQL
description: megtudhatja, hogyan másolhat és alakíthat át Azure Database for MySQLba Azure Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 4d13f6f435a21b467cae1b8e14211a001792787f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012605"
---
# <a name="copy-and-transform-data-in-azure-database-for-mysql-by-using-azure-data-factory"></a>Azure Database for MySQL adatmásolása és átalakítása a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához és a Azure Database for MySQLához, valamint az adatfolyamatok használata az adatok Azure Database for MySQLban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

Ez az összekötő [Azure Database for MySQL szolgáltatásra](../mysql/overview.md)specializálódott. A helyszínen vagy a felhőben található általános MySQL-adatbázisból származó adatok másolásához használja a [MySQL-összekötőt](connector-mysql.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for MySQL-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek Data Factory Azure Database for MySQL-összekötőhöz tartozó entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure Database for MySQL társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzureMySql** | Yes |
| connectionString | Az Azure Database for MySQL-példányhoz való kapcsolódáshoz szükséges adatok meghatározása. <br/> A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `password` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

Egy tipikus kapcsolatok karakterlánca: `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . További tulajdonságok állíthatók be az egyes esetekben:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| SSLMode | Ezzel a beállítással adható meg, hogy az illesztőprogram TLS titkosítást és ellenőrzést használ-e a MySQL-hez való kapcsolódáskor. Például `SSLMode=<0/1/2/3/4>`| Letiltva (0)/ELŐNYben részesített (1) **(alapértelmezett)** /kötelező (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | No |
| UseSystemTrustStore | Ezzel a beállítással adható meg, hogy a rendszer egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ-e a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból. Például `UseSystemTrustStore=<0/1>;`| Engedélyezve (1)/Letiltva (0) **(alapértelmezett)** | No |

**Példa**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a Jelszó tárolása Azure Key Vault**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Azure Database for MySQL adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Azure Database for MySQLból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **AzureMySqlTable** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **AzureMySqlTable** | Yes |
| tableName | A MySQL-adatbázisban található tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Azure Database for MySQL forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-mysql-as-source"></a>Azure Database for MySQL forrásként

Az adatok Azure Database for MySQLból történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **AzureMySqlSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |
| queryCommandTimeout | A lekérdezési kérelem időtúllépése előtti várakozási idő. Az alapértelmezett érték 120 perc (02:00:00) | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>Azure Database for MySQL fogadóként

Az adatAzure Database for MySQLba való másoláshoz a másolási tevékenység fogadója szakasz a  következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **AzureMySqlSink** | Yes |
| preCopyScript | Adja meg a másolási tevékenység végrehajtásához szükséges SQL-lekérdezést, mielőtt az egyes futtatások Azure Database for MySQLbe írna. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | No |
| writeBatchSize | Az Azure Database for MySQL táblázatba szúrja be az adatmennyiséget, amikor a puffer mérete eléri a writeBatchSize.<br>Az engedélyezett érték olyan egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 10 000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br>Az engedélyezett értékek a TimeSpan. Például 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:00:30) |

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatoknak a leképezési folyamatba való átalakításakor a táblázatok Azure Database for MySQLból való olvasását és írását is elvégezheti. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban. Dönthet úgy, hogy Azure Database for MySQL adatkészletet vagy egy [beágyazott adatkészletet](data-flow-source.md#inline-datasets) használ forrásként és fogadó típusúként.

### <a name="source-transformation"></a>Forrás-átalakítás

Az alábbi táblázat a Azure Database for MySQL forrás által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tábla | Ha a tábla bemenetként lehetőséget választja, az adatfolyam beolvassa az adatkészletben megadott tábla összes adatát. | No | - |*(csak a beágyazott adatkészletek esetében)*<br>tableName |
| Lekérdezés | Ha a lekérdezés bemenetként lehetőséget választja, adjon meg egy SQL-lekérdezést az adatok forrásból való beolvasásához, amely felülbírálja az adatkészletben megadott összes táblát. A lekérdezések használatával csökkentheti a sorok tesztelési vagy keresési lehetőségeit.<br><br>Az **Order by** záradék nem támogatott, de beállíthat egy teljes select from utasítást. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF, amely az SQL-ben egy olyan táblázatot ad vissza, amely az adatfolyamban használható.<br>Példa lekérdezésre: `select * from mytable where customerId > 1000 and customerId < 2000` vagy `select * from "MyTable"` .| Nem | Sztring | lekérdezés |
| Köteg mérete | A nagyméretű adathalmazok kötegbe való darabolásához Definiáljon egy batch-méretet. | No | Egész szám | batchSize |
| Elkülönítési szint | Válasszon egyet a következő elkülönítési szintek közül:<br>– Olvasás véglegesítve<br>– Nem véglegesített olvasás (alapértelmezett)<br>– Ismételhető olvasás<br>– Szerializálható<br>-None (az elkülönítési szint figyelmen kívül hagyása) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SZERIALIZÁLHATÓ<br/>NEZ egy</small> |isolationLevel |

#### <a name="azure-database-for-mysql-source-script-example"></a>Példa Azure Database for MySQL forrás parancsfájlra

A Azure Database for MySQL forrásként való használatakor a társított adatfolyam-parancsfájl a következő:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzureMySQLSource
```

### <a name="sink-transformation"></a>Fogadó transzformáció

Az alábbi táblázat felsorolja a Azure Database for MySQL fogadó által támogatott tulajdonságokat. Ezeket a tulajdonságokat a fogadó **beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Frissítési módszer | Adja meg, hogy milyen műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése.<br>Sorok frissítéséhez, upsert vagy törléséhez [módosítási sor átalakítására](data-flow-alter-row.md) van szükség a műveletek sorainak címkézéséhez. | Yes | `true` vagy `false` | törölhető <br/>Insertable <br/>frissíthető <br/>upsertable |
| Kulcsok oszlopai | A frissítések, a upsert és a törlések esetében meg kell adni a kulcs oszlop (oka) t, hogy meghatározza, melyik sort kell megváltoztatni.<br>A kulcsként kiválasztott oszlopnevet a következő frissítés, upsert, törlés részeként fogja használni a rendszer. Ezért ki kell választania egy oszlopot, amely szerepel a fogadó leképezésében. | No | Tömb | keys |
| Kulcsok oszlopainak kihagyása | Ha nem szeretné az értéket a kulcs oszlopba írni, válassza a "kulcs oszlopainak kihagyása" lehetőséget. | No | `true` vagy `false` | skipKeyWrites |
| Tábla művelete |Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.<br>- **Nincs**: a rendszer nem hajt végre műveletet a táblán.<br>- **Újból létrehozva**: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.<br>- **Csonkítás**: a céltábla összes sora el lesz távolítva. | No | `true` vagy `false` | hozza létre újra<br/>truncate |
| Köteg mérete | Itt adhatja meg, hogy hány sort kell írni az egyes kötegekben. A nagyobb méretű kötegek növelik a tömörítési és a memória-optimalizálást, de a gyorsítótárban tárolt adatmennyiség miatt kifogytak a memória | No | Egész szám | batchSize |
| SQL-parancsfájlok előtti és utáni | Adja meg azokat a többsoros SQL-parancsfájlokat, amelyeket a rendszer a (pre-Processing) és a (post-Processing) (utómunka) adatainak a fogadó adatbázisba való írása előtt hajt végre. | Nem | Sztring | preSQLs<br>postSQLs |

#### <a name="azure-database-for-mysql-sink-script-example"></a>Példa Azure Database for MySQL fogadó parancsfájlra

Ha a Azure Database for MySQL a fogadó típusaként használja, a társított adatfolyam-parancsfájl a következő:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzureMySQLSink
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>Azure Database for MySQL adattípusának leképezése

Az adatok Azure Database for MySQLból való másolása során a rendszer a következő leképezéseket használja a MySQL-adattípusokból Azure Data Factory ideiglenes adattípusokhoz. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| Azure Database for MySQL adattípus | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
