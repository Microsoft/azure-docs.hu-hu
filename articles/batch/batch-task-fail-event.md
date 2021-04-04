---
title: Azure Batch feladat meghiúsulása esemény
description: A Batch feladat sikertelen eseményének hivatkozása. Ez az esemény a feladat teljes eseményén kívül lesz kibocsátva, és felhasználható arra, hogy észlelje, ha egy feladat meghiúsult.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91850812"
---
# <a name="task-fail-event"></a>Tevékenység meghiúsult esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha egy feladat hibát jelez. A nullától eltérő kilépési kódok jelenleg nem minősülnek hibáknak. Ez az esemény a feladat teljes eseményén *kívül* lesz kibocsátva, és felhasználható arra, hogy észlelje, ha egy feladat meghiúsult.


 A következő példa egy feladat sikertelen eseményének törzsét mutatja be.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`jobId`|Sztring|A feladatot tartalmazó feladat azonosítója.|
|`id`|Sztring|A feladat azonosítója.|
|`taskType`|Sztring|A feladat típusa. Ez lehet "JobManager", amely azt jelzi, hogy egy Feladatkezelő feladat vagy "felhasználó", amely azt jelzi, hogy a feladat nem Feladatkezelő feladat. Ez az esemény nem lett kibocsátva a feladat-előkészítési feladatokhoz, a feladat-felszabadítási feladatokhoz és a tevékenységek indításához|
|`systemTaskVersion`|Int32|Ez a feladat belső újrapróbálkozási számlálója. A Batch szolgáltatás belsőleg újra tud próbálkozni az átmeneti problémák miatti feladatokkal. Ezek a problémák belső ütemezési hibákat tartalmazhatnak, vagy helytelen állapotú számítási csomópontokból történő helyreállításra tett kísérleteket okozhatnak.|
|`requiredSlots`|Int32|A feladat futtatásához szükséges tárolóhelyek.|
|[`nodeInfo`](#nodeInfo)|Összetett típus|A feladat futtatására szolgáló számítási csomóponttal kapcsolatos információkat tartalmazza.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Összetett típus|Megadja, hogy a feladat több számítási csomópontot igénylő többpéldányos feladat.  [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)Részletekért lásd:.|
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
|`numberOfInstances`|Int32|A feladat által igényelt számítási csomópontok száma.|

###  <a name="constraints"></a><a name="constraints"></a> korlátok

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|A feladat újrapróbálkozásának maximális száma. A Batch szolgáltatás újrapróbálkozik a feladattal, ha a kilépési kód nem nulla.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten meghatározza az újrapróbálkozások számát. A Batch szolgáltatás egyszer próbálkozik a feladattal, és ezt követően újra próbálkozik a korláttal. Ha például az újrapróbálkozások maximális száma 3, a Batch 4 alkalommal próbálkozik a feladattal (egy kezdeti próbálkozás és 3 újrapróbálkozás).<br /><br /> Ha a maximális újrapróbálkozások száma 0, a Batch szolgáltatás nem próbálkozik újra a tevékenységekkel.<br /><br /> Ha a maximális újrapróbálkozások száma-1, a Batch szolgáltatás korlátozás nélkül újrapróbálkozik a feladatokkal.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|


###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`startTime`|DateTime|Az az idő, amikor a feladat futása megkezdődött. A "Running" érték a **futó** állapotnak felel meg, így ha a feladat erőforrás-fájlokat vagy alkalmazáscsomagokat határoz meg, akkor a kezdési időpont azt az időpontot jelzi, amikor a feladat megkezdte a letöltését vagy üzembe helyezését.  Ha a feladat újraindult vagy újrapróbálkozott, ez a legutóbbi időpont, amikor a feladat elindult.|
|`endTime`|DateTime|Az az idő, amikor a feladat befejeződött.|
|`exitCode`|Int32|A feladat kilépési kódja.|
|`retryCount`|Int32|A Batch szolgáltatás által újrapróbált feladatok száma. A feladat újra próbálkozik, ha a nullától eltérő kilépési kóddal kilép a megadott MaxTaskRetryCount.|
|`requeueCount`|Int32|A Batch szolgáltatás által a felhasználói kérelem eredményeképpen újravárólistázott feladatok száma.<br /><br /> Ha a felhasználó eltávolít egy készletből származó csomópontokat (a készlet átméretezésével vagy kicsinyítésével), vagy ha a feladat le van tiltva, a felhasználó megadhatja, hogy a csomópontokon futó feladatokat a rendszer újravárólistára helyezi-e a végrehajtáshoz. Ez a szám azt követi nyomon, hogy a feladat hányszor lett újravárólistázott ezen okok miatt.|
