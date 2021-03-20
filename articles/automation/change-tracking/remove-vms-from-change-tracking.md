---
title: Virtuális gépek eltávolítása Azure Automation Change Tracking és leltárból
description: Ez a cikk azt ismerteti, Hogyan távolítható el a virtuális gépek a Change Trackingról és a leltárból.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131275"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Virtuális gépek eltávolítása a Change Trackingből és az Inventoryból

Amikor befejezte a környezetben lévő virtuális gépek változásainak nyomon követését, leállíthatja őket a [change Tracking és a leltár](overview.md) funkció használatával. A kezelésének leállításához szerkessze a mentett keresési lekérdezést a `MicrosoftDefaultComputerGroup` log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Virtuális gépek eltávolítása

1. A Azure Portal a Azure Portal felső navigációs sávján indítsa el az **Cloud Shell** . Ha nem ismeri a Azure Cloud Shellt, tekintse meg [a Azure Cloud Shell áttekintése](../../cloud-shell/overview.md)című témakört.

2. A következő paranccsal azonosíthatja egy olyan gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. A Azure Portal navigáljon **log Analytics munkaterületek** elemre. Válassza ki a munkaterületet a listából.

4. A Log Analytics munkaterületen válassza a **naplók** lehetőséget, majd a felső műveletek menüben válassza a **lekérdezési tallózó** elemet.

5. A jobb oldali ablaktáblán található **query Explorerben** bontsa ki a **mentett Queries\Updates** elemet, és válassza ki a mentett keresési lekérdezést a `MicrosoftDefaultComputerGroup` szerkesztéshez.

6. A lekérdezés-szerkesztőben tekintse át a lekérdezést, és keresse meg a virtuális gép UUID azonosítóját. Távolítsa el a virtuális gép UUID-azonosítóját, és ismételje meg az összes eltávolítani kívánt virtuális gép lépéseit.

7. Mentse a mentett keresést a Szerkesztés befejezése után a felső sávon található **Mentés** lehetőség kiválasztásával.

>[!NOTE]
>A gépek még a regisztráció törlése után is megjelennek, mert az elmúlt 24 órában mért összes gépet bejelentjük. A gép eltávolítása után 24 órát kell várnia, mielőtt azok már nem jelennek meg.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás újbóli engedélyezésével kapcsolatban lásd: [change Tracking és leltár engedélyezése Automation-fiókból](enable-from-automation-account.md), [az Change Tracking és a leltár engedélyezése a Azure Portal tallózásával, a](enable-from-portal.md) [change Tracking és a leltár runbook való](enable-from-runbook.md)engedélyezésével, illetve az [Azure-beli virtuális gépek Change Tracking és leltározásának engedélyezése](enable-from-vm.md).