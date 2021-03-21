---
title: Azure Monitor-naplók
description: Ismerteti Azure Monitor naplókat, amelyek a monitorozási adatelemzések speciális elemzéséhez használatosak.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 5066264777c66369205489fb27a6f9206c5da521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707999"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor-naplók – áttekintés
Azure Monitor naplók a Azure Monitor szolgáltatása, amely összegyűjti és rendszerezi a napló-és teljesítményadatokat a [figyelt erőforrásokból](../monitor-reference.md). A különböző forrásokból származó adatok, például az Azure-szolgáltatásokból származó [platformok naplói](../essentials/platform-logs-overview.md) , a [Virtual Machines-ügynökökből](../agents/agents-overview.md)származó naplók és teljesítményadatok, valamint az [alkalmazásokból](../app/app-insights-overview.md) származó használati és teljesítményadatokat összevonható egyetlen munkaterületre, így az elemzésük olyan kifinomult lekérdezési nyelv használatával végezhető el, amely képes több millió rekord elemzésére. Egy egyszerű lekérdezést is végrehajthat, amely csak egy adott rekordhalmazt kér le, vagy kifinomult adatelemzést végez a megfigyelési adatokban található kritikus minták azonosításához. A naplózási lekérdezésekkel és azok eredményeivel interaktív módon, Log Analytics használatával dolgozhat, a riasztási szabályok segítségével proaktívan értesítheti a problémákat, vagy megjelenítheti az eredményeket egy munkafüzetben vagy irányítópulton.

> [!NOTE]
> Azure Monitor naplók a Azure Monitor támogató adatplatform egyik fele. A másik [Azure monitor metrika](../essentials/data-platform-metrics.md) , amely egy idősorozat-adatbázisban tárolja a numerikus adatokat. Így az adatok sokkal könnyebbek, mint Azure Monitor naplókban tárolt adatok, és képesek a közel valós idejű forgatókönyvek támogatására, ami különösen hasznos lehet a riasztások és a problémák gyors észlelése érdekében. A metrikák azonban csak egy adott struktúrában tárolhatják a numerikus adatokat, míg a naplók számos különböző adattípust tárolhatnak a saját struktúrájuk használatával. A naplók adatain összetett elemzéseket is végrehajthat, ha olyan naplózási lekérdezéseket használ, amelyek nem használhatók a metrikák adatok elemzéséhez.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Mire használhatók Azure Monitor naplók?
A következő táblázat ismerteti az Azure Monitorban található naplók különböző módszereit:

