---
title: Gyors útmutató – az Azure-beli automanage engedélyezése virtuális gépekhez a Azure Portal
description: Megtudhatja, hogyan engedélyezheti gyorsan a virtuális gépek autofelügyeletét egy új vagy meglévő virtuális gépen a Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 5f5f1e70d9ae309c90291ccac1e6dd61e7a9d056
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038388"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Gyors útmutató: az Azure automanage engedélyezése virtuális gépekhez a Azure Portal

Ismerkedjen meg az Azure automata felügyeletével a virtuális gépeken a Azure Portal használatával, amely lehetővé teszi az automanagement használatát egy új vagy meglévő virtuális gépen.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

> [!IMPORTANT]
> Rendelkeznie kell a **közreműködő** szerepkörrel a virtuális gépeket tartalmazó erőforráscsoporthoz, amely lehetővé teszi az automanage használatát egy meglévő automanage-fiók használatával. Ha új automanage-fiókkal engedélyezi az autofelügyeletet, a következő engedélyek szükségesek: **tulajdonosi** szerepkör vagy **közreműködő** , valamint a **felhasználói hozzáférés rendszergazdai** szerepkörei az előfizetésben.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Egyetlen virtuális gép autofelügyeletének engedélyezése

1. Keresse meg azt a virtuális gépet, amelyet engedélyezni szeretne.

2. Kattintson az **automanage (előzetes verzió)** bejegyzésre a tartalomjegyzékben a **műveletek** területen.

3. Válassza az első **lépések** lehetőséget.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Első lépések: egyetlen virtuális gép.":::

4. Válassza ki az egyéni felügyelet beállításait (környezet, beállítások, automanage Account), és kattintson az **Engedélyezés** elemre.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Engedélyezés egyetlen virtuális gépen.":::

## <a name="enable-automanage-for-multiple-vms"></a>Több virtuális gép autofelügyeletének engedélyezése

1. A keresési sávban keresse meg és válassza ki az **automanage (az Azure Machine – ajánlott eljárások**) elemet.

2. Válassza az **Engedélyezés meglévő virtuális gépen** lehetőséget.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

3. A **gépek kiválasztása** panelen:
    1. A virtuális gépek listájának szűrése az **előfizetés** és az **erőforráscsoport** alapján.
    1. Jelölje be a bevezetéshez használni kívánt virtuális gépek jelölőnégyzetét.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

4. A **környezet** területen válassza ki a környezet típusát: **fejlesztési/tesztelési** vagy **éles** környezetben. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Válassza a környezetek lehetőséget.":::

   Kattintson a **környezet részleteinek összehasonlítása** lehetőségre a környezetek közötti különbségek megtekintéséhez.
    1. Válasszon ki egy környezetet a legördülő menüben: fejlesztés */tesztelés* tesztelésre, éles *üzemi* környezetben.
    1. Kattintson az **OK** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Tallózás éles környezetben.":::

5. Alapértelmezés szerint az **Azure ajánlott eljárásokat** választotta a konfigurációs beállításokhoz. Ennek módosításához hozzon létre egy új előnyt, vagy válasszon ki egy meglévőt. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Beállítások létrehozása":::

6. Kattintson az **Engedélyezés** gombra.


## <a name="enable-automanage-for-a-new-vm"></a>Új virtuális gép autokezelésének engedélyezése

Egy új virtuális gép létrehozásához jelentkezzen be [a Azure Portalba, és](https://aka.ms/AzureAutomanagePreview) engedélyezze az autofelügyeletet.

1. Töltse ki az **alapok** lapot a virtuális gép adataival.

> [!NOTE]
> Győződjön meg arról, hogy a [támogatott régiókat](automanage-virtual-machines.md#supported-regions) és a támogatott [Linux-disztribúciókat](automanage-linux.md#supported-linux-distributions-and-versions) és a [Windows Server-verziókat](automanage-windows-server.md#supported-windows-server-versions)a felügyeli.

2. Tallózással keresse meg a **felügyelet** lapot, és válassza ki az **automanage-környezetet**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Az autokezelés engedélyezése a felügyelet lapon.":::

3. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.

4. Amikor megjelenik az érvényesítés által átadott üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="disable-automanage-for-vms"></a>Virtuális gépek autokezelésének letiltása

Az automanagement szolgáltatás letiltásával gyorsan leállíthatja a virtuális gépek Azure-beli autofelügyeletét.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="A virtuális gépeken az automanage szolgáltatás letiltása.":::

1. Nyissa meg az automatikus **kezelés – Azure virtuális gépekre vonatkozó ajánlott eljárásokat** tartalmazó oldalt, amely felsorolja az összes automatikusan felügyelt virtuális gépet.
1. Jelölje be a letiltani kívánt virtuális gép melletti jelölőnégyzetet.
1. Kattintson a **Automanagent letiltása** gombra.
1. A **Letiltás** előtt olvassa el figyelmesen az üzenetküldést az eredményül kapott előugró ablakban.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot a virtuális gépek Azure-beli autokezelésének kipróbálásához, és már nincs szüksége rá, törölheti az erőforráscsoportot. A csoport törlésekor a virtuális gép és az erőforráscsoport összes erőforrása is törlődik.

Az Azure automanage alapértelmezett erőforráscsoportokat hoz létre az erőforrások tárolásához a alkalmazásban. A "DefaultResourceGroupRegionName" és a "AzureBackupRGRegionName" elnevezési konvencióval rendelkező erőforráscsoportok használatával törölje az összes erőforrást.

1. Válassza ki az **erőforráscsoportot**.
1. Az erőforráscsoport lapján válassza a **Törlés** lehetőséget.
1. Ha a rendszer kéri, erősítse meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban engedélyezte a virtuális gépek Azure-beli felügyeletét.

Ismerje meg, hogyan hozhat létre és alkalmazhat testreszabott beállításokat, amikor engedélyezi az automatikus felügyeletet a virtuális gépen.

> [!div class="nextstepaction"]
> [Azure automatikus felügyelet virtuális gépekhez – egyéni konfigurációs beállítások](virtual-machines-custom-preferences.md)
