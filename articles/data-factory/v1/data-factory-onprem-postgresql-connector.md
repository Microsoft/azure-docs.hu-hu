---
title: Adatok áthelyezése a PostgreSQL-ből a Azure Data Factory használatával
description: Ismerje meg, hogyan helyezheti át adatait a PostgreSQL-adatbázisból a Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 8ca8770944a12c6e0dd3e4f95d286758ebee5f9e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387274"
---
# <a name="move-data-from-postgresql-using-azure-data-factory"></a>Adatok áthelyezése a PostgreSQL-ből a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-postgresql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-postgresql.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [PostgreSQL-összekötőt a v2-ben](../connector-postgresql.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyszíni PostgreSQL-adatbázisból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az adatok egy helyszíni PostgreSQL-adattárból másolhatók bármely támogatott fogadó adattárba. A másolási tevékenység által mosogatóként támogatott adattárak listáját lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A adatfeldolgozó jelenleg támogatja az adatok egy PostgreSQL-adatbázisból más adattárakba való áthelyezését, az adatok más adattárakból egy PostgreSQL-adatbázisba való áthelyezését azonban nem.

## <a name="prerequisites"></a>Előfeltételek

Data Factory szolgáltatás támogatja a helyszíni PostgreSQL-forrásokhoz való kapcsolódást a adatkezelés átjáró használatával. Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

Az átjáróra akkor is szükség van, ha a PostgreSQL-adatbázist egy Azure IaaS virtuális gépen üzemeltetik. Az átjárót ugyanarra a IaaS virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.

> [!NOTE]
> A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ha adatkezelés átjárót a PostgreSQL-adatbázishoz való kapcsolódáshoz, telepítse a [PostgreSQL-hez készült Ngpsql-adatszolgáltatót](https://go.microsoft.com/fwlink/?linkid=282716) a 2.0.12 és a 3.1.9 között, a adatkezelés átjáróval megegyező rendszeren. A PostgreSQL 7,4-es és újabb verziója támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni PostgreSQL-adattárból származó adatokkal.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.
- A folyamat létrehozásához a következő eszközöket is használhatja:
  - Visual Studio
  - Azure PowerShell
  - Azure Resource Manager-sablon
  - .NET API
  - REST API

    A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. A helyszíni PostgreSQL-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a [JSON-példa: adatok másolása a PostgreSQL-ből az Azure blobba](#json-example-copy-data-from-postgresql-to-azure-blob) című szakaszt a jelen cikkből.

A következő szakaszokban részletesen ismertetjük a PostgreSQL-adattárra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a PostgreSQL-hez társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesPostgreSql** |Yes |
| kiszolgáló |A PostgreSQL-kiszolgáló neve. |Yes |
| adatbázis |A PostgreSQL-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. A séma neve megkülönbözteti a kis-és nagybetűket. |No |
| authenticationType |A PostgreSQL-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni PostgreSQL-adatbázishoz való kapcsolódáshoz. |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adatkészletek JSON-típusai, például a struktúra, a rendelkezésre állás és a szabályzat, az összes adatkészlet esetében hasonlóak.

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **RelationalTable** típusú (PostgreSQL-adatkészletet tartalmazó) adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a PostgreSQL-adatbázisnak a neve, amelyre a társított szolgáltatás hivatkozik. A táblanév megkülönbözteti a kis-és nagybetűket. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás típusa **RelationalSource** (beleértve a PostgreSQL-t is), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `"query": "select * from \"MySchema\".\"MyTable\""`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

> [!NOTE]
> A séma és a tábla neve megkülönbözteti a kis-és nagybetűket. Csatolja őket a `""` lekérdezésben (idézőjelek között).

**Példa**

 `"query": "select * from \"MySchema\".\"MyTable\""`

## <a name="json-example-copy-data-from-postgresql-to-azure-blob"></a>JSON-példa: adatok másolása a PostgreSQL-ből az Azure-Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók adatok a PostgreSQL-adatbázisból az Azure Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

> [!IMPORTANT]
> Ez a példa JSON-kódrészleteket biztosít. Nem tartalmaz részletes útmutatást az adatelőállító létrehozásához. Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhőalapú cikkek között](data-factory-move-data-between-onprem-and-cloud.md) .

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [OnPremisesPostgreSql](data-factory-onprem-postgresql-connector.md#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [RelationalTable](data-factory-onprem-postgresql-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [RelationalSource](data-factory-onprem-postgresql-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta minden órában átmásolja a PostgreSQL-adatbázis lekérdezési eredményét egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**PostgreSQL társított szolgáltatás:**

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
**Azure Blob Storage társított szolgáltatás:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```
**PostgreSQL bemeneti adatkészlete:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát a PostgreSQL-ben, és egy "Timestamp" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz.

A beállítás `"external": true` tájékoztatja a Data Factory szolgáltatást arról, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobPostgreSqlDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/postgresql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezés** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a PostgreSQL-adatbázis Public. usstates táblájának adatait.

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"public\".\"usstates\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "PostgreSqlDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobPostgreSqlDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "PostgreSqlToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
## <a name="type-mapping-for-postgresql"></a>Típus leképezése PostgreSQL-hez
Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok PostgreSQL-be való áthelyezésekor a rendszer a következő leképezéseket használja a PostgreSQL típusról a .NET típusra.

| PostgreSQL-adatbázis típusa | PostgresSQL-aliasok | .NET-keretrendszer típusa |
| --- | --- | --- |
| abstime | |Datetime |
| bigint |int8 |Int64 |
| bigserial |serial8 |Int64 |
| bit [(n)] | |Bájt [], karakterlánc |
| bit változó [(n)] |varbit |Bájt [], karakterlánc |
| boolean |logikai |Logikai |
| párbeszédpanel | |Bájt [], karakterlánc |
| bytea | |Bájt [], karakterlánc |
| karakter [(n)] |char [(n)] |Sztring |
| változó karakter [(n)] |varchar [(n)] |Sztring |
| CID | |Sztring |
| cidr | |Sztring |
| kör | |Bájt [], karakterlánc |
| dátum | |Datetime |
| daterange | |Sztring |
| dupla pontosság |FLOAT8 |Dupla |
| inet | |Bájt [], karakterlánc |
| intarr | |Sztring |
| int4range | |Sztring |
| int8range | |Sztring |
| egész szám |int, int4 |Int32 |
| intervallum [mezők] [(p)] | |Időtartomány |
| json | |Sztring |
| jsonb | |Bájt [] |
| vonal | |Bájt [], karakterlánc |
| lseg | |Bájt [], karakterlánc |
| macaddr | |Bájt [], karakterlánc |
| pénzt | |Tizedesjegy |
| numerikus [(p, s)] |decimális [(p, s)] |Tizedesjegy |
| numrange | |Sztring |
| OID | |Int32 |
| path | |Bájt [], karakterlánc |
| pg_lsn | |Int64 |
| pont | |Bájt [], karakterlánc |
| sokszög | |Bájt [], karakterlánc |
| valós szám |float4 |Egyirányú |
| smallint |int2 |Int16 |
| smallserial |serial2 |Int16 |
| sorozatszám |serial4 |Int32 |
| szöveg | |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
