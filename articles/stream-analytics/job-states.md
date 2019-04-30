---
title: Az Azure Stream Analytics-feladat állapotok
description: Ez a cikk ismerteti egy Stream Analytics-feladat különböző állapotok
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789451"
---
# <a name="azure-stream-analytics-job-states"></a>Az Azure Stream Analytics-feladat állapotok

Stream Analytics-feladat egy adott időpontban négy állapota lehet. A Stream Analytics-feladat áttekintése lapon a feladat állapotát az Azure Portalon találhatja meg. 

| Állapot | Leírás | Javasolt műveletek |
| --- | --- | --- |
| **Fut** | A feladat fut az Azure-ban meghatározott bemeneti forrás érkező, feldolgozását és írása az eredményeket a konfigurált kimeneti fogadóinak események olvasását. | Nyomon követheti a feladat teljesítményét a figyelési szolgáltatás által az ajánlott eljárás [kulcsfontosságú metrikát](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stopped** | A feladat le van állítva, és nem dolgozza fel az eseményeket. | NA | 
| **Csökkentett teljesítményű** | Átmeneti hibák valószínűleg negatív hatással vannak a feladatot. Stream Analytics azonnal megpróbálja az ilyen hibák, és térjen vissza a futási állapota (néhány percen belül). Ezek a hibák sikerült történik hálózati problémák, egyéb Azure-erőforrások rendelkezésre állása miatt deszerializálási hibák stb. A feladat teljesítményét negatív lehet, ha feladat csökkentett teljesítményű állapotban van.| Tekintse meg a [diagnosztikai vagy tevékenység naplók](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) átmeneti hibák okának tájékozódhat. Azokban az esetekben, például a deszerializálás hibák ajánlott annak biztosítására, események nem hibás korrekciós műveletek végrehajtására. Ha a feladat tartja erőforrás felhasználási korlát elérése, próbálja meg a SU számának növeléséhez vagy [párhuzamosíthatja a feladat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Más esetekben, ahol semmilyen műveletet nem lehet végrehajtani, a Stream Analytics megpróbálja helyreállítani kívánt egy *futó* állapota.  |
| **Nem sikerült** | A feladat kritikus hibát eredményez a hibás állapotban. Események nem olvasása és feldolgozása. Futásidejű hibák általában az okozza a feladat befejezési sikertelen állapotban. | Is [riasztások konfigurálása](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) úgy, hogy Ön értesítést kaphat, amikor a feladat sikertelen állapotba kerül. <br> <br>Is hibakeresést [tevékenységnaplóinak és diagnosztikai naplóinak](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) okát, és a probléma.|

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics-feladatok riasztások beállítása](stream-analytics-set-up-alerts.md)
* [A metrikák elérhető a Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Hibaelhárítás a tevékenység és a diagnosztikai naplók használatával](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
