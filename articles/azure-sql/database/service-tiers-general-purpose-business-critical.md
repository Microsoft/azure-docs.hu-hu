---
title: Általános célú és üzleti szempontból kritikus szolgáltatási szintek
titleSuffix: Azure SQL Database & SQL Managed Instance
description: A cikk a Azure SQL Database és az Azure SQL felügyelt példánya által használt virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási rétegeit tárgyalja.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 12/14/2020
ms.openlocfilehash: 95e11e98be8a58611a435de533ffcc16ec5ce357
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048556"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database és az Azure SQL felügyelt példányok szolgáltatási szintjei
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példánya SQL Server adatbázismotor-architektúrán alapul, amely a felhőalapú környezet számára lett kialakítva, hogy 99,99%-os rendelkezésre állást biztosítson, még akkor is, ha van infrastruktúra-meghibásodás. A Azure SQL Database és az Azure SQL felügyelt példánya két szolgáltatási szintet használ, amelyek mindegyike más építészeti modellel rendelkezik. A szolgáltatási szintek a következők:

- [Általános célú](service-tier-general-purpose.md), amely költségvetés-alapú számítási feladatokhoz készült.
- [Üzleti szempontból kritikus fontosságú](service-tier-business-critical.md), amely kis késleltetésű számítási feladatokhoz készült, nagy rugalmassággal és gyors feladatátvételsel.

Azure SQL Database további szolgáltatási szintet tartalmaz: 

- A [nagy kapacitású](service-tier-hyperscale.md), amely a legtöbb üzleti számítási feladathoz lett tervezve, nagy mértékben méretezhető tárolást, olvasási felskálázást és gyors adatbázis-visszaállítási képességeket biztosít.

Ez a cikk a virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási szintjeinek a szolgáltatási szintek, a tárolási és a biztonsági mentési megfontolások közötti különbségeit tárgyalja.

## <a name="service-tier-comparison"></a>Szolgáltatási Rétegek összehasonlítása

A következő táblázat ismerteti a legújabb generációs szolgáltatási szintek közötti fő különbségeket (Gen5). Vegye figyelembe, hogy a szolgáltatási szintek jellemzői eltérőek lehetnek a SQL Database és az SQL felügyelt példányain.

