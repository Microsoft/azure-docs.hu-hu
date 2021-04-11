---
title: Másolási tevékenység figyelése
description: Tudnivalók a másolási tevékenységek végrehajtásának figyeléséről Azure Data Factoryban.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jingwang
ms.openlocfilehash: 4aefeea33e61b6ee2541e929dbeb3fc36343cac5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771379"
---
# <a name="monitor-copy-activity"></a>Másolási tevékenység figyelése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan lehet figyelni a másolási tevékenységek végrehajtását Azure Data Factoryban. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="monitor-visually"></a>Vizuális megfigyelés

Miután létrehozta és közzétett egy folyamatot Azure Data Factoryban, társíthatja egy triggerhez, vagy manuálisan elindíthatja az ad hoc futtatást. Az összes folyamatot a Azure Data Factory felhasználói élményben natív módon figyelheti. Ismerkedjen meg a Azure Data Factory monitorozással a [vizuálisan figyelő Azure Data Factory](monitor-visually.md).

A másolási tevékenység futtatásának figyeléséhez nyissa meg a következőt: **& monitor** felhasználói felületét. A **figyelés** lapon megjelenik a folyamat-futtatások listája, és kattintson a **folyamat neve** hivatkozásra a folyamat futtatása során futó tevékenységek listájának eléréséhez.

![Folyamat futtatásának figyelése](./media/copy-activity-overview/monitor-pipeline-run.png)

Ezen a szinten megtekintheti a másolási tevékenység bemenetét, kimenetét és hibáit (ha a másolási tevékenység sikertelen lesz), valamint a statisztikát, például az időtartamot/állapotot. A másolási tevékenység neve melletti **részletek** gombra kattintva részletes információkat adhat meg a másolási tevékenység végrehajtásáról. 

![Másolási tevékenység futtatásának figyelése](./media/copy-activity-overview/monitor-copy-activity-run.png)

