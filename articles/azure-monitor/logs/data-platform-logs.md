---
title: Azure Monitor-naplók
description: A Azure Monitor adatok speciális elemzéséhez használt naplókat ismerteti.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 9794c5f048b8795652e4b31e0134b36a77715abe
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873378"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor-naplók – áttekintés
Azure Monitor Naplók szolgáltatása a Azure Monitor, amely napló- és teljesítményadatokat gyűjt és rendszerez a [figyelt erőforrásokból.](../monitor-reference.md) A különböző forrásokból származó adatok, például az Azure-szolgáltatásokból származó [platformnaplók,](../essentials/platform-logs-overview.md) a virtuálisgép-ügynökök napló- és teljesítményadata, valamint az alkalmazásokból származó használati és teljesítményadatok egyetlen munkaterületen egyeshetők, így együtt elemezhetők egy kifinomult lekérdezési nyelvvel, amely képes gyorsan elemezni több millió rekordot. [](../agents/agents-overview.md) [](../app/app-insights-overview.md) Végrehajthat egy egyszerű lekérdezést, amely csak lekér egy adott rekordhalmazt, vagy kifinomult adatelemzést végez a monitorozási adatok kritikus mintáinak azonosításához. A Log Analytics használatával interaktív módon dolgozhat naplólekérdezésekkel és azok eredményeivel, a riasztási szabályokban proaktívan értesítheti őket a problémákról, vagy egy munkafüzetben vagy irányítópulton vizualizálhatja az eredményeket.

> [!NOTE]
> Azure Monitor Naplók az adatplatform egyik fele, amely támogatja a Azure Monitor. A másik a [Azure Monitor metrikák,](../essentials/data-platform-metrics.md) amelyek numerikus adatokat tárolnak egy idősorozat-adatbázisban. Így ezek az adatok a Azure Monitor-naplókban lévő adatoknál kisebbek, és közel valós idejű forgatókönyvek támogatására is alkalmasak, így különösen hasznosak a riasztások és a problémák gyors észlelése során. A metrikák azonban csak egy adott struktúrában tárolnak numerikus adatokat, míg a naplók különböző adattípusokat tárolnak saját struktúrával. A naplóadatokon olyan naplólekérdezésekkel is elvégezhet összetett elemzést, amelyek nem használhatók metrikaadatok elemzésére.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Mire használhatja a Azure Monitor naplókat?
Az alábbi táblázat a naplók használatának különböző módjait ismerteti a Azure Monitor:

