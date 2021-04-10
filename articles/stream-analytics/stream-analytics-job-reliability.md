---
title: A szolgáltatások megszakításának elkerülése Azure Stream Analytics feladatokban
description: Ez a cikk útmutatást nyújt a Stream Analytics-feladatok rugalmas frissítésének végrehajtásához.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f4eda779b1bc719706f9eb42cf805a7d5ce864aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020332"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>A szolgáltatás frissítéseinek Stream Analytics a feladatok megbízhatóságának garantálása

Egy teljes körűen felügyelt szolgáltatás része, amely gyors ütemben képes új szolgáltatás-funkcionalitást és-fejlesztési funkciókat bevezetni. Ennek eredményeképpen a Stream Analytics hetente (vagy még gyakoribb) is rendelkezhetnek a szolgáltatás frissítésével. Függetlenül attól, hogy mennyi tesztelés történik, még mindig fennáll annak a kockázata, hogy egy meglévő, futó feladatot egy hiba bevezetése miatt megszakadhat. Ha a feladatok kritikus fontosságú feladatait futtatja, ezeket a kockázatokat el kell kerülni. Ezt a kockázatot az Azure **[párosított régiós](../best-practices-availability-paired-regions.md)** modelljét követve csökkentheti. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hogyan kezeli az Azure párosított régiói ezt a problémát?

Stream Analytics garantálja, hogy a párosított régiókban lévő feladatok külön kötegekben frissüljenek. Ennek eredményeképpen a frissítések között elegendő idő van a lehetséges problémák azonosítására és azok szervizelésére.

Ha _a közép-India_ (amelynek a párosított régiója, Dél-India, nem rendelkezik stream Analytics jelenléttel), akkor a stream Analytics frissítésének telepítése nem fordulhat elő egyszerre a párosított régiókban. Ugyanabban a **csoportban** több régióban üzemelő példányok is előfordulhatnak **egyszerre.**

A **[rendelkezésre állással és a párosított régiókkal](../best-practices-availability-paired-regions.md)** kapcsolatos cikk a legfrissebb információkat tartalmaz a régiók párosításáról.

Azt javasoljuk, hogy azonos feladatokat helyezzen üzembe a párosított régiókban. Ezután [Figyelje ezeket a feladatokat](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) , hogy értesítést kapjon, ha valami váratlan esemény történik. Ha a feladatok egyike [sikertelen állapotba](./job-states.md) kerül egy stream Analytics szolgáltatás frissítése után, forduljon az ügyfélszolgálathoz, és segít azonosítani a kiváltó okot. Az összes alsóbb rétegbeli fogyasztót is át kell adnia az egészséges feladat kimenetére.

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Stream Analyticsba](stream-analytics-introduction.md)
* [Ismerkedés a Stream Analytics szolgáltatással](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-feladatok skálázása](stream-analytics-scale-jobs.md)
* [Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)