---
title: Adatok másolása SQL Server
description: Ismerje meg, hogyan helyezheti át a helyszíni vagy egy Azure-beli virtuális gépen lévő SQL Server-adatbázis adatait Azure Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 8187a71e89c364ef5a52d7ad4615ed03e6b24a4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592052"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Adatok másolása SQL Serverba és onnan a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használt Azure Data Factory verzióját:"]
> * [1-es verzió](v1/data-factory-sqlserver-connector.md)
> * [Aktuális verzió](connector-sql-server.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SQL Server-adatbázisba való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SQL Server-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Az adatok másolása egy SQL Server adatbázisból bármely támogatott fogadó adattárba. Vagy bármely támogatott forrás adattárból átmásolhat egy SQL Server adatbázisba. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez az SQL Server-összekötő a következőket támogatja:

- SQL Server 2005-es vagy újabb verzió.
- Adatok másolása SQL vagy Windows-hitelesítés használatával.
- Forrásként egy SQL-lekérdezés vagy tárolt eljárás használatával beolvashatja az adatokból. A részleteket az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-database) részében is választhatja SQL Server forrásból.
- Fogadóként automatikusan hozza létre a céltábla, ha nem létezik a forrásoldali séma alapján; az adattábla hozzáfűzése vagy egy tárolt eljárás meghívása egyéni logikával a másolás során. 

[SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb) nem támogatott.

