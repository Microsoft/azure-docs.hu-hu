---
title: A Azure Automation Change Tracking és a leltárral kapcsolatos problémák elhárítása
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a problémákat a Azure Automation Change Tracking és a leltár szolgáltatással kapcsolatos problémák megoldásában.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: dd027f94edad580836f0afb8c7293c81ca77605a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723826"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>A Change Tracking és az Inventory hibáinak elhárítása

Ez a cikk a Azure Automation Change Tracking és a leltárral kapcsolatos problémák elhárítását és megoldását ismerteti. A Change Tracking és a leltárral kapcsolatos általános információkért lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Forgatókönyv: a gép már regisztrálva van egy másik fiókban

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ok

A gép már telepítve van a Change Tracking egy másik munkaterületére.

### <a name="resolution"></a>Feloldás

1. Győződjön meg arról, hogy a számítógép a megfelelő munkaterületre küld jelentést. Ennek ellenőrzésével kapcsolatos útmutatásért lásd: az [ügynök kapcsolatának ellenőrzése Azure monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Győződjön meg arról is, hogy ez a munkaterület a Azure Automation-fiókjához van csatolva. A megerősítéshez nyissa meg az Automation-fiókját, és a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

1. Győződjön meg arról, hogy a gépek megjelennek az Automation-fiókhoz társított Log Analytics munkaterületen. Futtassa a következő lekérdezést a Log Analytics munkaterületen.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Ha nem látja a gépet a lekérdezés eredményei között, azt nem ellenőrizte a közelmúltban. Valószínűleg van egy helyi konfigurációs probléma. Telepítse újra a Log Analytics-ügynököt.

   Ha a számítógép a lekérdezés eredményei között szerepel, ellenőrizze a **változáskövetési** listáján szereplő Solutions (megoldások) tulajdonságot. Ez ellenőrzi, hogy regisztrálva van-e Change Tracking és leltárban. Ha nem, ellenőrizze a hatókör-konfigurációs problémákat. A hatókör-konfiguráció határozza meg, hogy mely gépek vannak konfigurálva a Change Trackinghoz és a leltárhoz. A célszámítógép hatókör-konfigurációjának konfigurálásához tekintse meg az [Automation-fiók Change Tracking és leltározásának engedélyezése](../change-tracking/enable-from-automation-account.md)című témakört.

   A munkaterületen futtassa ezt a lekérdezést.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Ha ezt ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` az eredményt kapja, elérte a munkaterületen definiált kvótát, amely leállította az adatok mentését. A munkaterületen lépjen a **használati és becsült költségek** pontra. Válasszon ki egy új **díjszabási szintet** , amely lehetővé teszi, hogy több adatmennyiséget használjon, vagy kattintson a **napi korlát** lehetőségre, és távolítsa el a korlátot.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Használat és becsült költségek." lightbox="./media/change-tracking/change-tracking-usage.png":::

Ha a probléma továbbra is megoldatlan, kövesse a [Windows Hybrid Runbook Worker telepítése](../automation-windows-hrw-install.md) a hibrid feldolgozó Windows rendszerre való újratelepítésének lépéseit. Linux esetén kövesse a  [Linux Hybrid Runbook Worker üzembe helyezése](../automation-linux-hrw-install.md)című témakör lépéseit.

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

Ha nem látja a gépet a lekérdezés eredményei között, a közelmúltban nem volt bejelölve. Valószínűleg van egy helyi konfigurációs probléma, és újra kell telepítenie az ügynököt. További információ a telepítésről és a konfigurálásról: [a naplófájlok adatainak összegyűjtése a log Analytics ügynökkel](../../azure-monitor/agents/log-analytics-agent.md).

Ha a gép megjelenik a lekérdezés eredményei között, ellenőrizze a hatókör-konfigurációt. Lásd: [Azure monitor-figyelési megoldások célzása](../../azure-monitor/insights/solution-targeting.md).

A probléma további hibaelhárítását lásd [: probléma: nem jelenik meg Linux-alapú információ](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Linux-ügynök nem megfelelően van konfigurálva Log Analytics

Előfordulhat, hogy a Linux rendszerhez készült Log Analytics-ügynök nem megfelelően van konfigurálva a log és a parancssori kimeneti gyűjteményhez a OMS log Collector eszközzel. Lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy a Azure Security Center FIM-funkciója helytelenül ellenőrzi a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működik-e, és megfelelően van-e konfigurálva a Linux-fájlok figyeléséhez. Lásd: [change Tracking és leltár – áttekintés](../change-tracking/overview.md).

## <a name="next-steps"></a>Következő lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport) a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése** lehetőséget.