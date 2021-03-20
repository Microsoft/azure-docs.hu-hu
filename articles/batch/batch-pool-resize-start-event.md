---
title: Azure Batch készlet átméretezésének kezdési eseménye
description: A Batch-készlet átméretezési indítási eseményének hivatkozása. Például egy készlet átméretezési indítási esemény törzsét jeleníti meg, ha egy készlet 0 és 2 csomópont között átméretezi a manuális átméretezést.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be64a2ef30cbe3c404633b29202a4adf1e49ea9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803612"
---
# <a name="pool-resize-start-event"></a>Készlet átméretezésének indítása esemény

 Ezt az eseményt a rendszer a készlet átméretezésének megkezdése után bocsátja ki. Mivel a készlet átméretezése egy aszinkron esemény, várható, hogy a készlet átméretezése az átméretezési művelet befejezését követően kivezethető teljes eseményt.

 A következő példa egy készlet átméretezési indítási eseményének törzsét mutatja be, ha egy készlet 0 és 2 csomópont között átméretezi a manuális átméretezést.

```
{
   "id": "myPool1",
   "nodeDeallocationOption": "Invalid",
   "currentDedicatedNodes": 0,
   "targetDedicatedNodes": 2,
   "currentLowPriorityNodes": 0,
   "targetLowPriorityNodes": 2,
   "enableAutoScale": false,
   "isAutoPool": false
}
```

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`nodeDeallocationOption`|Sztring|Megadja, hogy a rendszer mikor távolítsa el a csomópontokat a készletből, ha a készlet mérete csökken.<br /><br /> Lehetséges értékek:<br /><br /> **újravárólista** – leállítja a futó feladatokat, és újravárólistára helyezi őket. A feladatok akkor futnak újra, amikor a feladat engedélyezve van. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **megszakítás** – futó feladatok leállítása. A feladatok nem futnak újra. A csomópontokat a feladatok leállítása után távolítsa el.<br /><br /> **taskcompletion** – a jelenleg futó feladatok befejezésének engedélyezése. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes feladat befejeződött.<br /><br /> **Retaineddata** – lehetővé teszi a jelenleg futó feladatok befejezését, majd várjon, amíg az összes feladat adatmegőrzési időszaka lejár. A várakozás közben nem ütemezhet új feladatokat. Csomópontok eltávolítása, ha az összes tevékenység megőrzési időszaka lejárt.<br /><br /> Az alapértelmezett érték az újraüzenetsor.<br /><br /> Ha a készlet mérete növekszik, az érték **érvénytelenre** van állítva.|
|`currentDedicatedNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetDedicatedNodes`|Int32|A készlethez igényelt számítási csomópontok száma.|
|`currentLowPriorityNodes`|Int32|A készlethez jelenleg hozzárendelt számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez igényelt számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Meghatározza, hogy a készlet mérete automatikusan igazodik-e az idő múlásával.|
|`isAutoPool`|Logikai|Azt határozza meg, hogy a készlet a feladatok autopool mechanizmusán keresztül lett-e létrehozva.|
