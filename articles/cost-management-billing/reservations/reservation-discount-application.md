---
title: Az Azure-beli foglalási kedvezmény alkalmazása
description: Ez a cikk a fenntartott példányokra vonatkozó kedvezmények általános alkalmazásával kapcsolatos információkat tartalmaz.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574872"
---
# <a name="how-a-reservation-discount-is-applied"></a>A foglalási kedvezmény alkalmazása

Ez a cikk a fenntartott példányokra vonatkozó kedvezmények általános alkalmazásával kapcsolatos információkat tartalmaz. A foglalási kedvezmény a foglalás megvásárlásakor kiválasztott jellemzőkkel egyező erőforrás-használatra érvényes. Ebbe beletartozik az a hatókör, amelyben az egyező virtuális gépek, SQL Database-adatbázisok, Azure Cosmos DB és más erőforrások futnak. Ha például az USA nyugati régiójában szeretne foglalási kedvezményt négy Standard D2 virtuális gépre, akkor azt az előfizetést válassza ki, amelyben a virtuális gépek futnak.

A foglalási kedvezmény csak akkor érvényes, ha *folyamatosan igénybe veszi*. Ha egy bizonyos órában nem rendelkezik egyező erőforrásokkal, akkor arra az órára elveszíti a foglalási mennyiséget. A lefoglalt, de fel nem használt órák nem vihetők tovább.

Egy erőforrás leállításakor a rendszer a foglalási kedvezményt automatikusan a megadott hatókör egy másik egyező erőforrására alkalmazza. Ha nem találhatók egyező erőforrások a megadott hatókörben, akkor a lefoglalt órák *elvesznek*.

Előfordulhat például, hogy később létrehoz egy erőforrást, és rendelkezni fog egy egyező, kihasználatlan erőforrással. A rendszer automatikusan az új egyező erőforrásra alkalmazza a foglalási kedvezményt.

Ha a virtuális gépek eltérő előfizetésben futnak a regisztráción vagy fiókon belül, akkor a hatókört megosztottként állítsa be. A megosztott hatókör lehetővé teszi a foglalási kedvezmény több előfizetésre történő alkalmazását. Foglalásvásárlás után módosíthatja a hatókört. További információkért tekintse meg [az Azure-foglalások kezelését](manage-reserved-vm-instance.md) ismertető cikket.

A foglalási kedvezmény csak nagyvállalati, Microsoft-ügyfélszerződéses, CSP- vagy használatalapú díjas előfizetésekhez társított erőforrásokra érvényes. A más ajánlattípusokkal rendelkező előfizetésekben futó erőforrásokra nem érvényes a foglalási kedvezmény.

## <a name="when-the-reservation-term-expires"></a>A foglalási időszak lejárata

A foglalási időszak végén a számlázási kedvezmény lejár, és az erőforrások számlázása használatalapú díjjal történik. Alapértelmezés szerint a foglalások automatikus megújítása nincs beállítva. A megújítási beállításokban elérhető lehetőség kiválasztásával engedélyezheti a foglalások automatikus megújítását. Az automatikus megújítás esetén a rendszer a meglévő foglalás lejáratakor megvásárol egy cserefoglalást. Alapértelmezés szerint a cserefoglalás ugyanolyan jellemzőkkel rendelkezik, mint a lejáró foglalás. Igény szerint módosíthatja a számlázási gyakoriságot, az időszakot vagy a mennyiséget a megújítási beállításokban. A megújítást bármely felhasználó beállíthatja, aki tulajdonosi hozzáféréssel rendelkezik a foglaláshoz, valamint a számlázáshoz használt előfizetéshez.  

## <a name="discount-applies-to-different-sizes"></a>A kedvezmény különböző méretekre érvényes

Foglalás vásárlásakor a foglalás alkalmazható az ugyanabban a méretcsoportban található attribútumokkal rendelkező más példányokra. Ezt a jellemzőt példányméret-rugalmasságnak nevezik. A kedvezmény általi lefedettség rugalmassága a foglalás típusától és a foglalás megvásárlásakor kiválasztott attribútumoktól függ.

Szolgáltatáscsomagok:

- Reserved VM Instances: Ha a foglalás megvásárlásakor kiválasztja a **Példányméret-rugalmasságra optimalizálva** beállítást, akkor a kedvezmény általi lefedettség a kiválasztott virtuálisgép-mérettől függ. A foglalást az ugyanabban a méretsorozat-csoportban található virtuálisgép-méretekre lehet alkalmazni. További információkért tekintse meg [a Reserved VM Instances virtuális gépeinek méretrugalmasságát](../../virtual-machines/reserved-vm-instance-size-flexibility.md) ismertető cikket.
- Fenntartott kapacitás az Azure Storage-ban: A standard Azure Storage-fiókoknak havi 100 TiB-os vagy 1 PiB-os egységben vásárolhat lefoglalt kapacitást. További információ az Azure Storage-beli fenntartott kapacitást támogató régiókról: [Blokkblobok díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/). A Fenntartott kapacitás az Azure Storage-ban minden hozzáférési szinthez (gyakori, ritka és archív) és replikációs konfigurációhoz (LRS, GRS és ZRS) elérhető.
- Fenntartott SQL Database-kapacitás: A kedvezmény általi lefedettség a kiválasztott teljesítményszinttől függ. További információkért lásd [az Azure-beli foglalási kedvezmény alkalmazását](understand-reservation-charges.md) ismertető cikket.
- Fenntartott Azure Cosmos DB-kapacitás: A kedvezmény általi lefedettség a kiosztott átviteli sebességtől függ. További információkért lásd [az Azure Cosmos DB-beli foglalási kedvezmény alkalmazását](understand-cosmosdb-reservation-charges.md) ismertető cikket.

## <a name="how-discounts-apply-to-specific-azure-services"></a>A kedvezmények alkalmazása egyes Azure-szolgáltatásokra

Olvassa el az Ön által használt szolgáltatásokra vonatkozó cikkeket, amelyekből megtudhatja, hogyan érvényesülnek a kedvezmények egy-egy Azure-szolgáltatáson:

- [APP SERVICE](reservation-discount-app-service.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Azure Database for MySQL](understand-reservation-charges-mysql.md)
- [Azure Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedikált gazdagépek](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Szoftvercsomagok](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Virtuális gépek](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
- [A foglalási használat ismertetése használatalapú fizetéses előfizetés esetén](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A Reservations díjában nem szereplő Windows-szoftverköltségek](reserved-instance-windows-software-costs.md)