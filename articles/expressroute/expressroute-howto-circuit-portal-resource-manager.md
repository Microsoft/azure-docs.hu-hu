---
title: 'Rövid útmutató: áramkör létrehozása és módosítása ExpressRoute-Azure Portal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, helyezhet üzembe, ellenőrizheti, frissítheti, törölheti és kiépítheti az ExpressRoute-áramkört a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: b0fd844021e7398e061072d7939b782616580c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185198"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit"></a>Gyors útmutató: ExpressRoute-kör létrehozása és módosítása

Ez a rövid útmutató bemutatja, hogyan hozhat létre ExpressRoute-áramkört a Azure Portal és az Azure Resource Manager üzemi modell használatával. Az áramkör állapotát, frissítését, törlését vagy megszüntetését is megtekintheti.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .
* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kör létrehozása és kiépítése

### <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="create-a-new-expressroute-circuit"></a>Új ExpressRoute-áramkör létrehozása

> [!IMPORTANT]
> A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Győződjön meg arról, hogy ezt a műveletet akkor hajtja végre, ha a kapcsolati szolgáltató készen áll az áramkör kiépítésére.

ExpressRoute-áramkör létrehozásához válassza az új erőforrás létrehozása lehetőséget. 

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Válassza a **hálózatkezelés**  >  **ExpressRoute** lehetőséget az alábbi ábrán látható módon:

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/create-expressroute-circuit-menu.png" alt-text="ExpressRoute-kapcsolatcsoport létrehozása":::

2. A **ExpressRoute** kiválasztása után megjelenik a **ExpressRoute létrehozása** lap. Adja meg az **erőforrás-csoportot**, a **régiót** és az áramkör  **nevét** . Ezután válassza a **következő: Configuration >**.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-basic.png" alt-text="Az erőforráscsoport és a régió konfigurálása":::

3. Ha kitölti az ezen a lapon található értékeket, győződjön meg arról, hogy a megfelelő SKU-szintet (helyi, standard vagy prémium) és az adatmérési számlázási modellt (korlátlan vagy mért) használja.

    :::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-create-configuration.png" alt-text="Az áramkör konfigurálása":::
    
    * A **port típusa** határozza meg, hogy csatlakozik-e egy szolgáltatóhoz vagy közvetlenül a Microsoft globális hálózatához egy egyenrangú helyen.
    * Az **új vagy a klasszikus létrehozásakor** meghatározza, hogy az új áramkör létrehozása folyamatban van-e, vagy a klasszikus áramkör áttelepítése Azure Resource Managerra.
    * A **szolgáltató** az internetszolgáltató, aki számára a szolgáltatást kérni fogja.
    * A társítás **helye** az a fizikai hely, ahol a Microsofttal folytatja a kapcsolatot.

    > [!IMPORTANT]
    > A társítási hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal való együttműködésre van szükség. Ez **nem** a "location" tulajdonsághoz van csatolva, amely arra a földrajzi helyre hivatkozik, ahol az Azure hálózati erőforrás-szolgáltató található. Noha nem kapcsolódnak egymáshoz, célszerű kiválasztania a hálózati erőforrás-szolgáltatót földrajzilag közel az áramkör egyenrangú helyéhez.

    * Az **SKU** meghatározza, hogy engedélyezve van-e egy ExpressRoute helyi, ExpressRoute vagy ExpressRoute prémium szintű bővítmény. Megadhatja a **helyi** **SKU-t, amely** a prémium szintű bővítmény standard SKU-jának vagy **premiumjának** beszerzésére szolgál. A prémium szintű bővítmény engedélyezéséhez módosíthatja az SKU-t.
    > [!IMPORTANT]
    > A SKU nem módosítható a **standard/prémium** szintről a **helyi** értékre.
    
    * A számlázási **modell** meghatározza a számlázási típust. Megadhatja **a mért adatcsomag díjait** , és **korlátlan** számú adatcsomagot. A számlázási típus a **mért** értékről **korlátlanra** módosítható.

    > [!IMPORTANT]
    > A típust nem lehet **korlátlanról** **mértre** módosítani.

    * A **klasszikus művelet** lehetővé teszi a klasszikus virtuális hálózatok számára az áramkörhöz való kapcsolódást.

### <a name="view-the-circuits-and-properties"></a>Az áramkörök és a tulajdonságok megtekintése

**Az összes áramkör megtekintése**

Az összes létrehozott áramkört megtekintheti, ha a bal oldali menüben a **minden szolgáltatás > a hálózatkezelés > ExpressRoute** .

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-menu.png" alt-text="Expressroute áramkör menü":::

Az előfizetésben létrehozott összes Expressroute-áramkör itt fog megjelenni.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-list.png" alt-text="Expressroute áramköri lista":::

**A tulajdonságok megtekintése**

Az áramkör tulajdonságait a kiválasztásával tekintheti meg. Az áramkör **Áttekintés** lapján a szolgáltatási kulcs megjelenik a szolgáltatás kulcsa mezőben. Az üzembe helyezési folyamat befejezéséhez tekintse meg az áramkör szolgáltatási kulcsát, és adja meg azt a szolgáltatónak. A szolgáltatási kulcs az áramkörre jellemző.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview.png" alt-text="Tulajdonságok megtekintése":::

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>A szolgáltatás kulcsának elküldése a kapcsolati szolgáltatónak a kiépítés céljából

