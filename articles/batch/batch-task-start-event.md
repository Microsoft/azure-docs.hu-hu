---
title: Azure Batch feladat indítása esemény
description: A Batch-feladat indítási eseményére vonatkozó információk. Ezt az eseményt akkor bocsátja ki a rendszer, ha az ütemező egy számítási csomóponton indítja el a feladatot.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 3a57ffbb1e1659cff54d101aa4b90ca1bd5d3a57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851016"
---
# <a name="task-start-event"></a>Tevékenység indítása esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha az ütemező egy számítási csomóponton indítja el a feladatot. Vegye figyelembe, hogy ha a feladat újrapróbálkozása vagy újbóli várólistára kerül, az esemény ugyanarra a feladatra kerül, de az újrapróbálkozások száma és a rendszerfeladat verziója ennek megfelelően frissül.


 A következő példa egy feladat-indítási esemény törzsét mutatja be.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`jobId`|Sztring|A feladatot tartalmazó feladat azonosítója.|
|`id`|Sztring|A feladat azonosítója.|
|`taskType`|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy egy Feladatkezelő feladat vagy "felhasználó", amely azt jelzi, hogy a feladat nem Feladatkezelő feladat.|
|`systemTaskVersion`|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra tud próbálkozni az átmeneti problémák miatti feladatokkal. Ezek a problémák belső ütemezési hibákat tartalmazhatnak, vagy helytelen állapotú számítási csomópontokból történő helyreállításra tett kísérleteket okozhatnak.|
|`requiredSlots`|Int32|A feladat futtatásához szükséges tárolóhelyek.|
|[`nodeInfo`](#nodeInfo)|Összetett típus|A feladat futtatására szolgáló számítási csomóponttal kapcsolatos információkat tartalmazza.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Összetett típus|Azt adja meg, hogy a feladat több számítási csomópontot igénylő többpéldányos feladat.  További részletekért lásd: [multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) .|
|[`constraints`](#constraints)|Összetett típus|A feladatra érvényes végrehajtási megkötések.|
|[`executionInfo`](#executionInfo)|Összetett típus|A feladat végrehajtásával kapcsolatos információkat tartalmaz.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`poolId`|Sztring|Annak a készletnek az azonosítója, amelyen a feladat futott.|
|`nodeId`|Sztring|Annak a csomópontnak az azonosítója, amelyen a feladat futott.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`numberOfInstances`|Int|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a><a name="constraints"></a> korlátok

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|A feladat újrapróbálkozásának maximális száma. A Batch szolgáltatás újrapróbálkozik a feladattal, ha a kilépési kód nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten meghatározza az újrapróbálkozások számát. A Batch szolgáltatás egyszer próbálkozik a feladattal, és ezt követően újra próbálkozik a korláttal. Ha például az újrapróbálkozások maximális száma 3, a Batch 4 alkalommal próbálkozik a feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálkozik újra a tevékenységekkel.<br /><br /> Ha a maximális újrapróbálkozások száma-1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`retryCount`|Int32|A Batch szolgáltatás által újrapróbált feladatok száma. A feladat újra próbálkozik, ha a nullától eltérő kilépési kóddal kilép a megadott MaxTaskRetryCount|
