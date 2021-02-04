---
title: Költségek megtakarítása az Azure VMware-megoldás fenntartott példányával
description: Megtudhatja, hogyan vásárolhat fenntartott példányt az Azure VMware-megoldáshoz.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550975"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Költségek megtakarítása Azure VMware-megoldással

Ha az [Azure VMware-megoldás](introduction.md)fenntartott példányára véglegesít, pénzt takarít meg.  A foglalási kedvezményt a rendszer automatikusan alkalmazza a lefoglalási hatókörnek és attribútumoknak megfelelő Azure VMware megoldás-gazdagépekre. A fenntartott példányok vásárlása csak a használat számítási részét fedi le, és magában foglalja a licencelési költségeket is. 

## <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott példányok bizonyos kivételekkel érhetők el.

-   **Felhők** – a foglalások csak az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) lapon felsorolt régiókban érhetők el.

-   Nem **elegendő kvóta** – az egyetlen/megosztott előfizetéshez tartozó foglalási kvótának az új fenntartott példány előfizetésében elérhetőnek kell lennie a gazdagépek számára. A probléma megoldásához [kvóta-növelési kérelmet hozhat létre](enable-azure-vmware-solution.md) .

-   **Ajánlati jogosultság**– szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](../cost-management-billing/manage/ea-portal-agreements.md) a Microsofttal.

-   **Kapacitás korlátozásai** – ritka körülmények között az Azure korlátozza az Azure VMware-megoldás gazdagépének új foglalásának megvásárlását az adott régió alacsony kapacitása miatt.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

Megvásárolhatja az Azure VMware Solution Host-példány fenntartott példányát a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

A foglalást [előre vagy havi fizetéssel](../cost-management-billing/reservations/prepare-buy-reservation.md)kell fizetni.

Ezek a követelmények a fenntartott dedikált gazdagép-példány megvásárlására vonatkoznak:

-   Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó *tulajdonosi* szerepkörrel kell rendelkeznie.

-   Az EA-előfizetések esetében engedélyeznie kell a **fenntartott példányok hozzáadása** lehetőséget az [EA portálon](https://ea.azure.com/). Ha le van tiltva, az előfizetés aktiválásához rendszergazdai jogosultsággal kell rendelkeznie.

-   A felhőalapú megoldás-szolgáltató (CSP) Azure-csomag alatti előfizetések esetén a partnernek a fenntartott példányokat kell megvásárolnia az ügyfél Azure Portal. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Fenntartott példányok vásárlása EA-előfizetéshez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.

3. Válassza a **vásárlás most** lehetőséget, majd az **Azure VMware-megoldás** elemet.

4. Adja meg a kötelező mezőket. Az Azure VMware-megoldás gazdagépeit futtató kiválasztott attribútumok megfelelnek a foglalási kedvezménynek.  Az attribútumok közé tartozik a termékváltozat, (adott esetben) a régiók és a hatókör. A foglalás hatóköre megadja a foglalási kedvezmények alkalmazásának helyét.

   Ha nagyvállalati szerződéssel rendelkezik, a **továbbiak hozzáadása lehetőséggel** gyorsan hozzáadhat példányokat. A beállítás más előfizetési típusok esetében nem érhető el.

   | Mező        |  Leírás |
   | ------------ | ------------ |
   | Előfizetés | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), Microsoft Customer Agreement vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak az Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait az előfizetés bankkártyája vagy számlás fizetési mód alapján számítjuk fel. |
   | Hatókör        | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja:<br><ul><li><b>Egy erőforráscsoport hatóköre</b> – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li><b>Egyszeri előfizetés hatóköre</b> – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li><b>Megosztott hatókör</b> – a foglalási kedvezményt a megfelelő, a számlázási környezetben található jogosult előfizetésekben lévő erőforrásokra alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>       |
   | Region       | A foglalás által érintett Azure-régió.   |
   | Gazdagép mérete    | AV36    |
   | Időszak         | Egy vagy három év.  |
   | Mennyiség     | A foglaláson belül megvásárolni kívánt példányok száma. A mennyiség azon Azure VMware-megoldási gazdagépek száma, amelyek beszerezhetik a számlázási kedvezményt.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Fenntartott példányok vásárlása CSP-előfizetéshez

Azokat a CSP-ket, amelyeknek fenntartott példányokat kívánnak vásárolni ügyfeleiknek, a [partner Center dokumentációjában](/partner-center/azure-plan-manage)kell használniuk a (AOBO) eljárás **adminisztrátorát** . További információkért tekintse meg a [(z) (AOBO) Videójának adminisztrátorát](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com).

2. Válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.

