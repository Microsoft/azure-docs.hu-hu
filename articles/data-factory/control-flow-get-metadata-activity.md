---
title: Metaadatok beolvasása tevékenység Azure Data Factory
description: Megtudhatja, hogyan használhatja a metaadatok beolvasása tevékenységet egy Data Factory folyamaton.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: f860225862dcbfb79535acfbd6eeb89a217e7ae9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385489"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Metaadatok beolvasása tevékenység Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A metaadatok beolvasása tevékenység segítségével lekérheti a Azure Data Factoryban található adatok metaadatait. Ezt a tevékenységet a következő esetekben használhatja:

- Ellenőrizze az adatok metaadatait.
- Folyamat elindítása, ha az adatgyűjtés kész/elérhető.

A következő funkciók érhetők el a vezérlési folyamatban:

- Az érvényesítés végrehajtásához használhatja a metaadatok beolvasása tevékenységből a feltételes kifejezésekben szereplő kimenetet.
- A folyamat akkor aktiválható, ha a feltételt a "Do" utasításon keresztül, a hurok nélkül kell megtenni.

## <a name="capabilities"></a>Képességek

A metaadatok beolvasása tevékenység bemenetként fogadja az adatkészletet, és a metaadatok adatait adja vissza kimenetként. Jelenleg a következő összekötők és a megfelelő lekérdezhető metaadatok támogatottak. A visszaadott metaadatok maximális mérete körülbelül 4 MB.

>[!NOTE]
>Ha a metaadatok lekérése tevékenységet egy saját üzemeltetésű integrációs modulon futtatja, a legújabb funkciók a 3,6-es vagy újabb verziókban támogatottak.

### <a name="supported-connectors"></a>Támogatott összekötők

**File Storage**

| Összekötő/metaadatok | itemName<br>(fájl/mappa) | itemType<br>(fájl/mappa) | size<br>fájl | létrehozott<br>(fájl/mappa) | lastModified<br>(fájl/mappa) |childItems<br>mappa |contentMD5<br>fájl | szerkezet<br/>fájl | columnCount<br>fájl | létezik<br>(fájl/mappa) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| [Azure Blob Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [Fájlrendszer](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

- Ha a metaadatok lekérése tevékenységgel egy mappára kattint, győződjön meg arról, hogy az adott mappához a lista/végrehajtás engedély van megadva.
- Az Amazon S3 és a Google Cloud Storage esetében `lastModified` a gyűjtőre és a kulcsra, de nem a virtuális mappára vonatkozik, és a `exists` gyűjtőre és a kulcsra, de nem az előtagra vagy a virtuális mappára vonatkozik.
- Az Azure Blob Storage esetében a `lastModified` tárolóra és a blobra vonatkozik, de a virtuális mappára nem.
- `lastModified` a szűrő jelenleg az alárendelt elemek szűrésére vonatkozik, de a megadott mappa vagy fájl nem.
- A mappák/fájlok helyettesítő szűrője nem támogatott a metaadatok beolvasása tevékenység esetén.
- `structure` a és `columnCount` a nem támogatott, ha bináris, JSON vagy XML-fájlokból olvas be metaadatokat.

**Relációs adatbázis**

| Összekötő/metaadatok | szerkezet | columnCount | létezik |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Felügyelt Azure SQL-példány](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>Metaadatok beállításai

A következő metaadatokat adhatja meg a metaadatok beolvasása tevékenység mezőinek listájában a megfelelő információk lekéréséhez:

| Metaadat típusa | Description |
|:--- |:--- |
| itemName | A fájl vagy mappa neve. |
| itemType | A fájl vagy mappa típusa. A visszaadott érték a következő: `File` vagy `Folder` . |
| size | A fájl mérete bájtban megadva. Csak a fájlokra érvényes. |
| létrehozott | A fájl vagy mappa dátum és idő (datetime) létrehozása. |
| lastModified | A fájl vagy mappa utolsó módosításának datetime értéke. |
| childItems | A megadott mappában található almappák és fájlok listája. Csak a mappákra érvényes. A visszaadott érték az egyes alárendelt elemek nevének és típusának listája. |
| contentMD5 | A fájl MD5-je. Csak a fájlokra érvényes. |
| szerkezet | A fájl vagy a viszonyítási adatbázis táblázatának adatstruktúrája. A visszaadott érték az oszlopnevek és az oszlopok típusának listája. |
| columnCount | A fájl vagy a rokon tábla oszlopainak száma. |
| létezik| Azt határozza meg, hogy létezik-e fájl, mappa vagy tábla. Ha `exists` meg van adva a metaadatok beolvasása mező listában, a tevékenység nem fog működni, még akkor sem, ha a fájl, mappa vagy tábla nem létezik. Ehelyett `exists: false` a rendszer visszaadja a kimenetet. |

>[!TIP]
>Ha szeretné ellenőrizni, hogy egy fájl, mappa vagy tábla létezik-e, `exists` a metaadatok beolvasása tevékenység mezők listájában adhatja meg. Ezt követően a tevékenység kimenetében is megtekintheti az `exists: true/false` eredményt. Ha `exists` nincs megadva a mezőlista, a metaadatok beolvasása tevékenység sikertelen lesz, ha az objektum nem található.

>[!NOTE]
>Ha a fájl tárolja a metaadatokat, és `modifiedDatetimeStart` konfigurálja `modifiedDatetimeEnd` a vagy a-t, a `childItems` kimenetben csak a megadott tartományon belüli utolsó módosítási időt tartalmazó fájlok jelennek meg. A nem tartalmazza az almappákban található elemeket.

## <a name="syntax"></a>Syntax

**Metaadatok beolvasása tevékenység**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**Adathalmaz**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

A metaadatok beolvasása tevékenység jelenleg a következő típusú metaadatokat tudja visszaadni:

Tulajdonság | Leírás | Kötelező
-------- | ----------- | --------
Mezőlista | A metaadatokhoz szükséges információk típusai. A támogatott metaadatokkal kapcsolatos részletekért tekintse meg a jelen cikk [metaadat-beállítások](#metadata-options) című szakaszát. | Yes 
adatkészlet | A metaadatok beolvasása tevékenység által a metaadatokat lekérő hivatkozási adatkészlet. A támogatott összekötők információit a [képességek](#capabilities) című szakaszban találja. Az adatkészlet szintaxisával kapcsolatos részletekért tekintse meg az összekötőhöz kapcsolódó témaköröket. | Yes
formatSettings | Alkalmazza a Format Type adatkészlet használatakor. | No
storeSettings | Alkalmazza a Format Type adatkészlet használatakor. | No

## <a name="sample-output"></a>Példakimenet

A metaadatok beolvasása eredmények a tevékenység kimenetében jelennek meg. A következő két minta kiterjedt metaadat-beállításokat jelenít meg. Ha az eredményeket egy későbbi tevékenységben szeretné használni, használja a következő mintát: `@{activity('MyGetMetadataActivity').output.itemName}` .

### <a name="get-a-files-metadata"></a>Fájl metaadatainak beolvasása

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>Mappa metaadatainak beolvasása

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések
További információ a Data Factory által támogatott egyéb irányítási folyamatokról:

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
