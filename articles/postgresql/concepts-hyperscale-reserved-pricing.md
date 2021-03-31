---
title: Fenntartott számítási díjszabás – Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: Előre fizetés Azure Database for PostgreSQL-nagy kapacitású (Citus) számítási erőforrások számára fenntartott kapacitással.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600731"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Előre fizetés a Azure Database for PostgreSQL-nagy kapacitású (Citus) számítási erőforrások számára fenntartott kapacitással

Azure Database for PostgreSQL – nagy kapacitású (Citus) mostantól lehetővé teszi a pénz megtakarítását a számítási erőforrások előre fizetett költségeivel, az utólagos elszámolású árakhoz képest. A nagy kapacitású (Citus) fenntartott kapacitással egy-vagy hároméves időszakra vonatkozó előzetes kötelezettségvállalást hozhat a nagy kapacitású-(Citus-) kiszolgálócsoport számára, hogy jelentős kedvezményt kapjon a számítási költségekkel kapcsolatban. A nagy kapacitású (Citus) számára fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a foglalási időszakot és a számlázási gyakoriságot.

> [!IMPORTANT]
> Ez a cikk a Azure Database for PostgreSQL – nagy kapacitású (Citus) számára fenntartott kapacitásról szól. Az Azure Database for PostgreSQL – egyetlen kiszolgáló számára fenntartott kapacitással kapcsolatos információkért lásd: [előre fizetés a Azure Database for PostgreSQL – egykiszolgálós számítási erőforrások számára fenntartott kapacitással](./concept-reserved-pricing.md).

A foglalást nem kell hozzárendelni adott nagy kapacitású-(Citus-) kiszolgálócsoportokhöz. Egy már futó nagy kapacitású-(Citus-) kiszolgálócsoport vagy újonnan üzembe helyezett szolgáltatás automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával egy vagy három évre előre fizet a számítási költségekért. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő nagy kapacitású-(Citus-) számítási díjakra az utólagos elszámolású díjszabásban már nem számítunk fel díjat. 