3. Bontsa ki az ügyfél adatait, és válassza a **a Microsoft Azure felügyeleti portálja** lehetőséget. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft partner Center – ügyfelek" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. A Azure Portal válassza a **minden szolgáltatás**  >  **foglalás** lehetőséget.

5. Válassza a **vásárlás most** lehetőséget, majd az **Azure VMware-megoldás** elemet.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure Portal foglalások" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Adja meg a kötelező mezőket. Az Azure VMware-megoldás gazdagépeit futtató kiválasztott attribútumok megfelelnek a foglalási kedvezménynek.  Az attribútumok közé tartozik a termékváltozat, (adott esetben) a régiók és a hatókör. A foglalás hatóköre megadja a foglalási kedvezmények alkalmazásának helyét.

   | Mező        |  Leírás |
   | ------------ | ------------ |
   | Előfizetés | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának jogosultnak kell lennie, amely ebben az esetben CSP-előfizetés|
   | Hatókör        | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja:<br><ul><li><b>Egy erőforráscsoport hatóköre</b> – a foglalási kedvezményt a kiválasztott erőforráscsoport megfelelő erőforrásaira alkalmazza.</li><li><b>Egyszeri előfizetés hatóköre</b> – a foglalási kedvezményt a kiválasztott előfizetésben lévő megfelelő erőforrásokra alkalmazza.</li><li><b>Megosztott hatókör</b> – a foglalási kedvezményt a megfelelő, a számlázási környezetben található jogosult előfizetésekben lévő erőforrásokra alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul>       |
   | Region       | A foglalás által érintett Azure-régió.   |
   | Gazdagép mérete    | AV36    |
   | Időszak         | Egy vagy három év.  |
   | Mennyiség     | A foglaláson belül megvásárolni kívánt példányok száma. A mennyiség azon Azure VMware-megoldási gazdagépek száma, amelyek beszerezhetik a számlázási kedvezményt.     |

Ha többet szeretne megtudni arról, hogyan tekintheti meg az ügyfél megvásárolt foglalásait, tekintse meg az [Azure-foglalások felhőalapú megoldás-szolgáltatóként (CSP) történő megtekintését](../cost-management-billing/reservations/how-to-view-csp-reservations.md) ismertető cikket.

## <a name="usage-data-and-reservation-usage"></a>Használati adatok és foglalások használata

A foglalási kedvezményt lefoglaló használat nulla értékű. Láthatja, hogy melyik Azure VMware Solution-példány fogadta el az egyes foglalások foglalási kedvezményét.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatokban:

- Nagyvállalati szerződéssel rendelkező ügyfelek esetében lásd: az [Azure foglalás használatának ismertetése a vállalati regisztrációhoz](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Egyéni előfizetések esetén lásd: az [Azure foglalás használatának ismertetése](../cost-management-billing/reservations/understand-reserved-instance-usage.md) az utólagos elszámolású előfizetéshez

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása a vásárlás után

A módosításokat a vásárlás után is elvégezheti a foglalás után:

-   Foglalás hatókörének frissítése

-   Példány méretének rugalmassága (ha van ilyen)

-   Tulajdonjog

A foglalásokat kisebb adattömbökre vagy egyesítési foglalásokra is kioszthatja. A módosítások egyike sem eredményez új kereskedelmi tranzakciót, vagy nem módosítja a foglalás befejezési dátumát.

A CSP által felügyelt foglalásokkal kapcsolatos részletekért lásd: [Microsoft Azure foglalások eladása a partner Center, a Azure Portal vagy az API-kat használó ügyfeleknek](/partner-center/azure-reservations).



>[!NOTE]
>A foglalás megvásárlása után a következő típusú módosításokat nem fogja tudni közvetlenül végezni:
>
> - Egy meglévő foglalás régiója
> - Termékváltozat
> - Mennyiség
> - Időtartam
>
>Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére* .

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

A kriptográfiai szolgáltatók lemondják, cserélik vagy visszafizetik a foglalásokat, és bizonyos korlátozásokkal megvásárolhatják az ügyfelet. További információ: [Microsoft Azure foglalások kezelése, megszakítása, cseréje vagy visszatérítése az ügyfelek](/partner-center/azure-reservations-manage)számára.

## <a name="next-steps"></a>Következő lépések

Most, hogy már megvásárolta az Azure VMware-megoldás fenntartott példányának megvásárlását, érdemes megismernie az alábbiakat:

- [Azure-beli VMware-megoldás értékelésének létrehozása](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [DHCP kezelése Azure VMware-megoldáshoz](manage-dhcp.md).
- [Azure VMware-megoldás virtuális gépek életciklus-kezelése](lifecycle-management-of-azure-vmware-solution-vms.md).
 