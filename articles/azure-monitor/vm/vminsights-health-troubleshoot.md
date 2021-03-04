---
title: A virtuális gépekkel kapcsolatban észlelt vendég állapotának hibáinak megoldása (előzetes verzió)
description: Ismerteti azokat a hibaelhárítási lépéseket, amelyek a virtuális gépek elemzésekor felmerülő problémák esetén is elvégezhető.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051939"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>A virtuális gépekkel kapcsolatban észlelt vendég állapotának hibáinak megoldása (előzetes verzió)
Ez a cikk azokat a hibaelhárítási lépéseket ismerteti, amelyekkel a virtuális gépek elemzésekor felmerülő problémák merülhetnek fel.

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

## <a name="next-steps"></a>Következő lépések

- [A VM-információk vendég állapotának áttekintése](vminsights-health-overview.md)