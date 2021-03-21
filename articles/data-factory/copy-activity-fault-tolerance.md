---
title: Másolási tevékenység hibatűrése az Azure Data Factoryban
description: Ismerje meg, hogyan adhat hozzá hibatűrést a Azure Data Factory másolásához a nem kompatibilis információk kiugrásával.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390963"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Másolási tevékenység hibatűrése az Azure Data Factoryban
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuális verzió](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Amikor Adatmásolást végez a forrásról a célhelyre, Azure Data Factory másolási tevékenység bizonyos szintű hibatűrést biztosít, hogy megakadályozza az adatáthelyezési hibák megszakadását. Tegyük fel például, hogy több millió sort másol a forrásról a célhelyre, ahol a céladatbázis egy elsődleges kulcsát hozta létre, de a forrásadatbázis nem rendelkezik meghatározott elsődleges kulcsokkal. Ha a duplikált sorokat a forrásról a célhelyre másolja, akkor a célként megadott adatbázis PK-megsértési hibáját fogja érinteni. A másolási tevékenység jelenleg két módszert kínál az ilyen hibák kezelésére: 
- A másolási tevékenységet a hiba előfordulását követően is megszakíthatja. 
- Továbbra is másolhatja a többit, ha engedélyezi a hibatűrést a nem kompatibilis adatok kihagyásához. Például ugorja át a duplikált sort ebben az esetben. A kihagyott adatok a másolási tevékenységen belüli munkamenet-napló engedélyezésével is bejelentkezhetnek. További részletekért tekintse meg a [munkamenet-napló a másolási tevékenységben](copy-activity-log.md) című témakört.

## <a name="copying-binary-files"></a>Bináris fájlok másolása 

Az ADF a következő hibatűrési forgatókönyveket támogatja a bináris fájlok másolásakor. Dönthet úgy is, hogy megszakítja a másolási tevékenységet, vagy folytatja a REST másolását a következő helyzetekben:

1. Az ADF által másolandó fájlokat a többi alkalmazás is törli.
2. Egyes mappák vagy fájlok nem engedélyezik az ADF elérését, mert a fájlok vagy mappák ACL-jei magasabb jogosultsági szint szükségesek, mint az ADF-ben konfigurált kapcsolati adatok.
3. Egy vagy több fájl nem ellenőrzi, hogy konzisztens-e a forrás-és a cél tárolóban, ha engedélyezi az adatkonzisztencia-ellenőrzés beállítást az ADF-ben.

### <a name="configuration"></a>Konfiguráció 
Ha bináris fájlokat másol a Storage Stores között, a következő módon engedélyezheti a hibatűrést: 

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
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
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
skipErrorFile | Az adatáthelyezés során kihagyni kívánt hibák típusait meghatározó tulajdonságok csoportja. | | No
fileMissing | A skipErrorFile tulajdonság táska egyik kulcs-érték párja határozza meg, hogy szeretné-e kihagyni a fájlokat, amelyeket más alkalmazások törölnek, amikor az ADF-et időközben másolja. <br/> -True (igaz): a többi alkalmazás által törölt fájlok kihagyásával szeretné átmásolni a többit. <br/> -False (hamis): a másolási tevékenységet szeretné megszakítani, ha a fájlok törlése a forrás-áruházból történik az adatáthelyezés közepén. <br/>Ügyeljen arra, hogy ez a tulajdonság a True (igaz) értékre van beállítva. | True (alapértelmezett) <br/>Hamis | No
fileForbidden | A skipErrorFile tulajdonság táska egyik kulcs-érték párokkal határozható meg, hogy ki szeretné-e hagyni az adott fájlokat, ha a fájlok vagy mappák ACL-jei magasabb jogosultsági szintet igényelnek, mint az ADF-ben konfigurált kapcsolatok. <br/> -True (igaz): a fájlok kihagyásával szeretné átmásolni a többit. <br/> -False (hamis): a másolási tevékenységet a mappák vagy fájlok engedélyeinek lekérése után szeretné megszakítani. | Igaz <br/>False (alapértelmezett) | No
dataInconsistency | A skipErrorFile tulajdonság táska egyik kulcs-érték párja, amely meghatározza, hogy ki szeretné-e hagyni a forrás-és a cél-tároló közötti inkonzisztens adatmennyiséget. <br/> -True (igaz): az inkonzisztens adatok kihagyásával szeretné átmásolni a REST-et. <br/> -False (hamis): a másolási tevékenységet szeretné megszakítani, ha a rendszer inkonzisztens adathalmazt talált. <br/>Ügyeljen arra, hogy ez a tulajdonság csak akkor érvényes, ha a validateDataConsistency értéke TRUE (igaz). | Igaz <br/>False (alapértelmezett) | No
invalidFileName | A skipErrorFile tulajdonság táska egyik kulcs-érték párja, hogy meghatározza, hogy ki szeretné-e hagyni az adott fájlokat, ha a fájlnevek érvénytelenek a célhelyen. <br/> -True (igaz): szeretné átmásolni a többit, ha kihagyja a fájlneveket tartalmazó fájlokat. <br/> -False (hamis): a másolási tevékenységet szeretné megszakítani, ha valamelyik fájl neve érvénytelen. <br/>Vegye figyelembe, hogy ez a tulajdonság akkor működik, ha a bináris fájlokat bármilyen tárolóhelyről másolja, hogy a bináris fájlokat az AWS S3-ból ADLS Gen2, vagy csak a Storage Store áruházba másolja. | Igaz <br/>False (alapértelmezett) | No
logSettings  | A kihagyott objektumok nevének naplózásához megadható tulajdonságok csoportja. | &nbsp; | No
linkedServiceName | Az [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása a munkamenet naplófájljainak tárolására. | Egy `AzureBlobStorage` vagy `AzureBlobFS` típusú társított szolgáltatás neve, amely a naplófájl tárolására használt példányra hivatkozik. | No
path | A naplófájlok elérési útja. | A naplófájlok tárolásához használt elérési út megadása. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No

> [!NOTE]
> A következő a bináris fájlok másolásakor a hibatűrés engedélyezése a másolási tevékenységben.
> Adott fájlok kihagyása a forrás-áruházból való törléskor:
> - A forrás-és a fogadó adatkészletnek bináris formátumúnak kell lennie, és a tömörítési típus nem adható meg. 
> - A támogatott adattár-típusok az Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, az Azure File Storage, a fájlrendszer, az FTP, az SFTP, az Amazon S3, a Google Cloud Storage és a HDFS.
> - Csak akkor, ha több fájlt ad meg a forrás adatkészletben, ami lehet mappa, helyettesítő karakter vagy fájlok listája, a másolási tevékenység kihagyhatja az adott hibaüzeneteket. Ha egyetlen fájl van megadva a forrás adatkészletben a célhelyre való másoláshoz, a másolási tevékenység sikertelen lesz, ha hiba történt.
>
> Bizonyos fájlok kihagyása, ha a hozzáférésük tiltott a forrás-áruházból:
> - A forrás-és a fogadó adatkészletnek bináris formátumúnak kell lennie, és a tömörítési típus nem adható meg. 
> - A támogatott adattár-típusok az Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, az Azure File Storage, az SFTP, az Amazon S3 és a HDFS.
> - Csak akkor, ha több fájlt ad meg a forrás adatkészletben, ami lehet mappa, helyettesítő karakter vagy fájlok listája, a másolási tevékenység kihagyhatja az adott hibaüzeneteket. Ha egyetlen fájl van megadva a forrás adatkészletben a célhelyre való másoláshoz, a másolási tevékenység sikertelen lesz, ha hiba történt.
>
> Ha a rendszer a forrás-és a célhelyek között inkonzisztensként ellenőrzi, hogy az adott fájlok kimaradnak-e:
> - Az adatkonzisztencia-dokumentációból további részleteket [itt](./copy-activity-data-consistency.md)talál.

### <a name="monitoring"></a>Figyelés 

#### <a name="output-from-copy-activity"></a>Kimenet a másolási tevékenységből
Lekérheti az olvasási, írásos és kihagyott fájlok számát az egyes másolási tevékenységek kimenetén keresztül. 

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

#### <a name="session-log-from-copy-activity"></a>Munkamenet-napló a másolási tevékenységből

Ha úgy konfigurálja, hogy naplózza a kihagyott fájlneveket, a naplófájlt a következő elérési úton találja: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

A naplófájloknak a CSV-fájloknak kell lenniük. A naplófájl sémája a következő:

Oszlop | Leírás 
-------- | -----------  
Időbélyeg | A fájl időbélyege, amikor az ADF kihagyja a fájlt.
Level | Az adott tétel naplózási szintje. A fájl kihagyása esetén a rendszer figyelmeztetési szinten jeleníti meg az elemeket.
OperationName | Az ADF másolási tevékenységének működési viselkedése minden fájlban. A kihagyni kívánt fájl megadásához "FileSkip" lesz.
OperationItem | A kihagyni kívánt fájlnevek.
Üzenet | További információ a fájl kihagyásának okának szemléltetéséhez.

A naplófájl példája a következő: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
A fenti naplóból megtekintheti, bigfile.csv ki lett hagyva, mert egy másik alkalmazás törölte ezt a fájlt az ADF másolásakor. A 3_nopermission.txt ki lett hagyva, mert az ADF nem fér hozzá az engedélyhez a probléma miatt.


## <a name="copying-tabular-data"></a>Táblázatos adatok másolása 

### <a name="supported-scenarios"></a>Támogatott esetek
A másolási tevékenység három olyan forgatókönyvet támogat, amelyek nem kompatibilis táblázatos adatok észlelésére, kihagyására és naplózására alkalmasak:

- Nem **kompatibilis a forrás adattípusa és a fogadó natív típusa között**. 

    Példa: adatok másolása CSV-fájlból a blob Storage-ból egy SQL Database-be egy olyan séma-definícióval, amely három INT típusú oszlopot tartalmaz. A numerikus adatértékeket tartalmazó CSV-fájlok sorai, például az 123 456 789-es sorok sikeres másolása a fogadó tárolóba. Azonban a nem numerikus értékeket tartalmazó sorok, például az 123 456, az ABC nem kompatibilisként és kimarad.

- A **forrás és a fogadó közötti oszlopok száma nem egyezik**.

    Például: adatok másolása egy CSV-fájlból a blob Storage-ban egy SQL Database-be olyan séma-definícióval, amely hat oszlopot tartalmaz. A hat oszlopot tartalmazó CSV-fájl sorainak másolása sikeresen megtörtént a fogadó tárolóba. A több mint hat oszlopot tartalmazó CSV-fájlok nem kompatibilisként vannak észlelve, és kimaradnak.

- **Az elsődleges kulcs megsértése SQL Server/Azure SQL Database/Azure Cosmos db írásakor**.

    Például: adatok másolása egy SQL-kiszolgálóról egy SQL-adatbázisba. Elsődleges kulcs van definiálva a fogadó SQL-adatbázisban, de nincs ilyen elsődleges kulcs definiálva a forrás SQL-kiszolgálón. A forrásban található duplikált sorok nem másolhatók a fogadóba. A másolási tevékenység csak a forrásadatok első sorát másolja a fogadóba. A duplikált elsődleges kulcs értékét tartalmazó következő forrásfájlokat inkompatibilisként észleli a rendszer, és kihagyja.

>[!NOTE]
>- Ha az Azure szinapszis Analyticsbe szeretne betölteni egy alapszintű adatbázist, konfigurálja a natív hibatűrési beállításokat úgy, hogy a másolási tevékenység "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" értékének megadásával visszautasítja a szabályzatokat. A lent látható módon továbbra is engedélyezheti a "Base inkompatibilis" sorok átirányítását a blob-vagy ADLS.
>- Ez a funkció nem érvényes, ha a másolási tevékenység úgy van konfigurálva, hogy meghívja az [Amazon vöröseltolódásának eltávolítását](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Ez a funkció nem érvényes, ha a másolási tevékenység konfigurálva van egy tárolt eljárás meghívására [egy SQL-](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink)fogadóból.

### <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definíciót biztosít a nem kompatibilis sorok másolási tevékenységbe való kihagyásának konfigurálásához:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
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
}, 
```

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Meghatározza, hogy a nem kompatibilis sorok kihagyása a másolás során vagy sem. | Igaz<br/>False (alapértelmezett) | No
logSettings | A nem kompatibilis sorok naplózásához megadható tulajdonságok csoportja. | &nbsp; | No
linkedServiceName | Az [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) társított szolgáltatása, amely a kihagyott sorokat tartalmazó naplót tárolja. | Egy `AzureBlobStorage` vagy `AzureBlobFS` típusú társított szolgáltatás neve, amely a naplófájl tárolására használt példányra hivatkozik. | No
path | A kihagyott sorokat tartalmazó naplófájlok elérési útja. | Itt adhatja meg a nem kompatibilis adatfájlok naplózásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No

### <a name="monitor-skipped-rows"></a>A kihagyott sorok figyelése
A másolási tevékenység futtatása után a másolási tevékenység kimenetében a kihagyott sorok száma látható:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Ha úgy konfigurálja, hogy naplózza a nem kompatibilis sorokat, a naplófájlt a következő elérési útról tekintheti meg: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

A naplófájlok a CSV-fájlok lesznek. A naplófájl sémája a következő:

Oszlop | Leírás 
-------- | -----------  
Időbélyeg | A nem kompatibilis sorok kihagyása az ADF-ben
Level | Az adott tétel naplózási szintje. A figyelmeztetési szinten jelenik meg, ha ez az érték a kihagyott sorokat mutatja
OperationName | Az ADF másolási tevékenységének műveleti viselkedése az egyes sorokon. A "TabularRowSkip" lesz annak a meghatározása, hogy az adott inkompatibilis sor ki lett hagyva.
OperationItem | A kihagyott sorok a forrás adattárból.
Üzenet | További információ az adott sor inkompatibilitásának okának szemléltetéséhez.


A naplófájl tartalmának példája a következő:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

A fenti minta naplófájlban egy "Data1, data2, data3" nevű sort is kihagyott, mert a forrásról a célhelyre történő átalakítási probléma típusú. Egy másik, "data4, data5, data6" sor lett kihagyva, mert a rendszer PK-megsértési problémát okozott a forrás és a cél tároló között. 


## <a name="copying-tabular-data-legacy"></a>Táblázatos adatok másolása (örökölt):

A következő az örökölt módszer, amely lehetővé teszi a hibatűrést a táblázatos adatok másolásához. Ha új folyamatot vagy tevékenységet hoz létre, javasoljuk, hogy [innen](#copying-tabular-data) induljon el.

### <a name="configuration"></a>Konfiguráció
A következő példa egy JSON-definíciót biztosít a nem kompatibilis sorok másolási tevékenységbe való kihagyásának konfigurálásához:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Meghatározza, hogy a nem kompatibilis sorok kihagyása a másolás során vagy sem. | Igaz<br/>False (alapértelmezett) | No
redirectIncompatibleRowSettings | A nem kompatibilis sorok naplózásához megadható tulajdonságok csoportja. | &nbsp; | No
linkedServiceName | Az [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) társított szolgáltatása, amely a kihagyott sorokat tartalmazó naplót tárolja. | Egy `AzureStorage` vagy `AzureDataLakeStore` típusú társított szolgáltatás neve, amely arra a példányra hivatkozik, amelyet a naplófájl tárolására kíván használni. | No
path | A kihagyott sorokat tartalmazó naplófájl elérési útja. | Itt adhatja meg a nem kompatibilis adatfájlok naplózásához használni kívánt elérési utat. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | No

### <a name="monitor-skipped-rows"></a>A kihagyott sorok figyelése
A másolási tevékenység futtatása után a másolási tevékenység kimenetében a kihagyott sorok száma látható:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Ha úgy konfigurálja, hogy naplózza a nem kompatibilis sorokat, a naplófájl ezen az elérési úton található: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

A naplófájlok csak a CSV-fájlok lehetnek. Ha szükséges, a kihagyott eredeti adatértékek vesszővel elválasztott oszlopként lesznek naplózva. A naplófájlban az eredeti forrásadatok további két oszlopát ("ErrorCode" és "ErrorMessage") is hozzáadjuk, ahol megtekintheti az inkompatibilitás kiváltó okát. A ErrorCode és a ErrorMessage idézőjelek alapján történik. 

A naplófájl tartalmának példája a következő:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Másolási tevékenység teljesítménye](copy-activity-performance.md)