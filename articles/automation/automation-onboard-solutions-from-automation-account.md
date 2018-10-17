---
title: Ismerje meg, hogyan kell előkészíteni az Update Management, Change Tracking and Inventory megoldások az Azure Automationben
description: Ismerje meg, hogyan üzembe helyezni egy Azure-beli virtuális gépek az Azure Automation részét képező az Update Management, Change Tracking and Inventory megoldásaival
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 1ed76b0505747831363ea829f1cb91b3e056282a
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352446"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Előkészítése az Update Management, Change Tracking és Inventory megoldásainak

Az Azure Automation segítségével a megoldások kezelése az operációs rendszer biztonsági frissítéseit, nyomon követéséhez és szoftverleltár, a számítógépeken telepítve legyen. Gépek előkészítésének több módja van, a segítségével készítheti elő a megoldás [a virtuális gépről](automation-onboard-solutions-from-vm.md), [több gép tallózással](automation-onboard-solutions-from-browse.md), az Automation-fiók vagy a [runbook](automation-onboard-solutions.md). Ez a cikk az Automation-fiók ezekkel a megoldásokkal előkészítést részletezi.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-ba a https://portal.azure.com címen

## <a name="enable-solutions"></a>Megoldások engedélyezése

Keresse meg az Automation-fiókját, és válassza **készlet** vagy **Change tracking** alatt **konfigurációkezelés**.

Válassza ki a Log analytics-munkaterületet és Automation-fiók, és kattintson a **engedélyezése** a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészítheti az Inventory megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

A Change Tracking és az Inventory megoldással [változáskövetési](automation-vm-change-tracking.md) és [leltárkészítési](automation-vm-inventory.md) képességeket biztosíthat a virtuális gépek számára. Ebben a lépésben engedélyezzük a megoldást egy virtuális gépen.

Ha a Change tracking és az Inventory megoldás előkészítése befejeződött az értesítés szerint, a **KONFIGURÁCIÓKEZELÉS** területen kattintson az **Update Management** lehetőségre.

Az Update Management megoldás segítségével kezelheti az Azure-beli Windows rendszerű virtuális gépek frissítéseit és javításait. Felmérheti az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, és áttekintheti a telepítési eredményeket, hogy ellenőrizze, sikeres volt-e a frissítések telepítése a virtuális gépen. Ez a művelet engedélyezett a megoldást a virtuális Gépen.