|  |  |
|:---|:---|
| **Elemzés** | A Azure Portal [log Analytics](./log-analytics-tutorial.md) használata a [naplók](./log-query-overview.md) írásához és interaktív módon történő elemzéséhez egy hatékony elemzési motor használatával |
| **Riasztás** | Olyan [naplózási riasztási szabályt](../alerts/alerts-log.md) konfigurálhat, amely értesítést küld, vagy [automatikus műveletet hajt végre](../alerts/action-groups.md) , ha a lekérdezés eredményei egy adott eredménynek felelnek meg. |
| **Vizualizáció** | A lekérdezés eredményei táblázatként vagy diagramként jelennek meg egy [Azure-irányítópulton](../../azure-portal/azure-portal-dashboards.md).<br>Hozzon létre egy [munkafüzetet](../visualize/workbooks-overview.md) , amely kombinálható több adatkészlettel egy interaktív jelentésben. <br>A lekérdezés eredményeinek exportálásával [Power BIhatja](../visualize/powerbi.md) a különböző vizualizációk használatát, és megoszthatja azokat az Azure-on kívüli felhasználókkal.<br>Egy lekérdezés eredményeinek exportálása a [Grafana](../visualize/grafana-plugin.md) , hogy kihasználja az irányítópultot, és kombinálja más adatforrásokkal.|
| **Insights** | Az [egyes](../monitor-reference.md#insights-and-core-solutions) alkalmazásokhoz és szolgáltatásokhoz testreszabott figyelési élményt nyújtó adatvizsgálatok támogatása.  |
| **Beolvasni** | A naplók lekérdezési eredményeinek elérése egy parancssorból az [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)használatával.<br>A log lekérdezés eredményeinek elérése a parancssorból [PowerShell-parancsmagok](/powershell/module/az.operationalinsights)használatával.<br>Hozzáférés a naplók lekérdezési eredményeihez egy egyéni alkalmazásból [REST API](https://dev.loganalytics.io/)használatával. |
| **Exportálás** | [A naplófájlok automatikus exportálásának](./logs-data-export.md) konfigurálása az Azure Storage-fiókba vagy az azure-Event Hubsba.<br>Hozzon létre egy munkafolyamatot a naplófájlok beolvasásához, és másolja azt egy külső helyre [Logic apps](./logicapp-flow-connector.md)használatával. |

![Naplók – áttekintés](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Adatgyűjtés
Log Analytics munkaterület létrehozása után különböző forrásokat kell konfigurálnia az adatküldéshez. A rendszer nem gyűjt automatikusan adatokat. Ez a konfiguráció az adatforrás típusától függően eltérő lesz. [Hozzon létre például diagnosztikai beállításokat](../essentials/diagnostic-settings.md) az erőforrás-naplók Azure-erőforrásokból a munkaterületre való küldéséhez. Az adatok virtuális gépekről történő gyűjtésének [engedélyezése a VM](../vm/vminsights-enable-overview.md) -elemzések számára. [A munkaterületen lévő adatforrások](../agents/data-sources.md) konfigurálásával további eseményeket és teljesítményadatokat gyűjthet.

- Nézze meg, [mi figyeli a Azure monitor?](../monitor-reference.md) az adatforrások teljes listáját, amelyeket konfigurálhat az AdatAzure monitor naplókba való adatküldéshez.


## <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek
Azure Monitor naplók által gyűjtött adatokat egy vagy több [log Analytics-munkaterületen](./design-logs-deployment.md)tárolja a rendszer. A munkaterület meghatározza az adatok földrajzi helyét, a hozzáférési jogokat, amelyek meghatározzák, hogy mely felhasználók férhetnek hozzá az adatokhoz, valamint a konfigurációs beállításokat, például a díjszabási szintet és az adatmegőrzést.  

Azure Monitor naplók használatához legalább egy munkaterületet létre kell hoznia. Egy munkaterület elegendő lehet az összes megfigyelési adathoz, vagy dönthet úgy, hogy a követelményektől függően több munkaterületet is létrehoz. Előfordulhat például, hogy rendelkezik egy munkaterülettel a termelési adataihoz, és egy másikat a teszteléshez. 

- Új munkaterület létrehozásához tekintse meg [log Analytics munkaterület létrehozása a Azure Portalben](./quick-create-workspace.md) című témakört.
- A több munkaterület létrehozásával kapcsolatos megfontolásokat lásd: [Azure monitor naplók tervezése](design-logs-deployment.md) .

## <a name="data-structure"></a>Adatszerkezet
A naplózási lekérdezések Log Analytics munkaterületről kérik le az adatait. Az egyes munkaterületek több táblázatot is tartalmaznak, amelyek több sornyi adatsort tartalmazó különálló oszlopba vannak rendezve. Az egyes táblákat az egyes oszlopok egyedi halmaza határozza meg, amelyeket az adatforrás által biztosított adatsorok közösen használnak. 

[![Azure Monitor naplók szerkezete](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


A Application Insightsről származó adatok naplózása is Azure Monitor naplókban történik, de az alkalmazás konfigurációjától függően eltérő módon tárolódik. A munkaterület-alapú alkalmazások esetén a rendszer az adathalmazokat egy szabványos táblázatban tárolja Log Analytics munkaterületen, amely az olyan adatmennyiségek tárolására szolgál, mint az alkalmazások, a kivételek és a lapok nézetei. Több alkalmazás is használhatja ugyanazt a munkaterületet. Klasszikus alkalmazások esetében az adatLog Analytics munkaterület nem tárolja. Ugyanazt a lekérdezési nyelvet használja, és a Azure Portal azonos Log Analytics eszközzel hozza létre és futtatja a lekérdezéseket. A klasszikus alkalmazások adatait azonban külön tárolja a rendszer. Az általános szerkezete megegyezik a munkaterület-alapú alkalmazásokkal, bár a tábla-és oszlopnevek eltérőek. A munkaterület-alapú és a klasszikus alkalmazások sémájának részletes összehasonlítását lásd: [munkaterület-alapú erőforrás-változások](../app/apm-tables.md) .


> [!NOTE]
> Továbbra is visszamenőleges kompatibilitást biztosítunk a Application Insights klasszikus erőforrás-lekérdezésekhez, a munkafüzetekhez és a napló alapú riasztásokhoz a Application Insights felhasználói felületén belül. Az [Új munkaterület-alapú tábla struktúrájának/sémájának](../app/apm-tables.md) lekérdezéséhez vagy megtekintéséhez először navigáljon a log Analytics munkaterületre. Az előzetes verzióban a **naplók** kiválasztása a Application Insights ablaktáblán elérhetővé teszi a klasszikus Application Insights lekérdezési élményt. További részletekért lásd a [lekérdezési hatókört](./scope.md) .


[![Azure Monitor a naplók struktúráját Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Naplólekérdezések
Az adatok beolvasása egy Log Analytics munkaterületről egy olyan naplózási lekérdezéssel, amely írásvédett kérelem az adatok feldolgozására és az eredmények visszaküldésére. A [Kusto lekérdezési nyelve (KQL)](/azure/data-explorer/kusto/query/)a napló lekérdezéseit írja le, amely az Azure adatkezelő által használt lekérdezési nyelv. A Log Analytics naplózhatja az eredmények interaktív elemzését, a riasztási szabályok segítségével pedig proaktívan értesítheti a problémákat, vagy belefoglalhatja az eredményeket munkafüzetekbe vagy irányítópultokra. Az elemzések előre elkészített lekérdezéseket tartalmaznak a nézeteik és a munkafüzetek támogatásához.

- Az első lépésekhez tekintse meg a [lekérdezések naplózása a Azure monitorban](./log-query-overview.md) című témakört, ahol megtalálhatja a naplók lekérdezésének helyét és az oktatóanyagokra és egyéb dokumentációra mutató hivatkozásokat

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Használja a Log Analytics, amely a Azure Portal eszköz, amely a naplók szerkesztésére és futtatására, valamint az eredmények interaktív elemzésére szolgál. Ezután a létrehozott lekérdezéseket használhatja a Azure Monitor egyéb funkcióinak támogatásához, például a log lekérdezési riasztások és a munkafüzetek számára. A Azure Monitor menüben vagy a Azure Portal legtöbb más szolgáltatásában lévő **naplók** lehetőséggel érheti el log Analytics.

- A Log Analytics leírását lásd: a [Azure Monitor log Analytics áttekintése](./log-analytics-overview.md) . 
- Tekintse meg a [log Analytics oktatóanyagot](./log-analytics-tutorial.md) , amely végigvezeti a log Analytics-funkciók használatával egy egyszerű napló-lekérdezés létrehozásán és az eredmények elemzésén.



## <a name="relationship-to-azure-data-explorer"></a>Kapcsolat az Azure Adatkezelő
Azure Monitor naplók az Azure Adatkezelőon alapulnak. Egy Log Analytics munkaterület nagyjából egy Adatkezelő Azure-beli adatbázisnak megfelelő, a táblák szerkezete azonos, és mindkettő ugyanazt a Kusto lekérdezési nyelvet (KQL) használja. Az Log Analytics használata a Azure Portal Azure Monitor lekérdezésekkel való együttműködéshez hasonló az Azure Adatkezelő webes felhasználói felületén keresztüli élményhez. Az [Azure adatkezelő-lekérdezésekben log Analytics munkaterületről is tartalmazhat adatok](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Következő lépések

- Tudnivalók a Log Analytics-munkaterületről származó adatok lekérdezéséhez és elemzéséhez szükséges [naplókról](./log-query-overview.md) .
- [A Azure monitor metrikáinak](../essentials/data-platform-metrics.md)megismerése.
- Ismerje meg az Azure különböző erőforrásaihoz [elérhető figyelési információkat](../agents/data-sources.md) .