A foglalások nem fedik le a nagy kapacitású-(Citus-) kiszolgálócsoportokhöz kapcsolódó szoftver-, hálózatkezelési és tárolási díjakat. A foglalási időszak végén a számlázási juttatás lejár, és a nagy kapacitású (Citus) kiszolgálói csoportok díjait az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem újítják meg az előfizetést. A díjszabással kapcsolatos információkért tekintse meg a [Azure Database for PostgreSQL – nagy kapacitású (Citus) fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

A [Azure Portal](https://portal.azure.com/)foglalt nagy kapacitású (Citus) fenntartott kapacitást is megvásárolhatja. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/prepare-buy-reservation.md). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati Szerződés (EA) vagy egyéni előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati Szerződés-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadását** az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetésben Nagyvállalati Szerződés rendszergazdának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg a nagy kapacitású (Citus) fenntartott kapacitást.

További információ arról, Nagyvállalati Szerződés hogy az ügyfelek és az utólagos elszámolású ügyfelek hogyan számítanak fel díjat a foglalás megvásárlásakor:
- [Az Azure foglalás használatának ismertetése a Nagyvállalati Szerződés-regisztrációhoz](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Az Azure foglalás használatának ismertetése az utólagos elszámolású előfizetéshez](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>A kiszolgáló csoport megfelelő méretének meghatározása a vásárlás előtt

A foglalás mérete a nagy kapacitású-(Citus-) kiszolgálócsoport meglévő vagy hamarosan üzembe helyezett koordinátora és munkavégző csomópontjai által a megadott régión belül használt teljes számítási mennyiségen alapul.

Tegyük fel például, hogy egy nagy kapacitású-(Citus-) kiszolgáló csoportot futtat 16 virtuális mag-koordinátorral és 3 8 virtuális mag Worker-csomóponttal. Továbbá tegyük fel, hogy a következő hónapban egy további nagy kapacitású-(Citus-) kiszolgáló csoportot tervez üzembe helyezni egy 32 virtuális mag-koordinátorral és 2 4 virtuális mag Worker-csomóponttal. Tegyük fel, hogy legalább egy évig szüksége van ezekre az erőforrásokra.

Ebben az esetben vásároljon egy éves foglalást a következőhöz:

* Összesen 16 virtuális mag + 32 virtuális mag = 48 virtuális mag a koordinátori csomópontokhoz
* Összesen 3 csomópont x 8 virtuális mag + 2 csomópont x 4 virtuális mag = 24 + 8 = 32 virtuális mag a munkavégző csomópontok számára

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget. A **vásárlási foglalások** ablaktáblán válassza a **Azure Database for PostgreSQL** lehetőséget a PostgreSQL-adatbázisok új foglalásának megvásárlásához.
1. Válassza ki a megvásárolni kívánt **nagy kapacitású (Citus) számítási** típust, majd kattintson a **kiválasztás** elemre.
1. Tekintse át a kiválasztott számítási típus mennyiségét a **Products (termékek** ) lapon.
1. A vásárlás befejezéséhez folytassa a vásárlás **+ Áttekintés** lapon.

A következő táblázat a kötelező mezőket ismerteti.

| Mező        | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Előfizetés | A Azure Database for PostgreSQL fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a Azure Database for PostgreSQL fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának Nagyvállalati Szerződésnak kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabású egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati Szerződés-előfizetés esetén a díjakat a beléptetési Azure-előfizetésből (korábban pénzügyi kötelezettségvállalásnak nevezett) egyenlegből, vagy a túlterhelésnek megfelelően kell levonni. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.                                                                                  |
| Hatókör        | A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a **megosztott** lehetőséget választja, a virtuális mag foglalási kedvezményt a számlázási környezetében lévő előfizetésekben futó nagy kapacitású-(Citus-) kiszolgálócsoportok esetében alkalmazza a rendszer. Nagyvállalati Szerződés ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. Az utólagos elszámolású ügyfelek esetében a megosztott hatókör a fiók rendszergazdája által létrehozott utólagos elszámolású előfizetések. Ha az **egyszeri előfizetést** választja, a virtuális mag foglalási kedvezményt a rendszer az előfizetésben található nagy kapacitású-(Citus-) kiszolgálócsoportok esetében alkalmazza. Ha **egyetlen erőforráscsoportot** választ, a foglalási kedvezményt a rendszer a kijelölt előfizetésben található nagy kapacitású (Citus), a kiválasztott erőforráscsoporthoz pedig az adott előfizetésen belül alkalmazza. |
| Region       | Az Azure-régió, amelyre a Azure Database for PostgreSQL – nagy kapacitású (Citus) foglalt kapacitás foglalása vonatkozik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Időszak         | Egy vagy három év.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Mennyiség     | A nagy kapacitású (Citus) foglalt kapacitás foglalása során megvásárolt számítási erőforrások mennyisége. Különösen a kijelölt Azure-régió koordinátorának vagy munkavégző csomópontjának a virtuális mag száma, amely a számlázási kedvezményt kapja. Ha például futtatja (vagy tervezi futtatni) a nagy kapacitású (Citus) kiszolgálói csoportokat az USA keleti régiójában az 64 koordinátor csomópont virtuális mag és 32 Worker Node virtuális mag teljes számítási kapacitásával, akkor az összes kiszolgáló előnyének maximalizálása érdekében adja meg a mennyiségét 64-ként és 32-ként a koordinátor és a munkavégző csomópontok számára.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információ: [önkiszolgáló cserék és visszatérítések az Azure-foglalásokhoz](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segítséget nyújt a koordinátorok és a munkavégző csomópontok egy adott régión belüli vertikális felskálázásához, a fenntartott kapacitási juttatás elvesztése nélkül.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezményt a rendszer automatikusan alkalmazza azon nagy kapacitású-(Citus-) kiszolgálócsoportok számára, amelyek megfelelnek a Azure Database for PostgreSQL fenntartott kapacitás foglalási hatókörének és attribútumainak. A Azure Database for PostgreSQL – nagy kapacitású (Citus) fenntartott kapacitás foglalásának hatókörét a Azure Portal, a PowerShell, az Azure CLI vagy az API használatával frissítheti.

Az Azure-beli foglalásokról az alábbi cikkek nyújtanak további tudnivalókat:

* [Mik az Azure-beli foglalások?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure-beli foglalások kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Az Azure foglalási kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [A Nagyvállalati Szerződés-regisztráció foglalási használatának ismertetése](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-foglalások a partner Center felhőalapú megoldás-szolgáltató programjában](/partner-center/azure-reservations)