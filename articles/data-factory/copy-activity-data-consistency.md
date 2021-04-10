---
title: Adatkonzisztencia-ellenőrzés a másolási tevékenységben
description: Ismerje meg, hogyan engedélyezhető az adatkonzisztencia-ellenőrzés a másolási tevékenységekben a Azure Data Factoryban.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373016"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Adatkonzisztencia-ellenőrzés a másolási tevékenységben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha a forrásról a célhelyre helyezi át az adatátvitelt, Azure Data Factory másolási tevékenység lehetővé teszi további adatkonzisztencia-ellenőrzés elvégzését, hogy az adatok ne legyenek csak a forrásról a célhelyre, hanem konzisztensek legyenek a forrás-és a célhely-tároló között. Ha inkonzisztens fájlok találhatók az adatáthelyezés során, megszakíthatja a másolási tevékenységet, vagy folytathatja a másolást. ehhez engedélyezze a hibatűrési beállítást az inkonzisztens fájlok kihagyásához. A kihagyott fájlnevek lekéréséhez engedélyezze a munkamenet-napló beállításait a másolási tevékenységben. További részletekért tekintse meg a [munkamenet-napló a másolási tevékenységben](copy-activity-log.md) című témakört.

## <a name="supported-data-stores-and-scenarios"></a>Támogatott adattárak és-forgatókönyvek

-   Az összes összekötő támogatja az adatkonzisztencia-ellenőrzést az FTP, az sFTP és a HTTP kivételével. 
-   Az adatkonzisztencia-ellenőrzés nem támogatott az átmeneti másolási forgatókönyvben.
-   A bináris fájlok másolásakor az adatkonzisztencia-ellenőrzés csak akkor érhető el, ha a "PreserveHierarchy" viselkedés a másolási tevékenységben van beállítva.
-   Ha több bináris fájlt másol egy másolási tevékenységben, és engedélyezve van az adatkonzisztencia-ellenőrzés, lehetősége van a másolási tevékenység megszakítására vagy a további másolásra, ha a hibatűrési beállítás lehetővé teszi a nem konzisztens fájlok kihagyását. 
-   Ha egy táblázatot egyetlen másolási tevékenységbe másol, és engedélyezve van az adatkonzisztencia-ellenőrzés, a másolási tevékenység meghiúsul, ha a forrásból beolvasott sorok száma eltér a célhelyre másolt sorok számától és a kihagyott inkompatibilis sorok számától.