>[!NOTE]
>Ez az összekötő jelenleg nem támogatja az SQL Server [Always Encryptedt](/sql/relational-databases/security/encryption/always-encrypted-database-engine) . A megoldáshoz használhat egy [általános ODBC-összekötőt](connector-odbc.md) és egy SQL Server ODBC-illesztőt. Kövesse [ezt az útmutatót](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver) az ODBC-illesztőprogram letöltésével és a kapcsolatok karakterlánc-beállításaival.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a SQL Server adatbázis-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SQL Server társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **SQLServer** értékre kell beállítani. | Yes |
| connectionString |Itt adhatja meg az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges **ConnectionString** -információkat. Tekintse át a következő mintákat.<br/>A jelszó Azure Key Vaultban is elhelyezhető. Ha SQL-hitelesítéssel rendelkezik, húzza `password` ki a konfigurációt a kapcsolatok sztringből. További információ: a táblázatot követő JSON-példa és a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md). |Yes |
| userName (Felhasználónév) |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Ilyen például a **tartománynév \\ Felhasználónév**. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. A mező megjelölése **SecureString** -ként, hogy biztonságosan tárolja Azure Data Factoryban. Vagy [hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. |No |
| Connectvia tulajdonsággal | Ez az [integrációs](concepts-integration-runtime.md) modul az adattárhoz való kapcsolódásra szolgál. További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime-t használja. |No |

>[!TIP]
>Ha hibát észlelt a "UserErrorFailedToConnectToSqlServer" hibakódmal, és egy olyan üzenet, mint például "az adatbázis munkamenet-korlátja XXX és elérte," adja hozzá `Pooling=false` a kapcsolati karakterláncot, és próbálkozzon újra.

**1. példa: SQL-hitelesítés használata**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: SQL-hitelesítés használata jelszóval Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

**3. példa: Windows-hitelesítés használata**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;",
            "userName": "<domain\\username>",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a SQL Server adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok SQL Server adatbázisba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **SqlServerTable** értékre kell állítani. | Yes |
| schema | A séma neve. |Nem, forrás, igen, fogadó  |
| tábla | A tábla vagy nézet neve. |Nem, forrás, igen, fogadó  |
| tableName | A tábla/nézet neve a sémával. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. Az új számítási feladatokhoz használja a és a elemet `schema` `table` . | Nem, forrás, igen, fogadó |

**Példa**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához használható csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a SQL Server forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="sql-server-as-a-source"></a>SQL Server forrásként

>[!TIP]
>Az adatok az adatparticionálással való hatékony betöltéséhez SQL Server az SQL Database-ből származó [párhuzamos másolásról](#parallel-copy-from-sql-database)többet is megtudhat.

Az adatok SQL Serverból való másolásához állítsa a forrás típusát a másolás tevékenység **SqlSource**. A másolási tevékenység forrása szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **SqlSource** értékre kell állítani. | Yes |
| sqlReaderQuery |Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Ez a tulajdonság annak a tárolt eljárásnak a neve, amely beolvassa az adatokat a forrás táblából. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |No |
| storedProcedureParameters |Ezek a paraméterek a tárolt eljáráshoz tartoznak.<br/>Az engedélyezett értékek név vagy érték párok. A paraméterek neveinek és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |
| isolationLevel | Meghatározza az SQL-forrás tranzakció-zárolási viselkedését. Az engedélyezett értékek a következők: **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **szerializálható**, **Pillanatkép**. Ha nincs megadva, a rendszer az adatbázis alapértelmezett elkülönítési szintjét használja. További részletekért tekintse meg [ezt a dokumentációt](/dotnet/api/system.data.isolationlevel) . | No |
| partitionOptions | Meghatározza az adatok SQL Serverból való betöltéséhez használt adatparticionálási beállításokat. <br>Az engedélyezett értékek a következők: **none** (alapértelmezett), **PhysicalPartitionsOfTable** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), akkor a párhuzamossági fok a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításával párhuzamosan töltődik be az adatok SQL Serverból való egyidejű betöltésére. | No |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció lehetőség nem `None` . | No |
| ***Alatt `partitionSettings` :*** | | |
| partitionColumnName | Adja meg a forrás oszlop nevét **Integer vagy Date/datetime típusban** (,,,,,, `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` vagy), `datetimeoffset` amelyet a tartomány particionálása használ a párhuzamos másoláshoz. Ha nincs megadva, a rendszer automatikusan észleli a tábla indexét vagy elsődleges kulcsát, és a partíció oszlopként használja.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához,  `?AdfDynamicRangePartitionCondition ` a WHERE záradékban lévő hookot. Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-database) szakaszát. | No |
| partitionUpperBound | A particionálási tartomány felosztásának partíciós oszlopának maximális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.  <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-database) szakaszát. | No |
| partitionLowerBound | A particionálási tartomány felosztásához szükséges partíciós oszlop minimális értéke. Ezzel az értékkel lehet eldönteni, hogy a partíció Stride-e, nem pedig a táblázat sorainak szűrésére. A program a tábla vagy a lekérdezés eredményének összes sorát particionálja és másolja. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Példaként tekintse meg az [SQL Database párhuzamos másolási](#parallel-copy-from-sql-database) szakaszát. | No |

**Vegye figyelembe a következő szempontokat:**

- Ha a **SqlSource** **sqlReaderQuery** van megadva, a másolási tevékenység lefuttatja ezt a lekérdezést a SQL Server forráson az adatkéréshez. Megadhat egy tárolt eljárást is a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával, ha a tárolt eljárás paraméterekkel rendelkezik.
- Ha tárolt eljárást használ a forrásban az adatok lekéréséhez, vegye figyelembe, hogy a tárolt eljárás úgy lett kialakítva, hogy eltérő sémát ad vissza, ha a különböző paraméterek értékét átadja

**Példa: SQL-lekérdezés használata**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Példa: tárolt eljárás használata**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**A tárolt eljárás definíciója**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-a-sink"></a>SQL Server fogadóként

> [!TIP]
> További információ a támogatott írási viselkedésekről, konfigurációkról és ajánlott eljárásokról az [adatok SQL Serverba való betöltéséhez ajánlott](#best-practice-for-loading-data-into-sql-server)eljárások közül.

Az adatSQL Serverba való másoláshoz állítsa a fogadó típust a másolási tevékenység **SqlSink**. A másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **SqlSink** értékre kell állítani. | Yes |
| preCopyScript |Ez a tulajdonság határozza meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az adatSQL Serverba írna. A rendszer csak egyszer hívja meg a példányt. Ennek a tulajdonságnak a használatával törölheti az előre feltöltött adatkészleteket. |No |
| tableOption | Meghatározza, hogy a [rendszer automatikusan létrehozza](copy-activity-overview.md#auto-create-sink-tables) -e a fogadó táblát, ha az nem létezik a forrásoldali séma alapján. Az automatikus tábla létrehozása nem támogatott, ha a fogadó megadja a tárolt eljárást. Az engedélyezett értékek a következők: `none` (alapértelmezett), `autoCreate` . |No |
| sqlWriterStoredProcedureName | Annak a tárolt eljárásnak a neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába. <br/>Ezt a tárolt eljárást batch-ként kell *meghívni*. Olyan műveletekhez, amelyek csak egyszer futnak, és nem kell megtenniük a forrásadatokat, például törlés vagy csonkítás, használja a `preCopyScript` tulajdonságot.<br>Lásd: példa [egy tárolt eljárás meghívására egy SQL-](#invoke-a-stored-procedure-from-a-sql-sink)fogadóból. | No |
| storedProcedureTableTypeParameterName |A tárolt eljárásban megadott tábla típusú paraméter neve.  |No |
| sqlWriterTableType |A tárolt eljárásban használandó táblanév neve. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a folyamatban lévő adattal másolt adatfájlokat. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei.<br/>Az engedélyezett értékek név és érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. | No |
| writeBatchSize |A *kötegekben* az SQL-táblába beszúrandó sorok száma.<br/>Az engedélyezett értékek a sorok számának egésze. Alapértelmezés szerint a Azure Data Factory dinamikusan meghatározza a megfelelő batch-méretet a sor mérete alapján. |No |
| writeBatchTimeout |Ez a tulajdonság határozza meg, hogy a kötegelt beszúrási művelet várakozási ideje befejeződjön, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a TimeSpan vonatkoznak. Egy példa: "00:30:00" 30 percig. Ha nincs megadva érték, az időtúllépés alapértelmezett értéke "02:00:00". |No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**1. példa: az adathozzáfűzés**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**2. példa: tárolt eljárás meghívása másolás közben**

További információ az SQL-fogadó [tárolt eljárásának meghívásáról](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Párhuzamos másolás az SQL Database-ből

A másolási tevékenységben található SQL Server-összekötő beépített adatparticionálást biztosít az adatmásoláshoz párhuzamosan. Az adatparticionálási beállítások a másolási tevékenység **forrás** lapján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-sql-server/connector-sql-partition-options.png)

A particionált másolás engedélyezésekor a másolási tevékenység párhuzamos lekérdezéseket futtat a SQL Server-forráson az adatpartíciók közötti betöltéshez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az adatok egy részét kéri le a SQL Server.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatparticionálással, különösen akkor, ha nagy mennyiségű adattal tölt be a SQL Server. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása esetén ajánlott több fájlként írni egy mappába (csak a mappa nevét adja meg), ebben az esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés nagyméretű táblából, fizikai partíciókkal.        | **Partíciós beállítás**: a tábla fizikai partíciói. <br><br/>A végrehajtás során a Data Factory automatikusan észleli a fizikai partíciókat, és az Adatmásolást partíciók szerint. <br><br/>Ha szeretné megnézni, hogy a tábla rendelkezik-e fizikai partícióval, tekintse át [ezt a lekérdezést](#sample-query-to-check-physical-partition). |
| Teljes terhelés a nagyméretű táblából fizikai partíciók nélkül, egész számmal vagy datetime oszloppal az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Partíciós oszlop** (nem kötelező): az adatparticionáláshoz használt oszlop megadása. Ha nincs megadva, a rendszer az indexet vagy az elsődleges kulcs oszlopot használja.<br/>**Partíció felső határa** és a **partíció alsó határa** (nem kötelező): adja meg, hogy meg szeretné-e állapítani a partíciós lépéseket. Ez nem a tábla sorainak szűrésére szolgál, a tábla összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értékeket.<br><br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. |
| Nagy mennyiségű adat betöltése egyéni lekérdezéssel fizikai partíciók nélkül, egész szám vagy dátum/datetime oszlop használatával az adatok particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál.<br>**Partíció felső határa** és a **partíció alsó határa** (nem kötelező): adja meg, hogy meg szeretné-e állapítani a partíciós lépéseket. Ez nem a tábla sorainak szűrésére szolgál, a lekérdezés eredményének összes sorát particionálja és másolja a rendszer. Ha nincs megadva, a másolási tevékenység automatikusan felismeri az értéket.<br><br>A végrehajtás során Data Factory lecseréli `?AdfRangePartitionColumnName` az egyes partíciók tényleges oszlop-és értéktartomány-tartományára, és elküldi a SQL Servernak. <br>Ha például az "ID" partíciós oszlop értéke 1 és 100 között van, és az alsó határ 20-ra van állítva, a felső határ pedig 80, és a párhuzamos másolás 4 Data Factory, akkor az adatok 4 partíció-azonosítóval vannak lekérdezve a következő tartományban: <= 20, [21, 50], [51, 80] és >= 81. <br><br>Több példa a különböző forgatókönyvekre:<br> 1. a teljes tábla lekérdezése: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. lekérdezés az oszlopok kiválasztásával és további WHERE-záradék szűrőkkel: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. lekérdezés allekérdezésekkel: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. lekérdezés partícióval az allekérdezésben: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Ajánlott eljárások az adattároláshoz a Partition kapcsolóval:

1. Válassza a megkülönböztető oszlop partíciós oszlopként (például az elsődleges kulcs vagy az egyedi kulcs) lehetőséget az adattorzítás elkerüléséhez. 
2. Ha a tábla beépített partícióval rendelkezik, a jobb teljesítmény érdekében használja a "fizikai partíciók tábla" partíciós beállítást.    
3. Ha Azure Integration Runtimet használ az adatmásoláshoz, több számítási erőforrás kihasználása érdekében megadhat nagyobb "[adatintegrációs egységeket (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4). Tekintse át a megfelelő forgatókönyveket.
4. "A[másolási párhuzamosság foka](copy-activity-performance-features.md#parallel-copy)" szabályozza a partíciók számát, így a szám túl nagy ideig nem árt a teljesítménynek, javasoljuk, hogy ezt a számot (DIU vagy saját ÜZEMELTETÉSű IR-csomópontok száma) * (2 – 4) állítsa be.

**Példa: teljes terhelés a nagyméretű táblából fizikai partíciókkal**

```json
"source": {
    "type": "SqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "SqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Mintául szolgáló lekérdezés a fizikai partíciók vizsgálatához

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Ha a tábla fizikai partícióval rendelkezik, a "HasPartition" a következőhöz hasonló módon jelenik meg: "yes".

![SQL-lekérdezés eredménye](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-server"></a>Ajánlott eljárás az adatSQL Serverba való betöltéshez

Amikor az Adatmásolás SQL Serverba történik, különböző írási viselkedésre lehet szükség:

- [Hozzáfűzés](#append-data): a forrásadatok csak új rekordokkal rendelkeznek.
- [Upsert](#upsert-data): a saját forrásadatok lapkákat és frissítéseket is tartalmaz.
- [Felülírás](#overwrite-the-entire-table): minden alkalommal újra szeretném tölteni a teljes dimenzió táblát.
- [Írás Egyéni logikával](#write-data-with-custom-logic): további feldolgozásra van szükség a céltábla utolsó beszúrása előtt.

Tekintse meg a megfelelő részeit, hogy miként konfigurálható a Azure Data Factory és az ajánlott eljárásokban.

### <a name="append-data"></a>Adathozzáfűzés

Az adathozzáfűzés a SQL Server fogadó összekötő alapértelmezett viselkedése. A Azure Data Factory egy tömeges beszúrást végez, hogy hatékonyan írjon a táblába. A forrást és a fogadót ennek megfelelően állíthatja be a másolási tevékenységbe.

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

**1. lehetőség:** Ha nagy mennyiségű adattal szeretne másolni, a másolási tevékenység használatával tömegesen betöltheti az összes rekordot egy előkészítési táblába, majd futtathatja a tárolt eljárási tevékenységet, hogy [egyesítse](/sql/t-sql/statements/merge-transact-sql) vagy INSERT/Update utasítást alkalmazzon egy adott felvételen. 

A másolási tevékenység jelenleg nem támogatja natív módon az betöltést egy adatbázis ideiglenes táblájába. A több tevékenység kombinációjának beállításával speciális módon állíthatja be az [SQL Database tömeges Upsert forgatókönyvek optimalizálását](https://github.com/scoriani/azuresqlbulkupsert). Az alábbi példa egy állandó tábla használatát mutatja be átmenetiként.

Azure Data Factory például létrehozhat egy **másolási tevékenységgel** rendelkező **folyamatot egy tárolt eljárási tevékenységgel**. A korábbi adatokat másol a forrás-áruházból egy SQL Server előkészítési táblába, például **UpsertStagingTable**, mint a tábla neve az adatkészletben. Ezután az utóbbi egy tárolt eljárást hív meg, hogy egyesítse a forrás adatait az előkészítési táblából a cél táblába, és törölje az előkészítési táblát.

![Beszúrás és frissítés](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Az adatbázisában Definiáljon egy EGYESÍTÉSi logikával ellátott tárolt eljárást, például az alábbi példát, amely az előző tárolt eljárási tevékenységből mutat. Tegyük fel, hogy a cél a **marketing** tábla három oszloppal: **ProfileID**, **State** és **category**. A upsert a **ProfileID** oszlop alapján végezze el.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**2. lehetőség:** Megadhat [egy tárolt eljárást a másolási tevékenységen belül](#invoke-a-stored-procedure-from-a-sql-sink). Ez a módszer a forrás táblában lévő összes köteget (a tulajdonság által szabályozott módon) futtatja `writeBatchSize` ahelyett, hogy a tömeges beszúrást használja a másolási tevékenység alapértelmezett módszere.

### <a name="overwrite-the-entire-table"></a>A teljes táblázat felülírása

A **preCopyScript** tulajdonságot egy másolási tevékenység fogadójában is konfigurálhatja. Ebben az esetben minden futó másolási tevékenységnél először Azure Data Factory futtatja a parancsfájlt. Ezután futtatja a másolatot az adatbeszúráshoz. Ha például felül szeretné írni a teljes táblázatot a legfrissebb adatokkal, adjon meg egy parancsfájlt, hogy először törölje az összes rekordot, mielőtt tömegesen betölti az új adatokat a forrásból.

### <a name="write-data-with-custom-logic"></a>Az adatírás egyéni logikával

Az egyéni logikával történő adatírás lépései hasonlóak az [Upsert](#upsert-data) -adatszakaszban leírt lépésekhez. Ha további feldolgozást kell alkalmaznia a forrásadatok végső beszúrása előtt, akkor betöltheti az előkészítési táblát, majd meghívja a tárolt eljárási tevékenységet, vagy meghívhat egy tárolt eljárást a másolási tevékenység fogadójában az adatalkalmazáshoz.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Tárolt eljárás meghívása egy SQL-gyűjtőből

Az adatok SQL Server adatbázisba való másolása során egy felhasználó által megadott tárolt eljárás is konfigurálható és meghívható a forrástábla minden egyes kötegén további paraméterekkel. A tárolt eljárás funkció kihasználja a [tábla értékű paraméterek](/dotnet/framework/data/adonet/sql/table-valued-parameters)előnyeit.

Tárolt eljárás használható, ha a beépített másolási mechanizmusok nem szolgálják ki a célt. Ilyen eset például, ha további feldolgozást szeretne alkalmazni a forrásadatok végső beszúrása előtt a céltáblaba. Néhány további feldolgozási példa: Ha egyesíteni szeretné az oszlopokat, további értékeket kereshet, és több táblába szúrhat be.

Az alábbi minta azt mutatja be, hogyan használható egy tárolt eljárás egy upsert a SQL Server-adatbázis egyik táblájába. Tegyük fel, hogy a bemeneti adatok és a fogadó **marketing** tábla mindhárom oszlopot tartalmaz: **ProfileID**, **State** és **category**. A upsert a **ProfileID** oszlop alapján végezze el, és csak a "producta" nevű adott kategóriára alkalmazza.

1. Az adatbázisában adja meg a tábla típusát a **sqlWriterTableType** megegyező névvel. A tábla típusának sémája megegyezik a bemeneti adatok által visszaadott sémával.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Az adatbázisban adja meg a tárolt eljárást ugyanazzal a névvel, mint a **sqlWriterStoredProcedureName**. Kezeli a megadott forrásból származó bemeneti adatokat, és egyesíti a kimeneti táblába. A tárolt eljárásban szereplő tábla típusának neve megegyezik az adatkészletben definiált **Táblanév** .

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. A Azure Data Factory a másolási tevékenységben adja meg az **SQL** -fogadó szakaszt a következőképpen:

    ```json
    "sink": {
        "type": "SqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="data-type-mapping-for-sql-server"></a>SQL Server adattípusának leképezése

A és a rendszerből a SQL Serverba másolt adatokból az alábbi leképezések használatosak SQL Server adattípusokból Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| SQL Server adattípus | Azure Data Factory időközi adattípus |
|:--- |:--- |
| bigint |Int64 |
| binary |Bájt [] |
| bit |Logikai |
| char |Karakterlánc, char [] |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Tizedesjegy |Tizedesjegy |
| FILESTREAM attribútum (varbinary (max)) |Bájt [] |
| Float |Dupla |
| image |Bájt [] |
| int |Int32 |
| pénzt |Tizedesjegy |
| NCHAR |Karakterlánc, char [] |
| ntext |Karakterlánc, char [] |
| numerikus |Tizedesjegy |
| nvarchar |Karakterlánc, char [] |
| valós szám |Egyirányú |
| ROWVERSION |Bájt [] |
| idő adattípusúra |DateTime |
| smallint |Int16 |
| túlcsordulási |Tizedesjegy |
| sql_variant |Objektum |
| szöveg |Karakterlánc, char [] |
| time |időtartam |
| időbélyeg |Bájt [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Sztring |

>[!NOTE]
> A decimális átmeneti típusra leképezett adattípusok esetén a másolási tevékenység jelenleg legfeljebb 28-ig támogatja a pontosságot. Ha a 28-nál nagyobb pontosságot igénylő adatmennyiségre van szüksége, érdemes lehet egy SQL-lekérdezésben szereplő sztringre konvertálni.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Always Encrypted használata

Ha [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)használatával másol át vagy SQL Server az adatokból, az [általános ODBC-összekötőt](connector-odbc.md) és az SQL Server ODBC-illesztőt használja saját üzemeltetésű Integration Runtimeon keresztül. Ez az SQL Server-összekötő jelenleg nem támogatja Always Encrypted. 

Pontosabban:

1. Hozzon létre egy saját üzemeltetésű Integration Runtime, ha még nem rendelkezik ilyennel. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.

2. Töltse le [innen](/sql/connect/odbc/download-odbc-driver-for-sql-server)SQL Server a 64 bites ODBC-illesztőt, és telepítse a Integration Runtime gépre. További információ arról, hogy az illesztőprogram hogyan működik a [Always encrypted és a SQL Server ODBC-illesztő használatával](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Az SQL-adatbázishoz való kapcsolódáshoz hozzon létre egy ODBC-típusú társított szolgáltatást. Az SQL-hitelesítés használatához adja meg az alábbi ODBC-kapcsolódási karakterláncot, és válassza az **alapszintű** hitelesítés lehetőséget a Felhasználónév és a jelszó megadásához.

    ```
    Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
    ```

4. Ennek megfelelően hozzon létre adatkészletet és másolási tevékenységet az ODBC-típussal. További tudnivalók az [ODBC-összekötőről](connector-odbc.md) című cikkben olvashatók.

## <a name="troubleshoot-connection-issues"></a>Kapcsolati problémák hibaelhárítása

1. Konfigurálja a SQL Server-példányt a távoli kapcsolatok fogadására. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal a **kiszolgáló** elemre, majd válassza a **Tulajdonságok** lehetőséget. Válassza a listában a **kapcsolatok** lehetőséget, majd jelölje be a **távoli kapcsolatok engedélyezése a kiszolgálón** jelölőnégyzetet.

    ![Távoli kapcsolatok engedélyezése](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    A részletes lépésekért lásd: [a távelérési kiszolgáló konfigurációs beállításának konfigurálása](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option).

2. **SQL Server konfigurációkezelő** elindítása. Bontsa ki **SQL Server hálózati konfigurációt** a kívánt példányhoz, és válassza az **MSSQLSERVER protokollokat**. A protokollok a jobb oldali ablaktáblán jelennek meg. A TCP/IP engedélyezéséhez kattintson a jobb gombbal a **TCP/IP** elemre, és válassza az **Engedélyezés** lehetőséget.

    ![TCP/IP engedélyezése](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    További információ és a TCP/IP protokoll engedélyezésének alternatív módjai: [kiszolgálói hálózati protokoll engedélyezése vagy letiltása](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).

3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságok** ablak elindításához.
4. Váltson az **IP-címek** lapra. Görgessen le a **IPAll** szakasz megtekintéséhez. Jegyezze fel a **TCP-portot**. Az alapértelmezett érték a **1433**.
5. Hozzon létre egy szabályt a számítógépen a **Windows tűzfal** számára, hogy engedélyezze a bejövő forgalmat ezen a porton keresztül. 
6. **Kapcsolat ellenőrzése**: Ha SQL Server szeretne csatlakozni egy teljes névvel, használja a SQL Server Management Studio egy másik gépről. Például: `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).