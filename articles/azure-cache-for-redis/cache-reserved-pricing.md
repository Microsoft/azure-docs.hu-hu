---
title: Előre fizetés a számításhoz fenntartott kapacitással – Azure cache a Redis
description: Előre fizetés az Azure cache számára a fenntartott kapacitású számítási erőforrások Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598546"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Előre fizetés az Azure cache számára a fenntartott kapacitású számítási erőforrások Redis

Az Azure cache for Redis mostantól lehetővé teszi a pénz megtakarítását a számítási erőforrások előre fizetett költségeivel az utólagos elszámolású árakhoz képest. Az Azure cache for Redis fenntartott kapacitás esetén egy vagy három éves időszakra vonatkozó előzetes kötelezettségvállalás a gyorsítótárban, hogy a számítási költségek jelentős kedvezményt kapjanak. Az Azure cache Redis fenntartott kapacitásának megvásárlásához meg kell adnia az Azure-régiót, a szolgáltatási szintet és a kifejezést.

A foglalást nem kell hozzárendelni adott Azure cache-hez a Redis-példányokhoz. A Redis vagy újonnan üzembe helyezett Azure cache már futtatva automatikusan kihasználja a fenntartott díjszabás előnyeit, a lefoglalt gyorsítótár méretétől függően. A foglalás megvásárlásával előre fizet a számítási költségekre egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő Redis számítási díjakra vonatkozó Azure cache-t már nem számoljuk el az utólagos elszámolású díjszabás alapján. A foglalás nem vonatkozik a gyorsítótárhoz társított hálózati vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és a Redis tartozó Azure cache számlázása az utólagos elszámolású díjszabás szerint történik. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg az [Azure cache Redis fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/cache).

Az Azure cache Redis fenntartott kapacitást is vásárolhat a [Azure Portalban](https://portal.azure.com/). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg az Azure cache-t a Redis fenntartott kapacitáshoz.

Annak részleteiről, hogy a nagyvállalati ügyfelek és az utólagos elszámolású ügyfelek hogyan számítanak fel díjat a foglalások beszerzéséhez, tekintse meg az [Azure foglalás használatának ismertetése a nagyvállalati beléptetéssel](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) és [Az Azure-foglalás használatának megismerése](../cost-management-billing/reservations/understand-reserved-instance-usage.md)az utólagos elszámolású előfizetések esetében.


## <a name="determine-the-right-cache-size-before-purchase"></a>A gyorsítótár megfelelő méretének meghatározása a vásárlás előtt

A foglalás méretét a meglévő, illetve a hamarosan üzembe helyezett gyorsítótárnak egy adott régión belül és ugyanazt a szolgáltatási szintet használó teljes mérete alapján kell megadnia.

Tegyük fel például, hogy két gyorsítótárat futtat – az egyiket 13 GB-ra, a másikat pedig 26 GB-ra. Legalább egy évig szükséges. Továbbá tegyük fel, hogy a meglévő 13 GB-os gyorsítótárait 26 GB-ra tervezi egy hónapig, hogy az megfeleljen a szezonális igényeknek, majd méretezze vissza. Ebben az esetben 1 P2 gyorsítótárat és 1 P3 gyorsítótárat vagy 3 P2 gyorsítótárat vásárolhat egy éves foglalásban a megtakarítás maximalizálása érdekében. Kedvezményt kap a fenntartott gyorsítótár-memória teljes mennyiségétől, attól függetlenül, hogy az adott összeg milyen mértékben van lefoglalva a gyorsítótárban.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure cache vásárlása a Redis fenntartott kapacitáshoz

Egy fenntartott VM-példányt vásárolhat a [Azure Portalban](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/). A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Redis Azure cache** lehetőséget, ha új foglalást szeretne vásárolni a gyorsítótárak számára.
4. Töltse ki a kötelező mezőket. Meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó Redis-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.


![A fenntartott díjszabás áttekintése](media/cache-reserved-pricing/cache-reserved-price.png)


A következő táblázat a kötelező mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | A Redis számára fenntartott kapacitás foglalásához az Azure cache-re való fizetéshez használt előfizetés. Az előfizetéshez tartozó fizetési módot az Azure cache Redis fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.
| Hatókör | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: </br></br> **Megosztva** a foglalási kedvezményt a rendszer az Azure cache-re alkalmazza a számlázási környezetben lévő előfizetésekben futó Redis-példányok esetében. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egy előfizetés** esetén a foglalási kedvezmény az Azure cache-re érvényes az előfizetéshez tartozó Redis-példányok esetében. </br></br> **Egy erőforráscsoport**, a foglalási kedvezményt a rendszer az Azure cache-re alkalmazza a kijelölt előfizetésben található Redis-példányok esetében, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoportot.
| Region | A Redis fenntartott kapacitás foglalásához az Azure cache által érintett Azure-régió.
| Tarifacsomag | A Redis-kiszolgálók Azure gyorsítótárának szolgáltatási szintje.
| Időszak | Egy vagy három év
| Mennyiség | Az Azure cache-ben megvásárolni kívánt számítási erőforrások mennyisége a Redis fenntartott kapacitás foglalásához. A mennyiség a kijelölt Azure-régióban és szolgáltatási szinten foglalt gyorsítótárak száma, amelyek foglalása folyamatban van, és a számlázási kedvezményt kapja. Ha például olyan Redis-kiszolgálókon futtatja az Azure cache-t, amelyeken az USA keleti régiójában a teljes gyorsítótár kapacitása összesen 26 GB, akkor olyan mennyiséget kell megadnia, amely 26 GB-ot biztosít az összes gyorsítótár előnyének maximalizálása érdekében. Ez lehet 1 P3 vagy 2 P2 gyorsítótár.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="cache-size-flexibility"></a>Gyorsítótár méretének rugalmassága

A gyorsítótár méretének rugalmassága segít a szolgáltatási szinten vagy régióban történő vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A foglalási kedvezményt a rendszer automatikusan alkalmazza az Azure gyorsítótárba olyan Redis-példányok esetében, amelyek megfelelnek a foglalási hatókörnek és attribútumoknak. A foglalás hatókörét a Azure Portal, a PowerShell, az Azure CLI vagy az API segítségével frissítheti.

*  Ha szeretné megtudni, hogyan alkalmazza a fenntartott kapacitást a Redis Azure cache-re, tekintse meg [Az Azure foglalási kedvezmény ismertetése](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) című témakört.

* Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

    * [Mi az az Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)