Ebben a grafikus figyelési nézetben a Azure Data Factory megadja a másolási tevékenység végrehajtásával kapcsolatos információkat, beleértve az olvasási/írási kötetet, a forrásról a fogadóba másolt fájlok számát/sorait, az átviteli sebességet, a másolási forgatókönyvre alkalmazott konfigurációkat, a másolási tevékenység lépéseit pedig a megfelelő időtartamokkal és részletekkel együtt. Tekintse meg [ezt a táblázatot](#monitor-programmatically) minden lehetséges metrika és részletes leírása alapján. 

Bizonyos helyzetekben, amikor másolási tevékenységet futtat Data Factoryban, a másolási tevékenység figyelése nézet tetején a **"Performance tuning tippek"**  láthatók a példában látható módon. A tippekből megtudhatja, hogy az ADF milyen szűk keresztmetszetet azonosít az adott másolási futtatáshoz, és javaslatot tesz arra, hogy mit kell módosítani a másolási teljesítmény növelése érdekében. További információ az [automatikus teljesítmény-hangolási tippekről](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Az alsó **végrehajtási adatok és időtartamok** a másolási tevékenység lépéseit ismertetik, ami különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási Futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg a [másolási tevékenység teljesítményének hibaelhárítása](copy-activity-performance-troubleshooting.md) című témakört, amely az egyes szakaszok és a részletes hibaelhárítási útmutatót mutatja be.

**Példa: másolás az Amazon S3-ból Azure Data Lake Storage Gen2**

![A másolási tevékenység futtatási részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Programozott figyelés

A másolási tevékenység végrehajtásának részletei és a teljesítmény jellemzői a **másolási tevékenység futtatási eredményének**  >  **kimenete** szakaszban is megjelennek, amely a felhasználói felület figyelési nézetének megjelenítésére szolgál. A következő lista az esetleg visszaadott tulajdonságok teljes listáját tartalmazza. Csak a másolási forgatókönyvre vonatkozó tulajdonságokat fogja látni. További információ a tevékenységek figyeléséről általában programozott módon: Azure-beli adat- [előállító programozott figyelése](monitor-programmatically.md).

| Tulajdonság neve  | Leírás | Kimeneti egység |
|:--- |:--- |:--- |
| dataRead | A forrásból beolvasott adatok tényleges mennyisége. | Int64 érték bájtban |
| dataWritten | A fogadóba írt/elkötelezett adatok tényleges csatlakoztatása. A méret különbözhet a `dataRead` mérettől, mivel az egyes adattár az adatok tárolására szolgál. | Int64 érték bájtban |
| filesRead | A fájl alapú forrásból beolvasott fájlok száma. | Int64 érték (egység nélkül) |
| filesWritten | A fájl alapú fogadóba írt/véglegesített fájlok száma. | Int64 érték (egység nélkül) |
| filesSkipped | A fájl alapú forrásból kihagyott fájlok száma. | Int64 érték (egység nélkül) |
| dataConsistencyVerification | Az adatkonzisztencia-ellenőrzés részletei, ahol láthatja, hogy a másolt adatok konzisztensek-e a forrás-és a célhelyek között. További információ [ebben a cikkben](copy-activity-data-consistency.md#monitoring). | Tömb |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int64 érték (egység nélkül) |
| sinkPeakConnections | A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma. | Int64 érték (egység nélkül) |
| rowsRead | A forrásból beolvasott sorok száma. Ez a metrika nem vonatkozik arra az esetre, ha a fájlok a-ként való másolása nem történik meg, például ha a forrás-és fogadó adatkészletek bináris formátumúak, vagy más formátumúak azonos beállításokkal. | Int64 érték (egység nélkül) |
| rowsCopied | A fogadóba másolt sorok száma. Ez a metrika nem vonatkozik arra az esetre, ha a fájlok a-ként való másolása nem történik meg, például ha a forrás-és fogadó adatkészletek bináris formátumúak, vagy más formátumúak azonos beállításokkal.  | Int64 érték (egység nélkül) |
| rowsSkipped | A kihagyott inkompatibilis sorok száma. A nem kompatibilis sorok kihagyása az igaz értékre állításával engedélyezhető `enableSkipIncompatibleRow` . | Int64 érték (egység nélkül) |
| copyDuration | A másolás futtatásának időtartama. | Int32 érték másodpercben |
| korlátozások | Adatátviteli sebesség, amelyet a alapján `dataRead` oszt ki `copyDuration` . | Lebegőpontos szám (Kbit/s) |
| sourcePeakConnections | A másolási tevékenység futtatása során a forrás adattárban létesített egyidejű kapcsolatok maximális száma. | Int32 érték (nincs egység) |
| sinkPeakConnections| A fogadó adattárhoz a másolási tevékenység futtatása során létesített egyidejű kapcsolatok maximális száma.| Int32 érték (nincs egység) |
| sqlDwPolyBase | Azt határozza meg, hogy a rendszer az adatok Azure szinapszis Analyticsbe való másolásakor használja-e a Base-t. | Logikai |
| redshiftUnload | Azt jelzi, hogy a rendszer az ELTÁVOLÍTÁSt használja-e az adatok Vöröseltolódásból történő másolásakor. | Logikai |
| hdfsDistcp | Azt határozza meg, hogy a rendszer DistCp használ-e az adatok HDFS-ből való másolásakor. | Logikai |
| effectiveIntegrationRuntime | A tevékenység futtatásához használt integrációs modul (IR) vagy futtatókörnyezet a következő formátumban: `<IR name> (<region if it's Azure IR>)` . | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A tényleges adatintegrációs egységek a másolás során. | Int32 érték |
| usedParallelCopies | A tényleges parallelCopies a másolás során. | Int32 érték |
| logPath | A blob Storage-ban a kihagyott adatkapcsolati napló elérési útja. Lásd: [hibatűrés](copy-activity-overview.md#fault-tolerance). | Szöveg (karakterlánc) |
| executionDetails | További részletek a másolási tevékenység lépésein, valamint a megfelelő lépéseken, időtartamokon, konfigurációkon stb. Nem javasoljuk, hogy elemezze ezt a szakaszt, mert megváltozhat. Ha szeretné jobban megismerni, hogy miként segíti a másolási teljesítmény megismerését és hibakeresését, tekintse meg a [vizuális figyelés](#monitor-visually) című szakaszt. | Tömb |
| perfRecommendation | Teljesítmény-finomhangolási tippek másolása. A részletekért lásd a [teljesítmény-hangolási tippeket](copy-activity-performance-troubleshooting.md#performance-tuning-tips) . | Tömb |
| billingReference | Az adott futtatáshoz tartozó számlázási felhasználás. További információ a használat [figyelése tevékenység-futtatási szinten](plan-manage-costs.md#monitor-consumption-at-activity-run-level). | Objektum |
| durationInQueue | Az üzenetsor-kezelési időtartam a másolási tevékenység végrehajtásának megkezdése előtt másodpercben. | Objektum |

**Példa**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

\-[Másolási tevékenység – áttekintés](copy-activity-overview.md)

\- [Másolási tevékenység teljesítménye](copy-activity-performance.md)