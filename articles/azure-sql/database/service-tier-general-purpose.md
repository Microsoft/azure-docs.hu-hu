---
title: általános célú szolgáltatási szintet
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerkedjen meg a Azure SQL Database és az Azure SQL felügyelt példányának általános célú szolgáltatási szintjével.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85986641"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Általános célú Service réteg-Azure SQL Database és az Azure SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A virtuális mag-alapú vásárlási modell általános célú szolgáltatási rétegét a standard szintű szolgáltatási szintnek nevezzük a DTU-alapú vásárlási modellben. A virtuális mag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását lásd: [modellek és erőforrások vásárlása](purchasing-models.md).

A Azure SQL Database és az Azure SQL felügyelt példánya a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. 

A Azure SQL Database és az SQL felügyelt példánya két szolgáltatási szintet használ: 

- Általános célú
- Üzletileg kritikus

A Azure SQL Database egy harmadik szolgáltatási szintet is tartalmaz, amely jelenleg nem érhető el az Azure SQL felügyelt példányai esetében:

- Rugalmas skálázás

A általános célú szolgáltatási szintjéhez tartozó építészeti modell a számítás és a tárolás elkülönítésén alapul. Ez az építészeti modell az Azure Blob Storage magas rendelkezésre állására és megbízhatóságára támaszkodik, amely az adatbázis-fájlokat átlátható módon replikálja, és a mögöttes infrastruktúra meghibásodása esetén nem garantálja az adatvesztést.

Az alábbi ábrán a standard építészeti modell négy csomópontja látható a elkülönített számítási és tárolási rétegekkel.

![A számítás és a tárolás elkülönítése](./media/service-tier-general-purpose/general-purpose-service-tier.png)

A általános célú szolgáltatási réteg építészeti modellje két réteget biztosít:

- Egy állapot nélküli számítási réteg, amely futtatja a `sqlservr.exe` folyamatot, és csak átmeneti és gyorsítótárazott adatokból áll (például: terv gyorsítótár, puffer készlet, oszlopdiagram-készlet). Ezt az állapot nélküli csomópontot az Azure Service Fabric működteti, amely inicializálja a folyamatot, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik helyre.
- Az Azure Blob Storage-ban tárolt, adatbázis-fájlokat (. MDF/. ldf) tartalmazó állapot-nyilvántartó adatréteg. Az Azure Blob Storage garantálja, hogy az adatbázis-fájlokban elhelyezett összes rekord adatvesztést nem eredményez. Az Azure Storage beépített adatelérhetőséget/redundanciát biztosít, amely biztosítja, hogy a naplófájlban vagy az adatfájlban lévő lapok minden rekordja megmaradjon, még akkor is, ha a folyamat összeomlik.

Az adatbázismotor vagy az operációs rendszer frissítésekor az alapul szolgáló infrastruktúra egy része meghibásodik, vagy ha a folyamat valamilyen kritikus problémát észlel `sqlservr.exe` , akkor az Azure Service Fabric áthelyezi az állapot nélküli folyamatot egy másik állapot nélküli számítási csomópontra. A tartalék csomópontok készlete új számítási szolgáltatás futtatására vár, ha az elsődleges csomópont feladatátvétele a feladatátvételi idő csökkentése érdekében történik. Az Azure Storage-rétegben tárolt adatműveletek nem érintettek, és az adatfájlok és a naplófájlok az újonnan inicializált folyamathoz vannak csatolva. Ez a folyamat 99,99%-os rendelkezésre állást garantál, de lehetséges, hogy az átmeneti idő miatt a nagy teljesítményű számítási feladatokra is hatással lehet, és az új csomópont a hideg gyorsítótárral kezdődik.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet

A általános célú szolgáltatási szinten a Azure SQL Database és az Azure SQL felügyelt példányának alapértelmezett szolgáltatási szintje, amelyet az általános munkaterhelések többsége számára terveztek. Ha a legtöbb esetben egy teljes körűen felügyelt adatbázismotor 99,99%-os SLA-val, valamint 5 és 10 MS közötti tárolási késéssel rendelkezik, amely megfelel az Azure-beli virtuális gépek SQL Servernek a legtöbb esetben, a általános célú szint az Ön számára.

## <a name="next-steps"></a>Következő lépések

- Megkeresheti az [SQL felügyelt példányában](../managed-instance/resource-limits.md#service-tier-characteristics)található általános célú/standard csomag (magok, I/O, memória) erőforrás-jellemzőit (mag, I/O, memória [), valamint](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)a [virtuális mag-modellben](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) és a [DTU-modellben](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)található [virtuális mag](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) -modellben vagy rugalmas készletben.
- Ismerkedjen meg [üzletileg kritikus](service-tier-business-critical.md) -és [nagy kapacitású](service-tier-hyperscale.md) -szintekkel.
- A [Service Fabric](../../service-fabric/service-fabric-overview.md)megismerése.
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd az [üzletmenet folytonosságát](business-continuity-high-availability-disaster-recover-hadr-overview.md)ismertető témakört.
