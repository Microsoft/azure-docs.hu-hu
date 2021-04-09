---
title: Nagyméretű adathalmaz feltöltése Azure Data Lake Storage Gen1 kapcsolat nélküli módszerekre
description: Az import/export szolgáltatás használata az adatok Azure Blob Storage-ból Azure Data Lake Storage Gen1ba való másolásához
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98700839"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Az Azure import/export szolgáltatás használata az adatkapcsolat nélküli másoláshoz az Data Lake Storage Gen1

Ebből a cikkből megtudhatja, hogyan másolhat hatalmas adatkészleteket (>200 GB-ot) a Data Lake Storage Gen1ba offline másolási módszerek használatával, például az [Azure import/export szolgáltatással](../import-export/storage-import-export-service.md). A cikkben példaként használt fájl 339 420 860 416 bájt, vagy körülbelül 319 GB a lemezen. Hívjuk ezt a 319GB. TSV fájlt.

Az Azure import/export szolgáltatás segítségével nagy mennyiségű adatok biztonságosan vihetők át az Azure Blob Storage-ba az Azure-adatközpontok számára a merevlemez-meghajtók használatával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Storage-fiók**.
* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépései](data-lake-store-get-started-portal.md)című témakört.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Az import/export szolgáltatás használata előtt bontsa az adatfájlt úgy, hogy az átkerüljön a **200 GB-nál kisebb méretű másolatokra** . Az importálási eszköz nem működik a 200 GB-nál nagyobb méretű fájlokkal. Ebben a cikkben a fájlt a 100 GB-nyi méretű darabokra bontottuk. Ezt a [Cygwin](https://cygwin.com/install.html)használatával teheti meg. A Cygwin támogatja a Linux-parancsokat. Ebben az esetben használja a következő parancsot:

```console
split -b 100m 319GB.tsv
```

A felosztott művelet a következő neveket tartalmazó fájlokat hozza létre.

* *319GB. TSV – rész – AA*
* *319GB. TSV – rész – AB*
* *319GB. TSV – rész – AC*
* *319GB. TSV – rész – ad*

## <a name="get-disks-ready-with-data"></a>Lemezek beolvasása az adatfeldolgozással

A merevlemezek előkészítéséhez kövesse az [Azure import/export szolgáltatás használata](../import-export/storage-import-export-service.md) (a **meghajtók előkészítése** szakaszban) című témakör utasításait. A teljes sorozatot:

1. Olyan merevlemez beszerzése, amely megfelel az Azure import/export szolgáltatáshoz használandó követelménynek.
2. Azonosítson egy Azure Storage-fiókot, amelyet az Azure-adatközpontba való szállítás után másol a rendszer.
3. Használja a parancssori segédprogramot az [Azure import/export eszköz](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)használatával. Az alábbi kódrészlet bemutatja, hogyan használhatja az eszközt.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    További Példákért lásd: [Az Azure import/export szolgáltatás használata](../import-export/storage-import-export-service.md) .
4. Az előző parancs egy naplófájlt hoz létre a megadott helyen. A naplófájl használatával importálási feladatot hozhat létre a [Azure Portalból](https://portal.azure.com).

## <a name="create-an-import-job"></a>Importálási feladatok létrehozása

Most létrehozhat egy importálási feladatot az [Azure import/export szolgáltatás használata](../import-export/storage-import-export-service.md) (az **importálási feladatok létrehozása** szakasz alatt) található utasítások használatával. Ehhez az importálási feladathoz, és egyéb részletek is megadják a lemezmeghajtók előkészítése során létrehozott naplófájlt.

## <a name="physically-ship-the-disks"></a>A lemezek fizikai szállítása

Mostantól fizikailag is elvégezheti a lemezek szállítását egy Azure-adatközpontba. Az adatok az importálási feladatok létrehozásakor megadott Azure Storage-blobokra lesznek másolva. Emellett a feladatok létrehozásakor, ha később úgy döntött, hogy a nyomkövetési adatokat is megadja, most visszatérhet az importálási feladathoz, és frissítheti a nyomkövetési számot.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Adatok másolása blobokból Data Lake Storage Gen1ba

Miután az importálási feladatok állapota megjelenik, ellenőrizheti, hogy az adatok elérhetők-e az Ön által megadott Azure Storage-blobokban. Ezután különböző módszerekkel helyezheti át az adatait a blobokból Azure Data Lake Storage Gen1ba. Az adatfeltöltés összes rendelkezésre álló beállítását lásd: [adatfeldolgozás a Data Lake Storage Gen1ba](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Ebben a szakaszban azokat a JSON-definíciókat ismertetjük, amelyekkel Azure Data Factory folyamat hozható létre az adatok másolásához. Ezeket a JSON-definíciókat a [Azure Portal](../data-factory/tutorial-copy-data-portal.md) vagy a [Visual studióból](../data-factory/tutorial-copy-data-dot-net.md)is használhatja.

### <a name="source-linked-service-azure-storage-blob"></a>Forráshoz társított szolgáltatás (Azure Storage blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Cél társított szolgáltatás (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Bemeneti adatkészlet

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
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

### <a name="output-data-set"></a>Kimeneti adatkészlet

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Folyamat (másolási tevékenység)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

További információ: [adatok áthelyezése az Azure Storage-blobból a Azure Data Lake Storage Gen1 Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Az adatfájlok újraépítése Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy 319 GB-os fájlból kezdtük el, és kisebb méretű fájlokra szakítottuk, hogy az Azure import/export szolgáltatással átvihető legyen. Most, hogy az adatAzure Data Lake Storage Gen1ban van, a fájlt átalakíthatja az eredeti méretre. Ehhez a következő Azure PowerShell parancsmagokat használhatja.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Következő lépések

* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
