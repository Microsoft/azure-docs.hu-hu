---
title: Azure Batch feladat-ütemterv sikertelen eseménye
description: A Batch-feladat ütemezett sikertelen eseményének hivatkozása. Ez az esemény akkor jelenik meg, ha egy feladatot nem sikerült ütemezni, és később újra próbálkozik.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852141"
---
# <a name="task-schedule-fail-event"></a>Tevékenységütemezés meghiúsult esemény

 Ezt az eseményt akkor bocsátja ki a rendszer, ha egy feladatot nem sikerült ütemezni, és később újra próbálkozik. Ez az erőforrás-korlátozás miatti ideiglenes Hiba a feladatütemezés során, például nem áll rendelkezésre elegendő tárolóhely a csomópontokon a megadott feladat futtatásához `requiredSlots` .

 Az alábbi példa egy feladatütemezés sikertelen eseményének törzsét mutatja be.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
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
|[`schedulingError`](#schedulingError)|Összetett típus|A feladat ütemezési hibájának adatait tartalmazza.|

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


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Elem neve|Típus|Jegyzetek|
|------------------|----------|-----------|
|`category`|Sztring|A hiba kategóriája.|
|`code`|Sztring|A feladatütemezés hibájának azonosítója. A kódok Invariant típusúak, és programozott módon való felhasználásra készültek.|
|`message`|Sztring|A feladat-ütemezési hibát leíró üzenet, amely egy felhasználói felületen való megjelenítésre alkalmas.|