Válassza ki **frissítéskezelés** alatt **az UPDATE MANAGEMENT**. A kiválasztott Log Analytics-munkaterület megegyezik az előző lépésben használt munkaterülettel. Az Update Management megoldás előkészítéséhez kattintson az **Engedélyezés** lehetőségre. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Előkészíteni az update megoldás](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Hatókör-konfiguráció

Egyes megoldások belül a munkaterülethez hatókör-konfigurációt használ, amelyekre a számítógépeket, amelyek a megoldás beszerzése. A hatókör-konfiguráció olyan egy vagy több mentett keresések, amellyel korlátozhatja a megoldást az adott számítógépek számára. A hatókör-konfigurációk, az Automation-fiók alatt eléréséhez **kapcsolódó erőforrások**válassza **munkaterület**. Majd munkaterületén **MUNKATERÜLET ADATFORRÁSAI**válassza **hatókör-konfigurációk**.

Ha a kijelölt munkaterület nem rendelkezik a Frissítésfelügyeleti vagy változáskövetési megoldások, de a következő hatókör-konfigurációk jönnek létre:

* **MicrosoftDefaultScopeConfig – változáskövetési**

* **MicrosoftDefaultScopeConfig-frissítések**

Ha a kijelölt munkaterület a megoldás már rendelkezik. A megoldás újbóli nincs telepítve, és a hatókör-konfiguráció nem adnak hozzá.

## <a name="saved-searches"></a>Mentett keresések

A Frissítésfelügyeleti vagy a Change Tracking és Inventory megoldásainak számítógép ad hozzá, azok bővül két mentett keresés a munkaterületen. Ezek a mentett keresések olyan lekérdezések, amelyek tartalmazzák a számítógépeken, amelyekre vonatkozik a ezeket a megoldásokat.

Az Automation-fiókjában keresse meg és válassza **mentett keresések** alatt **általános**. A két mentett keresések, ezek a megoldások által használt az alábbi táblázatban láthatók:

|Name (Név)     |Kategória  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Change Tracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Frissítések        | Updates__MicrosoftDefaultComputerGroup         |

Válassza ki vagy mentett keresést a lekérdezés a csoport feltöltéséhez használt megtekintéséhez. Az alábbi képen látható, a lekérdezés, és az eredményeket:

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Az Azure virtuális gépek előkészítése

Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Kattintson a **+ Azure-beli virtuális gépek hozzáadása**, válasszon ki egy vagy több virtuális gépet a listából. Virtuális gépek, amelyek nem lehet engedélyezni, szürkén jelennek meg lejárt, ezért nem tudja, ki kell jelölni. Az a **Update Management engedélyezése** kattintson **engedélyezése**. A kijelölt virtuális gépek hozzáadása a számítógép (csoport)-beli mentett keresés a megoldáshoz.

![Az Azure virtuális gépek engedélyezése](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Nem Azure-beli gép előkészítése

Gépek Azure-ban nem kell manuálisan kell hozzáadni. Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Kattintson a **hozzáadása nem Azure-beli gép**. Megnyílik egy új böngészőablakot, és a [való telepítése és konfigurálása a Microsoft Monitoring Agent a gépen](../log-analytics/log-analytics-concept-hybrid.md) , a gép megkezdheti a megoldás a jelentéskészítés. Ha előkészítése a System Center Operations Manager által kezelt számítógép, egy új ügynököt nem szükséges, a munkaterület-információk írja be a rendszer a meglévő ügynököt.

## <a name="onboard-machines-in-the-workspace"></a>Előkészítheti a gépeket a munkaterületen

Manuálisan telepített gépek vagy a gép már a munkaterületen kell hozzáadni az Azure Automation-megoldás engedélyezni kell a jelentéskészítés. Az Automation a fiókot válassza **készlet** vagy **a Change tracking** alatt **konfigurációkezelés**, vagy **frissítéskezelés** a **az UPDATE MANAGEMENT**.

Válassza ki **gépek kezelése**. Megnyílik a **gépek kezelése** lapot. Ezen a lapon válassza konfigurálásában a gépek, az összes rendelkezésre álló gépek, a megoldás engedélyezéséhez vagy a megoldás összes aktuális gépek engedélyezéséhez és összes további gépen engedélyezi azt teszi lehetővé.

![Mentett keresések](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Az összes rendelkezésre álló gépek

Az összes rendelkezésre álló gépek a megoldás engedélyezéséhez válassza **engedélyezés minden rendelkezésre álló gépen**. Ez a művelet letiltja a vezérlő egyenként hozzáadni. Ez a feladat hozzáadása a a keresési lekérdezés mentett számítógépcsoport-munkaterületre jelentő gépek összes nevét. Kiválasztásakor Ez letiltja a **gépek kezelése** gombra.

### <a name="all-available-and-future-machines"></a>Az összes rendelkezésre álló és jövőbeli gépen

Az összes rendelkezésre álló és jövőbeli gépen összes megoldás engedélyezéséhez válassza **engedélyezés minden rendelkezésre álló és jövőbeli gépen**. Ez a lehetőség törli a mentett keresések és a hatókör-konfigurációk a munkaterületről. Ekkor megnyílik az Azure és a nem Azure-a-munkaterületre jelentő gépek megoldást. Kiválasztásakor Ez letiltja a **gépek kezelése** gombra.

### <a name="selected-machines"></a>Kijelölt gépek

Egy vagy több gépet a megoldás engedélyezéséhez válassza **engedélyezése a kiválasztott gépeken** kattintson **hozzáadása** mellett minden gép, a megoldáshoz hozzá szeretné. Ez a feladat a keresési lekérdezés megoldás mentett számítógépcsoport-ad hozzá a kijelölt számítógépek nevével.

## <a name="unlink-workspace"></a>Munkaterület leválasztása

A következő megoldásokat a Log Analytics-munkaterület függnek:

* [Frissítéskezelés](automation-update-management.md)
* [Változáskövetés](automation-change-tracking.md)
* [Virtuális gépek indítása/leállítása munkaidőn kívül](automation-solution-vm-management.md)

Ha úgy dönt, hogy már nem szeretne az Automation-fiók integrálása a Log Analytics-szel, megszüntetheti a fiók közvetlenül az Azure Portalról.  Mielőtt továbblépne, először el kell távolítania a megoldások azt korábban említettük, ellenkező esetben ez a folyamat megakadályozza a folytatás. Tekintse át a cikk az adott megoldás importált távolítsa el a szükséges lépések megértéséhez.

Miután eltávolítja ezeket a megoldásokat, az Automation-fiók leválasztása a következő lépéseket végezheti.

> [!NOTE]
> Egyes megoldások, beleértve az Azure SQL-figyelési megoldás a korábbi lehet, hogy létre automation-adategységeket, és is szükség lehet a munkaterület leválasztása előtt el kell távolítani.

1. Az Azure Portalról nyissa meg az Automation-fiókját, és a az Automation-fiók oldalon válassza ki **csatolt munkaterület** a szakaszában **kapcsolódó erőforrások** a bal oldalon.

2. A leválasztás munkaterület oldalán kattintson a **munkaterület leválasztása**.

   ![Munkaterület oldalán leválasztása](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   A rendszer felkéri, hogy erősítse meg, valóban folytani kívánja-e.

3. Bár az Azure Automation próbál meg a fiók leválasztása a Log Analytics-munkaterület, nyomon követheti a folyamat állapotát **értesítések** a menüből.

Az Update Management megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Frissítés ütemezése – a neve megegyezik a létrehozott frissítéstelepítések lesz)

* A megoldás - számára létrehozott hibrid feldolgozócsoportok egyes lesznek elnevezve hasonlóan a: gép1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Virtuális gépek indítása/leállítása munkaidőn kívül megoldás használata esetén igény szerint, előfordulhat, hogy el kívánja távolítani a következő elemek, amelyek a megoldás eltávolítása után már nem szükséges.

* Elindíthatja és leállíthatja a virtuális gép runbook ütemezése
* Virtuális gép runbookok elindítása és leállítása
* Változók

## <a name="next-steps"></a>További lépések

Az oktatóanyagok, megtudhatja, hogyan használhatja azokat a megoldásokat a továbbra is.

* [Oktatóanyag – a virtuális gép frissítéseinek kezelése](automation-tutorial-update-management.md)

* [Oktatóanyag – virtuális gépek szoftvereinek azonosítása](automation-tutorial-installed-software.md)

* [Oktatóanyag – a virtuális gép módosításainak hibaelhárítása](automation-tutorial-troubleshoot-changes.md)