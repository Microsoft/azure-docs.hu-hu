---
title: 'Oktatóanyag: a ExpressRoute Circuit társításának konfigurálása – Azure Portal'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe ExpressRoute privát és Microsoft-társait a Azure Portal használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 11a4798c0cb3bc010bbdbae1fcb709951c67781a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721911"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Oktatóanyag: társítások létrehozása és módosítása ExpressRoute-áramkörhöz a Azure Portal használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre és kezelhet útválasztási konfigurációt egy Azure Resource Manager ExpressRoute áramkörhöz a Azure Portal használatával. A ExpressRoute-áramkör állapota, frissítése vagy törlése és megszüntetése is ellenőrizhető. Ha más módszert szeretne használni az áramkörrel való munkavégzéshez, válasszon egy cikket a következő listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó – privát peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

A ExpressRoute-áramkörhöz privát és Microsoft-társítást is beállíthat (az Azure nyilvános társítása elavult az új áramkörök esetében). A társításokat tetszőleges sorrendben lehet konfigurálni. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományokról és a társításokról: [ExpressRoute-útválasztási tartományok](expressroute-circuit-peerings.md). A nyilvános hálózattal kapcsolatos további információkért lásd: [nyilvános ExpressRoute](about-public-peering.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - Microsoft-társítások konfigurálása, frissítése és törlése egy áramkörhöz
> - Azure-beli privát kapcsolatok konfigurálása, frissítése és törlése egy áramkörhöz

## <a name="prerequisites"></a>Előfeltételek

* A konfigurálás megkezdése előtt győződjön meg arról, hogy áttekintette a következő lapokat:
    * [Előfeltételek](expressroute-prerequisites.md) 
    * [Útválasztási követelmények](expressroute-routing.md)
    * [Munkafolyamatok](expressroute-workflows.md)
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-portal-resource-manager.md) , és a folytatás előtt engedélyezze az áramkört a kapcsolat szolgáltatója számára. A társítás (ok) konfigurálásához a ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie. 
* Ha megosztott kulcsot vagy MD5-kivonatot szeretne használni, ügyeljen arra, hogy az alagút mindkét oldalán használja a kulcsot. A korlát legfeljebb 25 alfanumerikus karakterből állhat. A különleges karakterek nem támogatottak. 

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat kínál (jellemzően IPVPN, például MPLS), a kapcsolati szolgáltató konfigurálja és felügyeli az útválasztást. 

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP-társítások konfigurálása előtt érdeklődjön a szolgáltatónál.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-partnerek

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Microsoft-társi konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás-előtagokkal rendelkezik, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő. További információ: [útválasztási szűrő beállítása a Microsoft-társak](how-to-routefilter-powershell.md)számára.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a kapcsolat szolgáltatója teljes mértékben kiépíti az áramkört a folytatás előtt.

   Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolat szolgáltatóját, hogy engedélyezze a Microsoft-partneri kapcsolatot. Nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa ezeket a lépéseket.

   **Áramköri szolgáltató állapota: nincs kiépítve**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Képernyőkép, amely a ExpressRoute bemutató oldalát jeleníti meg a szolgáltató állapotának kiemelése a nincs kiépítve értékre":::

   **Áramkör-szolgáltató állapota: kiépítve**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Képernyőkép, amely a ExpressRoute bemutató oldalát jeleníti meg a szolgáltatói állapot kiépítve értékre való kiemelésével":::

2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. A folytatás előtt győződjön meg arról, hogy rendelkezik a következő információkkal.

   * Az Ön által birtokolt és RIR/IRR regisztrált alhálózatok párja. A rendszer egy alhálózatot használ az elsődleges hivatkozáshoz, míg a másikat a másodlagos hivatkozáshoz fogja használni. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli hozzá, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz. Ezt az alhálózatot három lehetőséggel látja el:
       * IPv4: két/30 alhálózat. Ennek érvényes nyilvános IPv4-előtagnak kell lennie.
       * IPv6: két/126 alhálózat. Ezeknek érvényes nyilvános IPv6-ELŐTAGOKNAK kell lenniük.
       * Mindkettő: két/30 alhálózat és két/126 alhálózat.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos kapcsolatok esetében ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: megtekintheti a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Nem **kötelező –** Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nincsenek regisztrálva a társítás mint szám mezőben, megadhatja az AS-számot, amelyre regisztrálva van.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.
1. Kiválaszthatja a konfigurálni kívánt társítást, ahogy az az alábbi példában is látható. Válassza ki a Microsoft társviszony-létesítés sorát.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Válassza ki a Microsoft egyenrangú sorát":::

