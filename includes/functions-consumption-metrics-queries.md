---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168110"
---
#### <a name="determine-memory-usage"></a>Memóriahasználat meghatározása 

A **figyelés** területen válassza a **naplók (Analitika)** lehetőséget, majd másolja a következő telemetria-lekérdezést, és illessze be a lekérdezési ablakba, majd válassza a **Futtatás** lehetőséget. Ez a lekérdezés a teljes memóriahasználat értékét adja vissza az egyes mintavételi időpontokban.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Az eredmények a következő példához hasonlóan jelennek meg:

| időbélyeg \[ UTC\]          | name          | érték       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 am | Saját bájtok | 209 932 288 |
| 9/12/2019, 1:06:14 \. 994 am | Saját bájtok | 212 189 184 |
| 9/12/2019, 1:06:30 \. 010 am | Saját bájtok | 231 714 816 |
| 9/12/2019, 1:07:15 \. 040 am | Saját bájtok | 210 591 744 |
| 9/12/2019, 1:12:16 \. 285 am | Saját bájtok | 216 285 184 |
| 9/12/2019, 1:12:31 \. 376 am | Saját bájtok | 235 806 720 |

#### <a name="determine-duration"></a>Időtartam meghatározása 

Azure Monitor nyomon követi a metrikákat az erőforrás szintjén, amely a functions alkalmazás. Application Insights integrációja a metrikákat a függvények alapján bocsátja ki. Íme egy példa az elemzési lekérdezésre egy függvény átlagos időtartamának lekéréséhez:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