## <a name="configuration"></a>Konfiguráció
Az alábbi példa egy JSON-definíciót biztosít az adatkonzisztencia-ellenőrzés engedélyezéséhez a másolási tevékenységben: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | Ha a True értéket állítja be ehhez a tulajdonsághoz, a bináris fájlok másolásakor a másolási tevékenység a forrás és a cél tároló közötti adatkonzisztencia biztosítása érdekében minden bináris fájl esetében a fájl méretét, lastModifiedDate és MD5 ellenőrzőösszegét fogja ellenőrizni. Táblázatos adatok másolásakor a másolási tevékenység a feladatok befejezése után ellenőrizni fogja a sorok teljes számát, így biztosítva, hogy a forrásból beolvasott sorok teljes száma megegyezzen a célhelyre másolt sorok számával és a kihagyott inkompatibilis sorok számával. Vegye figyelembe, hogy a másolási teljesítmény a beállítás engedélyezésével lesz hatással.  | Igaz<br/>False (alapértelmezett) | No
dataInconsistency | A skipErrorFile tulajdonság táska egyik kulcs-érték párja, amely meghatározza, hogy ki szeretné-e hagyni a inkonzisztens fájlokat. <br/> -True (igaz): az inkonzisztens fájlok kihagyásával szeretné átmásolni a többit.<br/> -False (hamis): a másolási tevékenységet a fájl inkonzisztens leállítása után szeretné megszakítani.<br/>Ügyeljen arra, hogy ez a tulajdonság csak akkor érvényes, ha bináris fájlokat másol, és a validateDataConsistency értéke TRUE (igaz).  | Igaz<br/>False (alapértelmezett) | No
logSettings | Olyan tulajdonságok csoportja, amelyek lehetővé teszik a munkamenet naplójának a kihagyott fájlok naplóba való beadását. | | No
linkedServiceName | Az [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása a munkamenet naplófájljainak tárolására. | Egy `AzureBlobStorage` vagy több `AzureBlobFS` típusú társított szolgáltatás neve, amely a naplófájlok tárolásához használt példányra hivatkozik. | No
path | A naplófájlok elérési útja. | Itt adhatja meg a naplófájlok tárolásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No

>[!NOTE]
>- Ha bináris fájlokat másol a vagy az Azure Blobba vagy Azure Data Lake Storage Gen2ba, az ADF az [Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) -t és a [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers)-t kihasználva blokkolja a szintű MD5 ellenőrzőösszeg-ellenőrzést. Ha az Azure blobon vagy Azure Data Lake Storage Gen2 adatforrásként ContentMD5 vannak, az ADF a fájlok elolvasása után a fájl szintű MD5 ellenőrzőösszeg-ellenőrzést is támogatja. Miután fájlokat másol az Azure Blobba vagy Azure Data Lake Storage Gen2 adatcélhelyként, az ADF az Azure Blobba vagy Azure Data Lake Storage Gen2ba írja az ContentMD5, amelyet az alárendelt alkalmazások tovább használhatnak adatkonzisztencia-ellenőrzés céljából.
>- Az ADF a fájl méretének ellenőrzését végzi a bináris fájlok bármely tárolási tároló közötti másolásakor.

## <a name="monitoring"></a>Figyelés

### <a name="output-from-copy-activity"></a>Kimenet a másolási tevékenységből
A másolási tevékenység teljes futtatása után megtekintheti az adatkonzisztencia-ellenőrzés eredményét az egyes másolási tevékenységek kimenetéről:

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
Az adatkonzisztencia-ellenőrzés részleteit a "dataConsistencyVerification" tulajdonságban tekintheti meg.

**VerificationResult** értéke: 
-   **Ellenőrizve**: a másolt adatai konzisztensek a forrás-és a célhely-tároló között. 
-   **NotVerified**: a másolt adatait nem ellenőrizte, hogy konzisztensek legyenek, mert nem engedélyezte a validateDataConsistency a másolási tevékenységben. 
-   Nem **támogatott**: a másolt adatok nem lettek konzisztensek, mert az adatkonzisztencia-ellenőrzés nem támogatott az adott másolási pár esetében. 

**InconsistentData** értéke: 
-   **Found**: Az ADF másolási tevékenysége inkonzisztens adathalmazt talált. 
-   **Kihagyva**: Az ADF-másolási tevékenység megtalálta és kihagyta a inkonzisztens adatértékeket. 
-   **Nincs**: Az ADF másolási tevékenysége nem talált inkonzisztens adathalmazt. Ennek oka az lehet, hogy az adatai konzisztensek a forrás-és a célhelyek között, vagy azért, mert letiltotta a validateDataConsistency a másolási tevékenységben. 

### <a name="session-log-from-copy-activity"></a>Munkamenet-napló a másolási tevékenységből

Ha úgy konfigurálja, hogy naplózza a inkonzisztens fájlt, a naplófájlt a következő elérési úttal tekintheti meg: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  A naplófájlok a CSV-fájlok lesznek. 

A naplófájl sémája a következő:

Oszlop | Leírás 
-------- | -----------  
Időbélyeg | A nem konzisztens fájlok kihagyása az ADF-ben.
Level | Az adott tétel naplózási szintje. A fájl kihagyása esetén a rendszer figyelmeztetési szinten jeleníti meg az elemeket.
OperationName | Az ADF másolási tevékenységének működési viselkedése minden fájlban. A kihagyni kívánt fájl megadásához "FileSkip" lesz.
OperationItem | A kihagyni kívánt fájl neve.
Üzenet | További információ a kihagyott fájlok okának szemléltetéséről.

A naplófájl példája a következő: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
A fenti naplófájlban láthatja, sample1.csv ki lett hagyva, mert nem sikerült ellenőrizni, hogy konzisztens-e a forrás-és a célhely-tároló között. További részleteket tudhat meg arról, hogy miért inkonzisztensek az sample1.csv, mert más alkalmazások is módosították, amikor az ADF másolási tevékenysége egy időben történik. 



## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység hibatűrése](copy-activity-fault-tolerance.md)