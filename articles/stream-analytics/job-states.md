---
title: Azure Stream Analytics feladatok állapota
description: Ez a cikk a Stream Analytics feladatok négy különböző állapotát ismerteti; futó, leállított, csökkentett teljesítményű és sikertelen.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020451"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics feladatok állapota

Egy Stream Analytics feladatnak a négy állam egyikében lehet egy adott időpontban: futó, leállított, csökkentett teljesítményű vagy sikertelen. A feladatok állapotát a Azure Portal Stream Analytics feladatok Áttekintés oldalán találja. 

| Állam | Leírás | Ajánlott műveletek |
| --- | --- | --- |
| **Futó** | A feladatnak a megadott bemeneti forrásokból érkező, az Azure-beli olvasási eseményeken kell futnia, és fel kell dolgoznia azokat, és az eredményeket a konfigurált kimeneti mosdóba kell írni | Az ajánlott eljárás a feladatok teljesítményének nyomon követése a [fő mérőszámok](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)figyelésével. |
| **Leállítva** | A rendszer leállítja a feladatot, és nem dolgozza fel az eseményeket. | NA | 
| **Csökkentett teljesítményű** | Előfordulhat, hogy a bemeneti és kimeneti kapcsolatokban időnként problémák léptek fel. Ezeket a hibákat átmeneti hibáknak nevezzük, amelyekkel a feladatok csökkentett teljesítményű állapotba kerülhetnek. Stream Analytics azonnal megkísérli visszaállítani az ilyen hibákat, és visszatér egy futó állapotba (néhány percen belül). Ezek a hibák hálózati problémák, más Azure-erőforrások rendelkezésre állása, deszerializálási hibák stb. miatt fordulnak elő. A feladatok teljesítményének romlása befolyásolhatja, ha a feladatnak csökkentett állapotban van.| Az átmeneti hibák okának megismeréséhez tekintse meg a [diagnosztikai vagy a tevékenység naplóit](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) . Olyan esetekben, mint például a deszerializálási hibák, javasolt a javítási művelet végrehajtása annak biztosítása érdekében, hogy az események ne legyenek helytelenek. Ha a feladatokban továbbra is eléri az erőforrás-kihasználtsági korlátot, próbálja meg megnövelni a integrálással számát vagy a [feladatot](./stream-analytics-parallelization.md). Más esetekben, amikor nem végez semmilyen műveletet, Stream Analytics megkísérli a helyreállítást egy *futó* állapotba. <br> A küszöbértékek [késleltetésének](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) mérőszáma segítségével megtudhatja, hogy ezek az átmeneti hibák hatással vannak-e a feladatok teljesítményére.|
| **Sikertelen** | A feladatainak kritikus hibába ütközött, ami sikertelen állapotot eredményezett. Az események nincsenek beolvasva és feldolgozva. A futásidejű hibák gyakori oka, hogy a feladatok sikertelen állapotban vannak. | A [riasztásokat konfigurálhatja](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) úgy, hogy értesítést kapjon, ha a feladatot sikertelen állapotba kerül. <br> <br>A hibakereséshez a [tevékenység-és erőforrás-naplók](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) segítségével azonosíthatja a kiváltó okot, és elháríthatja a problémát.|

## <a name="next-steps"></a>Következő lépések
* [Riasztások beállítása Azure Stream Analytics feladatokhoz](stream-analytics-set-up-alerts.md)
* [Stream Analytics elérhető metrikák](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Hibakeresés a tevékenység-és erőforrás-naplók használatával](./stream-analytics-job-diagnostic-logs.md)