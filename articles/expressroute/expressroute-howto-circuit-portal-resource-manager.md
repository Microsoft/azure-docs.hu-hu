---
title: 'Rövid útmutató: Kapcsolat kapcsolati kapcsolat létrehozása és módosítása az ExpressRoute-Azure Portal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, hozhat létre, hozhat létre, ellenőrizhet, frissíthet, törölhet és megszüntethet egy ExpressRoute-kapcsolatkört a Azure Portal.
services: expressroute
author: duongau
ms.author: duau
ms.date: 10/21/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- mode-portal
ms.openlocfilehash: f62b60a9c33cd1b813b8f3307ec82d4242be168b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534805"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Rövid útmutató: ExpressRoute-kapcsolatkör létrehozása és módosítása

Ez a rövid útmutató bemutatja, hogyan hozhat létre Egy ExpressRoute-kapcsolatkört a Azure Portal és a Azure Resource Manager modell használatával. Emellett ellenőrizheti a kapcsolatkör állapotát, frissítését, törlését vagy megszüntetését.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* A [konfigurálás megkezdése](expressroute-prerequisites.md) előtt [tekintse](expressroute-workflows.md) át az előfeltételeket és a munkafolyamatokat.
* A lépések [jobb átértelmének](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) érdekében megtekintheti a videókat.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolatkör létrehozása és kiépítése

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="create-a-new-expressroute-circuit"></a>Új ExpressRoute-kapcsolat kapcsolat létrehozása

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatkör a szolgáltatáskulcs kibocsátásának pillanatában lesz kiszámlázva. Győződjön meg arról, hogy ezt a műveletet akkor végzi el, amikor a kapcsolatszolgáltató készen áll a kapcsolat kapcsolati kapcsolat létesítéséhez.

ExpressRoute-kapcsolatcsoportot az új erőforrás létrehozására vonatkozó lehetőség kiválasztásával hozhat létre. 

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Válassza **a Hálózat**  >  **expressroute** lehetőséget az alábbi képen látható módon:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="ExpressRoute-kapcsolatcsoport létrehozása":::

2. Az **ExpressRoute kiválasztása után** az **ExpressRoute** létrehozása lap látható. Adja meg **a kapcsolatcsoport erőforráscsoportját,**  **régióját** és nevét. Ezután válassza **a Tovább: Konfigurációs >** lehetőséget.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Az erőforráscsoport és a régió konfigurálása":::

3. Amikor ezen az oldalon tölti ki az értékeket, győződjön meg arról, hogy a megfelelő termékváltozat-szintet (Helyi, Standard vagy Prémium) és adatmérés számlázási modelljét (Korlátlan vagy Forgalmi díjas) adja meg.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="A kapcsolat kapcsolati kör konfigurálása":::
    
    * **A port típusa** határozza meg, hogy egy szolgáltatóhoz vagy közvetlenül a Microsoft globális hálózatához csatlakozik-e egy társviszony-létesítés helyén.
    * **Az Új létrehozása vagy** klasszikusból való importálás határozza meg, hogy a rendszer létrehoz-e egy új kapcsolatkört, vagy klasszikus kapcsolatkört minál a Azure Resource Manager.
    * **A** szolgáltató az az internetszolgáltató, amelytől a szolgáltatást kéri.
    * **A társviszony-létesítés** helye az a fizikai hely, ahol társviszonyt létesít a Microsofttal.

    > [!IMPORTANT]
    > A Társviszony helye azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol társviszonyt létesít a Microsofttal. Ez nem **kapcsolódik** a "Location" tulajdonsághoz, amely arra a földrajzi helyre vonatkozik, ahol az Azure hálózati erőforrás-szolgáltató található. Bár nem kapcsolódnak egymáshoz, a kapcsolatcsoport társviszony-létesítási helyétől földrajzilag közeli hálózati erőforrás-szolgáltatót kell választani.

    * **A termékváltozat** határozza meg, hogy engedélyezve van-e egy helyi ExpressRoute-, ExpressRoute-standard vagy ExpressRoute prémium bővítmény. A Helyi **beállítás** megadásával lekérte a helyi termékváltozatot, a **Standard** értéket a prémium szintű bővítmény standard termékváltozatának vagy Prémium változatának lekért értékhez.  A termékváltozatot módosíthatja a prémium bővítmény engedélyezéséhez.
    > [!IMPORTANT]
    > A termékváltozat nem változtatható **Standard/Prémium termékváltozatról** **Helyi termékváltozatra.**
    
    * **A számlázási modell** határozza meg a számlázási típust. A forgalmi **díjas adatcsomaghoz** a Forgalmi díjas, korlátlan **értéket** pedig korlátlan adatcsomaghoz adhatja meg. A számlázási típust forgalmi díjasról **korlátlanra** **módosíthatja.**

    > [!IMPORTANT]
    > Nem módosíthatja a típust **Korlátlanról** **Forgalmi díjas típusra.**

    * **A klasszikus művelet engedélyezése** lehetővé teszi, hogy a klasszikus virtuális hálózatok kapcsolódnak a kapcsolati kapcsolathoz.

