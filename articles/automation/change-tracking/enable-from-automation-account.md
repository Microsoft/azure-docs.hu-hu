---
title: Azure Automation Change Tracking és leltár engedélyezése az Automation-fiókból
description: Ez a cikk azt ismerteti, hogyan engedélyezhető az Change Tracking és a leltár egy Automation-fiókból.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 32fb95c88d632cc2c51cd2390f0244e9c1927051
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100585889"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>A Change Tracking és az Inventory engedélyezése Automation-fiókból

Ez a cikk azt ismerteti, hogyan használható az Automation-fiókja a virtuális gépek [change Trackingának és leltározásának](overview.md) engedélyezéséhez a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet.

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. Navigáljon az Automation-fiókjához, és válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció** felügyelete alatt.

2. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** lehetőségre a Change Tracking és a leltár engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

    ![A Change Tracking és az Inventory engedélyezése](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Azure-beli virtuális gépek engedélyezése

1. Az Automation-fiókban válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció** felügyelete alatt.

2. Kattintson az **+ Azure-beli virtuális gépek hozzáadása** lehetőségre, és válasszon ki egy vagy több virtuális gépet a listából. A nem engedélyezhető virtuális gépek szürkén jelennek meg, és nem választhatók ki. Az Azure-beli virtuális gépek bármely régióban létezhetnek, függetlenül az Automation-fiókja helyétől. 

3. Kattintson az **Engedélyezés** lehetőségre a kiválasztott virtuális gépek hozzáadásához a csoport mentett kereséséhez a szolgáltatásban. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](manage-scope-configurations.md).

      [![Azure-beli virtuális gépek engedélyezése](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Nem Azure-beli virtuális gépek engedélyezése

Az Azure-ban nem szereplő gépeket manuálisan kell hozzáadni. Javasoljuk, hogy a Windows vagy Linux rendszerhez készült Log Analytics-ügynök telepítésével először csatlakoztassa a gépet az [Azure arc-kompatibilis kiszolgálókhoz](../../azure-arc/servers/overview.md), majd Azure Policy használatával rendelje hozzá a [log Analytics-ügynököt a *Linux* vagy a *Windows* Azure arc Machines](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Ha azt is tervezi, hogy a gépeket Azure Monitor for VMs használatával figyeli, használja az [Enable Azure monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) Initiative parancsot.

1. Az Automation-fiókban válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció** felügyelete alatt.

2. Kattintson a **nem Azure-beli gép hozzáadása** lehetőségre. Ez a művelet egy új böngészőablakot nyit meg, amely [útmutatást nyújt a Windows log Analytics ügynökének telepítésére és konfigurálására](../../azure-monitor/agents/log-analytics-agent.md) , hogy a gép megkezdhesse a Change Tracking és a leltározási műveletek bejelentését. Ha olyan gépet engedélyez, amelyet jelenleg Operations Manager felügyel, nincs szükség új ügynökre, és a munkaterület adatai bekerülnek a meglévő ügynökbe.

## <a name="enable-machines-in-the-workspace"></a>Gépek engedélyezése a munkaterületen

A manuálisan telepített gépeket vagy számítógépeket, amelyeket a munkaterülethez már jelentett, a Change Tracking és a leltár engedélyezéséhez hozzá kell adni a Azure Automationhoz.

1. Az Automation-fiókban válassza a **leltár** vagy a **change Tracking** elemet a **konfiguráció** felügyelete alatt.

2. Válassza a **számítógépek kezelése** lehetőséget. Előfordulhat, hogy a **gépek kezelése** lehetőség szürkén jelenik meg, ha korábban az Engedélyezés lehetőséget választotta az **összes rendelkezésre álló és jövőbeli gépen**

    ![Mentett keresések](media/enable-from-automation-account/manage-machines.png)

3. Az összes rendelkezésre álló gép Change Tracking és leltárának engedélyezéséhez válassza az Engedélyezés lehetőséget az összes **elérhető gépen** a **gépek kezelése** lapon. Ez a művelet letiltja a vezérlőt, hogy egyenként vegyen fel gépeket, és hozzáadja a munkaterületnek jelentő összes gépet a számítógépcsoport mentett keresési lekérdezéséhez. Ha be van jelölve, ez a művelet letiltja a **számítógépek kezelése** lehetőséget.

4. Az összes rendelkezésre álló és jövőbeli gép funkciójának engedélyezéséhez válassza az **Engedélyezés lehetőséget az összes rendelkezésre álló és jövőbeli gépen**. Ez a beállítás törli a mentett keresés és a hatókör konfigurációját a munkaterületről, és megnyitja a szolgáltatást a munkaterületnek jelentett összes Azure-beli és nem Azure-beli gép számára. Ha bejelöli ezt a jelölőnégyzetet, ez a művelet véglegesen letiltja a **gépek kezelése** lehetőséget, mivel nem maradt hatókör-konfiguráció.

    > [!NOTE]
    > Mivel ez a lehetőség törli a mentett keresés és a hatókör konfigurációját a Log Analyticson belül, fontos, hogy a beállítás kiválasztása előtt távolítsa el az összes törlési zárolást a Log Analytics munkaterületen. Ha nem, akkor a beállítás nem távolítja el a konfigurációkat, ezért azokat manuálisan kell eltávolítania.

5. Szükség esetén visszaállíthatja a hatókör-konfigurációt a kezdeti mentett keresés újbóli hozzáadásával. További információ: a [change Tracking és a leltár központi telepítési hatókörének korlátozása](manage-scope-configurations.md).

6. Egy vagy több gép funkciójának engedélyezéséhez válassza az **Engedélyezés a kiválasztott gépeken** lehetőséget, majd az egyes gépek mellett kattintson a **Hozzáadás** elemre a funkció engedélyezéséhez. Ez a feladat hozzáadja a kiválasztott számítógépneveket a szolgáltatáshoz tartozó számítógépcsoport mentett keresési lekérdezéséhez.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking kezelése](manage-change-tracking.md) és a [leltár kezelése](manage-inventory-vms.md)című témakört.

* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](../troubleshoot/change-tracking.md)című témakört.
