---
title: A Windows rendszerű virtuális asztali számítógépek figyelése – Azure
description: A Windows rendszerű virtuális asztalok Azure Monitorével kapcsolatos hibák elhárítása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ef1a7f92fd31e8ae87a0ead6b5071d8240aa993e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286402"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztalok Azure Monitor hibáinak megoldása

Ez a cikk a Windows rendszerű virtuális asztali Azure Monitor gyakori problémáinak ismert problémáit és megoldásait ismerteti.

## <a name="issues-with-configuration-and-setup"></a>A konfigurációval és a beállítással kapcsolatos problémák

Ha a konfigurációs munkafüzet nem működik megfelelően a telepítő automatizálásához, ezeket az erőforrásokat használhatja a környezet manuális beállításához:

- A diagnosztika manuális engedélyezéséhez vagy a Log Analytics munkaterület eléréséhez lásd: [a Windows rendszerű virtuális asztali diagnosztika küldése log Analytics](diagnostics-log-analytics.md).
- Ha manuálisan szeretné telepíteni a Log Analytics-bővítményt egy munkamenet-állomáson, Log Analytics tekintse meg a [Windows rendszerhez készült virtuálisgép-bővítményt](../virtual-machines/extensions/oms-windows.md).
- Új Log Analytics munkaterület beállításához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/logs/quick-create-workspace.md)című témakört.
- Teljesítményszámlálók hozzáadásához, eltávolításához vagy szerkesztéséhez tekintse meg a teljesítményszámlálók [konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md)című témakört.
- Log Analytics munkaterület Windows-eseménynaplóinak konfigurálásával kapcsolatban lásd: a [Windows Eseménynapló adatforrásainak összegyűjtése log Analytics ügynökkel](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Az adataim nem megfelelően jelennek meg

Ha az adatai nem megfelelően jelennek meg, tekintse meg a következő gyakori megoldásokat:

- Először is győződjön meg arról, hogy helyesen állította be a konfigurációs munkafüzetet, ahogy az a [Windows rendszerű virtuális asztali környezet Azure monitor használata az üzembe helyezés figyeléséhez](azure-monitor.md)című témakörben leírtak szerint. Ha hiányoznak a számlálók vagy események, a hozzájuk társított adatai nem jelennek meg a Azure Portalban.
- Győződjön meg arról, hogy a hozzáférési engedélyei & a hiányzó engedélyek kéréséhez forduljon az erőforrás-tulajdonoshoz. a Windows rendszerű virtuális asztali számítógépek figyeléséhez a következő engedélyek szükségesek:
    - Olvasási hozzáférés a Windows rendszerű virtuális asztali erőforrásokkal rendelkező Azure-előfizetésekhez
    - Olvasási hozzáférés az előfizetés azon csoportjaihoz, amelyek a Windows rendszerű virtuális asztali munkamenet-gazdagépeket tárolják 
    - Olvasási hozzáférés az Ön által használt Log Analytics-munkaterületekhez
- Előfordulhat, hogy a kiszolgáló tűzfalán meg kell nyitnia a kimenő portokat, hogy Azure Monitor és Log Analytics az adatküldés a portálra. Ennek megismeréséhez tekintse meg a következő cikkeket:
      - [Kimenő portok Azure Monitor](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics a tűzfalra vonatkozó követelményeket](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Nem látja a legutóbbi tevékenységek adatait? Érdemes 15 percet várni, és frissíteni a hírcsatornát. A naplózási adatok feltöltéséhez a Azure Monitor 15 perces késési időszakot biztosít. További információ: [adatfeldolgozási idő naplózása Azure monitorban](../azure-monitor/logs/data-ingestion-time.md).

Ha nem ad meg semmilyen információt, de az adatai még nem jelennek meg megfelelően, előfordulhat, hogy probléma van a lekérdezésben vagy az adatforrásokban. Tekintse át [az ismert problémákat és korlátozásokat](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Szeretném testreszabni a Windows rendszerű virtuális asztali Azure Monitor

A Windows rendszerű virtuális asztali Azure Monitor Azure Monitor munkafüzeteket használ. A munkafüzetek lehetővé teszik a Windows rendszerű virtuális asztali munkafüzet sablonjának másolatának mentését, és a saját testreszabását.

A tervezés szerint az egyéni munkafüzetek sablonjai nem fogják automatikusan alkalmazni a frissítéseket a termékek csoportjából. További információ: a [munkafüzet-alapú információk hibaelhárítása](../azure-monitor/insights/troubleshoot-workbooks.md) és a [munkafüzetek áttekintése](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nem tudom értelmezni az adatforrást

További információ az adatkifejezésekről: [Azure monitor for Window Virtual Desktop Szószedet](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>A szükséges adatértékek nem érhetők el

Ha több teljesítményszámlálókat vagy Windows-eseménynaplókat szeretne figyelni, engedélyezheti, hogy diagnosztikai adatokat küldjön a Log Analytics munkaterületre, és figyelje azokat a **gazdagép diagnosztika szolgáltatásában: böngésző**. 

- Teljesítményszámlálók hozzáadásával kapcsolatban lásd: [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Windows-események hozzáadásával kapcsolatban lásd: [Windows-eseménynaplók konfigurálása](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Nem található olyan adatpont, amely segít a probléma diagnosztizálásában? Küldje el nekünk visszajelzését!

- A visszajelzések elküldésével kapcsolatos további információkért lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás a Windows rendszerű virtuális asztalokhoz](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

A következő problémák és korlátozások észlelhetők, és a javításra dolgozunk:

- Egyszerre csak egy gazdagépet tud figyelni. 
- Kedvenc beállítások mentéséhez mentenie kell a munkafüzet egyéni sablonját. Az egyéni sablonok nem fogadnak el automatikusan frissítéseket a termék csoportjából.
- A konfigurációs munkafüzet időnként a "lekérdezés sikertelen" hibákat jeleníti meg a beállítások betöltésekor. Frissítse a lekérdezést, ha szükséges, adja meg újból a kijelölést, és a hibát fel kell oldania. 
- Bizonyos hibaüzenetek nem jelennek meg felhasználóbarát módon, és nem minden hibaüzenetet ismertetnek a dokumentációban.
- A munkamenetek teljes teljesítményszámlálói egy kis számú munkamenetet is tartalmazhatnak, és előfordulhat, hogy a munkamenetek maximális száma túllépi a munkamenetek maximális számát.
- Az elérhető munkamenetek száma nem tükrözi a gazdagép skálázási házirendjeit.   
- Ellentmond vagy váratlan kapcsolatfelvételi időt lát? Ritka esetben a kapcsolat befejezési eseménye hiányzik, és hatással lehet bizonyos vizualizációra és mérőszámokra.
- A kapcsolódás ideje magában foglalja a felhasználóknak a hitelesítő adatok megadásához szükséges időt. Ez összefügg a gyakorlattal, de bizonyos esetekben hamis csúcsokat is megjeleníthet. 
    

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse [meg a Windows rendszerű virtuális asztali Azure monitor használata a telepítés figyeléséhez](azure-monitor.md)című témakört.
- Az adattárolási költségek becsléséhez, méréséhez és kezeléséhez tekintse meg a [becsült Azure monitor költségeket](azure-monitor-costs.md)ismertető témakört.
- Tekintse meg a [szószedetet](azure-monitor-glossary.md) , és ismerkedjen meg a Windows rendszerű virtuális asztalok Azure Monitorával kapcsolatos feltételekkel és fogalmakkal.
