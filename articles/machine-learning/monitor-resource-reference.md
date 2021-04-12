---
title: Figyelő Azure Machine Learning adathivatkozása | Microsoft Docs
description: Dokumentáció a Azure Machine Learning figyeléséhez. Ismerkedjen meg a Azure Monitorban gyűjtött és elérhető adatok & erőforrásaival.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031069"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Az Azure Machine learning-adatreferenciák monitorozása

Ismerje meg az Azure Monitor által gyűjtött adatokat és erőforrásokat az Azure Machine Learning munkaterületről. A figyelési adatok gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelési Azure Machine learning](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Mérőszámok

Ez a szakasz felsorolja a Azure Machine Learning összegyűjtött, automatikusan összegyűjtött platform-metrikákat. A metrikák erőforrás-szolgáltatója a [Microsoft. MachineLearningServices/workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modellezés**

| Metric | Unit (Egység) | Description |
|--|--|--|
| A modell regisztrálása sikerült | Darabszám | A munkaterületen sikeres modell-regisztrációk száma |
| A modell regisztrálása nem sikerült | Darabszám | A munkaterületen meghiúsult modell-regisztrációk száma |
| Modell-üzembehelyezés elindítva | Darabszám | A munkaterületen elindított modellek központi telepítésének száma |
| Modell-üzembehelyezés sikerült | Darabszám | A munkaterületen sikeres központi telepítési modellek száma |
| Modell-üzembehelyezés sikertelen | Darabszám | A munkaterületen sikertelen telepítési modellek száma |

**Kvóta**

A kvóta adatai csak Azure Machine Learning számítási feladatokhoz használhatók.

| Metric | Unit (Egység) | Description |
|--|--|--|
| Csomópontok összesen | Darabszám | A csomópontok száma összesen. Ez az összeg magában foglalja az aktív csomópontok, a tétlen csomópontok, a nem használható csomópontok, a előzik csomópontok és a csomópontok elhagyását |
| Aktív csomópontok | Darabszám | Az aktív csomópontok száma. A feladatokat aktívan futtató csomópontok. |
| Tétlen csomópontok | Darabszám | Az üresjárati csomópontok száma. Az üresjárati csomópontok azok a csomópontok, amelyek nem futtatnak feladatokat, de az elérhetővé tehetik az új feladatot. |
| Használhatatlan csomópontok | Darabszám | Használhatatlan csomópontok száma Néhány feloldhatatlan probléma miatt nem használható csomópontok nem működőképesek. Az Azure újrahasznosítja ezeket a csomópontokat. |
| Előzik-csomópontok | Darabszám | Előzik-csomópontok száma Ezek a csomópontok az alacsony prioritású csomópontok, amelyek el lesznek távolítva az elérhető csomópont-készletből. |
| Csomópontok elhagyása | Darabszám | A csomópontok elhagyásának száma. A csomópontok elhagyják azokat a csomópontokat, amelyek éppen befejezték a feladatok feldolgozását, és az inaktív állapotba kerülnek. |
| Magok összesen | Darabszám | Magok teljes száma |
| Aktív magok | Darabszám | Aktív magok száma |
| Üresjárati magok | Darabszám | Üresjárati magok száma |
| Használhatatlan magok | Darabszám | Használhatatlan magok száma |
| Előzik magok | Darabszám | Előzik magok száma |
| Magok kihagyása | Darabszám | Kihagyott magok száma |
| Kvóta kihasználtsága (%) | Darabszám | A felhasznált kvóta százaléka |

**Erőforrás**

| Metric| Unit (Egység) | Description |
|--|--|--|
| CpuUtilization | Darabszám | A CPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egyperces időközönként kell jelenteni. |
| GpuUtilization | Darabszám | A GPU-csomópont kihasználtságának százalékos értéke. A kihasználtságot egyperces időközönként kell jelenteni. |
| GpuMemoryUtilization | Darabszám | A memória kihasználtságának százalékos aránya egy GPU-csomóponton. A kihasználtságot egyperces időközönként kell jelenteni. |
| GpuEnergyJoules | Darabszám | A GPU-csomópontokon lévő joule-beli energia intervalluma. Az energia jelentése egyperces időközönként történik. |

**Futtatás**

A munkaterületre vonatkozó betanítással kapcsolatos információk.

| Metric | Unit (Egység) | Description |
|--|--|--|
| Megszakított futtatások | Darabszám | A munkaterülethez megszakított futtatások száma. A számláló a Futtatás sikeres megszakítása után frissül. |
| A kért futtatások megszakítása | Darabszám | Azon futtatások száma, amelyek esetében a rendszer a megszakítást kérelmezte ehhez a munkaterülethez. A Count akkor frissül, ha a lemondási kérelem érkezett a futtatáshoz. |
| Befejezett futtatások | Darabszám | A munkaterületen sikeresen befejeződött a futtatások száma. A számláló a Futtatás befejeződése után frissül, és a rendszer a kimenetet gyűjtötte. |
| Sikertelen futtatások | Darabszám | A munkaterületen nem sikerült a futtatások száma. A Count a Futtatás meghiúsulása esetén frissül. |
| Futtatások véglegesítése | Darabszám | A munkaterületre vonatkozó véglegesítési állapotba lépett futtatások száma. A Count akkor frissül, ha egy Futtatás befejeződött, de a kimeneti gyűjtemény még folyamatban van. | 
| Nem válaszoló futtatások | Darabszám | A munkaterületre nem válaszoló futtatások száma. A Count akkor frissül, ha a Futtatás nem válaszol. |
| Nem indult el a futtatások | Darabszám | A nem elindított futtatások száma ehhez a munkaterülethez. A Count frissítése akkor történik meg, ha egy futtatási kérelem érkezik, de a futtatási adatok még nem lettek feltöltve. |
| Futtatások előkészítése | Darabszám | A munkaterületre felkészülő futtatások száma. A Count akkor frissül, ha egy Futtatás előkészítési állapotba kerül, amíg a futtatási környezet előkészítése folyamatban van. |
| Kiépítési futtatások | Darabszám | A munkaterülethez kiépített futtatások száma. A Count frissítése akkor történik meg, ha egy Futtatás a számítási cél létrehozására vagy kiépítési célra vár. |
| Várólistán lévő futtatások | Darabszám | Azon futtatások száma, amelyek várólistára kerülnek ehhez a munkaterülethez. A Count akkor frissül, ha egy Futtatás várólistára kerül a számítási célra. Akkor fordulhat elő, ha a szükséges számítási csomópontok készenléti állapotra várnak. |
| Elindított futtatások | Darabszám | A munkaterületen futó futtatások száma. A Count akkor frissül, ha a Futtatás a szükséges erőforrásokon fut. |
| Futtatások indítása | Darabszám | A munkaterülethez elindított futtatások száma. A Count frissítése a futtatási és futtatási adatok (például a futtatási azonosító) létrehozási kérelme alapján történt. |
| Hibák | Darabszám | A munkaterületen futtatott hibák száma. A Count frissítése akkor történik meg, amikor a Futtatás hibát észlel. |
| Figyelmeztetések | Darabszám | A futtatási figyelmeztetések száma ebben a munkaterületen. A darabszám akkor frissül, amikor egy Futtatás figyelmeztetést észlel. |

## <a name="metric-dimensions"></a>Metrikus méretek

A metrikus dimenziókkal kapcsolatos további információkért lásd: [többdimenziós mérőszámok](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

A Azure Machine Learning a metrikához tartozó következő dimenziókkal rendelkezik.

| Dimenzió | Description |
| ---- | ---- |
| Fürt neve | A számítási fürt erőforrásának neve. Minden kvóta-metrika esetében elérhető. |
| VM-család neve | A fürt által használt virtuálisgép-család neve. Elérhető a kvóta kihasználtsága százalékban. |
| Virtuális gép prioritása | A virtuális gép prioritása. Elérhető a kvóta kihasználtsága százalékban.
| CreatedTime | Csak CpuUtilization és GpuUtilization esetén érhető el. |
| DeviceId | Az eszköz azonosítója (GPU). Csak a GpuUtilization esetében érhető el. |
| NodeId | Annak a csomópontnak az azonosítója, amelybe a feladatot futtatja. Csak CpuUtilization és GpuUtilization esetén érhető el. |
| RunId | A futtatási/feladatokhoz tartozó azonosító. Csak CpuUtilization és GpuUtilization esetén érhető el. |
| ComputeType | A futtatáshoz használt számítási típus. Csak a befejezett futtatások, a sikertelen futtatások és a megkezdett futtatások esetében érhető el. |
| PipelineStepType | A Futtatás során használt [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) típusa. Csak a befejezett futtatások, a sikertelen futtatások és a megkezdett futtatások esetében érhető el. |
| PublishedPipelineId | A futtatásban használt közzétett folyamat azonosítója. Csak a befejezett futtatások, a sikertelen futtatások és a megkezdett futtatások esetében érhető el. |
| RunType | A Futtatás típusa. Csak a befejezett futtatások, a sikertelen futtatások és a megkezdett futtatások esetében érhető el. |

A RunType dimenzió érvényes értékei a következők:

| Érték | Leírás |
| ----- | ----- |
| Experiment | Nem folyamatban lévő futtatások. |
| PipelineRun | Egy folyamat futtatása, amely egy StepRun szülője. |
| StepRun | Egy folyamat lépésének futtatása. |
| ReusedStepRun | Az előző futtatást használó folyamat lépéseinek futtatása. |

## <a name="activity-log"></a>Tevékenységnapló

A következő táblázat felsorolja azokat a Azure Machine Learning kapcsolódó műveleteket, amelyek a tevékenység naplójában hozhatók létre.

| Művelet | Description |
|:---|:---|
| Machine Learning munkaterület létrehozása vagy frissítése | Egy munkaterület lett létrehozva vagy frissítve |
| CheckComputeNameAvailability | Ellenőrizze, hogy a számítási név már használatban van-e |
| A számítási erőforrások létrehozása vagy frissítése | Egy számítási erőforrás lett létrehozva vagy frissítve |
| A számítási erőforrások törlése | Egy számítási erőforrás törölve lett |
| Titkos kulcsok listázása | Machine Learning munkaterület titkos kódjainak művelete |

## <a name="resource-logs"></a>Erőforrásnaplók

Ez a szakasz felsorolja a Azure Machine Learning munkaterülethez összegyűjthető erőforrás-naplók típusait.

Erőforrás-szolgáltató és típus: [Microsoft. MachineLearningServices/munkaterület](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategória | Megjelenítendő név |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Sémák

A következő sémákat használják Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents táblázat

| Tulajdonság | Leírás |
|:--- |:---|
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| JobId | A beküldött feladatokhoz tartozó azonosító |
| ExperimentId | A kísérlet azonosítója |
| ExperimentName | A kísérlet neve |
| CustomerSubscriptionId | SubscriptionId, ahol a kísérlet és a feladatok elküldve |
| WorkspaceName | A Machine learning-munkaterület neve |
| ClusterName | A fürt neve |
| ProvisioningState | A feladatok beküldésének állapota |
| ResourceGroupName | Az erőforráscsoport neve |
| JobName | A feladattípus neve |
| ClusterId | A fürt azonosítója |
| EventType | A feladatok eseményének típusa. Például: JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | A feladattípus állapota (a Futtatás). Például: várólistára állított, Futtatás, sikeres, sikertelen |
| ErrorDetails | A feladattal kapcsolatos hibák részletei |
| CreationApiVersion | A feladatok létrehozásához használt API-verzió |
| ClusterResourceGroupName | A fürt erőforráscsoport-neve |
| TFWorkerCount | TF-feldolgozók száma |
| TFParameterServerCount | A TF paraméter-kiszolgáló száma |
| ToolType | A használt eszköz típusa |
| RunInContainer | Az a jelző, amely leírja, hogy a feladatot egy tárolón belül kell-e futtatni |
| JobErrorMessage | a feladattípus részletes üzenete |
| NodeId | A feladatot futtató csomópont azonosítója |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents táblázat

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| ProvisioningState | A fürt kiépítési állapota |
| ClusterName | A fürt neve |
| ClusterType | A fürt típusa |
| CreatedBy | A fürtöt létrehozó felhasználó |
| CoreCount | A fürtben lévő magok száma |
| VmSize | A fürt virtuálisgép-mérete |
| VmPriority | A fürt dedikált/LowPriority létrehozott csomópontjainak prioritása |
| ScalingType | A fürt méretezésének manuális/automatikus típusa |
| InitialNodeCount | A fürt kezdeti csomópontjának száma |
| MinimumNodeCount | A fürt minimális csomópontjainak száma |
| MaximumNodeCount | A fürt maximális csomópontjainak száma |
| NodeDeallocationOption | A csomópont kiosztásának módja |
| Publisher | A fürt típusának közzétevője |
| Ajánlat | Az ajánlat, amellyel a fürt létrejött |
| SKU | A fürtön belül létrehozott csomópont/virtuális gép SKU-a |
| Verzió | A csomópont/virtuális gép létrehozásakor használt rendszerkép verziója |
| A netI | A fürt kimutatása |
| AllocationState | Fürt kiosztási állapota |
| CurrentNodeCount | A fürt aktuális csomópontjainak száma |
| TargetNodeCount | A fürt célként megadott csomópontjainak száma a fel/le Méretezés közben |
| EventType | Az esemény típusa a fürt létrehozása során. |
| NodeIdleTimeSecondsBeforeScaleDown | Üresjárati idő másodpercben a fürt skálázása előtt |
| PreemptedNodeCount | A fürt előzik-csomópontjainak száma |
| IsResizeGrow | A fürt felskálázását jelző jelző |
| VmFamilyName | A fürtön belül létrehozható csomópontok virtuálisgép-családjának neve |
| LeavingNodeCount | A fürt csomópontjainak kihagyása |
| UnusableNodeCount | A fürt használhatatlan csomópontok száma |
| IdleNodeCount | A fürt üresjárati csomópontjainak száma |
| RunningNodeCount | A fürt csomópontok számának futtatása |
| PreparingNodeCount | A fürt csomópontjai számának előkészítése |
| QuotaAllocated | Lefoglalt kvóta a fürthöz |
| QuotaUtilized | A fürt kihasználtsági kvótája |
| AllocationStateTransitionTime | Áttérési idő az egyik állapotból a másikba |
| ClusterErrorCodes | A fürt létrehozása vagy skálázása során kapott hibakód |
| CreationApiVersion | A fürt létrehozásakor használt API-verzió |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents táblázat

| Tulajdonság | Leírás |
|:--- |:--- |
| TimeGenerated | A naplóbejegyzés létrehozásának ideje |
| OperationName | A naplózási eseményhez társított művelet neve |
| Kategória | A naplózási esemény neve, AmlComputeClusterNodeEvent |
| ClusterName | A fürt neve |
| NodeId | A létrehozott fürtcsomópont azonosítója |
| VmSize | A csomópont virtuális gép mérete |
| VmFamilyName | A virtuális gép családja, amelyhez a csomópont tartozik |
| VmPriority | Dedikált/LowPriority létrehozott csomópont prioritása |
| Publisher | A virtuális gép rendszerképének közzétevője. Például: Microsoft-dsvm |
| Ajánlat | A virtuális gép létrehozásával kapcsolatos ajánlat |
| SKU | A létrehozott csomópont/virtuális gép SKU-jának száma |
| Verzió | A csomópont/virtuális gép létrehozásakor használt rendszerkép verziója |
| ClusterCreationTime | A fürt létrehozásának ideje |
| ResizeStartTime | A fürt vertikális fel-és leskálázásának ideje |
| ResizeEndTime | A fürt vertikális felskálázásának ideje vége |
| NodeAllocationTime | A csomópont lefoglalásának ideje |
| NodeBootTime | A csomópont indításának ideje |
| StartTaskStartTime | A feladat csomóponthoz való hozzárendelésének és elindításának időpontja |
| StartTaskEndTime | A csomóponthoz rendelt feladat befejezésének ideje |
| TotalE2ETimeInSeconds | A teljes idő csomópont aktív volt |


## <a name="see-also"></a>Lásd még

- A figyelési Azure Machine Learning leírását a [figyelés Azure Machine learning](monitor-azure-machine-learning.md) című részben tekintheti meg.
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .
