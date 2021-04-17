---
title: Adatfolyam tevékenység
description: Adatfolyamok végrehajtása egy data factory-folyamaton belülről.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 04/16/2021
ms.openlocfilehash: da8d193d140d96d9742666429ebc85672c71ad4e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567264"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam tevékenység a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Adatfolyam használatával adatokat alakíthat át és mozgathat leképezési adatfolyamokkal. Ha még nem látta az adatfolyamokat, tekintse meg a leképezés Adatfolyam [áttekintését](concepts-data-flow-overview.md)

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
adatfolyam | A végrehajtandó Adatfolyam hivatkozása | DataFlowReference (Adatfolyam-dedencia) | Yes
integrationRuntime (integrationRuntime) | Az adatfolyam által futtatott számítási környezet. Ha nincs megadva, a rendszer az automatikusan feloldott Azure integration runtime-t használja. | IntegrationRuntimeReference (IntegrationRuntimeReference) | No
compute.coreCount | A Spark-fürtben használt magok száma. Csak akkor lehet megadni, ha az automatikusan feloldott Azure Integration Runtime van használatban | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType | A Spark-fürtben használt számítási típus. Csak akkor lehet megadni, ha az automatikusan feloldott Azure Integration Runtime van használatban | "Általános", "ComputeOptimized", "MemoryOptimized" | No
staging.linkedService | Ha egy forrást vagy fogadót Azure Synapse Analytics, adja meg a PolyBase előkészítéshez használt tárfiókot.<br/><br/>Ha az Azure Storage VNet-szolgáltatásvégponttal van konfigurálva, felügyelt identitáshitelesítést kell használnia, ha a tárfiókon engedélyezve van a "Megbízható Microsoft-szolgáltatás engedélyezése", tekintse meg a virtuális hálózati szolgáltatásvégpont Azure-tárolóval való használatának hatását. [](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) Emellett megismeri az [Azure Blob](connector-azure-blob-storage.md#managed-identity) és a Azure Data Lake Storage Gen2 [szükséges](connector-azure-data-lake-storage.md#managed-identity) konfigurációkat is.<br/> | LinkedServiceReference (LinkedServiceReference) | Csak akkor, ha az adatfolyam egy Azure Synapse Analytics
staging.folderPath | Ha forrást vagy fogadót Azure Synapse Analytics használ, a PolyBase előkészítéshez használt Blob Storage-fiók mappájának elérési útja | Sztring | Csak akkor, ha az adatfolyam beolvassa vagy írja a Azure Synapse Analytics
traceLevel | Az adatfolyam-tevékenység végrehajtásának naplózási szintjének beállítása | Finom, coarse, None | No

![Végrehajtás Adatfolyam](media/data-flow/activity-data-flow.png "Végrehajtás Adatfolyam")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Adatfolyam számítási kapacitásának dinamikus mérete futásidőben

A Magok száma és a Számítási típus tulajdonságok dinamikusan állíthatók be a bejövő forrásadatok méretéhez a futtatáskor. A forrásadatkészlet-adatok méretének megkeresése érdekében használjon olyan folyamattevékenységeket, mint a Lookup (Keresés) vagy a Get Metadata (Metaadatok lekeresése). Ezután használja a Dinamikus tartalom hozzáadása gombra Adatfolyam tevékenység tulajdonságaiban.

![Dinamikus Adatfolyam](media/data-flow/dyna1.png "Dinamikus adatfolyam")

[Az alábbi rövid oktatóvideó ezt a technikát ismerteti](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integrációs futtatás

Válassza ki Integration Runtime tevékenység végrehajtáshoz Adatfolyam használni. Alapértelmezés szerint a Data Factory automatikusan feloldott Azure Integration Runtime-t fogja használni négy munkavégző maggal. Ez az integrációs megoldás általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. A működőképes folyamatok esetében erősen ajánlott saját Azure Integration Runtime-okat létrehozni, amelyek meghatározott régiókat, számítási típust, magszámokat és TTL-t határoznak meg az adatfolyam-tevékenységek végrehajtáshoz.

A általános célú minimális számítási típusa (nagy számítási feladatokhoz nem ajánlott) 8+8 (összesen 16 virtuális mag) konfigurációval és 10 perces minimális javaslattal a legtöbb éles számítási feladathoz. Egy kis TTL beállításával a Azure IR egy olyan meleg fürtöt tud fenntartani, amely nem fogja a hideg fürt több perces kezdési idejét igénybeni. Az adatfolyamok végrehajtásának felgyorsítása érdekében válassza a Gyors újrahasználat lehetőséget az adatfolyam Azure IR konfigurációkban. További információ: [Azure integration runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> A Integration Runtime kiválasztott Adatfolyam tevékenység csak a folyamat *aktivált* végrehajtására vonatkozik. A folyamat adatfolyamokkal való hibakeresése a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha fogadóként vagy forrásként Azure Synapse Analytics egy tárolót, akkor a PolyBase kötegelt terhelésének előkészítési helyét kell választania. A PolyBase lehetővé teszi a kötegelt betöltést tömegesen az adatok sorról sorra való betöltése helyett. A PolyBase jelentősen csökkenti a betöltési időt a Azure Synapse Analytics.

## <a name="logging-level"></a>Naplózási szint

Ha nincs szükség az adatfolyam-tevékenységek minden folyamatvégrehajtására az összes részletes telemetriai napló teljes naplózása érdekében, a naplózási szintet beállíthatja "Alapszintű" vagy "Nincs" szintre. Amikor "Részletes" módban (alapértelmezett) végrehajtja az adatfolyamokat, az adatátalakítás során az ADF-nek teljes körű naplózást kell végrehajtania az egyes partíciók szintjén. Ez költséges művelet lehet, így csak a hibaelhárítás során a részletes engedélyezés javíthatja az adatfolyam és a folyamat általános teljesítményét. Az "Alapszintű" mód csak az átalakítási időtartamot naplóz, míg a "Nincs" csak az időtartamok összegzését adja meg.

![Naplózási szint](media/data-flow/logging.png "Naplózási szint beállítása")

## <a name="sink-properties"></a>Fogadó tulajdonságai

Az adatfolyamok csoportosítási funkciója lehetővé teszi a fogadók végrehajtási sorrendjének beállítását, valamint a fogadók csoportosítását ugyanazokkal a csoportszámmal. A csoportok kezeléséhez megkérheti az ADF-et, hogy ugyanabban a csoportban futtatassa párhuzamosan a fogadókat. Azt is beállíthatja, hogy a fogadócsoport akkor is folytatódjon, ha az egyik fogadó hibába ütközik.

Az adatfolyam-fogadók alapértelmezett viselkedése az, hogy egymást követő sorrendben végrehajtják az egyes fogadókat, és ha hiba történik a fogadóban, az adatfolyam meghiúsul. Emellett az összes fogadó alapértelmezés szerint ugyanannak a csoportnak a tagja, kivéve, ha az adatfolyam tulajdonságai között különböző prioritásokat ad meg a fogadókhoz.

![Fogadó tulajdonságai](media/data-flow/sink-properties.png "Fogadó tulajdonságainak beállítása")

## <a name="parameterizing-data-flows"></a>Adatfolyamok paraméterezése

### <a name="parameterized-datasets"></a>Paraméteres adatkészletek

Ha az adatfolyam paraméteres adathalmazokat használ, állítsa be a paraméterértékeket a **Beállítások lapon.**

![Végrehajtási Adatfolyam végrehajtása](media/data-flow/params.png "Paraméterek")

### <a name="parameterized-data-flows"></a>Paraméteres adatfolyamok

Ha az adatfolyam paraméterezett, állítsa be az adatfolyam-paraméterek dinamikus értékeit a **Paraméterek lapon.** Dinamikus vagy literális paraméterértékek hozzárendeléséhez használhatja az ADF folyamatkifejezés-nyelvet vagy az adatfolyam-kifejezésnyelvet. További információ: [Adatfolyam.](parameters-data-flow.md)

### <a name="parameterized-compute-properties"></a>Paraméteres számítási tulajdonságok.

A magok számát vagy a számítási típust paraméterizálhatja, ha az Azure Integration Runtime automatikus feloldását használja, és a compute.coreCount és a compute.computeType értékeit adja meg.

![Példa Adatfolyam paraméter végrehajtására](media/data-flow/parameterize-compute.png "Példa paraméterre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Folyamat hibakeresése Adatfolyam tevékenységhez

Egy hibakeresési folyamat futásának Adatfolyam-tevékenységgel való végrehajtásához be kell kapcsolnia az adatfolyam hibakeresési módját a felső sáv **Adatfolyam Hibakeresés** csúszkával. A hibakeresési mód lehetővé teszi az adatfolyam aktív Spark-fürtön való futtatását. További információ: [Hibakeresési mód.](concepts-data-flow-debug-mode.md)

![A Hibakeresés gomb helyének képernyőképe](media/data-flow/debug-button-3.png)

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem pedig az integrációs Adatfolyam beállításaiban megadott környezeten. A hibakeresési mód indításakor kiválaszthatja a számítási környezet hibakeresését.

## <a name="monitoring-the-data-flow-activity"></a>A tevékenység Adatfolyam monitorozása

A Adatfolyam tevékenység speciális figyelési élményt biztosít, ahol megtekintheti a particionálási, fázisidő- és adatkapcsolat-információkat. Nyissa meg a figyelési panelt a Műveletek alatti szemüveg **ikonnal.** További információ: [Adatfolyamok figyelése.](concepts-data-flow-monitoring.md)

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>A Adatfolyam eredmények használata egy későbbi tevékenységben

Az adatfolyam-tevékenység metrikákat ad vissza az egyes fogadókba írt sorok számával és az egyes forrásból beolvasott sorok számával kapcsolatban. Ezeket az eredményeket a `output` tevékenységfutat eredményének szakaszában lehet visszaadni. A visszaadott metrikák formátuma az alábbi json.

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

Ha például a "dataflowActivity" nevű tevékenység "sink1" nevű fogadóba írt sorait keres, használja a következőt: `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

A fogadóban használt "source1" nevű forrásból beolvasott sorok számának lekért száma a következő: `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Ha egy fogadóhoz nulla sor van megírva, az nem fog mutatni a metrikákban. A megléte a függvény használatával `contains` ellenőrizhető. A például `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` ellenőrzi, hogy vannak-e sorok a sink1-be írva.

## <a name="next-steps"></a>Következő lépések

Lásd a következő által támogatott folyamtevékenységeket Data Factory: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
