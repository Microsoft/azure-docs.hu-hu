---
title: A Azure Automation Change Tracking és a leltárral kapcsolatos problémák elhárítása
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a problémákat a Azure Automation Change Tracking és a leltár szolgáltatással kapcsolatos problémák megoldásában.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/31/2019
ms.topic: troubleshooting
ms.openlocfilehash: 516f1a4e5e7c677b17a2941ee3c300db44d49a3b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896545"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>A Change Tracking és az Inventory hibáinak elhárítása

Ez a cikk a Azure Automation Change Tracking és a leltárral kapcsolatos problémák elhárítását és megoldását ismerteti. A Change Tracking és a leltárral kapcsolatos általános információkért lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Forgatókönyv: a Change Tracking és a leltári rekordok nem láthatók Windows rendszerű gépek esetén

#### <a name="issue"></a>Probléma

A szolgáltatáshoz engedélyezett Windows rendszerű gépekhez nem jelenik meg Change Tracking és leltározási eredmény.

#### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A Windows rendszerhez készült Azure Log Analytics-ügynök nem fut.
* A rendszer blokkolja az Automation-fiókkal való kommunikációt.
* A Change Tracking és a leltár felügyeleti csomagjai nem tölthetők le.
* Előfordulhat, hogy az engedélyezni kívánt virtuális gép olyan klónozott gépről származik, amely nem készült rendszer-előkészítő rendszerrel (Sysprep), és a Windows Log Analytics ügynöke telepítve van.

#### <a name="resolution"></a>Feloldás

A Log Analytics ügynök gépen lépjen a **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** , és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Ha továbbra is segítségre van szüksége, diagnosztikai információkat gyűjthet, és kapcsolatba léphet a támogatási szolgálattal.

> [!NOTE]
> A Log Analytics ügynök alapértelmezés szerint engedélyezi a hibák nyomkövetését. Az előző példában szereplő részletes hibaüzenetek engedélyezéséhez használja a `VER` paramétert. Az információk követéséhez használja a következőt: `INF` `StartTracing.cmd` .

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics ügynök nem fut a Windows rendszerben

Ellenőrizze, hogy a számítógépen fut-e a Windows Log Analytics-ügynök (**HealthService.exe**).

##### <a name="communication-to-automation-account-blocked"></a>Az Automation-fiókkal folytatott kommunikáció blokkolva

Ellenőrizze Eseménynapló a gépen, és keresse meg azokat az eseményeket, amelyekben szó van `changetracking` bennük.

A Change Tracking és a leltár működéséhez engedélyezni kívánt címekkel és portokkal kapcsolatos további tudnivalókért lásd: [hálózati tervezés](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Nem letöltött felügyeleti csomagok

Győződjön meg arról, hogy a következő Change Tracking és leltár-felügyeleti csomagok helyileg vannak telepítve:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>A klónozott gépről nem Sysprep használatával létrehozott virtuális gép

Klónozott rendszerkép használata esetén először a rendszerképet, majd a Windows rendszerhez készült Log Analytics-ügynököt telepítse.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Forgatókönyv: nincs Change Tracking és a leltár eredményei a Linux rendszerű gépeken

#### <a name="issue"></a>Probléma

A szolgáltatáshoz engedélyezett Linux rendszerű gépekhez nem jelennek meg Change Tracking és leltározási eredmények. 

#### <a name="cause"></a>Ok
A probléma lehetséges okai a következők:
* A Linux Log Analytics ügynöke nem fut.
* A Log Analytics-ügynök Linux rendszerhez nincs megfelelően konfigurálva.
* A fájlok integritásának figyelése (FIM) ütközik.

#### <a name="resolution"></a>Feloldás 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics a Linux-ügynök nem fut

Győződjön meg arról, hogy a (z) Linux rendszerhez készült Log Analytics-ügynök (**omsagent**) démona fut a gépen. Futtassa az alábbi lekérdezést a Log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Ha nem látja a gépet a lekérdezés eredményei között, a közelmúltban nem volt bejelölve. Valószínűleg van egy helyi konfigurációs probléma, és újra kell telepítenie az ügynököt. További információ a telepítésről és a konfigurálásról: [a naplófájlok adatainak összegyűjtése a log Analytics ügynökkel](../../azure-monitor/platform/log-analytics-agent.md).

Ha a gép megjelenik a lekérdezés eredményei között, ellenőrizze a hatókör-konfigurációt. Lásd: [Azure monitor-figyelési megoldások célzása](../../azure-monitor/insights/solution-targeting.md).

A probléma további hibaelhárítását lásd [: probléma: nem jelenik meg Linux-alapú információ](../../azure-monitor/platform/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Linux-ügynök nem megfelelően van konfigurálva Log Analytics

Előfordulhat, hogy a Linux rendszerhez készült Log Analytics-ügynök nem megfelelően van konfigurálva a log és a parancssori kimeneti gyűjteményhez a OMS log Collector eszközzel. Lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy a Azure Security Center FIM-funkciója helytelenül ellenőrzi a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működik-e, és megfelelően van-e konfigurálva a Linux-fájlok figyeléséhez. Lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.
