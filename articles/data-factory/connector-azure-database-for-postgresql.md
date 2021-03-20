---
title: Adatmásolás és átalakítás Azure Database for PostgreSQL
description: Megtudhatja, hogyan másolhat és alakíthat át Azure Database for PostgreSQLba Azure Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: ec4ea645e325ef48d4cb5951cd39fd4e9cbe1617
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738055"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Azure Database for PostgreSQL adatmásolása és átalakítása a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához és a Azure Database for PostgreSQLához, valamint az adatfolyamatok használata az adatok Azure Database for PostgreSQLban való átalakításához. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

Ez az összekötő a [Azure Database for PostgreSQL szolgáltatásra](../postgresql/overview.md)specializálódott. A helyszíni vagy a felhőben található általános PostgreSQL-adatbázisból származó adatok másolásához használja a [PostgreSQL-összekötőt](connector-postgresql.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Database for PostgreSQL-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatfolyam jelenleg a PostgreSQL-hez készült Azure Database-t támogatja, de nem rugalmas kiszolgáló-vagy nagy kapacitású (Citus).

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő fejezetekben a Azure Database for PostgreSQL-összekötőhöz tartozó entitások definiálásához használt tulajdonságok részletes ismertetését találhatja Data Factory meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Database for PostgreSQL társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **AzurePostgreSql**. | Yes |
| connectionString | Az Azure Database for PostgreSQLhoz való kapcsolódáshoz használandó ODBC-kapcsolati karakterlánc.<br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `password` konfigurációt a kapcsolatok karakterláncáról. További részletekért tekintse meg a következő mintákat, és [tárolja Azure Key Vault a hitelesítő adatokat](store-credentials-in-key-vault.md) . | Yes |
| Connectvia tulajdonsággal | Ez a tulajdonság az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modult jelöli. Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár a magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

Egy tipikus kapcsolatok karakterlánca: `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Az alábbiakban több tulajdonságot is beállíthat:

| Tulajdonság | Leírás | Beállítások | Kötelező |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Az illesztőprogram és az adatbázis-kiszolgáló között továbbított adattitkosításhoz használt metódus. Például:  `EncryptionMethod=<0/1/6>;`| 0 (nincs titkosítás) **(alapértelmezett)** /1 (SSL)/6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | Meghatározza, hogy az illesztőprogram érvényesítse-e az adatbázis-kiszolgáló által az SSL-titkosítás engedélyezésekor eljuttatott tanúsítványt (titkosítási módszer = 1). Például:  `ValidateServerCertificate=<0/1>;`| 0 (letiltva) **(alapértelmezett)** /1 (engedélyezve) | No |

**Példa**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Példa**:

***Jelszó tárolása Azure Key Vaultban***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listájáért lásd: [Adatkészletek Azure Data Factoryban](concepts-datasets-linked-services.md). Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Database for PostgreSQL támogat az adatkészletekben.

Az adatok Azure Database for PostgreSQLból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **AzurePostgreSqlTable** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **AzurePostgreSqlTable** értékre kell beállítani. | Yes |
| tableName | A tábla neve | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a következő témakörben találja: [folyamatok és tevékenységek a Azure Data Factoryban](concepts-pipelines-activities.md). Ez a szakasz a Azure Database for PostgreSQL forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-database-for-postgresql-as-source"></a>Azure-adatbázis PostgreSql-hez forrásként

Az adatok Azure Database for PostgreSQLból való másolásához állítsa a forrás típusát a másolás tevékenység **AzurePostgreSqlSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **AzurePostgreSqlSource** értékre kell állítani. | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `SELECT * FROM mytable` vagy `SELECT * FROM "MyTable"` . Megjegyzés a PostgreSQL-ben a rendszer az entitás nevét kis-és nagybetűket nem megkülönböztetőként kezeli, ha nem idézi elő. | Nem (ha az adatkészlet táblanév tulajdonsága meg van adva) |

**Példa**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL fogadóként

Az adatAzure Database for PostgreSQLba való másoláshoz a másolási tevékenység fogadója szakasz a  következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **AzurePostgreSQLSink** értékre kell állítani. | Yes |
| preCopyScript | Adja meg azt az SQL-lekérdezést, amelyet végre szeretne hajtani a másolási tevékenységhez, mielőtt az egyes futtatások Azure Database for PostgreSQLba írna. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. | No |
| writeMethod | Az adatAzure Database for PostgreSQLba való íráshoz használt metódus.<br>Az engedélyezett értékek a következők: **CopyCommand** (előzetes verzió, amely nagyobb teljesítményű), **BulkInsert** (alapértelmezett). | No |
| writeBatchSize | A kötegelt Azure Database for PostgreSQLba betöltött sorok száma.<br>Az engedélyezett érték egy egész szám, amely a sorok számát jelöli. | Nem (az alapértelmezett érték 1 000 000) |
| writeBatchTimeout | Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br>Az engedélyezett értékek a TimeSpan karakterláncok. Például 00:30:00 (30 perc). | Nem (az alapértelmezett érték 00:30:00) |

**Példa**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatoknak a leképezési folyamatba való átalakításakor a táblázatok Azure Database for PostgreSQLból való olvasását és írását is elvégezheti. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban. Dönthet úgy, hogy Azure Database for PostgreSQL adatkészletet vagy egy [beágyazott adatkészletet](data-flow-source.md#inline-datasets) használ forrásként és fogadó típusúként.

### <a name="source-transformation"></a>Forrás-átalakítás

Az alábbi táblázat a Azure Database for PostgreSQL forrás által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tábla | Ha a tábla bemenetként lehetőséget választja, az adatfolyam beolvassa az adatkészletben megadott tábla összes adatát. | No | - |*(csak a beágyazott adatkészletek esetében)*<br>tableName |
| Lekérdezés | Ha a lekérdezés bemenetként lehetőséget választja, adjon meg egy SQL-lekérdezést az adatok forrásból való beolvasásához, amely felülbírálja az adatkészletben megadott összes táblát. A lekérdezések használatával csökkentheti a sorok tesztelési vagy keresési lehetőségeit.<br><br>Az **Order by** záradék nem támogatott, de beállíthat egy teljes select from utasítást. A felhasználó által definiált Table functions is használható. a **select * from udfGetData ()** egy olyan UDF, amely az SQL-ben egy olyan táblázatot ad vissza, amely az adatfolyamban használható.<br>Példa lekérdezésre: `select * from mytable where customerId > 1000 and customerId < 2000` vagy `select * from "MyTable"` . Megjegyzés a PostgreSQL-ben a rendszer az entitás nevét kis-és nagybetűket nem megkülönböztetőként kezeli, ha nem idézi elő.| Nem | Sztring | lekérdezés |
| Köteg mérete | A nagyméretű adathalmazok kötegbe való darabolásához Definiáljon egy batch-méretet. | No | Egész szám | batchSize |
| Elkülönítési szint | Válasszon egyet a következő elkülönítési szintek közül:<br>– Olvasás véglegesítve<br>– Nem véglegesített olvasás (alapértelmezett)<br>– Ismételhető olvasás<br>– Szerializálható<br>-None (az elkülönítési szint figyelmen kívül hagyása) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SZERIALIZÁLHATÓ<br/>NEZ egy</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Példa Azure Database for PostgreSQL forrás parancsfájlra

A Azure Database for PostgreSQL forrásként való használatakor a társított adatfolyam-parancsfájl a következő:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Fogadó transzformáció

Az alábbi táblázat felsorolja a Azure Database for PostgreSQL fogadó által támogatott tulajdonságokat. Ezeket a tulajdonságokat a fogadó **beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Frissítési módszer | Adja meg, hogy milyen műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése.<br>Sorok frissítéséhez, upsert vagy törléséhez [módosítási sor átalakítására](data-flow-alter-row.md) van szükség a műveletek sorainak címkézéséhez. | Yes | `true` vagy `false` | törölhető <br/>Insertable <br/>frissíthető <br/>upsertable |
| Kulcsok oszlopai | A frissítések, a upsert és a törlések esetében meg kell adni a kulcs oszlop (oka) t, hogy meghatározza, melyik sort kell megváltoztatni.<br>A kulcsként kiválasztott oszlopnevet a következő frissítés, upsert, törlés részeként fogja használni a rendszer. Ezért ki kell választania egy oszlopot, amely szerepel a fogadó leképezésében. | No | Tömb | keys |
| Kulcsok oszlopainak kihagyása | Ha nem szeretné az értéket a kulcs oszlopba írni, válassza a "kulcs oszlopainak kihagyása" lehetőséget. | No | `true` vagy `false` | skipKeyWrites |
| Tábla művelete |Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.<br>- **Nincs**: a rendszer nem hajt végre műveletet a táblán.<br>- **Újból létrehozva**: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.<br>- **Csonkítás**: a céltábla összes sora el lesz távolítva. | No | `true` vagy `false` | hozza létre újra<br/>truncate |
| Köteg mérete | Itt adhatja meg, hogy hány sort kell írni az egyes kötegekben. A nagyobb méretű kötegek növelik a tömörítési és a memória-optimalizálást, de a gyorsítótárban tárolt adatmennyiség miatt kifogytak a memória | No | Egész szám | batchSize |
| SQL-parancsfájlok előtti és utáni | Adja meg azokat a többsoros SQL-parancsfájlokat, amelyeket a rendszer a (pre-Processing) és a (post-Processing) (utómunka) adatainak a fogadó adatbázisba való írása előtt hajt végre. | Nem | Sztring | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Példa Azure Database for PostgreSQL fogadó parancsfájlra

Ha a Azure Database for PostgreSQL a fogadó típusaként használja, a társított adatfolyam-parancsfájl a következő:

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
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságokkal kapcsolatos további információkért lásd: [keresési tevékenység Azure Data Factoryban](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
