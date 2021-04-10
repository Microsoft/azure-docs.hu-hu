---
title: Munkamenet-napló a másolási tevékenységben
description: Ismerje meg, hogyan engedélyezheti a munkamenet-naplót a másolási tevékenységekben a Azure Data Factoryban.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385268"
---
#  <a name="session-log-in-copy-activity"></a>Munkamenet-napló a másolási tevékenységben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A másolt fájlneveket a másolási tevékenységben naplózhatja, ami segít biztosítani, hogy az adatok ne csak a forrásról a célhelyre legyenek átmásolva, hanem a forrás-és a célhelyen is konzisztensek legyenek, ha áttekinti a másolt fájlokat a másolási tevékenység munkamenet-naplófájljaiban.  

Ha a másolási tevékenységben engedélyezte a hibatűrési beállítást a hibás adatértékek kihagyásához, a kihagyott fájlok és a kihagyott sorok is bejelentkezhetnek.  A [másolási tevékenységben](copy-activity-fault-tolerance.md)további részleteket tudhat meg a hibatűrésről. 

## <a name="configuration"></a>Konfiguráció
Az alábbi példa egy JSON-definíciót biztosít a munkamenet-napló másolási tevékenységben való engedélyezéséhez: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
    },
    "validateDataConsistency": true,
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
}
```

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | -------- 
enableCopyActivityLog | Ha igaz értékre állítja, lehetősége lesz a másolt fájlok, a kihagyott fájlok vagy a kihagyott sorok naplózására.  | Igaz<br/>False (alapértelmezett) | No
Naplózási szint | Az "info" a másolt fájlok, a kihagyott fájlok és a kihagyott sorok naplózására fog bejelentkezni. A "figyelmeztetés" csak a kihagyott fájlokat és a kihagyott sorokat fogja naplózni.  | Információ<br/>Figyelmeztetés (alapértelmezett) | No
enableReliableLogging | Ha az értéke igaz, a másolási tevékenység megbízható módban azonnal kiüríti a naplókat, amint az egyes fájlok a célhelyre másolódnak.  Ha olyan nagy mennyiségű fájlt másol át, amely megbízható naplózási módban van engedélyezve a másolási tevékenység során, akkor a másolási átviteli sebesség hatással lesz, mivel az egyes fájlok másolásához dupla írási műveletek szükségesek. Az egyik kérelem a célhelyre mutat, és egy másik kérelem a log Storage-tárolóba.  A másolási tevékenység a legjobb lehetőség módban egy adott időtartamon belül üríti a naplókat a rekordok kötegével, ahol a másolási sebesség sokkal kevésbé lesz hatással. Ebben a módban nem garantáljuk a naplózás teljességét és időszerűségét, mivel néhány lehetőség van arra, hogy a naplózott események utolsó kötege nem lett kiürítve a naplófájlba, ha a másolási tevékenység meghiúsult. Ebben a pillanatban látni fogja, hogy a rendszer nem naplózza a célhelyre másolt néhány fájlt.  | Igaz<br/>False (alapértelmezett) | No
logLocationSettings | Olyan tulajdonságok csoportja, amelyek segítségével megadhatja a munkamenet-naplók tárolására szolgáló helyet. | | No
linkedServiceName | Az [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása a munkamenet naplófájljainak tárolására. | Egy `AzureBlobStorage` vagy több `AzureBlobFS` típusú társított szolgáltatás neve, amely a naplófájlok tárolásához használt példányra hivatkozik. | No
path | A naplófájlok elérési útja. | Itt adhatja meg a naplófájlok tárolásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No


## <a name="monitoring"></a>Figyelés

### <a name="output-from-copy-activity"></a>Kimenet a másolási tevékenységből
A másolási tevékenység teljes futtatása után megtekintheti a naplófájlok elérési útját az egyes másolási tevékenységek kimenetéről. A naplófájlok a következő elérési útról találhatók: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  A naplófájlok a CSV-fájlok lesznek. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

> [!NOTE]
> Ha a `enableCopyActivityLog` tulajdonság értéke `Enabled` , a rendszer létrehozza a naplófájlok nevét.

### <a name="the-schema-of-the-log-file"></a>A naplófájl sémája

A következő egy naplófájl sémája.

Oszlop | Leírás 
-------- | -----------  
Időbélyeg | Az a timestamp, amikor az ADF beolvassa, írja vagy kihagyja az objektumot.
Level | Az adott tétel naplózási szintje. Ez lehet "figyelmeztetés" vagy "információ".
OperationName | Az ADF másolási tevékenységének műveleti viselkedése minden objektumon. Ez lehet "FileRead", "FileWrite", "FileSkip" vagy "TabularRowSkip".
OperationItem | A fájlnevek vagy a kihagyott sorok.
Üzenet | További információ annak megjelenítéséhez, hogy a fájl el lett-e olvasva a forrás-vagy a célhelyre. Azt is megteheti, hogy a fájl vagy a sorok kimaradnak.

A következő példa egy naplófájlt mutat be. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
A fenti naplófájlban láthatja, sample1.csv ki lett hagyva, mert nem sikerült ellenőrizni, hogy konzisztens-e a forrás-és a célhely-tároló között. További részleteket tudhat meg arról, hogy miért inkonzisztensek az sample1.csv, mert más alkalmazások is módosították, amikor az ADF másolási tevékenysége egy időben történik. Azt is megteheti, hogy a sample2.csv sikeresen átmásolta a forrásról a célhelyre.

A naplófájlok részletesebb elemzéséhez több elemzési motor is használható.  Íme néhány példa arra, hogy az SQL-lekérdezés használatával elemezze a naplófájlt úgy, hogy a CSV-naplófájlt importálja az SQL Database-be, ahol a tábla neve SessionLogDemo.  

-   Adja meg a másolt fájlok listáját. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Egy adott időtartományon belül másolt fájlok listájának megadását adja meg. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Egy adott fájlt adjon meg a másolt idejével és metaadataival. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Egy időtartományon belül másolt metaadatokat tartalmazó fájlok listáját adja meg. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   A kihagyott fájlok listájának megadására szolgál. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Adja meg az okot, amiért egy adott fájl kimaradt. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   Adja meg a kihagyott fájlok listáját ugyanezen ok miatt: a blob-fájl nem létezik. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   Adja meg a fájlnevet, amelyhez a leghosszabb időt kell másolni.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység hibatűrése](copy-activity-fault-tolerance.md)
- [Másolási tevékenység adatkonzisztenciája](copy-activity-data-consistency.md)
