---
title: A Windows rendszerű virtuális asztal előzetes verziójának figyelése – Azure
description: A Windows rendszerű virtuális asztalok Azure Monitorével kapcsolatos hibák elhárítása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c335c1cf7e5319b812345714dbdc6b87ddc4e81b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709172"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>A Windows rendszerű virtuális asztali számítógép (előzetes verzió) Azure Monitor hibáinak megoldása

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali Azure Monitor jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Windows rendszerű virtuális asztalok (előzetes verzió) Azure Monitor gyakori problémáinak ismert problémáit és megoldásait ismerteti.

## <a name="issues-with-configuration-and-setup"></a>A konfigurációval és a beállítással kapcsolatos problémák

Ha a konfigurációs munkafüzet nem működik megfelelően a telepítő automatizálásához, ezeket az erőforrásokat használhatja a környezet manuális beállításához:

- A diagnosztika manuális engedélyezéséhez vagy a Log Analytics munkaterület eléréséhez lásd: [a Windows rendszerű virtuális asztali diagnosztika küldése log Analytics](diagnostics-log-analytics.md).
- Ha manuálisan szeretné telepíteni a Log Analytics-bővítményt egy gazdagépre, tekintse meg [log Analytics virtuálisgép-bővítményt a Windowshoz](../virtual-machines/extensions/oms-windows.md).
- Új Log Analytics munkaterület beállításához tekintse meg a [log Analytics munkaterület létrehozása a Azure Portalben](../azure-monitor/logs/quick-create-workspace.md)című témakört.
- Teljesítményszámlálók hozzáadásához vagy eltávolításához tekintse meg a [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md)című témakört.
- Log Analytics munkaterület eseményeinek konfigurálásához lásd: a [Windows Eseménynapló adatforrásainak összegyűjtése log Analytics ügynökkel](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Az adataim nem megfelelően jelennek meg

Ha az adatai nem megfelelően jelennek meg, ellenőrizze a konfigurációt, az engedélyeket, és ellenőrizze, hogy a szükséges IP-címek blokkolva vannak-e. 

- Először is győződjön meg róla, hogy kitöltötte az összes mezőt a konfigurációs munkafüzetben az [Azure monitor használata a Windows rendszerű virtuális asztalhoz](azure-monitor.md)című témakörben leírtak szerint az üzemelő példány figyeléséhez. Ha hiányoznak a számlálók vagy események, a hozzájuk társított adatai nem jelennek meg a Azure Portalban.

- Győződjön meg arról, hogy a hozzáférési engedélyei & a hiányzó engedélyek kéréséhez forduljon az erőforrás-tulajdonoshoz. a Windows rendszerű virtuális asztali számítógépek figyeléséhez a következő engedélyek szükségesek:

    - Olvasási hozzáférés a Windows rendszerű virtuális asztali erőforrásokkal rendelkező Azure-előfizetésekhez
    - Olvasási hozzáférés az előfizetés azon csoportjaihoz, amelyek a Windows rendszerű virtuális asztali munkamenet-gazdagépeket tárolják 
    - Olvasási hozzáférés a Log Analytics munkaterülethez

- Előfordulhat, hogy a kiszolgáló tűzfalán meg kell nyitnia a kimenő portokat, hogy Azure Monitor az adatküldést a portálra, lásd: [kimenő portok](../azure-monitor/app/ip-addresses.md). 

- Nem látja a legutóbbi tevékenységek adatait? Érdemes 15 percet várni, és frissíteni a hírcsatornát. A naplózási adatok feltöltéséhez a Azure Monitor 15 perces késési időszakot biztosít. További információ: [adatfeldolgozási idő naplózása Azure monitorban](../azure-monitor/logs/data-ingestion-time.md).

Ha nem ad meg semmilyen információt, de az adatai még nem jelennek meg megfelelően, előfordulhat, hogy probléma van a lekérdezésben vagy az adatforrásokban. Tekintse át az ismert problémákat és korlátozásokat. 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Szeretném testreszabni a Windows rendszerű virtuális asztali Azure Monitor

A Windows rendszerű virtuális asztali Azure Monitor Azure Monitor munkafüzeteket használ. A munkafüzetek lehetővé teszik a Windows rendszerű virtuális asztali munkafüzet sablonjának másolatának mentését, és a saját testreszabását.

A tervezés szerint az egyéni munkafüzetek sablonjai nem fogják automatikusan alkalmazni a frissítéseket a termékek csoportjából. További információ: a [munkafüzet-alapú információk hibaelhárítása](../azure-monitor/insights/troubleshoot-workbooks.md) és a [munkafüzetek áttekintése](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Nem tudom értelmezni az adatforrást

További információ az adatkifejezésekről: [Azure monitor for Window Virtual Desktop Szószedet](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>A szükséges adatértékek nem érhetők el

Ha több teljesítményszámlálókat vagy eseményt szeretne figyelni, engedélyezheti őket a Log Analytics munkaterületre való küldéshez, és megfigyelheti őket a gazdagép diagnosztika szolgáltatásában: böngésző. 

- Teljesítményszámlálók hozzáadásával kapcsolatban lásd: [teljesítményszámlálók konfigurálása](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Windows-események hozzáadásával kapcsolatban lásd: [Windows-eseménynaplók konfigurálása](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Nem található olyan adatpont, amely segít a probléma diagnosztizálásában? Küldje el nekünk visszajelzését!

- A visszajelzések elküldésével kapcsolatos további információkért lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás a Windows rendszerű virtuális asztalokhoz](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali Windows Virtual Desktop [visszajelzési központ](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) vagy [a UserVoice fórum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)visszajelzéseit is elhagyhatja.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Ezek a problémák és korlátozások jelenleg a következőkkel kapcsolatosak:

- Egyszerre csak egy gazdagépet tud figyelni. 

- Kedvenc beállítások mentéséhez mentenie kell a munkafüzet egyéni sablonját. Az egyéni sablonok nem fogadnak el automatikusan frissítéseket a termék csoportjából.

- Bizonyos hibaüzenetek nem a felhasználóbarát módon jelennek meg, és nem minden hibaüzenetet ismertetnek a dokumentációban.

- A munkamenetek teljes teljesítményszámlálói egy kis számú munkamenetet is tartalmazhatnak, és előfordulhat, hogy a munkamenetek maximális száma túllépi a munkamenetek maximális számát.

- Az elérhető munkamenetek száma nem tükrözi a gazdagép skálázási házirendjeit. 
    
- Ritka esetben a kapcsolat befejezési eseménye hiányzik, és ez hatással lehet bizonyos vizualizációk, például a kapcsolatok időbeli és a felhasználó kapcsolati állapotára.  
    
- A konfigurációs munkafüzet csak az adott régióban található gazdagépek konfigurálását támogatja. 

- A kapcsolódás ideje magában foglalja a felhasználóknak a hitelesítő adatok megadásához szükséges időt. Ez összefügg a gyakorlattal, de bizonyos esetekben hamis csúcsokat is megjeleníthet. 
    

## <a name="next-steps"></a>Következő lépések

Ha nem tudja, hogyan értelmezze az adatait, vagy többet szeretne megtudni a gyakori feltételekről, tekintse meg a [Windows rendszerű virtuális asztali szószedet Azure monitorét](azure-monitor-glossary.md). Ha szeretné megismerni, hogyan kell beállítani és Azure Monitor használni a Windows rendszerű virtuális asztali gépeket, tekintse meg a Windows rendszerű virtuális asztali Azure Monitor használata az üzemelő [példány figyeléséhez](azure-monitor.md)című témakört.