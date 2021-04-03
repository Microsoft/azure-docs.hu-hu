---
title: A Azure Automation Change Tracking és a leltár funkció eltávolítása
description: Ez a cikk azt ismerteti, hogyan állíthatja le a Change Tracking és a leltár használatát, és hogyan lehet lemondani egy Automation-fiókot a Log Analytics munkaterületről.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209818"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Change Tracking és leltár eltávolítása az Automation-fiókból

Miután engedélyezte a virtuális gépek felügyeletét Azure Automation Change Tracking és leltár használatával, dönthet úgy, hogy leállítja a használatát, és eltávolítja a fiókot a fiókból és a csatolt Log Analytics munkaterületről. Ebből a cikkből megtudhatja, hogyan távolíthatja el a Change Tracking és a leltárt a felügyelt virtuális gépekről, az Automation-fiókból és Log Analytics munkaterületről.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Virtuális gépek felügyeletének eltávolítása

Change Tracking és leltár eltávolítása előtt először le kell állítania a virtuális gépek felügyeletét. További információ: a [virtuális gépek eltávolítása Change Trackingről](remove-vms-from-change-tracking.md) a szolgáltatásból való regisztráció törléséhez.

## <a name="remove-changetracking-solution"></a>Változáskövetési-megoldás eltávolítása

Az Automation-fiók munkaterületről való leválasztásához az alábbi lépéseket kell követnie a Change Tracking és a leltár teljes eltávolításához. A **változáskövetési** megoldást a munkaterületről távolíthatja el.

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a bemenet alapján szűri a javaslatokat. Válassza a **Log Analytics** elemet.

2. Log Analytics munkaterületek listájában válassza ki azt a munkaterületet, amelyet a Change Tracking és a leltár engedélyezésekor választott ki.

3. A bal oldalon válassza a **megoldások** elemet.  

4. A megoldások listájában válassza a **változáskövetési (munkaterület neve)** elemet. A megoldás **Áttekintés** lapján válassza a **Törlés** lehetőséget. Amikor a rendszer felszólítja a megerősítésre, válassza az **Igen** lehetőséget.

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

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újbóli engedélyezésével kapcsolatban lásd: [change Tracking és leltár engedélyezése Automation-fiókból](enable-from-automation-account.md), [az Change Tracking és a leltár engedélyezése a Azure Portal tallózásával, a](enable-from-portal.md) [change Tracking és a leltár runbook való](enable-from-runbook.md)engedélyezésével, illetve az [Azure-beli virtuális gépek Change Tracking és leltározásának engedélyezése](enable-from-vm.md).
