---
title: A virtuális gépekkel kapcsolatban észlelt vendég állapotának hibáinak megoldása (előzetes verzió)
description: Ismerteti azokat a hibaelhárítási lépéseket, amelyek a virtuális gépek elemzésekor felmerülő problémák esetén is elvégezhető.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932777"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>A virtuális gépekkel kapcsolatban észlelt vendég állapotának hibáinak megoldása (előzetes verzió)
Ez a cikk azokat a hibaelhárítási lépéseket ismerteti, amelyekkel a virtuális gépek elemzésekor felmerülő problémák merülhetnek fel.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Az elérhető frissítés üzenet továbbra is megjelenik a vendég állapotának frissítése után 

- Ellenőrizze, hogy a virtuális gép fut-e a globális Azure-ban. Az ív használatára képes kiszolgálók még nem támogatottak.
- Győződjön meg arról, hogy a virtuális gép régiója és az operációs rendszer verziója támogatott a következő témakörben leírtak szerint: [Azure monitor for VMS vendég állapotának engedélyezése (előzetes verzió)](vminsights-health-enable.md).
- Győződjön meg arról, hogy a vendég állapot-bővítmény sikeresen telepítve van 0 kilépési kóddal.
- Győződjön meg arról, hogy Azure Monitor ügynök bővítménye sikeresen telepítve van.
- Ellenőrizze, hogy a rendszerhez rendelt felügyelt identitás engedélyezve van-e a virtuális gépen.
- Győződjön meg arról, hogy nincs megadva felhasználó által hozzárendelt felügyelt identitás a virtuális géphez.
- Ellenőrizze, hogy a területi beállítással rendelkező Windows rendszerű virtuális gépek az *USA angol nyelvűek*-e. Azure Monitor ügynök jelenleg nem támogatja a honosítást.
- Ellenőrizze, hogy a virtuális gép nem a hálózati proxyt használja-e. Azure Monitor ügynök jelenleg nem támogatja a proxykat.
- Győződjön meg arról, hogy az állapotfigyelő ügynök hibák nélkül indult el. Ha az ügynök nem tud elindulni, lehet, hogy az ügynök állapota sérült. Törölje az ügynök állapota mappa tartalmát, majd indítsa újra az ügynököt.
  - Linux esetén: Daemon is *vmGuestHealthAgent*. Az állapot mappa */var/opt/vmGuestHealthAgent/**
  - Windows esetén: a szolgáltatás a *VM Guest Health ügynök*. Az állapot mappa _%ProgramData%\Microsoft\VMGuestHealthAgent \\ *_.
- Ellenőrizze, hogy a Azure Monitor ügynök rendelkezik-e hálózati kapcsolattal. 
  - A virtuális gépről próbálkozzon a ping _<region> . Handler.Control.monitor.Azure.com_. A westeurope-ben lévő virtuális gépek esetében például a _westeurope.Handler.Control.monitor.Azure.com:443_ pingelését kísérli meg.
- Ellenőrizze, hogy a virtuális gép rendelkezik-e olyan adatgyűjtési szabállyal való társítással, amely ugyanabban a régióban található, mint a Log Analytics munkaterületet.
  -  A DCR szerkezetének helyességének biztosítása érdekében tekintse meg az **adatgyűjtési szabály (DCR) létrehozása** a [Azure monitor for VMS vendég állapotának engedélyezése (előzetes verzió)](vminsights-health-enable.md) című témakört. Különös figyelmet kell fordítani a *performanceCounters* adatforrás jelenlétére, amely az állapot-kiterjesztés konfigurációjában a három számlálót és a *inputDataSources* jelenlétét határozza meg, hogy a számlálókat a bővítménynek küldje.
-  Keresse meg a virtuális gépet a vendég állapot-kiterjesztési hibákért.
   -  Linux esetén: keresse meg a _/var/log/Azure/Microsoft.Azure.monitor.VirtualMachines.GuestHealthLinuxAgent/*. log naplófájlt_.
   -  Windows esetén: keresse meg a naplókat a _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.monitor.VirtualMachines.GuestHealthWindowsAgent- \{ bővítmény verziója} \* . log_ címen.
-  Keresse meg a virtuális gépet Azure Monitor-ügynök hibáihoz.
   -  Linux esetén: Jelentkezzen be a _/var/log/MDSD. *_ címen.
   -  Windows esetén: a (z _) \* {vmName} C:\WindowsAzure\Resources lévő naplók megtekintése. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Hibaüzenet, amely nem érhető el. 

![Nincsenek adatkészletek](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Annak ellenőrzése, hogy a virtuális gép megfelel-e a konfigurációs követelményeknek

1. Ellenőrizze, hogy a virtuális gép Azure-beli virtuális gép-e. A kiszolgálói Azure Arc jelenleg nem támogatott.
2. Ellenőrizze, hogy a virtuális gép [támogatott operációs rendszert futtat-e](vminsights-health-enable.md?current-limitations.md).
3. Ellenőrizze, hogy a virtuális gép [támogatott régióban](vminsights-health-enable.md?current-limitations.md) lett-e telepítve.
4. Ellenőrizze, hogy a Log Analytics-munkaterület [támogatott régióban](vminsights-health-enable.md?current-limitations.md) lett-e telepítve.

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Ellenőrizze, hogy a virtuális gép megfelelően be van-e állítva
Ellenőrizze, hogy a Azure Monitor ügynök bővítménye és a vendég virtuális gép állapota ügynök sikeresen kiépítve lett-e a virtuális gépen. Válassza a **bővítmények** lehetőséget a Azure Portal virtuális gép menüjében, és győződjön meg arról, hogy a két ügynök szerepel a listában.

![Virtuálisgép-bővítmények](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Ellenőrizze, hogy engedélyezve van-e a rendszerhez rendelt identitás a virtuális gépen
Ellenőrizze, hogy a rendszerhez rendelt identitás engedélyezve van-e a virtuális gépen. Válassza az **identitás** lehetőséget a virtuális gép menüjében a Azure Portal. Ha a felhasználó által felügyelt identitás engedélyezve van, a rendszer által felügyelt identitás állapotától függetlenül Azure Monitor ügynök nem fog tudni kommunikálni a konfigurációs szolgáltatással, és a vendég állapotának kiterjesztése nem fog működni.

![Rendszerhez rendelt identitás](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Adatgyűjtés szabályának ellenőrzése
Ellenőrizze, hogy a virtuális géphez társítva van-e az adatforrásként megadott adatgyűjtési szabály az állapot kiterjesztésével.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Nem megfelelő engedélyek miatti hibás kérelem hibaüzenete
Ez a hiba azt jelzi, hogy a **Microsoft. WorkloadMonitor** erőforrás-szolgáltató nincs regisztrálva az előfizetésben. Az erőforrás-szolgáltató regisztrálásával kapcsolatos részletekért tekintse meg az [Azure erőforrás-szolgáltatókat és-típusokat](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Hibás kérelem](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Az állapot az "ismeretlen" érték jelenik meg a vendég állapotának engedélyezése után.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Ellenőrizze, hogy a Windows-csomópontok teljesítményszámlálók megfelelően működnek-e 
A vendég állapota attól függ, hogy az ügynök képes-e teljesítményszámlálók gyűjtésére a csomópontból. Előfordulhat, hogy a teljesítményszámláló-kódtárak alapkészlete sérült lesz, és újra kell építeni. A teljesítményszámlálók újraépítéséhez kövesse az utasításokat a [teljesítményszámláló-függvénytár értékeinek manuális](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) újraépítéséhez.





## <a name="next-steps"></a>Következő lépések

- [A VM-információk vendég állapotának áttekintése](vminsights-health-overview.md)