### <a name="view-the-circuits-and-properties"></a>A kapcsolati körök és tulajdonságok megtekintése

**Az összes megtekintése kapcsolati körök Az összes megtekintése**

Az összes létrehozott kapcsolati kör megtekintéséhez válassza a bal oldali menüBen > Összes szolgáltatás > **ExpressRoute-kapcsolat** kapcsolati kapcsolatokkal lehetőséget.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="ExpressRoute-kapcsolat kapcsolatrendszer menüje":::

Itt jelenik meg az előfizetésben létrehozott összes ExpressRoute-kapcsolat kapcsolat.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="ExpressRoute-kapcsolatörök listája":::

**A tulajdonságok megtekintése**

A kapcsolati kör tulajdonságainak megtekintéséhez jelölje ki azt. A **kapcsolatkör Áttekintés** lapján a szolgáltatáskulcs megjelenik a szolgáltatáskulcs mezőben. Tekintse meg a kapcsolatkör szolgáltatáskulcsát, és adja meg a szolgáltatónak a kiépítési folyamat befejezéséhez. A szolgáltatáskulcs a kapcsolatkörre jellemző.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Tulajdonságok megtekintése":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>A szolgáltatáskulcs küldése a kapcsolatszolgáltatónak a kiépítéshez

Ezen az oldalon a **Szolgáltató állapota** a szolgáltatói oldalon a kiépítés aktuális állapotát adja meg. **A kapcsolatkör** állapota a Microsoft oldalán mutatja meg az állapotot. A kapcsolati kapcsolat létesítésének állapotairól a Munkafolyamatok [cikkben talál további](expressroute-workflows.md#expressroute-circuit-provisioning-states) információt.

Amikor létrehoz egy új ExpressRoute-kapcsolatkört, a kapcsolatkör a következő állapotban van:

Szolgáltató állapota: **Nincs kiépítve**<BR>
Kapcsolatkör állapota: **Engedélyezve**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Elindítja a kiépítési folyamatot":::

Ha a kapcsolatszolgáltató jelenleg engedélyezi a kapcsolatszolgáltatót, a kapcsolati kapcsolat a következő állapotra változik:

Szolgáltató állapota: **Kiépítés**<BR>
Kapcsolatkör állapota: **Engedélyezve**

Az ExpressRoute-kapcsolat kapcsolat kapcsolatának a következő állapotban kell lennie:

Szolgáltató állapota: **Kiépítve**<BR>
Kapcsolatkör állapota: **Engedélyezve**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Rendszeres időközönként ellenőrizze az áramköri kulcs állapotát és állapotát

A kívánt kapcsolat kapcsolati kör tulajdonságait a kiválasztásával tudja megtekinteni. A folytatás előtt ellenőrizze **a Szolgáltató állapotát,** és győződjön meg arról, hogy átkerült a **Kiépítve** állapotra.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Kapcsolati kör és szolgáltató állapota":::

### <a name="create-your-routing-configuration"></a>Az útválasztási konfiguráció létrehozása

Részletes útmutatásért tekintse meg az [ExpressRoute-kapcsolatcsoport](expressroute-howto-routing-portal-resource-manager.md) útválasztásának konfigurálási cikkét a kapcsolatcsoport-társviszonyok létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak azokra a kapcsolati körökre vonatkoznak, amelyek 2. rétegben elérhető kapcsolati szolgáltatásokat nyújtó szolgáltatókkal vannak létrehozva. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegi szolgáltatásokat (általában IP VPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal

Ezután csatolja a virtuális hálózatot az ExpressRoute-kapcsolathálózathoz. A virtuális [hálózatok ExpressRoute-kapcsolatokkal](expressroute-howto-linkvnet-arm.md) való összekapcsolása cikkben a virtuális hálózatok üzembe helyezési modelljével Resource Manager dolgozhat.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>ExpressRoute-kapcsolat kapcsolat kapcsolati állapotának lekért állapota

A kapcsolati kör állapotát úgy tudja megtekinteni, hogy kijelöli azt, és megtekinti az Áttekintés oldalt.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute-kapcsolat kapcsolat kapcsolatának módosítása

Az ExpressRoute-kapcsolat kapcsolatának bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja. A sávszélességet, a termékváltozatot és a számlázási modellt módosíthatja, és engedélyezheti a klasszikus műveleteket a **Konfiguráció lapon.** További információ a korlátozásokról: [ExpressRoute – gyakori kérdések.](expressroute-faqs.md)

A következő feladatokat használhatja állásidő nélkül:

* ExpressRoute Premium-bővítmény engedélyezése vagy letiltása az ExpressRoute-kapcsolaton.

> [!IMPORTANT]
  > A termékváltozat **Standard/Prémium termékváltozatról** **Helyire** való módosítása nem támogatott.

* Növelje az ExpressRoute-kapcsolatkör sávszélességét, ha van szabad kapacitás a porton.

  > [!IMPORTANT]
  > A kapcsolat körök sávszélességének csökkentése nem támogatott.

* Módosítsa a mérési tervet Forgalmi díjas *adatokról* *Korlátlan adatra.*

  > [!IMPORTANT]
  > A mérési terv Korlátlan **adatról** **Forgalmi** díjas adatokra váltása nem támogatott.

* Engedélyezheti és letilthatja a *Klasszikus műveletek engedélyezése lehetőséget.*
  > [!IMPORTANT]
  > Előfordulhat, hogy újra létre kell hoznia az ExpressRoute-kapcsolatkört, ha nincs elegendő kapacitás a meglévő porton. Nem frissítheti a kapcsolatkört, ha nincs további elérhető kapacitás ezen a helyen.
  >
  > Bár zökkenőmentesen frissítheti a sávszélességet, az ExpressRoute-kapcsolatörök sávszélessége nem csökkenthető megszakítás nélkül. A sávszélesség-visszalépéshez meg kell megszüntetni az ExpressRoute-kapcsolatkört, majd újra ki kellépíteni egy új ExpressRoute-kapcsolatkört.
  >
  > A prémium bővítményművelet letiltása sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak, mint ami a standard kapcsolatkör számára engedélyezett.

ExpressRoute-kapcsolat kapcsolat kapcsolatának módosításához válassza a **Konfiguráció lehetőséget.**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Kapcsolat kapcsolati kör módosítása":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolat kapcsolat kapcsolatának megszüntetése

Ha az ExpressRoute-kapcsolatkör szolgáltatójának kiépítési állapota Kiépítés vagy Kiépítve, akkor a szolgáltatóval együtt kell megszüntetnie a kapcsolatkört a szolgáltató oldalán.   Továbbra is fenntartjuk az erőforrásokat, és kiszámlázjuk, amíg a szolgáltató be nem fejeződik a kapcsolatkészlet megszüntetése után, és nem küld nekünk értesítést.

> [!NOTE]
>* A megszüntetés *előtt* az összes virtuális hálózatot le kell különülnie az ExpressRoute-kapcsolathálózatról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok csatolva a kapcsolati kapcsolathoz.
>* Ha a szolgáltató megszüntette a kapcsolatkört (a szolgáltató kiépítési állapota **Nincs** kiépítve), akkor törölheti a kapcsolatkört. Ez leállítja a kapcsolatcsoport számlázását.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ExpressRoute-kapcsolatkört a Törlés ikonra **kattintva törölheti.** A folytatás előtt győződjön meg arról, hogy a szolgáltató Állapota *Nincs kiépítve.*

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Kapcsolati kör törlése":::

## <a name="next-steps"></a>Következő lépések

Miután létrehozott egy kapcsolatkört, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Az ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
