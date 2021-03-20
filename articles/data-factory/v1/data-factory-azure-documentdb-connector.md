---
title: Adatok áthelyezése Azure Cosmos DB
description: Megtudhatja, hogyan helyezheti át az adatok Azure Cosmos DB gyűjteménybe való áthelyezését Azure Data Factory használatával
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
robots: noindex
ms.openlocfilehash: a90b26c677c3d53794bce3c2ff48c618415e4cf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379369"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Adatok áthelyezése Azure Cosmos DBba és onnan a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-documentdb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure Cosmos db Connector v2-ben](../connector-azure-cosmos-db.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure Cosmos DBba (SQL API) való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Bármilyen támogatott forrásból származó adattárból másolhatja az adatait, hogy Azure Cosmos DB vagy Azure Cosmos DB bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

> [!IMPORTANT]
> Azure Cosmos DB-összekötő csak az SQL API-t támogatja.

Az adatok fájlként történő másolásához JSON-fájlok vagy más Cosmos DB-gyűjtemények esetében lásd: [JSON-dokumentumok importálása/exportálása](#importexport-json-documents).

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával áthelyezi az adatok Azure Cosmos DBba vagy a rendszerbe.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok Cosmos DBba való másolásához használt Data Factory JSON-definíciókkal rendelkező mintákhoz lásd a jelen cikk [JSON-példák](#json-examples) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek a Cosmos DB specifikus entitások definiálásához használhatók Data Factory:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Azure Cosmos DB társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **DocumentDb** |Yes |
| connectionString |Azure Cosmos DB adatbázishoz való kapcsolódáshoz szükséges információk megadásához. |Yes |

Példa:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló &i tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. A (z), például a szerkezet, a rendelkezésre állás és az adathalmazok JSON-szabályzata hasonló az összes adatkészlet-típushoz (Azure SQL, Azure Blob, Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **DocumentDbCollection** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| collectionName |A Cosmos DB dokumentum-gyűjtemény neve. |Yes |

Példa:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Séma Data Factory szerint
A séma nélküli adattárakhoz (például Azure Cosmos DB) a Data Factory szolgáltatás a következő módszerek egyikével vezeti ki a sémát:

1. Ha az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságának használatával határozza meg, akkor a Data Factory szolgáltatás sémaként értékeli ezt a struktúrát. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket fog megadni.
2. Ha nem határozza meg az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságával, akkor a Data Factory szolgáltatás az adat első során kikövetkezteti a sémát. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányzik a másolási művelet eredményében.

Ezért a séma nélküli adatforrások esetében az ajánlott eljárás az adatszerkezetek meghatározása a **Structure** tulajdonság használatával.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek meghatározására szolgáló & tulajdonságok teljes listájáért tekintse meg a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikket. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

A tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok és a másolási tevékenységek esetében eltérőek lehetnek, a források típusától és a mosdótól függően.

Másolási tevékenység esetén, ha a forrás típusa **DocumentDbCollectionSource** , a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| lekérdezés |Itt adhatja meg az adatolvasási lekérdezést. |Azure Cosmos DB által támogatott lekérdezési karakterlánc. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Ha nincs megadva, a futtatott SQL-utasítás: `select <columns defined in structure> from mycollection` |
| nestingSeparator |A dokumentum beágyazásának jelzésére szolgáló speciális karakter |Bármilyen karakter. <br/><br/>Azure Cosmos DB a JSON-dokumentumok NoSQL-tárolója, ahol beágyazott struktúrák engedélyezettek. Azure Data Factory lehetővé teszi, hogy a felhasználó a nestingSeparator-on keresztül megjelölje a hierarchiát, amely a fenti példákban ".". Az elválasztó használatával a másolási tevékenység a "név. First", a "név. középső" és a "name. Last" kifejezésnek megfelelően létrehozza a "Name" objektumot az első, középső és utolsó értékkel. |No |

A **DocumentDbCollectionSink** a következő tulajdonságokat támogatja:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevének egy speciális karaktere, amely azt jelzi, hogy beágyazott dokumentumra van szükség. <br/><br/>A fenti példában például a `Name.First` kimenet táblában a következő JSON-struktúra jön létre a Cosmos db dokumentumban:<br/><br/>"Name": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Az alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Az alapértelmezett érték `.` (pont). |
| writeBatchSize |A Azure Cosmos DB szolgáltatás számára a dokumentumok létrehozásához szükséges párhuzamos kérelmek száma.<br/><br/>Ennek a tulajdonságnak a használatával javíthatja a teljesítményt a Cosmos DBba való adatmásoláskor. Nagyobb teljesítmény várható, ha a writeBatchSize növelése miatt Cosmos DB több párhuzamos kérelem küldése történik. Azonban el kell kerülnie a szabályozást, amely a következő hibaüzenetet eredményezi: "a kérelmek aránya nagy".<br/><br/>A szabályozást számos tényező határozza meg, többek között a dokumentumok mérete, a dokumentumok feltételeinek száma, a célhelyek indexelési szabályzata stb. A másolási műveleteknél jobb gyűjteményt (például S3) is használhat, hogy a lehető legtöbb átviteli sebesség elérhető legyen (2 500 kérelem egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várakozási idő a művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |

## <a name="importexport-json-documents"></a>JSON-dokumentumok importálása/exportálása
A Cosmos DB-összekötő használatával könnyedén elvégezheti a

* A különböző forrásokból származó JSON-dokumentumok importálása Cosmos DBba, beleértve az Azure blobot, a Azure Data Lake, a helyszíni fájlrendszert vagy más, a Azure Data Factory által támogatott fájl-alapú tárolókat.
* JSON-dokumentumok exportálása Cosmos DB gyűjteményből különböző file-alapú áruházakba.
* Áttelepítheti az adatait két Cosmos DB gyűjtemény között.

Az ilyen séma-független példány eléréséhez
* A másolás varázsló használatakor tekintse **meg az "Exportálás a JSON-fájlokban vagy a Cosmos db-gyűjtemény"** lehetőséget.
* JSON-szerkesztés használatakor ne határozza meg a "Structure" szakaszt Cosmos DB adatkészletben és a "nestingSeparator" tulajdonságban Cosmos DB forrás/fogadó másolási tevékenységben. A JSON-fájlokba való importáláshoz/exportáláshoz a file Store adatkészletben adja meg a formátum típusát "JsonFormat", config "filePattern", és hagyja ki a REST formátum beállításait: [JSON-formátum](data-factory-supported-file-and-compression-formats.md#json-format) szakasz, részletek.

## <a name="json-examples"></a>JSON-példák
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok Azure Cosmos DB és az Azure Blob Storageba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott, a Azure Data Factoryban található másolási tevékenység használatával.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Példa: adatok másolása Azure Cosmos DBból az Azure-Blobba
Az alábbi minta a következőket mutatja be:

1. [DocumentDb](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [DocumentDbCollection](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [DocumentDbCollectionSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta Azure Cosmos DB az Azure Blobba másolja az adatmásolt fájlokat. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure Cosmos DB társított szolgáltatás:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
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
**Azure Document DB bemeneti adatkészlet:**

A minta azt feltételezi, hogy rendelkezik egy **személy** nevű gyűjteményrel egy Azure Cosmos db adatbázisban.

A "külső": "true" beállítás és a externalData házirend adatainak megadása a Azure Data Factory szolgáltatás, amely a tábla kívül esik az adat-előállítón, és amelyeket nem az adat-előállító tevékenysége hozott létre.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure-Blob kimeneti adatkészlete:**

Az adatokat a rendszer óránként egy új blobba másolja, és a blob elérési útját a megadott dátum és idő (óra) részletességgel mutatja.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Példa JSON-dokumentumra egy Cosmos DB-adatbázisban lévő személy gyűjteményében:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB támogatja a dokumentumok lekérdezését az SQL-hez hasonló szintaxissal hierarchikus JSON-dokumentumokon keresztül.

Példa:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

A következő folyamat átmásolja az adatait a Azure Cosmos DB adatbázisban található person gyűjteményből egy Azure-blobba. A másolási tevékenység részeként a bemeneti és a kimeneti adatkészletek meg lettek adva.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Példa: adatok másolása az Azure Blobból a Azure Cosmos DBba
Az alábbi minta a következőket mutatja be:

1. DocumentDb típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. DocumentDbCollection típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és DocumentDbCollectionSink használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta adatok másolása az Azure blobból a Azure Cosmos DBba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

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
**Azure Cosmos DB társított szolgáltatás:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob bemeneti adatkészlet:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB kimeneti adatkészlet:**

A minta a "person" nevű gyűjteménybe másol egy adott adathalmazt.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
A következő folyamat az Azure-Blobból származó adatok másolását végzi el a Cosmos DB személy gyűjteményéből. A másolási tevékenység részeként a bemeneti és a kimeneti adatkészletek meg lettek adva.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Ha a minta blob bemenete

```
1,John,,Doe
```
Ezután a Cosmos DB kimeneti JSON a következőképpen fog megjelenni:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB a JSON-dokumentumok NoSQL-tárolója, ahol beágyazott struktúrák engedélyezettek. A Azure Data Factory lehetővé teszi a felhasználó számára, hogy a hierarchiát a **nestingSeparator**-on keresztül jelölje meg, amely ebben a példában ".". Az elválasztó használatával a másolási tevékenység a "név. First", a "név. középső" és a "name. Last" kifejezésnek megfelelően létrehozza a "Name" objektumot az első, középső és utolsó értékkel.

## <a name="appendix"></a>Függelék
1. **Kérdés:** Támogatja a másolási tevékenység a meglévő rekordok frissítését?

    **Válasz:** Nem.
2. **Kérdés:** Hogyan próbálkozik újra egy másolattal, hogy Azure Cosmos DB a már másolt rekordokkal?

    **Válasz:** Ha a rekordok "ID" mezővel rendelkeznek, és a másolási művelet egy ugyanazzal az AZONOSÍTÓval rendelkező rekordot próbál beszúrni, a másolási művelet hibát jelez.
3. **Kérdés:** Data Factory támogatja a [tartomány vagy a kivonatoló alapú adatparticionálást](../../cosmos-db/partitioning-overview.md)?

    **Válasz:** Nem.
4. **Kérdés:** Megadhatok egynél több Azure Cosmos DB gyűjteményt egy táblához?

    **Válasz:** Nem. Jelenleg csak egy gyűjtemény adható meg.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.