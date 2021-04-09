---
title: Adatok áthelyezése SQL Server
description: Ismerje meg, hogy miként helyezhetők át adatok a helyszíni vagy egy Azure-beli virtuális gépen lévő SQL Server-adatbázisba Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368812"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Adatok áthelyezése SQL Serverba és onnan a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sqlserver-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sql-server.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az SQL Server Connector v2-ben](../connector-sql-server.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SQL Server-adatbázisba való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az adatok másolása **SQL Server-adatbázisból** a következő adattárakba:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatok másolhatók **egy SQL Server adatbázisba**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Támogatott SQL Server verziók
Ez az SQL Server-összekötő támogatja az adatok másolását a (z) és a (z) rendszerből a helyszíni vagy az Azure IaaS-ben üzemeltetett példány következő verzióira az SQL-hitelesítés és a Windows-hitelesítés használatával: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A helyszíni vagy az Azure IaaS-(infrastruktúra-szolgáltatásbeli) virtuális gépekhez SQL Server való csatlakozáshoz szükséges fogalmak és lépések megegyeznek. Mindkét esetben adatkezelés átjárót kell használnia a kapcsolódáshoz.

Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) . Az átjáró-példányok beállítása a SQL Serverhoz való csatlakozás előfeltétele.

Habár az átjárót ugyanarra a helyszíni gépre vagy Felhőbeli virtuálisgép-példányra is telepítheti, mint a jobb teljesítmény érdekében SQL Server, javasoljuk, hogy telepítse őket külön gépekre. Az átjáró és a SQL Server külön gépeken csökkenti az erőforrás-tartalmat.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával áthelyezi az adatok egy SQL Server-adatbázisba vagy egy másikba.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy SQL Server adatbázisból másol egy Azure Blob Storage-ba, két társított szolgáltatást hoz létre, amely összekapcsolja a SQL Server-adatbázist és az Azure Storage-fiókot az adatok gyárával. Az SQL Server adatbázisra jellemző társított szolgáltatások tulajdonságairól a [társított szolgáltatás tulajdonságai](#linked-service-properties) című részben olvashat.
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó SQL Server adatbázisban található SQL-táblázatot. Emellett létrehoz egy másik adatkészletet a blob-tároló és a SQL Server-adatbázisból másolt adatokat tartalmazó mappa megadásához. SQL Server adatbázisra vonatkozó adatkészlet-tulajdonságok esetében lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a SqlSource forrásként és BlobSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha az Azure Blob Storageról SQL Server adatbázisra másol, a másolási tevékenységben a BlobSource és a SqlSink is használja. A SQL Server-adatbázisra vonatkozó másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. A Data Factory JSON-definíciókkal rendelkező mintákhoz, amelyek az adatok SQL Server adatbázisba történő másolásához használatosak, a jelen cikk [JSON-példák](#json-examples-for-copying-data-from-and-to-sql-server) című szakasza tartalmazza.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek a SQL Server specifikus entitások definiálásához használhatók Data Factory:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Hozzon létre egy **OnPremisesSqlServer** típusú társított szolgáltatást egy SQL Server adatbázis egy adatgyárhoz való csatolásához. A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

A következő táblázat a SQL Server társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesSqlServer**. |Yes |
| connectionString |Az SQL-hitelesítés vagy a Windows-hitelesítés használatával a SQL Server-adatbázishoz való kapcsolódáshoz szükséges connectionString-adatokat adjon meg. |Yes |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a SQL Server adatbázishoz való kapcsolódáshoz. |Yes |
| username |Windows-hitelesítés használata esetén adja meg a felhasználónevet. Példa: **tartománynév \\ Felhasználónév**. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |

A **New-AzDataFactoryEncryptValue** parancsmaggal titkosíthatja a hitelesítő adatokat, és a következő példában látható módon használhatja azokat a kapcsolatok karakterláncában (**EncryptedCredential** tulajdonság):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Példák
**JSON az SQL-hitelesítés használatához**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON a Windows-hitelesítés használatához**

Adatkezelés átjáró megszemélyesíti a megadott felhasználói fiókot a SQL Server adatbázishoz való csatlakozáshoz.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
A mintákban egy **SqlServerTable** típusú adathalmazt használt, amely egy SQL Server adatbázisban található táblát jelöl.

Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet-típushoz hasonlóak (SQL Server, Azure Blob, Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **SqlServerTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának vagy nézetnek a neve, amely a társított szolgáltatás által hivatkozott SQL Server adatbázis-példányban található. |Yes |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Ha SQL Server-adatbázisból helyez át adatátvitelt, a másolási tevékenységben a forrás típusát **SqlSource** kell beállítania. Hasonlóképpen, ha az adatátvitelt egy SQL Server adatbázisba helyezi át, a másolási tevékenységben állítsa be a fogadó típusát a **SqlSink** értékre. Ez a szakasz a SqlSource és a SqlSink által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

### <a name="sqlsource"></a>SqlSource
Ha egy másolási tevékenységben a forrás **SqlSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from Sajáttábla. A bemeneti adatkészlet által hivatkozott adatbázisból több táblát is hivatkozhat. Ha nincs megadva, a futtatott SQL-utasítás: válasszon a Sajáttábla közül. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

Ha a **sqlReaderQuery** meg van adva a SqlSource, a másolási tevékenység futtatja ezt a lekérdezést a SQL Server adatbázis-forráson az adatkéréshez.

Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName-t, a struktúra szakaszban definiált oszlopok a SQL Server adatbázison futtatandó választó lekérdezés összeállítására szolgálnak. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

> [!NOTE]
> A **sqlReaderStoredProcedureName** használatakor továbbra is meg kell adnia a **Táblanév** tulajdonság értékét az adatkészlet JSON-fájljában. Erre a táblára vonatkozóan nem történt érvényesítés.

### <a name="sqlsink"></a>SqlSink
A **SqlSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |A másolási tevékenységhez tartozó lekérdezés megadása úgy, hogy az egy adott szeletből származó adatmennyiséget takarítson meg. További információ: [ismételhető másolás](#repeatable-copy) szakasz. |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Adja meg az oszlop nevét a másolási tevékenységhez, amely automatikusan generált szelet-azonosítóval egészül ki, amely egy adott szelet adatának az újrafuttatáskor való kitakarítására szolgál. További információ: [ismételhető másolás](#repeatable-copy) szakasz. |A bináris adattípusú oszlop neve (32). |No |
| sqlWriterStoredProcedureName |A tárolt eljárás neve, amely meghatározza, hogy a forrásadatok hogyan alkalmazhatók a célként megadott táblába, például a saját üzleti logikával történő upsert vagy átalakításra. <br/><br/>Figyelje meg, hogy ez a tárolt eljárás batch-ként lesz **meghívva**. Ha olyan műveletet szeretne végrehajtani, amely csak egyszer fut, és nem rendelkezik a forrásadatok végrehajtásával, például törlés/csonkítása, használja a `sqlWriterCleanupScript` tulajdonságot. |A tárolt eljárás neve. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |
| sqlWriterTableType |Adja meg a tárolt eljárásban használni kívánt táblanév nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatáthelyezést egy ideiglenes táblában, amely ebben a táblázatban szerepel. A tárolt eljárási kód ezután egyesítheti a meglévő adattal másolható adatmásolási műveleteket. |Egy tábla típusának neve. |No |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-példák adatok másolásához és SQL Server
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Az alábbi példák azt mutatják be, hogyan másolhatók az adatok SQL Server és az Azure Blob Storageba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Példa: adatok másolása SQL Serverból az Azure-Blobba
A következő minta a következőket mutatja be:

1. [OnPremisesSqlServer](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [SqlServerTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok másolását végzi egy SQL Server táblából óránként egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**Társított szolgáltatás SQL Server**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás**

```json
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
**Bemeneti adatkészlet SQL Server**

A minta azt feltételezi, hogy létrehozott egy "Sajáttábla" táblát a SQL Serverban, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz. Egyetlen adatkészletet használva több táblán is lekérdezheti ugyanazon az adatbázison belül, de az adatkészlet táblanév-typeProperty egyetlen táblázatot kell használnia.

A "külső": "true" beállítás azt tájékoztatja, Data Factory szolgáltatás, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Azure blobkimeneti adatkészlet**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
**Másolási tevékenységgel rendelkező folyamat**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
Ebben a példában a SqlSource **sqlReaderQuery** van megadva. A másolási tevékenység ezt a lekérdezést a SQL Server adatbázis-forráson futtatja, hogy lekérje az adatgyűjtést. Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik). A sqlReaderQuery hivatkozhat több táblára a bemeneti adatkészlet által hivatkozott adatbázison belül. Nem csak az adatkészlet táblanév-typeProperty beállított táblára korlátozódik.

Ha nem ad meg sqlReaderQuery-vagy sqlReaderStoredProcedureName-t, a struktúra szakaszban definiált oszlopok a SQL Server adatbázison futtatandó választó lekérdezés létrehozásához használatosak. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

A SqlSource és a BlobSink által támogatott tulajdonságok listájának megtekintéséhez tekintse meg az [SQL-forrás](#sqlsource) szakaszt és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) .

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Példa: adatok másolása az Azure Blobból a SQL Serverba
A következő minta a következőket mutatja be:

1. A [OnPremisesSqlServer](#linked-service-properties)típusú társított szolgáltatás.
2. A [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és SqlSink használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta óránként másolja az Azure-blobokból származó idősorozat-adatok egy SQL Server táblába. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Társított szolgáltatás SQL Server**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás**

```json
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
**Azure-Blob bemeneti adatkészlete**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**Kimeneti adatkészlet SQL Server**

A minta egy "Sajáttábla" nevű táblába másol egy SQL Server. Hozza létre a táblát SQL Server azonos számú oszloppal, ahogy azt várná, hogy a blob CSV-fájlja tartalmazni fog. Minden órában új sor kerül a táblázatba.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Másolási tevékenységgel rendelkező folyamat**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák hibaelhárítása
1. Konfigurálja úgy a SQL Server, hogy fogadja a távoli kapcsolatokat. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal a **kiszolgáló** elemre, majd kattintson a **Tulajdonságok** elemre. Válassza ki a **kapcsolatok** elemet a listából, és jelölje be a **távoli kapcsolatok engedélyezése a kiszolgálóhoz lehetőséget**.

    ![Távoli kapcsolatok engedélyezése](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    A részletes lépésekért lásd: [a távelérési kiszolgáló konfigurálási beállításának konfigurálása](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) .
2. **SQL Server konfigurációkezelő** elindítása. Bontsa ki **SQL Server hálózati konfigurációt** a kívánt példányhoz, és válassza az **MSSQLSERVER protokollokat**. A protokollok a jobb oldali panelen jelennek meg. A TCP/IP engedélyezéséhez kattintson a jobb gombbal a **TCP/IP** elemre, majd kattintson az **Engedélyezés** parancsra.

    ![TCP/IP engedélyezése](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    A TCP/IP protokoll engedélyezésének részleteit és alternatív módszereit lásd: [kiszolgálói hálózati protokoll engedélyezése vagy letiltása](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) .
3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságok** ablak elindításához.
4. Váltson az **IP-címek** lapra. Görgessen le a **IPAll** szakasz megtekintéséhez. Jegyezze fel a **TCP-portot**(az alapértelmezett érték **1433**).
5. Hozzon létre egy szabályt a számítógépen a **Windows tűzfal** számára, hogy engedélyezze a bejövő forgalmat ezen a porton keresztül.
6. **Kapcsolat ellenőrzése**: ha teljesen minősített névvel szeretne csatlakozni a SQL Serverhoz, használja a SQL Server Management Studio egy másik gépről. Például: " \<machine\> . \<domain\> . Corp. \<company\> . com, 1433. "

   > [!IMPORTANT]
   > 
   > Részletes információkért lásd: [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáróval](data-factory-move-data-between-onprem-and-cloud.md) .
   > 
   > A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="identity-columns-in-the-target-database"></a>Azonosító oszlopok a céladatbázis
Ez a szakasz egy olyan példát mutat be, amely egy Identity oszlopot nem tartalmazó forrástábla adatait másolja egy cél táblába.

**Forrástábla:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Céltábla:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Figyelje meg, hogy a céltábla tartalmaz egy Identity oszlopot.

**Forrás-adatkészlet JSON-definíciója**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Lásd: [tárolt eljárás meghívása az SQL-fogadónak a másolási tevékenységekben](data-factory-invoke-stored-procedure-from-copy-activity.md) című cikkből megtudhatja, hogyan hívható meg az SQL-fogadó tárolt eljárása egy folyamat másolási tevékenységében.

## <a name="type-mapping-for-sql-server"></a>Típus leképezése az SQL Serverhez
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikkben említettük, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Amikor az SQL Serverről &ba helyezi az adatátvitelt, a következő leképezéseket használja az SQL típusa és a .NET típus között, és fordítva.

A leképezés megegyezik a ADO.NET adattípusának SQL Server-leképezésével.

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

## <a name="mapping-source-to-sink-columns"></a>Forrás hozzárendelése a fogadó oszlopokhoz
Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="repeatable-copy"></a>Ismételhető másolat
Amikor az adatok másolása SQL Server adatbázisba történik, a másolási tevékenység alapértelmezés szerint hozzáfűzi az adatokhoz a fogadó táblához. A UPSERT elvégzéséhez tekintse [meg az ismételhető írás SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) szóló cikket.

Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.