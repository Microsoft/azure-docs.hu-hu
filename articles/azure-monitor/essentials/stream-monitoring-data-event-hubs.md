---
title: Azure monitorozási adatok streamelése az eseményközpontba és külső partnereknek
description: Megtudhatja, hogyan streamelhet Azure-beli monitorozási adatokat egy eseményközpontba, hogy az adatokat egy partner SIEM- vagy elemzési eszközbe juttathatja.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768740"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Azure monitorozási adatok streamelése egy eseményközpontba vagy külső partnerbe

Azure Monitor teljes körű monitorozási megoldást kínál az Azure-ban, más felhőkben és a helyszínen található alkalmazásokhoz és szolgáltatásokhoz. Amellett, hogy Azure Monitor adatok elemzésére és különböző figyelési forgatókönyvekhez való használatra is használható, előfordulhat, hogy el kell küldenie az adatokat a környezet más monitorozási eszközeinek. A monitorozási adatok külső eszközökre streamelése a leghatékonyabb módszer a [Azure Event Hubs.](../../event-hubs/index.yml) Ez a cikk röviden bemutatja ennek a lépését, majd felsorol néhány partnert, akik adatokat küldhetnek. Némelyik speciális integrációval Azure Monitor, és az Azure-ban üzemelhetnek.  

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Mielőtt bármilyen adatforráshoz konfigurálja a streamelést, létre kell hoznia egy Event Hubs [és eseményközpontot.](../../event-hubs/event-hubs-create.md) Ez a névtér és eseményközpont az összes figyelési adat célja. A Event Hubs névterek olyan eseményközpontok logikai csoportosításai, amelyek ugyanazt a hozzáférési szabályzatot osztják meg, ugyanúgy, mintha egy tárfiók külön blobokkal rendelkezik a tárfiókon belül. Vegye figyelembe a monitorozási adatok streamelése során használt Event Hubs-névtér és eseményközpontok alábbi adatait:

* Az átviteli egységek száma lehetővé teszi az eseményközpontok átviteli sebességének skálázását. Általában csak egy átviteli egységre van szükség. Ha a naplóhasználat növekedésével fel kell skáláznia, manuálisan növelheti a névtér átviteli egységeinek számát, vagy engedélyezheti az automatikus memóriahasználatot.
* A partíciók száma lehetővé teszi a használat párhuzamosizálását számos fogyasztó között. Egy partíció legfeljebb 20 MB/s vagy körülbelül 20 000 üzenetet támogat másodpercenként. Az adatokat felhasználó eszköztől függően előfordulhat, hogy nem támogatja több partícióból történő használatot. Ha nem biztos a beállítható partíciók számában, akkor ésszerű a négy partícióval kezdeni.
* Az üzenetmegőrzést legalább 7 napra kell állítania az eseményközpontban. Ha a fogyasztó eszköz egy napnál tovább leáll, ez biztosítja, hogy az eszköz ott haladjon tovább, ahol 7 napos események esetén leállt.
* Az eseményközponthoz használja az alapértelmezett fogyasztói csoportot. Nincs szükség más fogyasztói csoportok létrehozására vagy különálló fogyasztói csoport használatára, kivéve, ha azt tervezi, hogy két különböző eszköz ugyanazt az adatot használja fel ugyanazon eseményközpontból.
* Az Azure-tevékenységnaplóhoz válasszon egy Event Hubs-névteret, és Azure Monitor létrehoz egy _insights-logs-operational-logs_ nevű eseményközpontot a névtérben. Más naplótípusok esetén választhat egy meglévő eseményközpontot, vagy Azure Monitor eseményközpontot naplókategóriánként.
* Az 5671-es és az 5672-es kimenő portot általában az eseményközpontból adatokat fogyasztó számítógépen vagy virtuális hálózaton kell megnyitni.

## <a name="monitoring-data-available"></a>Rendelkezésre álló monitorozási adatok
[A monitorozási adatok forrásai Azure Monitor](../agents/data-sources.md) az Azure-alkalmazások különböző adatszintit és az egyes alkalmazásokhoz elérhető monitorozási adatok fajtáit ismertetik. Az alábbi táblázat felsorolja ezeket a szinteket, valamint az adatok eseményközpontba való streamelésének leírását. További részletekért kövesse a megadott hivatkozásokat.

