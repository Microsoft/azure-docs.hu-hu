---
title: Rövid útmutató – Virtuális gépek Azure Automanage engedélyezése a Azure Portal
description: Ismerje meg, hogyan engedélyezheti gyorsan a virtuális gépek automatikus Azure Portal.
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: 7121d83f9401fe985966324afe6a61cf8396b2bb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534071"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Rövid útmutató: Azure Automanage virtuális gépeken való Azure Portal

A virtuális gépek Azure Automanage használatának első Azure Portal az automatikus automatizáció engedélyezéséhez egy új vagy meglévő virtuális gépen.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) létre egy fiókot.

> [!NOTE]
> Az ingyenes próbafiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Frissítsen egy fizetős előfizetésre.

> [!IMPORTANT]
> A virtuális gépeket tartalmazó erőforráscsoport közreműködői szerepkörével kell, hogy fel tudja venni az automatikus adatokat egy meglévő automatikus kezelőfiókkal való automatikus automatizálni.  Ha új automatikus felügyeleti fiókkal engedélyezi az automatikus adatokat, a következő  engedélyekre  lesz szüksége: **Tulajdonosi** szerepkör vagy Közreműködő, valamint felhasználói hozzáférés-rendszergazdai szerepkörök az előfizetésben.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Automatikus automatizálás engedélyezése egyetlen virtuális géphez

1. Keresse meg azt a virtuális gépet, amely számára engedélyezni szeretné.

2. Kattintson a Tartalomjegyzék Műveletek **alatti Automatikus felügyelet (előzetes verzió)** **bejegyzésre.**

3. Válassza **az Első lépések lehetőséget.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-getstartedbutton.png" alt-text="Egyetlen virtuális gép – első lépések.":::

4. Válassza ki az Automatikus kezelése beállításokat (Környezet, Beállítások, Fiók automatikus kezelése), majd válassza az **Engedélyezés lehetőséget.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmmanage-enablepane.png" alt-text="Engedélyezés egyetlen virtuális gépen.":::

## <a name="enable-automanage-for-multiple-vms"></a>Automatikus automatizálás engedélyezése több virtuális géphez

1. A keresősávban keresse meg és válassza az **Automanage – Azure machine best practices (Az** Azure-gépekre vonatkozó ajánlott eljárások) lehetőséget.

2. Válassza az **Engedélyezés meglévő virtuális gépen lehetőséget.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

3. A Gépek **kiválasztása panelen:**
    1. Szűrje a virtuális gépek listáját az **előfizetése és** az **erőforráscsoport alapján.**
    1. Jelölje be a jelölőnégyzetet az egyes virtuális gépeken, amelyekre fel szeretneni.
    1. Kattintson a **Kijelölés gombra.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Válasszon ki egy meglévő virtuális gépet az elérhető virtuális gépek listájából.":::

4. A **Környezet alatt** válassza ki a környezet típusát: **Dev/Test** vagy **Production.** 

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Válassza ki a környezeteket.":::

   A **környezetek közötti különbségeket** a Környezeti adatok összehasonlítása lehetőségre kattintva láthatja.
    1. Válasszon ki egy környezetet a legördülő menüben: *Fejlesztés/tesztelés* teszteléshez, *Éles* üzemhez.
    1. Kattintson az **OK** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Az éles környezet tallózása.":::

5. Alapértelmezés szerint az **Ajánlott eljárások beállítás** van kiválasztva a konfigurációs beállításokhoz. Ennek módosításához hozzon létre egy új beállítást, vagy válasszon ki egy meglévőt. 

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-preference.png" alt-text="Beállítás létrehozása.":::

6. Kattintson az **Engedélyezés** gombra.


## <a name="enable-automanage-for-a-new-vm"></a>Automatikus automatizálás engedélyezése új virtuális géphez

Jelentkezzen be az Azure Portal [az új](https://aka.ms/AzureAutomanagePreview) virtuális gép létrehozásához és az automatikus automatizálás engedélyezéséhez.

1. Töltse ki **az Alapvető beállítások lapot** a virtuális gép adataival.

> [!NOTE]
> Tekintse meg a [](automanage-virtual-machines.md#supported-regions) támogatott régiók és az Automanage által támogatott [Linux-disztribúciók](automanage-linux.md#supported-linux-distributions-and-versions) és [Windows Server-verziók jelölőnégyzetét.](automanage-windows-server.md#supported-windows-server-versions)

2. Lépjen a **Felügyelet lapra,** és válassza ki az **Automatikus felügyeleti környezet lehetőséget.**

    :::image type="content" source="media\quick-create-virtual-machine-portal\vmcreate-managementtab.png" alt-text="Az automatikus kezelés engedélyezése a Felügyelet lapon.":::

3. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.

4. Amikor megjelenik az érvényesítésről küldött üzenet, válassza a **Létrehozás lehetőséget.**

## <a name="disable-automanage-for-vms"></a>Virtuális gépek automatikus ének letiltása

Az automatikus Azure Automanage letiltásával gyorsan leállíthatja a virtuális gépekhez való használatot.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Az automatikus automatizáció letiltása egy virtuális gépen.":::

1. Az Automatikus **szolgáltatásokat –** Azure-beli virtuális gépekre vonatkozó ajánlott eljárások oldalon felsorolhatja az összes automatikusan felügyelt virtuális gépet.
1. Jelölje be a letiltani kívánt virtuális gép melletti jelölőnégyzetet.
1. Kattintson az **Automanagent letiltása gombra.**
1. A Disable (Letiltás) engedélyezése előtt olvassa el figyelmesen az üzenetkezelést az eredményül kapott **előugró ablakban.**


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot, hogy megpróbálja Azure Automanage virtuális gépekhez, és már nincs rá szüksége, törölheti az erőforráscsoportot. A csoport törlésével a virtuális gépet és az erőforráscsoportban található összes erőforrást is törli.

Azure Automanage alapértelmezett erőforráscsoportokat hoz létre az erőforrások tárolására. Az összes erőforrás tisztítása érdekében ellenőrizze a "DefaultResourceGroupRegionName" és az "AzureBackupRGRegionName" elnevezési konvencióval rendelkezik erőforráscsoportokat.

1. Válassza ki az **Erőforráscsoportot.**
1. Az erőforráscsoport lapján válassza a Törlés **lehetőséget.**
1. Amikor a rendszer kéri, erősítse meg az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban engedélyezte a virtuális Azure Automanage számára.

Ismerje meg, hogyan hozhat létre és alkalmazhat testreszabott beállításokat, amikor engedélyezi az automatikus automatizációt a virtuális gépen.

> [!div class="nextstepaction"]
> [Azure Automanage konfigurálása – Egyéni konfigurációs beállítások](virtual-machines-custom-preferences.md)
