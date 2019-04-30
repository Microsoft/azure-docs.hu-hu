---
title: Az Azure Batch tevékenység kész esemény |} A Microsoft Docs
description: Referencia a Batch-feladat befejeződésének eseményét.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: b5fd1a8020c8e95323bc2333c0583dafe58e8456
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549971"
---
# <a name="task-complete-event"></a>Tevékenység kész esemény

 Ez az esemény bocsásson ki, ha egy feladat befejeződött, függetlenül a kilépési kódot. Ezt az eseményt, ha a feladat lefutott, és kísérelte meg, hogy a feladat időtartamának meghatározásához használható.


 Az alábbi példa bemutatja egy tevékenység kész esemény törzsét.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
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
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|jobId|String|A feladat a tevékenységet tartalmazó azonosítója.|
|id|String|A feladat azonosítója.|
|taskType|String|A feladat típusát. Ez is jelezve, hogy ez a feladatkezelői tevékenység JobManager, vagy már nem a feladatkezelői tevékenység jelző "felhasználó". Ez az esemény nem kibocsátott, feladat-előkészítési tevékenységeket, a feladatkiadási tevékenységeket vagy az indítási tevékenységeket.|
|systemTaskVersion|Int32|Ez a tevékenység a belső újrapróbálkozási számláló. A Batch szolgáltatás belsőleg áthidalhatók az átmeneti hibák feladat újra. Ezek a helyzetek közé tartoznak a belső ütemezési hibák, vagy a helyreállítás kísérletek számítási csomópontok rossz állapotban.|
|[nodeInfo](#nodeInfo)|Komplex típus|A számítási csomóponton, amelyen a feladat futott kapcsolatos információt tartalmazza.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplex típus|Megadja, hogy a tevékenység egy többpéldányos tevékenység több számítási csomópontokat igényelnek.  Lásd: [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) részleteiről.|
|[Korlátozások](#constraints)|Komplex típus|A végrehajtási korlátozások, amelyek a alkalmazni ezt a feladatot.|
|[executionInfo](#executionInfo)|Komplex típus|A feladat végrehajtásának kapcsolatos információt tartalmazza.|

###  <a name="nodeInfo"></a> nodeInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|poolId|String|A annak a készletnek azonosítója, amelyen a tevékenység futott.|
|nodeId|String|A csomópont, amelyen a feladat futott azonosítója.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|numberOfInstances|Int32|A tevékenységhez szükséges számítási csomópontok száma.|

###  <a name="constraints"></a> Korlátozások

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|A maximális száma a próbálhassa. A Batch szolgáltatás feladat újrapróbálkozik, ha annak kilépési kódja nullától eltérő.<br /><br /> Vegye figyelembe, hogy ez az érték kifejezetten határozza meg az újrapróbálkozások számát. A Batch szolgáltatás egyszer megkísérli a feladat, és előfordulhat, hogy próbálkozzon újra legfeljebb ezt a korlátot. Például ha az újrapróbálkozások maximális száma 3, köteg próbálkozás egy feladat legfeljebb 4 alkalommal (kezdeti próbálkozás és 3 újrapróbálás).<br /><br /> Ha az újrapróbálkozások maximális száma 0, a Batch szolgáltatás nem próbálkozik újra a feladatot.<br /><br /> Ha az újrapróbálkozások maximális száma -1, akkor a Batch szolgáltatás korlátozás nélkül feladatok újrapróbálkozik.<br /><br /> Az alapértelmezett érték: 0 (nincs újrapróbálkozás).|

###  <a name="executionInfo"></a> executionInfo

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|startTime|DateTime|Amikor a feladat elindításának ideje. "Fut" megfelel a **futó** állapotba, így ha a feladat Erőforrásfájlok vagy az alkalmazáscsomagok határozza meg, majd a kezdési időpont tükrözi az idő, amikor a feladat elindult letöltése vagy telepítése ezeket.  Ha a feladat újraindítása, vagy a rendszer megpróbálja újból végrehajtani, akkor a legutóbbi idő, amikor a feladat elindításának.|
|endTime|DateTime|Az az időpont, amikor a tevékenység befejeződött.|
|exitCode|Int32|A tevékenység kilépési kódját.|
|RetryCount|Int32|A száma a feladat rendelkezik lett rendszer megpróbálja újból végrehajtani a Batch szolgáltatás által. A rendszer megpróbálja újból végrehajtani a feladatot, ha egy nem nulla kilépési kóddal, legfeljebb a megadott MaxTaskRetryCount kilép.|
|requeueCount|Int32|A száma a feladat eredményeként egy felhasználói kérelem rendelkezik a Batch szolgáltatás által lett lefutni.<br /><br /> Amikor a felhasználó eltávolítja csomópontok egy készletet (átméretezése, vagy a készletre kicsinyítésével), vagy ha a feladat letiltása közben, a felhasználó is adja meg, hogy fut a csomópontokon feladatok várólistára kerülnek végrehajtásra. Ez a szám ebből kifolyólag a feladat rendelkezik lett várólistára hány alkalommal követi nyomon.|

<!-- Update_Description: update metedata properties -->