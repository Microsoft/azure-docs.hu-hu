---
title: Fenntartott számítási díjszabás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Előre fizetés Azure Database for PostgreSQL számítási erőforrások számára fenntartott kapacitással
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735472"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Előre fizetés Azure Database for PostgreSQL – egykiszolgálós számítási erőforrások számára fenntartott kapacitással

A Azure Database for PostgreSQL mostantól segít pénzt megtakarítani a számítási erőforrások előfizetésével, az utólagos elszámolású árakhoz képest. Azure Database for PostgreSQL fenntartott kapacitással egy vagy három éves időszakra kiterjedően vállalt kötelezettséget vállal a PostgreSQL-kiszolgálón, hogy jelentős kedvezményt kapjon a számítási költségekkel szemben. Azure Database for PostgreSQL fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést. </br>

A foglalást nem kell hozzárendelni adott Azure Database for PostgreSQL kiszolgálókhoz. Egy már futó Azure Database for PostgreSQL (vagy újonnan telepített) automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával előre fizet a számítási költségekre egy vagy három év alatt. A foglalás megvásárlása után az Azure Database for PostgreSQL számítási díjait, amelyek megfelelnek a foglalási attribútumoknak, már nem számítjuk fel az utólagos elszámolású díjszabást. A foglalás nem vonatkozik a PostgreSQL-adatbázis-kiszolgálókhoz kapcsolódó szoftverekre, hálózatokra vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, a Azure Database for PostgreSQL pedig az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg a [Azure Database for PostgreSQL fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> A fenntartott kapacitás díjszabása a Azure Database for PostgreSQL [egykiszolgálós](./overview.md#azure-database-for-postgresql---single-server) és [nagy kapacitású-Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) üzembe helyezési lehetőségeiben érhető el. További információ a nagy kapacitású (Citus) RI díjszabásáról: [ezen az oldalon](concepts-hyperscale-reserved-pricing.md).

A [Azure Portal](https://portal.azure.com/)Azure Database for PostgreSQL fenntartott kapacitást is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Database for PostgreSQL fenntartott kapacitást. </br>

Az ügyfelek és az utólagos elszámolású ügyfelek díjszabása a foglalások beszerzése után: az [Azure foglalási szolgáltatás használatának megismerése a nagyvállalati beléptetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , valamint az [Azure-foglalás használatának](../cost-management-billing/reservations/understand-reserved-instance-usage.md)ismertetése az utólagos elszámolású előfizetéshez.


## <a name="determine-the-right-server-size-before-purchase"></a>A kiszolgáló megfelelő méretének meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett, adott régión belüli kiszolgálók által használt számítási mennyiségnek kell alapulnia, és ugyanazt a teljesítményszint és a hardver generációját kell használnia.</br>

Tegyük fel például, hogy egy általános célú Gen5 – 32 virtuális mag PostgreSQL-adatbázist futtat, és két memória-optimalizált Gen5 – 16 virtuális mag PostgreSQL-adatbázist használ. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célú Gen5 – 8 virtuális mag adatbázis-kiszolgálót és egy memória-optimalizált Gen5 – 32 virtuális mag adatbázis-kiszolgálót. Tegyük fel, hogy tudnia kell, hogy legalább egy évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben meg kell vásárolnia egy 40 (32 + 8) virtuális mag, egyéves foglalást az önálló adatbázisok általános célú Gen5 és a 64 (2x16 + 32) virtuális mag egyéves foglalással a különálló adatbázis-memóriára optimalizált-Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Azure Database for PostgreSQL** lehetőséget a PostgreSQL-adatbázisok új foglalásának megvásárlásához.
4. Töltse ki a kötelező mezőket. Meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó Azure Database for PostgreSQL kiszolgálók tényleges száma függ a hatókörtől és a kiválasztott mennyiségtől.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="A fenntartott díjszabás áttekintése":::


A következő táblázat a kötelező mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | A Azure Database for PostgreSQL fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a Azure Database for PostgreSQL fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.
| Hatókör | A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: </br></br> **Megosztva** a virtuális mag foglalási kedvezményt a számlázási környezetben lévő előfizetésekben futó Azure Database for PostgreSQL-kiszolgálókra alkalmazza a rendszer. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyszeri előfizetés** esetén a virtuális mag foglalási kedvezményt az előfizetésben lévő Azure Database for PostgreSQL-kiszolgálókra alkalmazza a rendszer. </br></br> **Egy erőforráscsoport**, a foglalási kedvezményt a rendszer a kijelölt előfizetésben található Azure Database for PostgreSQL-kiszolgálókra és az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz alkalmazza.
| Region | Az Azure Database for PostgreSQL fenntartott kapacitás foglalása által érintett Azure-régió.
| Központi telepítés típusa | Az a Azure Database for PostgreSQL erőforrástípus, amelyre a foglalást megvásárolni szeretné.
| Teljesítményszint | A Azure Database for PostgreSQL-kiszolgálók szolgáltatási szintje.
| Időszak | Egy év
| Mennyiség | A Azure Database for PostgreSQL fenntartott kapacitás foglalása keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például olyan Azure Database for PostgreSQL-kiszolgálókat futtat, amelyeken az USA keleti régiójában az Gen5 16 virtuális mag teljes számítási kapacitása fut, akkor az összes kiszolgáló előnyének maximalizálása érdekében a 16-as mennyiséget kell megadnia.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. Ha a fenntartott kapacitásnál magasabb virtuális mag méretezhető, akkor a felesleges virtuális mag az utólagos elszámolású díjszabás alapján számítjuk fel.


## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezmény automatikusan érvényes azon Azure Database for PostgreSQL-kiszolgálók számára, amelyek megfelelnek az Azure Database for PostgreSQL fenntartott kapacitás foglalási hatókörének és attribútumainak. A PostgreSQL-hez készült Azure-adatbázis hatókörét a Azure Portal, a PowerShell, a CLI vagy az API segítségével frissítheti.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mi a Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Az Azure Reservations kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](/partner-center/azure-reservations)