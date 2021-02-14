---
title: Adatok áthelyezése az Azure-táblába
description: Megtudhatja, hogyan helyezheti át adatait az Azure Table Storage Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc8e0272dc690ed541883ae943c118cbbe2f1854
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392119"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Adatok áthelyezése az Azure Table-be és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-table-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-table-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure Table Storage Connector v2-ben](../connector-azure-table-storage.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure-Table Storageba való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel. 

Bármilyen támogatott forrásból származó adattárból átmásolhatja az adatait az Azure Table Storageba vagy az Azure Table Storageból bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely az Azure-Table Storage különböző eszközök/API-k használatával helyezi át az adatátvitelt.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait: 

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok Azure Table Storageba való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében lásd a jelen cikk [JSON-példák](#json-examples) című szakaszát.

A következő szakaszokban részletesen ismertetjük az Azure Table Storage-re jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat: 

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az Azure Blob Storage egy Azure-beli adatgyárhoz való összekapcsolásához két különböző típusú társított szolgáltatás használható. Ezek a következők: **AzureStorage** társított szolgáltatás és **AzureStorageSas** társított szolgáltatás. Az Azure Storage társított szolgáltatása biztosítja az Azure Storage-hoz való globális hozzáféréssel rendelkező adatelőállítót. Míg az Azure Storage SAS (közös hozzáférésű aláírás) társított szolgáltatás az adat-előállító számára korlátozott/időhöz kötött hozzáférést biztosít az Azure Storage-hoz. A két társított szolgáltatás között nincs más különbség. Válassza ki az igényeinek megfelelő társított szolgáltatást. A következő szakaszokban további részleteket talál a két társított szolgáltatásról.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **AzureTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Azon tábla neve, amely a társított szolgáltatás által hivatkozott Azure Table Database-példányban található. |Igen. Ha egy Táblanév azureTableSourceQuery nélkül van megadva, a rendszer a táblából származó összes rekordot átmásolja a célhelyre. Ha a azureTableSourceQuery is meg van adva, a rendszer a lekérdezésnek megfelelő rekordokat a célhelyre másolja. |

### <a name="schema-by-data-factory"></a>Séma Data Factory szerint
A séma nélküli adattárak (például az Azure Table) esetében a Data Factory szolgáltatás a következő módszerek egyikével vezeti le a sémát:

1. Ha az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságának használatával határozza meg, akkor a Data Factory szolgáltatás sémaként értékeli ezt a struktúrát. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket biztosít.
2. Ha nem határozza meg az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságával, Data Factory kikövetkezteti a sémát az adat első sorának használatával. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát, a másolási művelet eredményeképpen egyes oszlopok kimaradnak.

Ezért a séma nélküli adatforrások esetében az ajánlott eljárás az adatszerkezetek meghatározása a **Structure** tulajdonság használatával.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti adatkészletek, valamint a házirendek minden típusú tevékenységhez elérhetők.

A tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

A **AzureTableSource** a következő tulajdonságokat támogatja a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableSourceQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |Azure Table lekérdezési karakterlánc. Tekintse meg a példákat a következő szakaszban. |Nem. Ha egy Táblanév azureTableSourceQuery nélkül van megadva, a rendszer a táblából származó összes rekordot átmásolja a célhelyre. Ha a azureTableSourceQuery is meg van adva, a rendszer a lekérdezésnek megfelelő rekordokat a célhelyre másolja. |
| azureTableSourceIgnoreTableNotFound |Jelezze, hogy nem létezik-e a lenyelés a tábla kivételével. |IGAZ<br/>HAMIS |No |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery-példák
Ha az Azure Table oszlop karakterlánc típusú:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Ha az Azure Table oszlop datetime típusú:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

A **AzureTableSink** a következő tulajdonságokat támogatja a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |A fogadó által használható alapértelmezett partíciós kulcs értéke. |Egy karakterlánc-érték. |No |
| azureTablePartitionKeyName |Adja meg annak az oszlopnak a nevét, amelynek értékeit partíciós kulcsként használja a rendszer. Ha nincs megadva, a rendszer a AzureTableDefaultPartitionKeyValue használja a partíciós kulcsként. |Egy oszlop neve. |No |
| azureTableRowKeyName |Adja meg annak az oszlopnak a nevét, amelynek az oszlop értékeit a rendszer a sor kulcsaként használja. Ha nincs megadva, használja az egyes sorok GUID azonosítóját. |Egy oszlop neve. |No |
| azureTableInsertType |Az adatgyűjtés módja az Azure Table-be.<br/><br/>Ez a tulajdonság azt szabályozza, hogy a kimeneti táblában található, egyező partícióval és sorokkal rendelkező meglévő sorok felülírják vagy összevonták-e az értékeket. <br/><br/>Ha szeretné megtudni, hogyan működnek ezek a beállítások (egyesítés és csere), tekintse meg az [entitás beszúrása és egyesítése](/rest/api/storageservices/Insert-Or-Merge-Entity) , illetve az [entitások beszúrása vagy cseréje](/rest/api/storageservices/Insert-Or-Replace-Entity) témaköröket. <br/><br> Ez a beállítás a sor szintjére vonatkozik, nem a tábla szintjére, és egyik sem törli a bemeneti tábla azon sorait, amelyek nem szerepelnek a bemenetben. |egyesítés (alapértelmezett)<br/>csere |No |
| writeBatchSize |Beilleszti az adatbevitelt az Azure-táblába, amikor a writeBatchSize vagy a writeBatchTimeout találat. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Beilleszti az adatbevitelt az Azure-táblába a writeBatchSize vagy a writeBatchTimeout találatakor |időtartomány<br/><br/>Például: "00:20:00" (20 perc) |Nem (alapértelmezett érték a Storage-ügyfél alapértelmezett időtúllépési értéke 90 mp) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Társítsa a forrás oszlopot a cél oszlophoz a Translator JSON tulajdonság használatával, mielőtt a azureTablePartitionKeyName használja a Destination (cél) oszlopot.

Az alábbi példában a forrás oszlop DivisionID a következő cél oszlopra van leképezve: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
A DivisionID a partíciós kulcsként van megadva.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>JSON-példák
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok az Azure Table Storage és az Azure Blob Database-be. Az adatok azonban **közvetlenül** a forrásokból is átmásolhatók bármelyik támogatott mosogatóba. További információ: "támogatott adattárak és-formátumok" című rész, az [adatok áthelyezése másolási tevékenység használatával](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Példa: adatok másolása az Azure Table-ből az Azure Blobba
A következő minta a következőket mutatja be:

1. Egy [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú társított szolgáltatás (mindkét Table & blob esetében használatos).
2. [AzureTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
4. A AzureTableSource és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy Azure-táblában lévő alapértelmezett partícióhoz tartozó, óránkénti adatmásolási adatmásolt. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure Storage-beli társított szolgáltatás:**

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
Azure Data Factory az Azure Storage társított szolgáltatásainak két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első beállításnál megadhatja a kapcsolati karakterláncot, amely tartalmazza a fiók kulcsát, és a későbbiekben a közös hozzáférésű aláírás (SAS) URI-ját is megadja. Részletekért lásd a [társított szolgáltatások](#linked-service-properties) szakaszt.  

**Azure Table bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát az Azure táblában.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```JSON
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

**Másolási tevékenység AzureTableSource és BlobSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **AzureTableSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **AzureTableSourceQuery** tulajdonsággal megadott SQL-lekérdezés minden órában kiválasztja az alapértelmezett partíció adatait.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Példa: adatok másolása az Azure Blobból az Azure Table-be
A következő minta a következőket mutatja be:

1. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú társított szolgáltatás (mindkét tábla & blob esetében használatos)
2. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
3. [AzureTable](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
4. A [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureTableSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok másolását egy Azure-blobból az Azure-táblázatba óránként történik. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure Storage (az Azure Table & Blobhoz) társított szolgáltatás:**

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

Azure Data Factory az Azure Storage társított szolgáltatásainak két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első beállításnál megadhatja a kapcsolati karakterláncot, amely tartalmazza a fiók kulcsát, és a későbbiekben a közös hozzáférésű aláírás (SAS) URI-ját is megadja. Részletekért lásd a [társított szolgáltatások](#linked-service-properties) szakaszt.

**Azure Blob bemeneti adatkészlet:**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
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

**Azure-tábla kimeneti adatkészlete:**

A minta az Azure Table "Sajáttábla" nevű táblájába másolja az adatmásolt fájlokat. Hozzon létre egy olyan Azure-táblázatot, amely azonos számú oszlopot tartalmaz, mint amennyit a blob CSV-fájljának tárolására vár. Minden órában új sor kerül a táblázatba.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Másolási tevékenység BlobSource és AzureTableSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Típus leképezése az Azure Table-hez
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokba.

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Amikor az Azure-táblázatból &ba helyezi az adatátvitelt, az Azure- [Table Service által definiált következő leképezések](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) az Azure Table OData-típusok és a .net-típus között használatosak, és fordítva.

| OData-adatok típusa | .NET-típus | Részletek |
| --- | --- | --- |
| EDM. Binary |bájt [] |Legfeljebb 64 KB méretű bájtok tömbje. |
| Edm.Boolean |logikai |Logikai érték. |
| EDM. DateTime |DateTime |64 bites érték, amely egyezményes világidő (UTC) szerint van kifejezve. A támogatott dátum/idő tartomány 12:00 éjféltől kezdődik, január 1-től 1601 A.D. (CE), UTC. A tartomány 9999. december 31-ig ér véget. |
| Edm.Double |double |64 bites lebegőpontos érték. |
| EDM. GUID |Guid |Egy 128 bites globálisan egyedi azonosító. |
| Edm.Int32 |Int32 |32 bites egész szám. |
| Edm.Int64 |Int64 |64 bites egész szám. |
| Edm.String |Sztring |Egy UTF-16 kódolású érték. A sztringek értéke legfeljebb 64 KB lehet. |

### <a name="type-conversion-sample"></a>Típus átalakítási minta
A következő minta az adatok Azure-Blobból Azure-táblázatba való másolását írja elő konverziók használatával.

Tegyük fel, hogy a blob-adatkészlet CSV formátumú, és három oszlopot tartalmaz. Ezek egyike egy datetime típusú oszlop, amely egyéni datetime formátumú, és rövidített francia neveket használ a hét napjára.

Adja meg a blob-forrás adatkészletét az alábbi módon, valamint az oszlopokhoz tartozó definíciókat.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
A típusnak az Azure Table OData típusról .NET-típusra való leképezése miatt a táblázatot az alábbi sémával kell megadnia az Azure táblában.

**Azure Table séma:**

| Oszlop neve | Típus |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |EDM. DateTime |

Ezután adja meg az Azure Table-adatkészletet az alábbiak szerint. Nem kell megadnia a "Structure" szakaszt a típus adataival, mivel a típus adatai már meg vannak adva az alapul szolgáló adattárban.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

Ebben az esetben Data Factory automatikusan beírja a konverziókat, beleértve a DateTime mezőt az egyéni datetime formátummal a "fr-fr" kulturális környezet használatával, amikor a Blobból az Azure-ba helyezi át az adatok áthelyezését.

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
További információ az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló fő tényezőkről Azure Data Factory és az optimalizálás különböző módjairól: a [másolási tevékenység teljesítményének & finomhangolási útmutatója](data-factory-copy-activity-performance.md).