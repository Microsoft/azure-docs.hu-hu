---
title: Virtuális gépek eltávolítása a Azure Automation Update Management
description: Ez a cikk azt ismerteti, Hogyan távolítható el a Update Management felügyelt gépek.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913192"
---
# <a name="remove-vms-from-update-management"></a>Virtuális gépek eltávolítása az Update Managementből

Amikor befejezte a frissítések kezelését a környezetben lévő virtuális gépeken, leállíthatja a virtuális gépek kezelését a [Update Management](overview.md) szolgáltatással. A kezelésének leállításához szerkessze a mentett keresési lekérdezést a `MicrosoftDefaultComputerGroup` log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

## <a name="sign-into-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Virtuális gépek eltávolítása

1. A Azure Portal a Azure Portal felső navigációs sávján indítsa el az **Cloud Shell** . Ha nem ismeri a Azure Cloud Shellt, tekintse meg [a Azure Cloud Shell áttekintése](../../cloud-shell/overview.md)című témakört.

2. A következő paranccsal azonosíthatja egy olyan gép UUID-azonosítóját, amelyet el szeretne távolítani a felügyelet alól.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. A Azure Portal navigáljon **log Analytics munkaterületek** elemre. Válassza ki a munkaterületet a listából.

4. A Log Analytics munkaterületen válassza a **Speciális beállítások** lehetőséget, majd a bal oldali menüben válassza a **számítógépcsoportok** lehetőséget.

5. A jobb oldali ablaktáblában található **számítógépcsoportok** területen válassza a **mentett csoportok** lehetőséget.

6. A táblából a mentett keresési lekérdezések **frissítései: MicrosoftDefaultComputerGroup**, kattintson a **tagok megtekintése** ikonra a futtatásához, és tekintse meg a tagjait.

7. A lekérdezés-szerkesztőben tekintse át a lekérdezést, és keresse meg a virtuális gép UUID azonosítóját. Távolítsa el a virtuális gép UUID-azonosítóját, és ismételje meg az összes eltávolítani kívánt virtuális gép lépéseit.

8. Mentse a mentett keresést a Szerkesztés befejezése után a felső sávon található **Mentés** lehetőség kiválasztásával. Ha a rendszer kéri, a következőket kell megadnia:

    * **Név**: MicrosoftDefaultComputerGroup
    * **Mentés másként**: függvény
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Kategória**: frissítések

>[!NOTE]
>A gépek még a regisztráció törlése után is megjelennek, mert az elmúlt 24 órában mért összes gépet bejelentjük. A gép eltávolítása után 24 órát kell várnia, mielőtt azok már nem jelennek meg.

## <a name="next-steps"></a>Következő lépések

A virtuális gép kezelésének újbóli engedélyezéséhez tekintse meg [a Update Management engedélyezése Azure Portal](enable-from-portal.md) vagy a [Update Management engedélyezése Azure-beli virtuális](enable-from-vm.md)gépről című témakört.