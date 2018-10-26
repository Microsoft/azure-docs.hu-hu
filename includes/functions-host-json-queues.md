---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7139b88ee9ef137ca28484538262b8bb2fe804db
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134220"
---
```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxPollingInterval|60000|A maximális időköz ezredmásodpercben várólista lekérdezések között.| 
|visibilityTimeout|0|Az üzenet feldolgozása során az újrapróbálkozások közötti időintervallum sikertelen lesz.| 
|batchSize|16|A Functions futtatókörnyezete egy időben kéri le, és párhuzamosan dolgozza fel üzenetsorbeli üzenetek száma. Ha a feldolgozás alatt szám lekérdezi le a a `newBatchThreshold`, a modul egy másik köteg lekérdezi, és elindítja a feldolgozási ezeket az üzeneteket. A függvény feldolgozott egyidejű üzenetek maximális száma így `batchSize` plusz `newBatchThreshold`. Ez a korlátozás külön-külön mindegyik üzenetsor által aktivált függvény vonatkozik. <br><br>Ha el szeretné kerülni a párhuzamos végrehajtása egy üzenetsorban fogadott üzenetek, beállíthat `batchSize` 1-re. Azonban ez a beállítás használata esetén nem egyidejűségi csak, feltéve, hogy a függvényalkalmazás futtatása egyetlen virtuális gépen (VM). Ha több virtuális gépre méretezhető a függvényalkalmazás, minden virtuális gép futhat egy példányát minden egyes üzenetsor által aktivált függvény.<br><br>A maximális `batchSize` 32. | 
|maxDequeueCount|5|A hányszor próbálkozzon, egy üzenet feldolgozása az ártalmas üzenetsor áthelyezés előtt.| 
|newBatchThreshold|batchSize/2|Minden alkalommal, amikor ez a szám a lekérdezi az egyidejűleg feldolgozott üzenetek száma, a modul egy másik köteg kérdezi le.| 



