---
title: Előre fizetés az Azure dedikált gazdagépek számára a pénz megtakarítása érdekében
description: Megtudhatja, hogyan vásárolhat Azure dedikált gazdagépeket fenntartott példányokat a számítási költségek mentéséhez.
services: virtual-machines
author: yashar
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: 1450f03ae72e79daca5a5ae2c04506e6bee5180b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672286"
---
# <a name="save-costs-with-azure-dedicated-host-reservations"></a>Költségek megtakarítása az Azure dedikált gazdagép foglalásával

Ha az Azure dedikált gazdagépek fenntartott példányát véglegesíti, akkor pénzt takaríthat meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörhöz és attribútumokhoz tartozó dedikált gazdagépek számára. A kedvezmények beszerzéséhez nem szükséges foglalást hozzárendelni egy dedikált gazdagéphez. A fenntartott példányok vásárlása csak a használat számítási részét fedi le, és a licencelési költségeket is magában foglalja. Tekintse [meg a virtuális gépekhez készült Azure dedikált gazdagépek áttekintését](./dedicated-hosts.md).

## <a name="determine-the-right-dedicated-host-sku-before-you-buy"></a>A megfelelő dedikált gazdagép SKU meghatározása a vásárlás előtt


A foglalás megvásárlása előtt meg kell határoznia, hogy melyik dedikált gazdagépre van szüksége. A virtuálisgép-sorozatot és-típust jelképező dedikált gazdagéphez SKU van definiálva. 

A virtuális gép sorozatának azonosításához a [Windows rendszerű virtuális gépek](./sizes.md) és a [Linux](./sizes.md) által támogatott méretek közül válassza az első lépéseket.

Ezt követően győződjön meg arról, hogy az Azure dedikált gazdagépeken támogatott. Az [Azure dedikált gazdagépek díjszabási](https://aka.ms/ADHPricing) oldalán megtalálhatók a dedikált gazdagépek, a CPU-adatok és a különböző díjszabási lehetőségek (beleértve a fenntartott példányokat is) teljes listáját.

Számos olyan SKU-t találhat, amely támogatja a VM-sorozatokat (különböző típusokkal). A gazdagép kapacitásának összehasonlításával azonosítsa a legjobb SKU-t (vCPU száma). Vegye figyelembe, hogy a foglalást több dedikált gazdagépre is alkalmazhatja, amelyek ugyanazt a virtuálisgép-sorozatot támogatják (például DSv3_Type1 és DSv3_Type2), de nem a különböző virtuálisgép-sorozatokban (például DSv3 és ESv3).



## <a name="purchase-restriction-considerations"></a>Vásárlási korlátozási megfontolások

A fenntartott példányok a legtöbb dedikált gazdagép méretéhez érhetők el, néhány kivétellel.

A foglalási kedvezmények nem vonatkoznak a következőkre:

- **Felhők** – a foglalások nem vásárolhatók meg németországi vagy kínai régiókban.

- Nem **elegendő kvóta** – egy adott előfizetésre vonatkozó foglalásnak az új fenntartott példányra vonatkozó előfizetésben elérhető vCPU-kvótával kell rendelkeznie. Ha például a cél előfizetéshez 10 vCPU van korlátozva a DSv3 sorozathoz, akkor nem vásárolhat olyan foglalási dedikált gazdagépeket, amelyek támogatják ezt a sorozatot. A foglalások kvótájának keresése magában foglalja az előfizetésben már üzembe helyezett virtuális gépeket és dedikált gazdagépeket. A probléma megoldásához [kvóta-növelési kérelmet hozhat létre](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

- **Kapacitás korlátozásai** – ritka esetekben az Azure korlátozza az új foglalások beszerzését a dedikált gazdagépek részhalmaza számára a régió alacsony kapacitása miatt.

## <a name="buy-a-reservation"></a>Foglalás vásárlása

A [Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)vásárolhat egy Azure dedikált gazdagép-példány fenntartott példányát is.

A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md). Ezek a követelmények a fenntartott dedikált gazdagép-példány megvásárlására vonatkoznak:

- Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.

- Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása** beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.

- A Cloud Solution Provider (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza a **Minden szolgáltatás** \> **Foglalások** lehetőséget.

3. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson a **dedikált gazdagépek** elemre.

4. Töltse ki a kötelező mezőket. Dedikált gazdagépeken futó példányok futtatása, amelyek megfelelnek a kiválasztott attribútumoknak a foglalási kedvezmény beszerzéséhez. Az engedményt megkapó dedikált gazdagép-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.

Ha nagyvállalati szerződéssel rendelkezik, a további példányok gyors hozzáadásához használhatja a **továbbiak hozzáadása lehetőséget** is. A beállítás más előfizetési típusok esetében nem érhető el.

| **Mező**           | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Előfizetés        | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfél-szerződés vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak az Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben. |
| Hatókör               | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Region              | A foglalás által érintett Azure-régió.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Dedikált gazdagép mérete | A dedikált gazdagép-példányok mérete.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Időszak                | Egy vagy három év.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Mennyiség            | A foglalás keretében megvásárolt példányok száma. A mennyiség a számlázási kedvezményt elérő dedikált gazdagép-példányok száma.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

- **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.

- **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.

- **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.

## <a name="usage-data-and-reservation-utilization"></a>Használati adatok és foglalás kihasználtsága

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. Láthatja, hogy melyik virtuálisgép-példány fogadta el a foglalási kedvezményt az egyes foglalásokhoz.

További információ arról, hogyan jelennek meg a foglalási kedvezmények a használati adatok között: az [Azure foglalási szolgáltatás használatának ismertetése a nagyvállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , ha Ön EA-ügyfél. Ha egyéni előfizetéssel rendelkezik, tekintse meg [Az Azure foglalás használatának megismerése](../cost-management-billing/reservations/understand-reserved-instance-usage.md)az utólagos elszámolású előfizetéshez című témakört.

## <a name="change-a-reservation-after-purchase"></a>Foglalás módosítása a vásárlás után

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

- Foglalás hatókörének frissítése

- Példány méretének rugalmassága (ha van ilyen)

- Tulajdonjog

A foglalásokat feloszthatja kisebb adattömbökre, és összevonhatja a már felosztott foglalásokat is. A módosítások egyike sem eredményez új kereskedelmi tranzakciót, vagy nem módosítja a foglalás befejezési dátumát.

A vásárlás után a következő típusú módosításokat nem végezheti el közvetlenül:

- Egy meglévő foglalás régiója

- Termékváltozat

- Mennyiség

- Időtartam

Ha szeretné módosítani a módosításokat, lehetősége van a foglalások *cseréjére* .

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalások kezelésének megismerése érdekében tekintse meg [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md) szakaszt.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)

- [Azure-beli dedikált gazdagépek használata](./dedicated-hosts.md)

- [Dedikált gazdagépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [A Reservations kezelése az Azure-ban](../cost-management-billing/reservations/manage-reserved-vm-instance.md)

- [A foglalási kedvezmény alkalmazásának ismertetése](../cost-management-billing/manage/understand-vm-reservation-charges.md)

- [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)

- [A Reservations díjában nem szereplő Windows-szoftverköltségek](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)

- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)