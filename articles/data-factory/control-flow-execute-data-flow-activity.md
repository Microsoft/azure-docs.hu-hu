---
title: Adatfolyam-tevékenység
description: Az adatfolyamatok végrehajtása egy adatfeldolgozó-folyamaton belül.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 01/03/2021
ms.openlocfilehash: 0663690318773ccad3bddfaaa03e456c2f58895e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383381"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam-tevékenység Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyam tevékenységgel átalakíthatja és áthelyezheti az adatait a leképezési adatfolyamatok használatával. Ha még nem ismeri az adatfolyamatokat, tekintse meg az [adatáramlás leképezése – áttekintés](concepts-data-flow-overview.md) című témakört.

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
adatfolyam | A végrehajtandó adatfolyamra mutató hivatkozás | DataFlowReference | Yes
integrationRuntime | Az a számítási környezet, amelyen az adatfolyam fut. Ha nincs megadva, a rendszer az Azure Integration Runtime automatikus feloldását fogja használni. | IntegrationRuntimeReference | No
számítás. coreCount | A Spark-fürtben használt magok száma. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | 8, 16, 32, 48, 80, 144, 272 | No
számítás. computeType | A Spark-fürtben használt számítási típus. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | "Általános", "ComputeOptimized", "MemoryOptimized" | No
előkészítés. linkedService | Ha Azure-beli szinapszis Analytics-forrást vagy-fogadót használ, határozza meg a alapszintű előkészítéshez használt Storage-fiókot.<br/><br/>Ha az Azure Storage VNet szolgáltatás-végponttal van konfigurálva, akkor a Storage-fiókon engedélyezve van a "megbízható Microsoft-szolgáltatás engedélyezése" nevű felügyelt identitás-hitelesítés, lásd: a [VNet szolgáltatás-végpontok Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)-ban való használatának következményei. Ismerje meg az [Azure Blob](connector-azure-blob-storage.md#managed-identity) és a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) szükséges konfigurációit is.<br/> | Linkedservicereference sématulajdonsággal | Csak akkor, ha az adatfolyam beolvassa vagy ír egy Azure szinapszis Analyticsbe
előkészítés. folderPath | Ha Azure szinapszis Analytics-forrást vagy-fogadót használ, a mappa elérési útja a blob Storage-fiókban | Sztring | Csak akkor, ha az adatfolyam beolvassa vagy ír az Azure szinapszis Analyticsbe
traceLevel | Az adatfolyam-tevékenységek végrehajtásának naplózási szintjének beállítása | Vékony, durva, nincs | No

![Adatfolyam végrehajtása](media/data-flow/activity-data-flow.png "Adatfolyam végrehajtása")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Adatáramlási számítások dinamikusan méretezése futásidőben

Az alapszám és a számítási típus tulajdonságainak beállítása dinamikusan beállítható úgy, hogy a bejövő forrásadatok mérete futásidőben is módosítható legyen. A forrás adatkészlet-adatok méretének megkereséséhez használjon olyan folyamat-tevékenységeket, mint a keresés vagy a metaadatok beolvasása. Ezután használja a dinamikus tartalom hozzáadása részt az adatfolyam tevékenység tulajdonságainál.

![Dinamikus adatfolyam](media/data-flow/dyna1.png "Dinamikus adatfolyam")

[Íme egy rövid videós oktatóanyag, amely leírja ezt a technikát](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integrációs modul

Válassza ki, hogy melyik Integration Runtime szeretné használni az adatfolyam tevékenységének végrehajtásához. Alapértelmezés szerint a Data Factory az Azure Integration Runtime automatikus feloldását fogja használni négy munkavégző maggal, és nincs élettartam (TTL). Ez az IR általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. Létrehozhat saját Azure-integrációs modulokat, amelyek meghatározott régiókat, számítási típusokat, alapszámokat és ÉLETTARTAMot határoznak meg az adatfolyam tevékenységének végrehajtásához.

A folyamatok végrehajtásához a fürt egy olyan fürt, amely a végrehajtás megkezdése előtt több percet vesz igénybe. Ha nem ad meg TTL-értéket, a rendszer ezt az indítási időt igényli minden folyamat futtatásához. Ha a TTL értéket adja meg, a meleg fürt a legutóbbi végrehajtás után megadott időre aktív marad, ami rövidebb indítási időt eredményez. Ha például 60 perc ÉLETTARTAMa van, és óránként egyszer futtat egy adatfolyamot, a fürtön marad aktív. További információ: [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Az adatfolyam-tevékenység Integration Runtime kiválasztása csak a folyamat *aktivált végrehajtására* vonatkozik. A folyamat hibakeresése az adatfolyamatokkal a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha az Azure szinapszis Analytics szolgáltatást fogadóként vagy forrásként használja, ki kell választania egy átmeneti helyet a Batch-kötegek terheléséhez. A Base lehetővé teszi a kötegek tömeges betöltését az adatsorok egymásba helyezése helyett. A Base drasztikusan csökkenti a betöltési időt az Azure szinapszis Analytics szolgáltatásban.

## <a name="logging-level"></a>Naplózási szint

Ha nincs szüksége az adatáramlási tevékenységek minden folyamatának végrehajtására az összes részletes telemetria-napló teljes naplózásához, igény szerint beállíthatja a naplózási szintet az "alapszintű" vagy a "None" értékre. Ha az adatfolyamatokat "részletes" módban hajtja végre (alapértelmezés), az automatikus lapadagolóba az adatátalakítás során minden egyes partíciós szinten teljes körű naplózási tevékenységet kér. Ez költséges művelet lehet, ezért csak akkor engedélyezze a részletes műveleteket, ha a hibaelhárítás javítja a teljes adatfolyamatot és a folyamat teljesítményét. Az "alapszintű" mód csak az átalakítási időtartamokat fogja naplózni, míg a "None" csak az időtartamok összegzését tartalmazza.

![Naplózási szint](media/data-flow/logging.png "Naplózási szint beállítása")

## <a name="sink-properties"></a>Fogadó tulajdonságai

Az adatfolyamatok csoportosítási funkciója lehetővé teszi, hogy egyszerre állítsa be a mosogatók végrehajtásának sorrendjét, valamint a nyelők csoportosítását az azonos csoport számával együtt. A csoportok kezeléséhez az ADF-et arra is kérheti, hogy párhuzamosan futtassa a mosogatókat ugyanabban a csoportban. A fogadó csoportot úgy is beállíthatja, hogy a folytatás akkor is megtörténjen, ha az egyik mosogató hibát észlel.

Az adatfolyam-elsüllyedés alapértelmezett viselkedése az egyes fogadók egymás utáni, soros módon történő végrehajtása, valamint az adatfolyamatok meghibásodása, amikor hiba történik a fogadóban. Emellett az összes mosogató alapértelmezés szerint ugyanahhoz a csoporthoz tartozik, hacsak nem lép be az adatfolyam tulajdonságaiba, és nem állítja be a mosdók különböző prioritásait.

![Fogadó tulajdonságai](media/data-flow/sink-properties.png "Fogadó tulajdonságainak beállítása")

## <a name="parameterizing-data-flows"></a>Parameterizing-adatfolyamok

### <a name="parameterized-datasets"></a>Paraméteres adatkészletek

Ha az adatfolyam paraméteres adatkészleteket használ, állítsa be a paraméterek értékét a **Beállítások** lapon.

![Adatfolyam paramétereinek végrehajtása](media/data-flow/params.png "Paraméterek")

### <a name="parameterized-data-flows"></a>Paraméteres adatfolyamatok

Ha az adatfolyam paraméteres, állítsa be az adatfolyam paramétereinek dinamikus értékeit a **Parameters (paraméterek** ) lapon. A dinamikus vagy literális paraméterérték kiosztásához használhatja az ADF-folyamat kifejezésének nyelvét vagy az adatfolyam kifejezésének nyelvét is. További információ: adatfolyam- [Paraméterek](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Paraméteres számítási tulajdonságok.

Ha az Azure Integration Runtime automatikus feloldása és a számítási. coreCount és számítások értékeit adja meg, parametrizálja az alapvető darabszámot vagy a számítási típust.

![Példa az adatforgalom paraméterének végrehajtására](media/data-flow/parameterize-compute.png "Példa paraméterre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Adatáramlási tevékenység folyamatának hibakeresése

Egy adatáramlási tevékenységgel futtatott hibakeresési folyamat végrehajtásához az adatfolyam-hibakeresési módot a felső sávon található **adatfolyam-hibakeresési** csúszka használatával kell bekapcsolni. A hibakeresési mód lehetővé teszi az adatfolyamok aktív Spark-fürtön való futtatását. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem az adatáramlási tevékenység beállításaiban megadott integrációs futtatókörnyezeti környezettel. A hibakeresési mód indításakor kiválaszthatja a hibakeresés számítási környezetét.

## <a name="monitoring-the-data-flow-activity"></a>Az adatfolyam tevékenységének figyelése

Az adatfolyam-tevékenység speciális figyelési felülettel rendelkezik, ahol megtekintheti a particionálást, a szakasz időpontját és az adatvonal-információkat. Nyissa meg a figyelés panelt a **műveletek** területen található szemüvegek ikon használatával. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Adatfolyam-tevékenységek eredményeinek használata egy későbbi tevékenységben

Az adatfolyam tevékenység az egyes fogadók számára írt sorok számát és az egyes forrásokból beolvasott sorokra vonatkozó mérőszámokat jeleníti meg. Ezek az eredmények a `output` tevékenység futtatási eredményének szakaszában lesznek visszaadva. A visszaadott metrikák az alábbi JSON formátumban jelennek meg.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Ha például a "sink1" nevű fogadóba írt sorok számát szeretné lekérni egy "dataflowActivity" nevű tevékenységben, használja a következőt: `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

A (z) "source1" nevű forrásból beolvasott sorok számának lekéréséhez használja a következőt: `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Ha a fogadó nulla sorból áll, akkor nem jelenik meg a mérőszámokban. A létezés ellenőrizhető a függvény használatával `contains` . Például `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` megvizsgálhatja, hogy a sorok sink1-e.

## <a name="next-steps"></a>Következő lépések

Lásd: Data Factory által támogatott vezérlési flow-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
