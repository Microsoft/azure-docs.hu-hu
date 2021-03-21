---
title: Egyéni preferencia létrehozása az Azure-beli automanage virtuális gépeken
description: Megtudhatja, hogyan módosíthatja a környezeti konfigurációt az Azure automatikus felügyelet szolgáltatásban, és hogyan állíthatja be a saját beállításait.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101647931"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Egyéni preferencia létrehozása az Azure-beli automanage virtuális gépeken

Az Azure-ban a virtuális gépekkel kapcsolatos ajánlott eljárások az alapértelmezett környezetek, amelyek szükség esetén módosíthatók. Ebből a cikkből megtudhatja, hogyan állíthatja be a saját beállításait, amikor egy új vagy meglévő virtuális gépen engedélyezi az automanagement szolgáltatást.

Jelenleg a [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) és a [Microsoft antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)testreszabásait is támogatjuk.


> [!NOTE]
> A környezet vagy a beállítás nem módosítható a virtuális gépen, amíg az automanage funkció engedélyezve van. Le kell tiltania a virtuális gép automatikus felügyeletét, majd újra engedélyeznie kell az automatikus felügyeletet a kívánt konfigurációs környezettel és beállításokkal.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

> [!IMPORTANT]
> A következő Azure-RBAC engedély szükséges az automanage: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreinek engedélyezéséhez.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Virtuális gépek autokezelésének engedélyezése meglévő virtuális gépen

1. A keresési sávban keresse meg és válassza ki az **automanage (az Azure Machine – ajánlott eljárások**) elemet.

2. Válassza az **Engedélyezés meglévő virtuális gépen** lehetőséget.

3. A **gépek kiválasztása** panelen:
    1. A virtuális gépek listájának szűrése az **előfizetés** és az **erőforráscsoport** alapján.
    1. Jelölje be a bevezetéshez használni kívánt virtuális gépek jelölőnégyzetét.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

    > [!NOTE]
    > Kattintson a nem **jogosult gépek megjelenítése** lehetőségre a nem támogatott gépek listájának megtekintéséhez és az indokláshoz. 

4. A **konfiguráció** alatt kattintson a **környezetek összehasonlítása** elemre.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Környezetek összehasonlítása.":::

5. A **környezet részletei** panelen válasszon ki egy környezetet a legördülő menüből: *dev/test* *for Testing,* Production for Production, és kattintson **az OK** gombra.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Tallózás éles környezetben.":::

6. A környezet kiválasztása után kiválaszthatja a **konfigurációs beállításokat**. Alapértelmezés szerint az Azure ajánlott eljárásainak előnyét fogja használni. Ez a beállítás az egyes szolgáltatások ajánlott beállításait használja. Módosítsa ezeket a beállításokat egyéni preferencia létrehozásával: 
    1. Kattintson az **új beállítások létrehozása** elemre.
    1. A **konfigurációs beállítások létrehozása** panelen töltse ki az alapok lapot:
        1. Előfizetés
        1. Erőforráscsoport
        1. Preferencia neve
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Adja meg a konfigurációs beállításokat.":::

7. Módosítsa a kívánt konfigurációs beállításokat.
        
    > [!NOTE]
    > A környezeti konfigurációk módosításakor csak az ajánlott eljárások felső és alsó határain belüli módosítások engedélyezettek.

8. Tekintse át a konfiguráció részleteit.
9. Kattintson a **Létrehozás** gombra.

10. Kattintson az **Engedélyezés** gombra.


## <a name="disable-automanage-for-vms"></a>Virtuális gépek autokezelésének letiltása

Az automanagement szolgáltatás letiltásával gyorsan leállíthatja a virtuális gépek Azure-beli autofelügyeletét.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="A virtuális gépeken az automanage szolgáltatás letiltása.":::

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

Ismerkedjen meg a gyakori kérdések megválaszolásával. 

> [!div class="nextstepaction"]
> [Gyakori kérdések](faq.md)