Ezen az oldalon a **szolgáltatói állapot** a szolgáltatás-szolgáltatói oldalon kiépítés aktuális állapotát adja meg. Az **áramkör állapota** biztosítja a Microsoft oldalán lévő állapotot. Az áramkör kiépítési állapotával kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) című cikket.

Új ExpressRoute-kör létrehozásakor az áramkör a következő állapotban van:

Szolgáltató állapota: **nincs kiépítve**<BR>
Áramkör állapota: **engedélyezve**

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-overview-provisioning-state.png" alt-text="Kiépítési folyamat elindítása":::

Az áramkör a következő állapotra vált, amikor a kapcsolati szolgáltató jelenleg engedélyezi az Ön számára:

Szolgáltatói állapot: **kiépítés**<BR>
Áramkör állapota: **engedélyezve**

A ExpressRoute áramkör használatához a következő állapotban kell lennie:

Szolgáltató állapota: **kiépítve**<BR>
Áramkör állapota: **engedélyezve**

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Az áramköri kulcs állapotának és állapotának rendszeres időközönkénti keresése

A kiválasztásával megtekintheti annak az áramkörnek a tulajdonságait, amelyre kíváncsi. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a folytatás előtt átkerült a **kiépített** állapotba.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png" alt-text="Áramkör és szolgáltató állapota":::

### <a name="create-your-routing-configuration"></a>Az útválasztási konfiguráció létrehozása

Részletes útmutatásért tekintse meg az [ExpressRoute-áramkör útválasztási konfigurációját](expressroute-howto-routing-portal-resource-manager.md) ismertető cikket az áramköri társítások létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. rétegbeli kapcsolati szolgáltatásokat nyújtó szolgáltatók által létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat (jellemzően IP VPN, például MPLS) kínál, a kapcsolati szolgáltatója konfigurálja és kezeli az útválasztást.

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal

Ezután csatoljon egy virtuális hálózatot a ExpressRoute-áramkörhöz. Ha a Resource Manager-alapú üzemi modellel dolgozik, használja a [virtuális hálózatok összekapcsolása a ExpressRoute áramkörökkel](expressroute-howto-linkvnet-arm.md) című cikket.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>ExpressRoute kör állapotának beolvasása

Az áramkör állapotát úgy tekintheti meg, ha kiválasztja, és megtekinti az Áttekintés lapot.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute áramkör módosítása

Egy ExpressRoute-kör bizonyos tulajdonságait módosíthatja a kapcsolat befolyásolása nélkül. Módosíthatja a sávszélességet, az SKU-t, a számlázási modellt, és engedélyezheti a klasszikus műveleteket a **konfiguráció** lapon. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

A következő feladatokat a leállás nélkül végezheti el:

* Engedélyezheti vagy letilthatja a ExpressRoute-áramkörhöz tartozó ExpressRoute Premium-bővítményt.

> [!IMPORTANT]
  > Az SKU **szabványos/prémiumról** **helyire** történő módosítása nem támogatott.

* Növelje a ExpressRoute-áramkör sávszélességét, ha rendelkezésre áll a porton elérhető kapacitás.

  > [!IMPORTANT]
  > Az áramkör sávszélességének visszaminősítése nem támogatott.

* Módosítsa a mérési tervet a *mért adatokról* a *korlátlan mennyiségű adatokra*.

  > [!IMPORTANT]
  > A mérési terv **korlátlan mennyiségű** és **mért adatok** közötti módosítása nem támogatott.

* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.
  > [!IMPORTANT]
  > Előfordulhat, hogy újra létre kell hoznia a ExpressRoute áramkört, ha nincs elegendő kapacitás a meglévő porton. Az áramkör nem frissíthető, ha az adott helyen nem érhető el további kapacitás.
  >
  > Bár zökkenőmentesen frissítheti a sávszélességet, a ExpressRoute áramkör sávszélességét nem lehet megzavarás nélkül csökkenteni. A lefokozási sávszélességhez meg kell szüntetnie a ExpressRoute áramkört, majd újra kell telepítenie egy új ExpressRoute áramkört.
  >
  > A prémium szintű bővítmény műveletének letiltása sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak a standard áramkör számára engedélyezettnél.

Egy ExpressRoute-áramkör módosításához válassza a **konfiguráció** lehetőséget.

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-configuration.png" alt-text="Áramkör módosítása":::

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute áramkör kiépítése

Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.

> [!NOTE]
>* A kiépítés előtt le kell vonni az *összes virtuális hálózatot* a ExpressRoute áramkörből. Ha a művelet meghiúsul, ellenőrizze, hogy a virtuális hálózatok az áramkörhöz vannak-e kapcsolva.
>* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A **Törlés** ikonra kattintva törölheti a ExpressRoute áramkörét. A folytatás előtt győződjön meg arról, hogy a szolgáltató állapota *nincs kiépítve* .

:::image type="content" source="./media/expressroute-howto-circuit-portal-resource-manager/expressroute-circuit-delete.png" alt-text="Áramkör törlése":::

## <a name="next-steps"></a>Következő lépések

Az áramkör létrehozása után folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
