---
title: Azure Automation Update Management funkció eltávolítása
description: Ez a cikk azt ismerteti, hogyan lehet leállítani a Update Managementt, és lemondani egy Automation-fiókot a Log Analytics munkaterületről.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92222239"
---
# <a name="remove-update-management-from-automation-account"></a>Update Management eltávolítása az Automation-fiókból

Miután engedélyezte a frissítések kezelését a virtuális gépeken Azure Automation Update Management használatával, dönthet úgy, hogy leállítja a használatát, és eltávolítja a fiókot a fiókból és a csatolt Log Analytics munkaterületről.  Ebből a cikkből megtudhatja, hogyan távolíthatja el teljesen Update Management a felügyelt virtuális gépekről, az Automation-fiókból és Log Analytics munkaterületről.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Virtuális gépek felügyeletének eltávolítása

Update Management eltávolítása előtt először le kell állítania a virtuális gépek felügyeletét. További információ: a [virtuális gépek eltávolítása Update managementről](remove-vms.md) a szolgáltatásból való regisztráció törléséhez.

## <a name="remove-updatemanagement-solution"></a>UpdateManagement-megoldás eltávolítása

Az Automation-fiók munkaterületről való leválasztásához az alábbi lépéseket kell követnie a Update Management teljes eltávolításához. A **frissítési** megoldást a munkaterületről távolíthatja el.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.

3. Log Analytics munkaterületek listájában válassza ki a Update Management engedélyezésekor kiválasztott munkaterületet.

4. A bal oldalon válassza a **megoldások** elemet.  

5. A megoldások listájában válassza a **frissítések (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés** lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen** lehetőséget.

## <a name="unlink-workspace-from-automation-account"></a>Munkaterület leválasztása Automation-fiókról

1. A Azure Portal válassza az **Automation-fiókok** elemet.

2. Nyissa meg az Automation-fiókját, és a bal oldali **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

3. A **munkaterület** leválasztása lapon válassza a **munkaterület megszüntetése** lehetőséget, és válaszoljon a felszólításokra.

   ![Munkaterület leválasztása lap](media/remove-feature/automation-unlink-workspace-blade.png)

Miközben megpróbálja leválasztani az Log Analytics munkaterületet, nyomon követheti a menü **értesítések** részén látható előrehaladást.

Azt is megteheti, hogy leválasztja az Log Analytics munkaterületet az Automation-fiókjából a munkaterületen belülről:

1. Az Azure Portalon válassza a **Log Analytics** lehetőséget.

2. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások** területen.

3. Az Automation-fiók lapon válassza a **fiók megszüntetése** lehetőséget.

Miközben megpróbálja leválasztani az Automation-fiókot, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

## <a name="cleanup-automation-account"></a>Automation-fiók törlése

Ha Update Management úgy lett konfigurálva, hogy támogassa az Azure SQL-figyelés korábbi verzióit, akkor előfordulhat, hogy a szolgáltatás telepítője olyan Automation-eszközöket hozott létre, amelyeket el kell távolítania. Update Management esetén előfordulhat, hogy el kívánja távolítani a következő elemeket, amelyekre már nincs szükség:

   * Frissítési ütemtervek – mindegyiknek van olyan neve, amely megfelel a létrehozott frissítési telepítésnek.
   * Update Managementhoz létrehozott hibrid feldolgozói csoportok – mindegyik neve *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újbóli engedélyezéséhez tekintse meg a [Update Management Automation-fiókból való engedélyezését](enable-from-automation-account.md) [Update Management, a Azure Portal tallózásával engedélyezze a](enable-from-portal.md) [Update Management Runbook](enable-from-runbook.md), vagy [engedélyezze az Azure-beli virtuális gépekről történő Update Management engedélyezését](enable-from-vm.md).
