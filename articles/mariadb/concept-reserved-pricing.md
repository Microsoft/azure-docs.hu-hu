---
title: Előre fizetés a számításhoz fenntartott kapacitással – Azure Database for MariaDB
description: Előre fizetés Azure Database for MariaDB számítási erőforrások számára fenntartott kapacitással
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 462ba0ccbd5d7e7048c2c7fcb9c5bece04adaebe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661763"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Előre fizetés Azure Database for MariaDB számítási erőforrások számára fenntartott kapacitással

A Azure Database for MariaDB mostantól segít pénzt megtakarítani a számítási erőforrások előfizetésével, az utólagos elszámolású árakhoz képest. A Azure Database for MariaDB fenntartott kapacitással a MariaDB-kiszolgálókon egy-egy vagy három éves időszakra vonatkozó előzetes kötelezettségvállalás áll rendelkezésre, hogy jelentős kedvezményt kapjon a számítási költségek. Azure Database for MariaDB fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést. </br>

A foglalást nem kell hozzárendelni adott Azure Database for MariaDB kiszolgálókhoz. Egy már futó Azure Database for MariaDB vagy újonnan üzembe helyezett szolgáltatás automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával előre fizet a számítási költségekre egy vagy három év alatt. A foglalás megvásárlása után az Azure Database for MariaDB számítási díjait a foglalási attribútumoknak megfelelően már nem számítjuk fel az utólagos elszámolású díjszabás szerint. A foglalások nem fedik le a MariaDB adatbázis-kiszolgálóhoz kapcsolódó szoftver-, hálózatkezelési és tárolási díjakat. A foglalási időszak végén a számlázási juttatás lejár, a Azure Database for MariaDB pedig az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg a [Azure Database for MariaDB fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/mariadb/). </br>

A [Azure Portal](https://portal.azure.com/)Azure Database for MariaDB fenntartott kapacitást is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Database for MariaDB fenntartott kapacitást. </br>

Az ügyfelek és az utólagos elszámolású ügyfelek díjszabása a foglalások beszerzése után: az [Azure foglalási szolgáltatás használatának megismerése a nagyvállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , valamint az [Azure-foglalás használatának](../cost-management-billing/reservations/understand-reserved-instance-usage.md)ismertetése az utólagos elszámolású előfizetéshez.


## <a name="determine-the-right-server-size-before-purchase"></a>A kiszolgáló megfelelő méretének meghatározása a vásárlás előtt

A foglalások méretének a meglévő vagy hamarosan üzembe helyezett adatbázisok egy adott régión belüli, illetve ugyanazon teljesítményszint és hardveres generáció használatával felhasznált számítási mennyiségén kell alapulnia.</br>

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 32 virtuális mag MariaDB-adatbázist, és két memóriát optimalizált, Gen5 – 16 virtuális mag MariaDB adatbázist. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célt, a Gen5 – 32 virtuális mag adatbázis-kiszolgálót és egy, a Gen5 – 16 virtuális mag adatbázis-kiszolgálót. Tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben vásárolnia kell egy 64 (2x32) virtuális mag, 1 éves foglalást az Egyadatbázisos általános célú Gen5 és a 48 (2x16 + 16) virtuális mag 1 év foglalása egyetlen adatbázishoz optimalizált memória esetén – Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Azure Database for MariaDB fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3.  Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Azure Database for MariaDB** lehetőséget a MariaDB-adatbázisok új foglalásának megvásárlásához.
4.  Töltse ki a kötelező mezőket. Meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó Azure Database for MariaDB kiszolgálók tényleges száma függ a hatókörtől és a kiválasztott mennyiségtől.


![A fenntartott díjszabás áttekintése](media/concepts-reserved-pricing/mariadb-reserved-price.png)


A következő táblázat a kötelező mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | A Azure Database for MariaDB fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a Azure Database for MariaDB fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.
| Hatókör | A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: </br></br> **Megosztva** a virtuális mag foglalási kedvezményt a számlázási környezetben lévő előfizetésekben futó Azure Database for MariaDB-kiszolgálókra alkalmazza a rendszer. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyszeri előfizetés** esetén a virtuális mag foglalási kedvezményt az előfizetésben lévő Azure Database for MariaDB-kiszolgálókra alkalmazza a rendszer. </br></br> **Egy erőforráscsoport**, a foglalási kedvezményt a rendszer a kijelölt előfizetésben található Azure Database for MariaDB-kiszolgálókra és az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz alkalmazza.
| Region | Az Azure Database for MariaDB fenntartott kapacitás foglalása által érintett Azure-régió.
| Központi telepítés típusa | Az a Azure Database for MariaDB erőforrástípus, amelyre a foglalást megvásárolni szeretné.
| Teljesítményszint | A Azure Database for MariaDB-kiszolgálók szolgáltatási szintje.
| Időszak | Egy év
| Mennyiség | A Azure Database for MariaDB fenntartott kapacitás foglalása keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például olyan Azure Database for MariaDB-kiszolgálókat futtat, amelyeken az USA keleti régiójában az Gen5 16 virtuális mag teljes számítási kapacitása fut, akkor az összes kiszolgáló előnyének maximalizálása érdekében a 16-as mennyiséget kell megadnia.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. 

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezmény automatikusan érvényes azon Azure Database for MariaDB-kiszolgálók számára, amelyek megfelelnek az Azure Database for MariaDB fenntartott kapacitás foglalási hatókörének és attribútumainak. A MariaDB fenntartott kapacitás foglalásának hatókörét a Azure Portal, a PowerShell, a CLI vagy az API segítségével frissítheti. </br></br>
A Azure Database for MariaDB fenntartott kapacitás kezelésével kapcsolatos információkért lásd: Azure Database for MariaDB fenntartott kapacitás kezelése.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mi a Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reservation-charges-mariadb.md)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)