| Szint | Adatok | Metódus |
|:---|:---|:---|
| [Azure-bérlő](../agents/data-sources.md#azure-tenant) | Azure Active Directory auditnaplók | Konfigurálja a bérlő diagnosztikai beállítását az AAD-bérlőn. További [részletek: Oktatóanyag: Azure Active Directory streamelése egy Azure-eseményközpontba.](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| [Azure-előfizetés](../agents/data-sources.md#azure-subscription) | Azure-tevékenységnapló | Hozzon létre egy naplóprofilt a Tevékenységnapló eseményeinek exportálására Event Hubs.  Részletekért [lásd: Stream Azure platformnaplók Azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs) megtekintéséhez. |
| [Azure-erőforrások](../agents/data-sources.md#azure-resources) | Platformmetrikák<br> Erőforrásnaplók |A rendszer mindkét adattípust egy erőforrás-diagnosztikai beállítással küldi el az eseményközpontba. További [információ: Azure-erőforrásnaplók streamelése egy eseményközpontba.](../essentials/resource-logs.md#send-to-azure-event-hubs) |
| [Operációs rendszer (vendég)](../agents/data-sources.md#operating-system-guest) | Azure-beli virtuális gépek | Telepítse a [Azure Diagnostics bővítményt](../agents/diagnostics-extension-overview.md) Windows és Linux rendszerű virtuális gépeken az Azure-ban. A [Windows rendszerű virtuális gépekkel kapcsolatos](../agents/diagnostics-extension-stream-event-hubs.md) részletekért lásd: Streaming Azure Diagnostics data in the hot path by using Event Hubs (Adatok streamelése a gyors elérési úton a Event Hubs használatával) és Use [Linux Diagnostic Extension to monitor metrics and](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) logs for details on Linux VMs (A Linux diagnosztikai bővítmény használata a metrikák és naplók figyelése a Linux rendszerű virtuális gépek részleteinek megtekintéséhez). |
| [Alkalmazáskód](../agents/data-sources.md#application-code) | Application Insights | Application Insights nem biztosít közvetlen metódust az adatok eseményközpontba való streameléhez. Beállíthatja [](../app/export-telemetry.md) a Application Insights-adatok folyamatos exportálását egy tárfiókba, majd egy logikai alkalmazással elküldheti az adatokat egy eseményközpontba a Manual streaming with Logic App (Manuális streamelés a [Logic App használatával) leírás szerint.](#manual-streaming-with-logic-app) |

## <a name="manual-streaming-with-logic-app"></a>Manuális streamelés a Logic App használatával
Olyan adatok esetén, amelyek nem streamelhetőek közvetlenül egy eseményközpontba, írhat az Azure [](../../connectors/connectors-create-api-azureblobstorage.md#add-action) Storage-ba, majd használhat egy idő által aktivált logikai alkalmazást, amely lehívja az adatokat a blobtárolóból, és üzenetként leküldi őket az [eseményközpontba.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnereszközök Azure Monitor integrációval

A monitorozási adatok egy eseményközpontba való Azure Monitor lehetővé teszi a külső SIEM- és monitorozási eszközökkel való egyszerű integrációt. Példák az integrációs Azure Monitor eszközökre:

| Eszköz | Az Azure-ban üzemeltetve | Description |
|:---|:---| :---|
|  IBM QRadar | No | A Microsoft Azure DSM és Microsoft Azure Event Hub Protocol az IBM támogatási webhelyéről [tölthető le.](https://www.ibm.com/support) Az Azure-integrációról a [QRadar DSM](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar)konfigurációjában olvashat bővebben. |
| Splunk | No | [Microsoft Azure Add-On Splunk egy](https://splunkbase.splunk.com/app/3757/) nyílt forráskódú projekt, amely a Splunkbase-ben érhető el. <br><br> Ha nem tud bővítményt telepíteni a Splunk-példányban, ha például proxyt használ, vagy a Splunk Cloudban fut, ezeket az eseményeket továbbíthatja a Splunk HTTP-eseménygyűjtőbe az [Azure Function for Splunk használatával,](https://github.com/Microsoft/AzureFunctionforSplunkVS)amelyet az eseményközpont új üzenetei aktiválnak. |
| SumoLogic | No | A SumoLogic eseményközpontból származó adatokkal való felhasználásra való beállítására vonatkozó utasítások a Naplók gyűjtése az Azure-naplózási alkalmazáshoz [az event hubról.](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) |
| ArcSight | No | Az ArcSight Azure Event Hub intelligens összekötője az ArcSight intelligens összekötők [gyűjteményének részeként érhető el.](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) |
| Syslog-kiszolgáló | No | Ha az adatokat közvetlenül Azure Monitor egy syslog-kiszolgálóra szeretné streamelni, használhat egy [Azure-függvényen alapuló megoldást.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm | No| A LogRhythm eseményközpontból való gyűjtésére való beállítására vonatkozó utasítások itt [érhetők el.](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/) 
|Logz.io | Yes | További információ: Ismerkedés a monitorozással és [naplózással Logz.io Azure-on futó Java-alkalmazásokhoz](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Más partnerek is elérhetők lehetnek. Az összes partner és azok képességeinek teljes Azure Monitor a [partnerintegrációkat Azure Monitor meg.](../partners.md)

## <a name="next-steps"></a>Következő lépések
* [A tevékenységnapló archiválása egy tárfiókba](./activity-log.md#legacy-collection-methods)
* [Az Azure-tevékenységnapló áttekintése](../essentials/platform-logs-overview.md)
* [Tevékenységnapló-eseményen alapuló riasztás beállítása](../alerts/alerts-log-webhook.md)