4. Konfigurálja a Microsoft társviszony-beállítást. **Mentse** a konfigurációt, miután megadta az összes paramétert. Az alábbi képen egy példa konfiguráció látható:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="A Microsoft társközi érvényesítésének konfigurálása szükséges":::

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés meghiúsul, az "érvényesítés szükséges" üzenet jelenik meg. 
>
> Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosaként listázott entitásban, és a támogatási jegy megnyitásával manuális ellenőrzés céljából elküldheti ezeket a dokumentumokat. 
>

   Ha az áramkör "érvényesítés szükséges" állapotba kerül, meg kell nyitnia egy támogatási jegyet, hogy igazolja az előtagok tulajdonjogát a támogatási csapatnak. Egy támogatási jegyet közvetlenül a portálról nyithat meg, ahogy az alábbi példában is látható:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Ellenőrzés szükséges – támogatási jegy":::

5. A konfiguráció sikeres elfogadását követően az alábbi képhez hasonlóan fog megjelenni:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Egyenrangú állapot: konfigurálva":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A Microsoft-társak tulajdonságainak megtekintéséhez válassza ki a társítás sorát.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Microsoft-partneri tulajdonságok megtekintése":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

Kiválaszthatja a módosítani kívánt társítás sorát, majd módosíthatja a társítási tulajdonságokat, és mentheti a módosításokat.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Egyenrangú sor kijelölése":::

## <a name="azure-private-peering"></a><a name="private"></a>Azure-beli privát társhálózat-létesítés

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Azure-beli privát társ-összevonási konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> A privát társítás IPv6-támogatása jelenleg **nyilvános előzetes** verzióban érhető el. Ha a virtuális hálózatát IPv6-alapú privát ExpressRoute-kapcsolattal szeretné összekapcsolni, győződjön meg arról, hogy a virtuális hálózata kettős verem, és követi az [itt](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)ismertetett irányelveket.
> 
> 

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. 

   Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolati szolgáltatót, hogy engedélyezze az Azure-beli privát kapcsolatot. Nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a következő lépésekkel.

   **Áramköri szolgáltató állapota: nincs kiépítve**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Képernyőfelvétel: a ExpressRoute-bemutató áttekintő lapja, a szolgáltató állapotának kiemelésével, amely nincs kiépítve":::

   **Áramkör-szolgáltató állapota: kiépítve**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Képernyőfelvétel: a ExpressRoute bemutató lapja a kiépített szolgáltatói állapotot jelző piros mezővel":::

2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. A következő lépések elvégzése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

   * Olyan alhálózatok párja, amelyek nem tartoznak a virtuális hálózatok számára fenntartott címtartomány részébe. A rendszer egy alhálózatot használ az elsődleges hivatkozáshoz, míg a másikat a másodlagos hivatkozáshoz fogja használni. Az egyes alhálózatokból az első használható IP-címet az útválasztóhoz rendeli hozzá, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz. Ezt az alhálózatot három lehetőséggel látja el:
       * IPv4: két/30 alhálózat.
       * IPv6: két/126 alhálózat.
       * Mindkettő: két/30 alhálózat és két/126 alhálózat.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos kapcsolatok esetében ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társhoz az 65515 és 65520 közötti számot is használhatja, amely magában foglalja a többit.
   * Az útvonalakat a helyszíni peremhálózati útválasztóról az Azure-ba kell hirdetni a BGP-n keresztül, amikor konfigurálja a privát társat.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.
3. Válassza ki az Azure privát társ-összevonási sorát az alábbi példában látható módon:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Válassza ki a privát társas sort":::

4. Konfigurálja a privát társviszony-létesítést. **Mentse** a konfigurációt, miután megadta az összes paramétert.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Privát társviszony konfigurálása":::

5. A konfiguráció sikeres elfogadását követően az alábbi példához hasonló módon jelenik meg:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Privát társak mentve":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Privát egyenrangú tulajdonságok megtekintése":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. A frissítés után mentse a módosításokat.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Privát társak frissítése":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A Microsoft társközi konfigurációját úgy távolíthatja el, ha a jobb gombbal a társításra kattint, és kiválasztja a **delete (Törlés** ) lehetőséget, ahogy az az alábbi képen is látható:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Microsoft-társak törlése":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A privát társ-összevonási konfigurációt úgy távolíthatja el, ha a jobb gombbal a társításra kattint, és kiválasztja a **delete (Törlés** ) lehetőséget a következő képen látható módon:

> [!WARNING]
> A művelet futtatása előtt győződjön meg arról, hogy a virtuális hálózatok és a ExpressRoute összes Global Reach kapcsolata el lesz távolítva. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Privát társak törlése":::

## <a name="next-steps"></a>Következő lépések

Miután konfigurálta az Azure Private-társítást, létrehozhat egy ExpressRoute-átjárót, amely összekapcsolja a virtuális hálózatot az áramkörrel. 

> [!div class="nextstepaction"]
> [Az ExpressRoute virtuális hálózati átjáróinak konfigurálása](expressroute-howto-add-gateway-resource-manager.md)