|-| Erőforrás típusa | Általános célú |  Rugalmas skálázás | Üzletileg kritikus |
|:---:|:---:|:---:|:---:|:---:|
| **A következőkre alkalmas** | |  A költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál. | A legtöbb üzleti számítási feladat. A tárterület méretének automatikus skálázása 100 TB-ig, folyékony vertikális és horizontális számítási skálázás, gyors adatbázis-visszaállítás. | OLTP alkalmazások nagy tranzakciós sebességgel és alacsony IO-késéssel. Maximális rugalmasságot biztosít a hibák és a gyors feladatátvételek esetében, több szinkronban frissített replika használatával.|
|  **Erőforrás-típusban elérhető:** ||SQL Database/SQL felügyelt példány | Egyetlen Azure SQL Database | SQL Database/SQL felügyelt példány |
| **Számítási méret**| SQL Database | 1 – 80 virtuális mag | 1 – 80 virtuális mag | 1 – 80 virtuális mag |
| | SQL Managed Instance | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| | SQL felügyelt példányok készletei | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N.A. | N.A. |
| **Tárolási típus** | Mind | Prémium szintű távoli tárterület (/példány) | A leválasztott tárterület helyi SSD-gyorsítótárral (/példány) | Villámgyors helyi SSD-tároló (példány) |
| **Adatbázisméret** | SQL Database | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **Tárterület mérete** | SQL Database | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | SQL Managed Instance  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB mérete** | SQL Database | [32 GB/virtuális mag](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB/virtuális mag](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB/virtuális mag](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance  | [24 GB/virtuális mag](../managed-instance/resource-limits.md#service-tier-characteristics) | N/A | Legfeljebb 4 TB – [a tárterület mérete korlátozva](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Írási sebesség naplózása** | SQL Database | [1,875 MB/s/virtuális mag (max. 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s/s/virtuális mag (max. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | SQL Managed Instance | [3 MB/s/s virtuális mag (max. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/A | [4 MB/s/s virtuális mag (max. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Rendelkezésre állás**|Mind| 99.99% |  [99,95% egyetlen másodlagos replikával, 99,99%-kal több replikával](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [99,995% a zóna redundáns önálló adatbázisával](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Biztonsági másolatok**|Mind|RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap). Az alapszintű csomag maximális megőrzése 7 nap. | RA-GRS, 7 nap, állandó időpontra történő helyreállítás (PITR) | RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap) |
|**Memóriában tárolt OLTP** | | N.A. | N.A. | Elérhető |
|**Írásvédett replikák**| | 0 beépített <br> 0-4 [geo-replikáció](active-geo-replication-overview.md) használatával | 0-4 beépített | 1 beépített, árba belefoglalva <br> 0-4 [geo-replikáció](active-geo-replication-overview.md) használatával |
|**Díjszabás/számlázás** | SQL Database | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/single/) díja. <br/>A IOPS nem számítunk fel díjat. | [az egyes replikák és a felhasznált tárolók virtuális mag](https://azure.microsoft.com/pricing/details/sql-database/single/) számítunk fel díjat. <br/>A IOPS még nincs felszámítva. | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/single/) díja. <br/>A IOPS nem számítunk fel díjat. |
|| SQL Managed Instance | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/managed/) díja. <br/>A IOPS nem számítunk fel díjat| N/A | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/managed/) díja. <br/>A IOPS nem számítunk fel díjat.| 
|**Kedvezményes modellek**| | [Fenntartott példányok](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések| [Azure Hybrid Benefit](../azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések| [Fenntartott példányok](reserved-capacity-overview.md)<br/>[Azure Hybrid Benefit](../azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések|

További információkért tekintse meg a [Azure SQL Database (virtuális mag)](resource-limits-vcore-single-databases.md), az [egyetlen Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), a [készletezett Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md)és az [Azure SQL felügyelt példányok](../managed-instance/resource-limits.md) lapjai közötti részletes különbségeket.

> [!NOTE]
> További információ a nagy kapacitású szolgáltatási szintjéről a virtuális mag-alapú vásárlási modellben: [nagy kapacitású szolgáltatási réteg](service-tier-hyperscale.md). A virtuális mag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását lásd: [modellek és erőforrások vásárlása](purchasing-models.md).

## <a name="data-and-log-storage"></a>Adatkezelési és naplózási tár

Az alábbi tényezők befolyásolják az adatokhoz és naplófájlokhoz használt tárterület mennyiségét, és a általános célúra és üzletileg kritikusra vonatkoznak. A nagy kapacitású-ben tárolt adatokkal és naplózási tárolóval kapcsolatos részletekért lásd: [nagy kapacitású szolgáltatási szintje](service-tier-hyperscale.md).

- A lefoglalt tárterületet az adatfájlok (MDF) és a naplófájlok (LDF) használják.
- Minden egyes adatbázis számítási mérete támogatja a maximális adatbázis-méretet, amely az alapértelmezett maximális méret 32 GB.
- A szükséges Egyadatbázisos méret (az MDF-fájl mérete) beállításakor a rendszer automatikusan hozzáadja a további tárterületet az LDF-fájlok támogatásához.
- Az egyes adatbázisok mérete 10 GB és a támogatott maximális érték között választható ki.
  - A standard vagy általános célú szolgáltatási rétegekben történő tároláshoz a 10 GB-os növekmények méretének növelése vagy csökkentése.
  - A prémium vagy üzleti szempontból kritikus szolgáltatási rétegekben a méret növeléséhez vagy csökkentéséhez növelje vagy csökkentse a 250 GB-os növekményeket.
- Az általános célú szolgáltatási szinten `tempdb` egy csatolt SSD-t használ, és ez a tárolási költség a virtuális mag ár részét képezi.
- Az üzleti szempontból kritikus szolgáltatási szinten `tempdb` megosztja a csatlakoztatott SSD-t az MDF-és az ldf-fájlokkal, és a `tempdb` tárolási költséget a virtuális mag ára tartalmazza.
- A prémium szintű DTU a `tempdb` mellékelt SSD-t az MDF-és az ldf-fájlokkal osztja meg.
- A felügyelt SQL-példányok tárolási méretét a 32 GB-os többszörösében kell megadni.


> [!IMPORTANT]
> Az MDF-és az LDF-fájlokhoz lefoglalt teljes tárterületért kell fizetnie.

Az MDF-és az LDF-fájlok aktuális teljes méretének figyeléséhez használja a [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF-és LDF-fájlok aktuális méretének figyeléséhez használja a [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).

## <a name="backups-and-storage"></a>Biztonsági másolatok és tárolás

Az adatbázis biztonsági másolatainak tárolása a SQL Database és az SQL felügyelt példányának az időponthoz kötött visszaállításának (PITR) és [hosszú távú adatmegőrzési (ltr)](long-term-retention-overview.md) képességeinek támogatásához van lefoglalva. Ezt a tárolót külön kell lefoglalni az egyes adatbázisokhoz, és a számlázást két külön adatbázis-díj alapján számoljuk el.

- **PITR**: az egyes adatbázisok biztonsági másolatait a rendszer automatikusan átmásolja az [olvasási hozzáférésű geo-redundáns (ra-GRS) tárolóba](../../storage/common/geo-redundant-design.md) . A tárterület mérete dinamikusan növekszik, ahogy új biztonsági másolatok jönnek létre. A tárterületet hetente teljes biztonsági mentések, napi különbözeti biztonsági másolatok és tranzakciónapló-biztonsági másolatok használják, amelyek 5 percenként másolódnak át. A tárterület-felhasználás az adatbázis változásának és a biztonsági mentések megőrzési idejének a függvénye. Minden adatbázishoz külön megőrzési időtartamot állíthat be 7 és 35 nap között. Az adatbázis méretének 100 százalékkal (1x) megegyező minimális tárterülete külön díj nélkül elérhető. A legtöbb adatbázis esetében ez az érték elegendő a biztonsági másolatok 7 napos tárolására.
- **Ltr**: lehetősége van arra is, hogy a teljes biztonsági másolatok hosszú távú megőrzését akár 10 évig is konfigurálhatja az [SQL felügyelt példányai esetében](long-term-retention-overview.md). Ha LTR szabályzatot állít be, akkor ezeket a biztonsági másolatokat az RA-GRS tárolóban automatikusan tárolja a rendszer, de szabályozhatja, hogy a biztonsági másolatok milyen gyakran legyenek átmásolva. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A kiválasztott konfiguráció határozza meg, hogy mekkora tárterületet használ a rendszer a LTR biztonsági mentések esetén. A LTR-tároló költségének megbecsléséhez használhatja a LTR árképzési számológépet. További információ: [SQL Database hosszú távú adatmegőrzés](long-term-retention-overview.md).

## <a name="next-steps"></a>Következő lépések

Az általános célú és az üzleti szempontból kritikus szolgáltatási szinten elérhető konkrét számítási és tárolási méretek részletes ismertetését lásd: 

- [Azure SQL Database virtuális mag-alapú erőforrás-korlátai](resource-limits-vcore-single-databases.md).
- [virtuális mag-alapú erőforrás-korlátok a Azure SQL Database készletezett adatbázisaihoz](resource-limits-vcore-elastic-pools.md).
- [virtuális mag-alapú erőforrás-korlátok az Azure SQL felügyelt példányaihoz](../managed-instance/resource-limits.md).
