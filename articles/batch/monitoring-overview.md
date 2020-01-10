---
title: Figyelő Azure Batch | Microsoft Docs
description: Ismerje meg az Azure monitoring Services, a metrikák, a diagnosztikai naplók és a Azure Batch további figyelési funkcióit.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: f7155baa7b899922c9553a9a556e5bfcf3562d1b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745237"
---
# <a name="monitor-batch-solutions"></a>Batch-megoldások monitorozása

Az Azure és a Batch szolgáltatás számos szolgáltatást, eszközt és API-t biztosít a Batch-megoldások figyeléséhez. Ez az áttekintő cikk segítséget nyújt az igényeinek megfelelő figyelési megközelítés kiválasztásában.

Az Azure-erőforrások figyeléséhez elérhető Azure-összetevők és-szolgáltatások áttekintését lásd: [Azure-alkalmazások és-erőforrások figyelése](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Előfizetés szintű figyelés

Az előfizetési szinten, amely tartalmazza a Batch-fiókokat, az [Azure-tevékenység naplója](../azure-monitor/platform/platform-logs-overview.md) [több kategóriában](../azure-monitor/platform/activity-log-view.md#categories-in-the-activity-log)gyűjti az operatív események adatait.

A Batch-fiókok esetében a műveletnapló a fiókok létrehozásával és törlésével, valamint a kulcskezelő szolgáltatással kapcsolatos eseményeket gyűjti.

Az események a tevékenység naplóból való beolvasásának egyik módja a Azure Portal használata. Kattintson **a minden szolgáltatás** > **tevékenység naplója**elemre. Vagy az Azure CLI-vel, a PowerShell-parancsmagokkal vagy a Azure Monitor REST APIával kapcsolatos eseményeket kérdezheti le. Exportálhatja a tevékenység naplóját is, vagy konfigurálhatja a [tevékenység naplójának riasztásait](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Batch-fiók szintű figyelés

Az egyes batch-fiókokat a [Azure monitor](../azure-monitor/overview.md)funkcióival figyelheti. Azure Monitor a Batch-fiók szintjén (például készletekben, feladatokban és feladatokban) lévő erőforrások [mérőszámait](../azure-monitor/platform/data-platform-metrics.md) és opcionálisan [diagnosztikai naplóit](../azure-monitor/platform/platform-logs-overview.md) gyűjti. Az adatok gyűjtése és felhasználása manuálisan vagy programozott módon a Batch-fiókban lévő tevékenységek figyeléséhez és a problémák diagnosztizálásához. Részletekért lásd: [Batch-metrikák, riasztások és naplók a diagnosztika kiértékeléséhez és figyeléséhez](batch-diagnostics.md).
 
> [!NOTE]
> A metrikák alapértelmezés szerint a Batch-fiókban, további konfiguráció nélkül érhetők el, és 30 napos múlttal rendelkeznek. Engedélyeznie kell a diagnosztikai naplózást egy batch-fiókhoz, és további költségek merülhetnek fel a diagnosztikai naplózási információk tárolásához vagy feldolgozásához. 

## <a name="batch-resource-monitoring"></a>Kötegelt erőforrás-figyelés

A Batch-alkalmazásokban a Batch API-k segítségével figyelheti vagy lekérdezheti az erőforrások állapotát, beleértve a feladatokat, a feladatokat, a csomópontokat és a készleteket. Példa:

* [Feladatok és számítási csomópontok számlálása állapot szerint](batch-get-resource-counts.md)
* [Lekérdezések létrehozása a Batch-erőforrások hatékony listázásához](batch-efficient-list-queries.md)
* [Feladat függőségeinek létrehozása](batch-task-dependencies.md)
* Feladat- [kezelő feladat](/rest/api/batchservice/job/add#jobmanagertask) használata
* A [feladat állapotának](/rest/api/batchservice/task/list#taskstate) figyelése
* A [csomópont állapotának](/rest/api/batchservice/computenode/list#computenodestate) figyelése
* A [készlet állapotának](/rest/api/batchservice/pool/get#poolstate) figyelése
* [A készlet használatának figyelése a fiókban](/rest/api/batchservice/pool/listusagemetrics)
* [Pool-csomópontok állapot szerinti számlálása](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM-teljesítményszámlálók és-alkalmazások figyelése

* [Application Insights](../azure-monitor/app/app-insights-overview.md) egy Azure-szolgáltatás, amellyel programozott módon figyelheti a Batch-feladatok és-feladatok rendelkezésre állását, teljesítményét és használatát. Egyszerűen lekérheti a teljesítményszámlálókat a számítási csomópontokból (VM) és a virtuális gépeken kívüli feladatokhoz tartozó egyéni adatokból. 

  Példa: [Batch .NET-alkalmazás figyelése és hibakeresése Application Insights](monitor-application-insights.md) és a hozzá tartozó [kódrészlettel](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > A Application Insights használatához további költségek merülhetnek fel. Tekintse meg a [díjszabási lehetőségeket](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* A [Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, gazdag funkcionalitású, önálló ügyfél-eszköz, amely a Azure batch alkalmazások létrehozását, hibakeresését és figyelését segíti elő. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. Opcionálisan konfigurálhatja a Batch-megoldást úgy, hogy az Application Insights adatait, például a virtuális gépek teljesítményszámlálói a Batch Explorerben [jelenjenek meg](https://github.com/Azure/batch-insights) .


## <a name="next-steps"></a>Következő lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* További információ a Batch szolgáltatással történő [diagnosztikai naplózásról](batch-diagnostics.md) .