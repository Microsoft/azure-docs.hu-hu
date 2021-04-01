---
title: Azure Automation Update Management engedélyezése az Automation-fiókból
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management Automation-fiókból.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089d5d70d8ad8060455e5c1bee45e0bee4a12fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575834"
---
# <a name="enable-update-management-from-an-automation-account"></a>Az Update Management engedélyezése Automation-fiókból

Ez a cikk azt ismerteti, hogyan használhatja az Automation-fiókját a környezetben található virtuális gépek [Update Management](overview.md) funkciójának engedélyezésére, beleértve az [Azure arc-kompatibilis kiszolgálókon](../../azure-arc/servers/overview.md)regisztrált gépeket és kiszolgálókat is. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő Azure-beli virtuális gépet.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* Az arc-kompatibilis kiszolgálókon regisztrált Azure-beli [virtuális gépek](../../virtual-machines/windows/quick-create-portal.md)vagy virtuális gépek vagy kiszolgálók. A nem Azure-beli virtuális gépeknek vagy kiszolgálóknak telepítve kell lennie a Windows vagy Linux rendszerhez készült [log Analytics ügynöknek](../../azure-monitor/agents/log-analytics-agent.md) , és az Automation-fiókhoz társított munkaterületre kell jelentenie Update Management engedélyezve van a rendszerben. Javasoljuk, hogy a Windows vagy Linux rendszerhez készült Log Analytics Agent telepítéséhez először csatlakoztassa a gépet az [Azure arc-kompatibilis kiszolgálókhoz](../../azure-arc/servers/overview.md), majd a Azure Policy használatával rendelje hozzá a [log Analytics-ügynök üzembe helyezését a *Linux* vagy a *Windows* Azure arc Machines](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Ha a gépeket Azure Monitor for VMs használatával kívánja figyelni, Ehelyett használja a [Azure monitor for VMS engedélyezése](../../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. Az Automation-fiókban válassza **a frissítés kezelése** lehetőséget az **Update Management** alatt.

2. Válassza ki az Log Analytics munkaterületet és az Automation-fiókot, és válassza az **Engedélyezés** lehetőséget Update Management engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

    ![Az Update Management engedélyezése](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

1. Az Automation-fiókban válassza **az Update Management alatt az** **Update Management** elemet.

2. Válassza az **+ Azure-beli virtuális gépek hozzáadása** lehetőséget, és válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől.

3. Válassza az **Engedélyezés** lehetőséget, ha a kiválasztott virtuális gépeket hozzá szeretné adni a számítógépcsoport mentett kereséshez a szolgáltatáshoz.

    ![Azure-beli virtuális gépek engedélyezése](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Nem Azure-beli virtuális gépek engedélyezése

Az Azure-on kívül üzemeltetett gépek vagy kiszolgálók esetében, beleértve az Azure arc-kompatibilis kiszolgálókon regisztráltkat is, a következő lépésekkel engedélyezheti azokat a Update Management.  

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management** alatt.

2. Válassza a **nem Azure-beli gép hozzáadása** lehetőséget. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Windows log Analytics ügynökének telepítéséhez és konfigurálásához](../../azure-monitor/agents/log-analytics-agent.md) , hogy a gép megkezdhesse a jelentéskészítést Update Management. Ha olyan gépet engedélyez, amelyet jelenleg a Operations Manager felügyel, nincs szükség új ügynökre. A munkaterület adatai hozzáadódnak az ügynökök konfigurációjához.

## <a name="enable-machines-in-the-workspace"></a>Gépek engedélyezése a munkaterületen

A munkaterülethez már jelentést küldő gépeket vagy gépeket manuálisan kell hozzáadni a Azure Automationhoz a Update Management engedélyezéséhez.

1. Az Automation-fiókban válassza **a frissítés kezelése lehetőséget az** **Update Management** alatt.

2. Válassza a **számítógépek kezelése** lehetőséget. Előfordulhat, hogy a **számítógépek kezelése** gomb szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**

    ![Mentett keresések](media/enable-from-automation-account/managemachines.png)

3. A munkaterületre jelentett összes elérhető gép Update Management engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes elérhető gépen** a gépek kezelése lapon. Ez a művelet letiltja a vezérlőt, hogy egyenként vegyen fel gépeket, és hozzáadja a munkaterületnek jelentő összes gépet a számítógépcsoport mentett keresési lekérdezéséhez `MicrosoftDefaultComputerGroup` . Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** lehetőséget.

4. Az összes rendelkezésre álló és jövőbeli gép funkciójának engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a beállítás törli a mentett keresés és a hatókör konfigurációját a munkaterületről, és lehetővé teszi, hogy a szolgáltatás a jelenleg vagy a jövőben is tartalmazza az összes Azure-és nem Azure-beli gépet, amely a munkaterületre vonatkozik. Ha ez a beállítás be van jelölve, a művelet véglegesen letiltja a **gépek kezelése** lehetőséget, mivel nincs elérhető hatókör-konfiguráció.

    > [!NOTE]
    > Mivel ez a lehetőség törli a mentett keresés és a hatókör konfigurációját a Log Analyticson belül, fontos, hogy a beállítás kiválasztása előtt távolítsa el az összes törlési zárolást a Log Analytics munkaterületen. Ha nem, akkor a beállítás nem távolítja el a konfigurációkat, ezért azokat manuálisan kell eltávolítania.

5. Szükség esetén visszaállíthatja a hatókör-konfigurációt a kezdeti mentett keresési lekérdezés újbóli hozzáadásával. További információ: a [Update Management központi telepítési hatókörének korlátozása](scope-configuration.md).

6. Egy vagy több gép funkciójának engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd az egyes gépek melletti **Hozzáadás** lehetőséget. Ez a feladat hozzáadja a kiválasztott számítógépneveket a szolgáltatáshoz tartozó számítógépcsoport mentett keresési lekérdezéséhez.

## <a name="next-steps"></a>Következő lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](manage-updates-for-vm.md).

* Ha már nincs szükség a virtuális gépek vagy kiszolgálók Update Management használatával történő felügyeletére, tekintse meg a [virtuális gépek eltávolítása a Update Managementról](remove-vms.md)című témakört.

* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
