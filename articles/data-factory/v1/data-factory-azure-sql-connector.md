---
title: Adatok másolása Azure SQL Database
description: Megtudhatja, hogyan másolhat adatokba vagy Azure SQL Database a Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 738b875a273faddd20a67be0f6feb90825f66c9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370512"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Adatok másolása Azure SQL Databaseba és onnan a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-sql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-database.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure SQL Database Connector v2-ben](../connector-azure-sql-database.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure SQL Databaseba és onnan történő áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

## <a name="supported-scenarios"></a>Támogatott esetek
**Azure SQL Database** adatait a következő adattárakba másolhatja:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból másolhatók **Azure SQL Databaseba**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Azure SQL Database-összekötő támogatja az egyszerű hitelesítést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely a különböző eszközök/API-k segítségével áthelyezi az adatokra egy Azure SQL Database.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másolja az adatait Azure SQL Databaseba, két társított szolgáltatást hoz létre az Azure Storage-fiók összekapcsolásához és az adatok Azure SQL Databaseához. A Azure SQL Databasera jellemző társított szolgáltatás tulajdonságairól a [társított szolgáltatás tulajdonságai](#linked-service-properties) című részben olvashat.
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehozhat egy másik adatkészletet is, amely meghatározza a blob Storage-ból másolt adatokat tároló Azure SQL Databaseban található SQL-táblázatot. A Azure Data Lake Storera jellemző adatkészlet-tulajdonságokért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és SqlSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha Azure SQL Databaseról az Azure Blob Storagera másol, a másolási tevékenységben a SqlSource és a BlobSink is használja. A Azure SQL Databasera jellemző másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok egy Azure SQL Databaseba való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében tekintse meg a jelen cikk [JSON-példák](#json-examples-for-copying-data-to-and-from-sql-database) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek a Azure SQL Database specifikus entitások definiálásához használhatók Data Factory:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az Azure SQL társított szolgáltatás összekapcsolja Azure SQL Database adatait a saját adatgyárával. A következő táblázat az Azure SQL társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureSqlDatabase** |Yes |
| connectionString |A connectionString tulajdonsághoz Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatok megadása. Csak az alapszintű hitelesítés támogatott. |Yes |

> [!IMPORTANT]
> Konfigurálja [Azure SQL Database tűzfalat](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) úgy, hogy az adatbázis-kiszolgáló [engedélyezze az Azure-szolgáltatások számára a kiszolgáló elérését](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Emellett, ha az Azure-on kívülről Azure SQL Databasera másol adatokat, beleértve a helyszíni adatforrások és a (z) adatokat a Factory Gateway használatával, konfigurálja a megfelelő IP-címtartományt azon számítógép számára, amely adatokat küld a Azure SQL Databasenak.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
A Azure SQL Database bemeneti vagy kimeneti adatokat jelölő adatkészlet megadásához az adatkészlet Type (típus) tulajdonságát állítsa a következőre: **tulajdonsága azuresqltable**. Állítsa az adatkészlet **linkedServiceName** tulajdonságát az Azure SQL társított szolgáltatás nevére.

Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **tulajdonsága azuresqltable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Azon Azure SQL Database-példányban található tábla vagy nézet neve, amelyre a társított szolgáltatás hivatkozik. |Yes |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

Míg a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha Azure SQL Databaseból helyez át adatáthelyezést, a másolási tevékenységben állítsa be a **SqlSource**. Hasonlóképpen, ha áthelyezi az adatátvitelt a Azure SQL Databaseba, a másolási tevékenységben a **SqlSink** értékre állíthatja a fogadó típusát. Ez a szakasz a SqlSource és a SqlSink által támogatott tulajdonságok listáját tartalmazza.

### <a name="sqlsource"></a>SqlSource
A másolási tevékenységben, ha a forrás **SqlSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

Ha a **sqlReaderQuery** meg van adva a SqlSource, a másolási tevékenység lefuttatja ezt a lekérdezést a Azure SQL Database forráson az adatkéréshez. Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, akkor a JSON-adatkészlet szerkezet szakaszában definiált oszlopok a Azure SQL Databaseon futtatandó lekérdezés () létrehozásához használatosak `select column1, column2 from mytable` . Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

> [!NOTE]
> A **sqlReaderStoredProcedureName** használatakor továbbra is meg kell adnia a **Táblanév** tulajdonság értékét az adatkészlet JSON-fájljában. Erre a táblára vonatkozóan nem történt érvényesítés.
>
>

### <a name="sqlsource-example"></a>SqlSource példa

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**A tárolt eljárás definíciója:**

```SQL
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

### <a name="sqlsink"></a>SqlSink
A **SqlSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. További információ: [ismételhető másolás](#repeatable-copy). |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység oszlopának nevét, amely automatikusan generált szelet-azonosítóval kitöltésre kerül, amely egy adott szelet adatának az újrafuttatáskor való kitakarítására szolgál. További információ: [ismételhető másolás](#repeatable-copy). |A bináris adattípusú oszlop neve (32). |No |
| sqlWriterStoredProcedureName |A tárolt eljárás neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába, például a saját üzleti logikával történő upsert vagy átalakításra. <br/><br/>Figyelje meg, hogy ez a tárolt eljárás batch-ként lesz **meghívva**. Ha olyan műveletet szeretne végrehajtani, amely csak egyszer fut, és nem rendelkezik a forrásadatok végrehajtásával, például törlés/csonkítása, használja a `sqlWriterCleanupScript` tulajdonságot. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |
| sqlWriterTableType |Adja meg a tárolt eljárásban használni kívánt táblanév nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a meglévő adattal másolható adatmásolási műveleteket. |Egy tábla típusának neve. |No |

#### <a name="sqlsink-example"></a>SqlSink példa

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-példák az adatok SQL Databaseba való másolásához
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok Azure SQL Database és az Azure Blob Storageba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Példa: adatok másolása Azure SQL Databaseból az Azure-Blobba
Ugyanez a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDatabase](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [Tulajdonsága azuresqltable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [Azure-Blob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősoros adatsorozat-adatok (óránként, naponta stb.) másolásával egy Azure SQL Database lévő táblából óránként egyszer másolja a blobokat. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL Database társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
A társított szolgáltatás által támogatott tulajdonságok listájáért tekintse meg az Azure SQL társított szolgáltatás című szakaszát.

**Azure Blob Storage társított szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
A társított szolgáltatás által támogatott tulajdonságok listájáért tekintse meg az [Azure blobot](data-factory-azure-blob-connector.md#azure-storage-linked-service) ismertető cikket.


**Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát az Azure SQL-ben, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősoros adatsorokhoz.

A "külső": "true" beállítás azt tájékoztatja a Azure Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

Az adatkészlet típusa által támogatott tulajdonságok listájáért tekintse meg az Azure SQL-adatkészlet típusának tulajdonságai szakaszt.

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Az adatkészlet típusa által támogatott tulajdonságok listájáért tekintse meg az [Azure Blob-adatkészlet típusának tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) szakaszt.

**Másolási tevékenység egy folyamaton az SQL-forrással és a blob-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
A példában a **sqlReaderQuery** meg van adva a SqlSource. A másolási tevékenység ezt a lekérdezést a Azure SQL Database forráson futtatja az adatlekérdezéshez. Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, a JSON-adatkészlet szerkezet szakaszában definiált oszlopok a Azure SQL Databaseon futtatandó lekérdezés létrehozásához használatosak. Példa: `select column1, column2 from mytable`. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

A SqlSource és a BlobSink által támogatott tulajdonságok listájának megtekintéséhez tekintse meg az [SQL-forrás](#sqlsource) szakaszt és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) .

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Példa: adatok másolása az Azure Blobból a Azure SQL Databaseba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDatabase](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [Tulajdonsága azuresqltable](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok (óránként, naponta stb.) másolása az Azure blobból egy, Azure SQL Database óránkénti táblába. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
A társított szolgáltatás által támogatott tulajdonságok listájáért tekintse meg az Azure SQL társított szolgáltatás című szakaszát.

**Azure Blob Storage társított szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
A társított szolgáltatás által támogatott tulajdonságok listájáért tekintse meg az [Azure blobot](data-factory-azure-blob-connector.md#azure-storage-linked-service) ismertető cikket.


**Azure Blob bemeneti adatkészlet:**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy ez a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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
Az adatkészlet típusa által támogatott tulajdonságok listájáért tekintse meg az [Azure Blob-adatkészlet típusának tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) szakaszt.

**Azure SQL Database kimeneti adatkészlet:**

A minta az Azure SQL "Sajáttábla" nevű táblájába másolja az adatmásolt fájlokat. Hozzon létre egy táblázatot az Azure SQL-ben ugyanazzal a számú oszloppal, mint amennyit a blob CSV-fájljának tárolására vár. Minden órában új sor kerül a táblázatba.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Az adatkészlet típusa által támogatott tulajdonságok listájáért tekintse meg az Azure SQL-adatkészlet típusának tulajdonságai szakaszt.

**Egy folyamat másolási tevékenysége blob forrással és SQL-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
A SqlSink és a BlobSource által támogatott tulajdonságok listájának megtekintéséhez tekintse meg az [SQL mosogató](#sqlsink) szakaszt és a [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="identity-columns-in-the-target-database"></a>Azonosító oszlopok a céladatbázis
Ez a szakasz egy olyan példát mutat be, amely az adatok egy forrásoldali táblából való másolását ismerteti egy Identity oszlop nélkül.

**Forrástábla:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Céltábla:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Figyelje meg, hogy a céltábla tartalmaz egy Identity oszlopot.

**Forrás-adatkészlet JSON-definíciója**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Cél adatkészlet JSON-definíciója**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Figyelje meg, hogy mivel a forrás-és a céltábla eltérő sémával rendelkezik (a cél egy további, identitással rendelkező oszlop). Ebben az esetben meg kell adnia a **Structure** tulajdonságot a cél adatkészlet definíciójában, amely nem tartalmazza az Identity oszlopot.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Tárolt eljárás meghívása az SQL-fogadóból
Ha például egy folyamat másolási tevékenységében egy tárolt eljárást kell meghívnia az SQL-fogadóból, tekintse meg a [tárolt eljárás meghívása az SQL-fogadónak a másolási tevékenységben](data-factory-invoke-stored-procedure-from-copy-activity.md) című cikket.

## <a name="type-mapping-for-azure-sql-database"></a>Azure SQL Database leképezésének típusa
Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok Azure SQL Databaseba való áthelyezésekor a rendszer a következő leképezéseket használja az SQL típusról a .NET típusra, és fordítva. A leképezés megegyezik a ADO.NET adattípusának SQL Server-leképezésével.

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
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
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Ismételhető másolat
Amikor az adatok másolása SQL Server adatbázisba történik, a másolási tevékenység alapértelmezés szerint hozzáfűzi az adatokhoz a fogadó táblához. A UPSERT elvégzéséhez tekintse [meg az ismételhető írás SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) szóló cikket.

Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.