|  | Description |
|:---|:---|
| **Elemzés** | A [Log Analytics használatával](./log-analytics-tutorial.md) Azure Portal naplólekérdezéseket írhat, és interaktív módon elemezheti a naplóadatokat egy hatékony elemzőmotorral [](./log-query-overview.md) |
| **Riasztás** | Állítson be [egy naplóriasztás-szabályt,](../alerts/alerts-log.md) amely értesítést küld, vagy automatikus műveletet végez, ha a lekérdezés eredményei megegyeznek egy adott eredményekkel. [](../alerts/action-groups.md) |
| **Vizualizáció** | Táblaként vagy diagramként megjelenített lekérdezési eredményeket rögzítheti egy [Azure-irányítópulton.](../../azure-portal/azure-portal-dashboards.md)<br>Létrehozhat egy [munkafüzetet,](../visualize/workbooks-overview.md) amely több adatkészletet kombinál egy interaktív jelentésben. <br>Exportálja egy lekérdezés eredményeit, [hogy Power BI](../visualize/powerbi.md) vizualizációkat használjon, és megosztja őket az Azure-beli felhasználókkal.<br>Exportálja egy lekérdezés eredményeit a [Grafanába,](../visualize/grafana-plugin.md) hogy kihasználja az irányítópultját, és kombinálja más adatforrásokkal.|
| **Insights** | Olyan [elemzések támogatása,](../monitor-reference.md#insights-and-core-solutions) amelyek testreszabott figyelési élményt biztosítanak az egyes alkalmazásokhoz és szolgáltatásokhoz.  |
| **Elhoz** | Naplólekérdezés eredményeinek elérése parancssorból az [Azure CLI használatával.](/cli/azure/monitor/log-analytics)<br>Naplólekérdezés eredményeinek elérése parancssorból [PowerShell-parancsmagok használatával.](/powershell/module/az.operationalinsights)<br>Egyéni alkalmazásból származó naplólekérdezés eredményeinek elérése a [REST API.](https://dev.loganalytics.io/) |
| **Exportálás** | Naplóadatok [automatikus exportálásának konfigurálása](./logs-data-export.md) Azure Storage-fiókba vagy Azure Event Hubs.<br>Munkafolyamat létrehozása a naplóadatok lekéréséhez és külső helyre való másolásához a [Logic Apps.](./logicapp-flow-connector.md) |

![Naplók áttekintése](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Adatgyűjtés
Miután létrehozott egy Log Analytics-munkaterületet, különböző forrásokat kell konfigurálnia az adataik elküldését. A rendszer nem gyűjt automatikusan adatokat. Ez a konfiguráció az adatforrástól függően eltérő lesz. Létrehozhat például [diagnosztikai beállításokat,](../essentials/diagnostic-settings.md) amelyek erőforrásnaplókat küldenek az Azure-erőforrásokból a munkaterületre. [Virtuálisgép-elemzések engedélyezése](../vm/vminsights-enable-overview.md) a virtuális gépekről való adatgyűjtéshez. Konfigurálja [az adatforrásokat a munkaterületen](../agents/data-sources.md) további események és teljesítményadatok gyűjtéséhez.

- A [What is monitored by Azure Monitor?](../monitor-reference.md) (Mit figyel meg a Azure Monitor? az adatforrások teljes listáját tartalmazza, amelyek konfigurálhatóak arra, hogy adatokat küldjenek a Azure Monitor Naplókba.


## <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek
A naplók által Azure Monitor adatokat egy vagy több [Log Analytics-munkaterület tárolja.](./design-logs-deployment.md) A munkaterület határozza meg az adatok földrajzi helyét, a hozzáférési jogosultságokat, amelyek meghatározzák, hogy mely felhasználók férhetnek hozzá az adatokhoz, valamint konfigurációs beállításokat, például a tarifacsomagot és az adatmegőrzést.  

Legalább egy munkaterületet létre kell hoznia a Azure Monitor való használathoz. Egyetlen munkaterület elegendő lehet az összes monitorozási adathoz, vagy dönthet úgy, hogy több munkaterületet hoz létre a követelményektől függően. Előfordulhat például, hogy egy munkaterülettel dolgozik az éles adatokhoz, és egy másikkal a teszteléshez. 

- Új [munkaterület létrehozásához lásd: Log Analytics-munkaterület Azure Portal](./quick-create-workspace.md) létrehozása a munkaterületen.
- A [több munkaterület létrehozásával kapcsolatos szempontokról Azure Monitor Megtervezése](design-logs-deployment.md) a naplók üzembe helyezését.

## <a name="data-structure"></a>Adatszerkezet
A naplólekérdezések adatokat szereznek be egy Log Analytics-munkaterületről. Minden munkaterület több táblát tartalmaz, amelyek több adatsort tartalmazó külön oszlopokba vannak rendezve. Minden táblát az adatforrás által biztosított adatsorok által megosztott oszlopok egyedi készlete határoz meg. 

[![Azure Monitor naplók struktúrája](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


A naplóadatok Application Insights is a Azure Monitor naplókban vannak tárolva, de az alkalmazás konfigurálásától függően eltérőek. Munkaterület-alapú alkalmazások esetén az adatok egy Log Analytics-munkaterületen vannak tárolva szabványos táblákban, amelyek olyan adatokat tárolnak, mint az alkalmazáskérések, kivételek és lapnézetek. Több alkalmazás is használhatja ugyanazt a munkaterületet. Klasszikus alkalmazásoknál az adatok nem Log Analytics-munkaterületen tárolódnak. Ugyanazt a lekérdezési nyelvet használja, és a lekérdezéseket ugyanazokkal a Log Analytics-eszközzel hozhatja létre és futtathatja a Azure Portal. A klasszikus alkalmazások adatai azonban egymástól elkülönítve vannak tárolva. Az általános struktúrája megegyezik a munkaterület-alapú alkalmazásokkal, bár a tábla- és oszlopnevek eltérnek. A [munkaterület-alapú és](../app/apm-tables.md) a klasszikus alkalmazások sémájának részletes összehasonlításához tekintse meg a munkaterület-alapú erőforrás-módosításokat.


> [!NOTE]
> Továbbra is teljes körű visszamenőleges kompatibilitást biztosítunk Application Insights klasszikus erőforrás-lekérdezésekkel, munkafüzetekkel és naplóalapú riasztásokkal a Application Insights felhasználói élményben. Az új munkaterület-alapú táblastruktúra/séma [lekérdezéséhez/megtekintéséhez](../app/apm-tables.md) először a Log Analytics-munkaterületre kell navigálnia. Az előzetes verzióban a **Panelek** panelen Application Insights Naplók lehetőséget választva hozzáférhet a klasszikus Application Insights lekérdezési élményhez. További [részletekért lásd:](./scope.md) Lekérdezési hatókör.


[![Azure Monitor naplók szerkezete a Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Naplólekérdezések
Az adatok egy Log Analytics-munkaterületről kérhetők le egy naplólekérdezés használatával, amely egy csak olvasható kérés az adatok feldolgozásához és az eredmények visszaadása érdekében. A naplólekérdezések [a Kusto lekérdezési nyelven (KQL)](/azure/data-explorer/kusto/query/)vannak megírva, amely megegyezik a lekérdezési Azure Data Explorer. Naplólekérdezéseket írhat a Log Analyticsben az eredményeik interaktív elemzéséhez, a riasztási szabályokban való használatukhoz, hogy proaktívan értesítsen a problémákról, vagy hogy az eredményeket munkafüzetekbe vagy irányítópultokba foglalja. Az elemzések előre összeállított lekérdezéseket tartalmaznak a nézeteik és munkafüzeteik támogatásához.

- A [naplólekérdezések](./log-query-overview.md) Azure Monitor a naplólekérdezések helyének listájáért, valamint az oktatóanyagokra és egyéb dokumentációkra mutató hivatkozásokat az első lépésekhez.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
A naplólekérdezések szerkesztéséhez és futtatásához, valamint az eredményeik interaktív elemzéséhez használja a Log Analytics eszközt, amely a Azure Portal eszköz. Ezt követően a létrehozott lekérdezésekkel más funkciókat is támogathat a Azure Monitor, például naplólekérdezés-riasztásokat és munkafüzeteket. A Log Analyticset a **naplók menü** Naplók Azure Monitor vagy a naplókban található legtöbb szolgáltatásból Azure Portal.

- A Log Analytics leírását lásd: Overview [of Log Analytics in Azure Monitor (A](./log-analytics-overview.md) Log Analytics áttekintése) Azure Monitor a Log Analyticsről. 
- Az [egyszerű naplólekérdezés](./log-analytics-tutorial.md) létrehozásához és az eredmények elemzéséhez tekintse meg a Log Analytics-oktatóanyagot, amely végigveszi a Log Analytics funkcióinak használatán.



## <a name="relationship-to-azure-data-explorer"></a>Kapcsolat Azure Data Explorer
Azure Monitor-naplók a naplók Azure Data Explorer. A Log Analytics-munkaterület nagyjából megegyezik egy adatbázissal a Azure Data Explorer, a táblák strukturálva vannak, és mindkettő ugyanazt a Kusto lekérdezési nyelvet (KQL) használja. A Log Analytics használata a Azure Monitor lekérdezésekkel való Azure Portal hasonló a webes felhasználói felület Azure Data Explorer élményéhez. Egy Log [Analytics-munkaterületről](/azure/data-explorer/query-monitor-data)származó adatokat is bele lehet foglalni egy Azure Data Explorer lekérdezésbe. 


## <a name="next-steps"></a>Következő lépések

- Megismerheti [a Log Analytics-munkaterületről](./log-query-overview.md) adatokat lekért és elemező naplólekérdezéseket.
- Ismerje meg [a metrikákat a Azure Monitor.](../essentials/data-platform-metrics.md)
- Ismerje meg a [különböző Azure-erőforrásokhoz](../agents/data-sources.md) elérhető monitorozási adatokat.