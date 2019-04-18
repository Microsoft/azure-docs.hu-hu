---
title: Az Azure Batch figyelése |} A Microsoft Docs
description: További információk az Azure figyelési szolgáltatások, metrikák, a diagnosztikai naplók és más figyelési szolgáltatásokat az Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848038"
---
# <a name="monitor-batch-solutions"></a>Batch-megoldások monitorozása

Az Azure és a Batch szolgáltatás biztosít a szolgáltatások, eszközök és a Batch-megoldások monitorozása az API-k széles. Áttekintő cikkben segítséget nyújt a figyelési módszer is létezik, amely a legjobban az igényeinek.

Az Azure-összetevőket és Azure-erőforrások figyeléséhez elérhető szolgáltatások áttekintését lásd: [figyelése az Azure-alkalmazások és erőforrások](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Előfizetés-szintű figyelése

Az előfizetés szintjén, amely magában foglalja a Batch-fiókok, a [Azure tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md) gyűjti a működési események adatait [különböző kategóriákba](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log).

A Batch-fiókok kifejezetten, a tevékenységnapló gyűjti a fiók létrehozásának és törlésének és kulcskezelési kapcsolódó eseményeket.

Események lekérése a tevékenységnaplót egyik módja, hogy az Azure Portalon. Kattintson a **minden szolgáltatás** > **tevékenységnapló**. Vagy, a lekérdezés az események az Azure CLI, PowerShell-parancsmagok vagy az Azure Monitor REST API használatával. A tevékenységnapló exportálása, vagy is konfigurálása [tevékenységnapló-riasztások](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Batch-fiókszintű figyelése

Szolgáltatását használja, amely minden egyes Batch-fiók figyelése [Azure Monitor](../azure-monitor/overview.md). Az Azure Monitor gyűjt [metrikák](../azure-monitor/platform/data-platform-metrics.md) és opcionálisan [diagnosztikai naplók](../azure-monitor/platform/diagnostic-logs-overview.md) a Batch-fiókot, például a készletek, feladatok és tevékenységek szintjén hatókörön belüli erőforrások. Gyűjtése és felhasználása ezeket az adatokat, manuálisan vagy programon keresztül a Batch-fiókban lévő tevékenységek figyelését, és diagnosztizálhatja a problémákat. További információkért lásd: [Batch-metrikák, a riasztások és a naplókat a további diagnosztikai kiértékeléséhez és figyeléséhez](batch-diagnostics.md).
 
> [!NOTE]
> Metrikák érhetők el további konfiguráció nélkül a Batch-fiók alapértelmezés szerint, és a egy 30 napos gördülő előzmények rendelkeznek. Engedélyeznie kell a Batch-fiók diagnosztikai naplózását, és Ön további költségekkel járhat tárolásához, vagy diagnosztikai naplóadatokat feldolgozni. 

## <a name="batch-resource-monitoring"></a>Batch-erőforrások monitorozása

A Batch-alkalmazások a Batch API-k vagy többek között a feladatok, a feladatok, a csomópontok és a készletek az erőforrások állapotának monitorozásához használja. Példa:

* [Feladatok száma, és a számítási csomópontok állapot szerint](batch-get-resource-counts.md)
* [Hozzon létre hatékony lekérdezések Batch-erőforrások listája](batch-efficient-list-queries.md)
* [Tevékenységfüggőségek létrehozása](batch-task-dependencies.md)
* Használja a [feladatkezelői tevékenység](/rest/api/batchservice/job/add#jobmanagertask)
* A figyelő a [feladat állapota](/rest/api/batchservice/task/list#taskstate)
* A figyelő a [csomópont állapota](/rest/api/batchservice/computenode/list#computenodestate)
* A figyelő a [tárolókészlet állapota](/rest/api/batchservice/pool/get#poolstate)
* A figyelő [készletéből, a fiók használata](/rest/api/batchservice/pool/listusagemetrics)
* [Készlet csomópontjainak száma a state szerint](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Virtuális gép teljesítményszámlálók és az alkalmazás figyelése

* [Az Application Insights](../azure-monitor/app/app-insights-overview.md) használhatja a rendelkezésre állását, teljesítményét és használatát, a Batch-feladatok és a feladatok programozott figyelése Azure-szolgáltatások. Egyszerűen get Teljesítménymérők számítási csomópontok (virtuális gépek) és a virtuális gépek minden feladatokhoz egyéni információ. 

  Egy vonatkozó példáért lásd: [Monitor és a Batch .NET-alkalmazás, az Application Insights hibakeresési](monitor-application-insights.md) és a kísérő [kódminta](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights).

  > [!NOTE]
  > Az Application Insights használatával további költségeket számítanak fel. Tekintse meg a [díjszabási lehetőségek](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [A Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz létrehozásához, hibakereséséhez és monitorozásához az Azure Batch-alkalmazások segítségével. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. Választható lehetőségként konfigurálhatja a Batch-megoldás a [Application Insights-adatok megjelenítése](https://github.com/Azure/batch-insights) például a virtuális gép teljesítményszámlálók a Batch Explorer.


## <a name="next-steps"></a>További lépések

* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* Tudjon meg többet [diagnosztikai naplózás](batch-diagnostics.md) a Batch használatával.