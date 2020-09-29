---
title: Egyéni preferencia létrehozása az Azure-beli automanage virtuális gépeken
description: Megtudhatja, hogyan állíthatja be a konfigurációs profilt az Azure automatikus felügyelet szolgáltatásban virtuális gépekhez, és hogyan állíthatja be a saját beállításait.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 7a716ed9bca6d34ad4dbcd2566837a839f71153b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450311"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Egyéni preferencia létrehozása az Azure-beli automanage virtuális gépeken

Az Azure-beli automatikus felügyelet a virtuális gépekhez ajánlott eljárások alapértelmezett konfigurációs profilokkal rendelkeznek, amelyek szükség esetén módosíthatók. Ez a cikk azt ismerteti, hogyan állíthatja be a saját konfigurációs profil beállításait, ha engedélyezi az automatikus felügyeletet egy új vagy meglévő virtuális gépen.

Jelenleg a [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) és a [Microsoft antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)testreszabásait is támogatjuk.


> [!NOTE]
> A konfigurációs profil vagy a beállítás nem módosítható a virtuális gépen, amíg az automatikus felügyelet engedélyezve van. Le kell tiltania a virtuális gép automatikus felügyeletét, majd újra engedélyeznie kell az automatikus felügyeletet a kívánt konfigurációs profillal és beállításokkal.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

> [!IMPORTANT]
> A következő RBAC engedélyre van szükség az automanage engedélyezése: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreivel együtt.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Virtuális gépek autokezelésének engedélyezése meglévő virtuális gépen

1. A keresési sávban keresse meg és válassza ki az **automanage – Azure virtuális gépek ajánlott eljárásait**.

2. Válassza az **Engedélyezés meglévő virtuális gépen**lehetőséget.

3. A **gépek kiválasztása** panelen:
    1. A virtuális gépek listájának szűrése az **előfizetés** és az **erőforráscsoport**alapján.
    1. Jelölje be a bevezetéshez használni kívánt virtuális gépek jelölőnégyzetét.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

4. A **konfigurációs profil**területen kattintson a Tallózás gombra, és válassza a **profilok és beállítások módosítása**elemet.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

5. A **konfigurációs profil és beállítások kiválasztása panelen** válasszon ki egy profilt a bal oldali oldalon: fejlesztés */* tesztelés tesztelésre *, éles* üzemi környezetben.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

6. A kiválasztott profil **konfigurációs beállítások** területén található egy legördülő lista, amely bizonyos szolgáltatások esetében módosítható.
    1. Kattintson az **új beállítások létrehozása**elemre.
    1. A **konfigurációs beállítások létrehozása** panelen töltse ki az alapok lapot:
        1. Előfizetés
        1. Erőforráscsoport
        1. Preferencia neve
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

7. Lépjen a Beállítások lapra, és állítsa be a kívánt konfigurációs beállításokat.
        
    > [!NOTE]
    > A profilok konfigurációjának módosításakor a rendszer csak az ajánlott eljárások felső és alsó határain belüli módosításokat engedélyezi.

8. Tekintse át a konfigurációs profilt.
9. Kattintson a **Létrehozás** gombra.

10. Kattintson az **Engedélyezés** gombra.


## <a name="disable-automanage-for-vms"></a>Virtuális gépek autokezelésének letiltása

Az automanagement szolgáltatás letiltásával gyorsan leállíthatja a virtuális gépek Azure-beli autofelügyeletét.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Válassza ki a meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

1. Nyissa meg az automatikus **kezelés – Azure virtuális gépekre vonatkozó ajánlott eljárásokat** tartalmazó oldalt, amely felsorolja az összes automatikusan felügyelt virtuális gépet.
1. Jelölje be a letiltani kívánt virtuális gép melletti jelölőnégyzetet.
1. Kattintson a **Automanagent letiltása** gombra.
1. A **Letiltás**előtt olvassa el figyelmesen az üzenetküldést az eredményül kapott előugró ablakban.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot a virtuális gépek Azure-beli autokezelésének kipróbálásához, és már nincs szüksége rá, törölheti az erőforráscsoportot. A csoport törlésekor a virtuális gép és az erőforráscsoport összes erőforrása is törlődik.

Az Azure automanage alapértelmezett erőforráscsoportokat hoz létre az erőforrások tárolásához a alkalmazásban. A "DefaultResourceGroupRegionName" és a "AzureBackupRGRegionName" elnevezési konvencióval rendelkező erőforráscsoportok használatával törölje az összes erőforrást.

1. Válassza ki az **erőforráscsoportot**.
1. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget.
1. Ha a rendszer kéri, erősítse meg az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.


## <a name="next-steps"></a>Következő lépések 

Ismerkedjen meg a gyakori kérdések megválaszolásával. 

> [!div class="nextstepaction"]
> [Gyakori kérdések](faq.md)