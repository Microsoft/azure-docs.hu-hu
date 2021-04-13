---
title: DTU erőforrás-korlátok önálló adatbázisok
description: Ez a lap a Azure SQL Database önálló adatbázisaihoz tartozó általános DTU-erőforrás-korlátokat ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/09/2021
ms.openlocfilehash: 3afc2a1e3b279e7c4fd350e96787454e01e01f79
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303219"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell használatával – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk részletes erőforrás-korlátozásokat tartalmaz Azure SQL Database önálló adatbázisok számára a DTU beszerzési modell használatával.

* A kiszolgálón található önálló adatbázisokra vonatkozó DTU megvásárlásával kapcsolatban lásd: a [kiszolgálók erőforrás-korlátainak áttekintése](resource-limits-logical-server.md).
* Az Azure SQL Database DTU beszerzési modellre vonatkozó korlátozásait lásd: a DTU erőforrás-korlátozásai az [önálló adatbázisok](resource-limits-dtu-single-databases.md) és a [DTU-erőforrások korlátai rugalmas készletek](resource-limits-dtu-elastic-pools.md).
* A virtuális mag erőforrás-korlátaival kapcsolatban lásd: virtuális mag-erőforrás korlátai [– Azure SQL Database](resource-limits-vcore-single-databases.md) és [virtuális mag erőforrás-korlátok – rugalmas készletek](resource-limits-vcore-elastic-pools.md).
* A különböző vásárlási modellekkel kapcsolatos további információkért lásd: [modellek és szolgáltatási szintek beszerzése](purchasing-models.md).

Minden írásvédett replika saját erőforrásokkal rendelkezik, például a DTU, a feldolgozókkal és a munkamenetekkel. Az egyes írásvédett replikák a cikk későbbi részében részletesen ismertetett erőforrás-korlátokra vonatkoznak. 


## <a name="single-database-storage-sizes-and-compute-sizes"></a>Önálló adatbázis: tárolási méretek és számítási méretek

A következő táblázatok az egyes szolgáltatási rétegekben és számítási méretekben elérhető erőforrásokat jelenítik meg az egyes adatbázisokhoz. Megadhatja a szolgáltatási szintet, a számítási méretet és a tárterület mennyiségét egyetlen adatbázishoz a következő használatával:

* [Transact-SQL](single-database-manage.md#transact-sql-t-sql) az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database) használatával
* [Azure Portal](single-database-manage.md#the-azure-portal)
* [PowerShell](single-database-manage.md#powershell)
* [Azure CLI](single-database-manage.md#the-azure-cli)
* [REST API](single-database-manage.md#rest-api)

> [!IMPORTANT]
> Az útmutatás és a megfontolások méretezésével kapcsolatban lásd: [önálló adatbázis skálázása](single-database-scale.md)

### <a name="basic-service-tier"></a>Alapszintű szolgáltatási szint

| **Számítási méret** | **Basic** |
| :--- | --: |
| DTU-k maximális száma | 5 |
| Belefoglalt tárterület (GB) | 2 |
| Maximális tárterület (GB) | 2 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) |N/A |
| Egyidejű feldolgozók maximális száma (kérelem) | 30 |
| Egyidejű munkamenetek maximális száma | 300 |
|||

> [!IMPORTANT]
> Az alapszintű szolgáltatási csomag kevesebb mint egy virtuális mag (CPU) biztosít.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott.
>
>Az adattárolásra vonatkozó alapszintű szolgáltatási szintet a standard oldal Blobokra helyezi. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier"></a>Standard szolgáltatási szint

| **Számítási méret** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU-k maximális száma | 10 | 20 | 50 | 100 |
| Foglalt tárterület (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Maximális tárterület (GB) | 250 | 250 | 250 | 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N.A. | N.A. | N.A. | N.A. |
| Egyidejű feldolgozók maximális száma (kérelem)| 60 | 90 | 120 | 200 |
| Egyidejű munkamenetek maximális száma |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/single/) .

> [!IMPORTANT]
> A standard S0, S1 és S2 szintjei kevesebb mint egy virtuális mag (CPU) biztosítanak.  A CPU-igényes számítási feladatokhoz az S3 vagy nagyobb szolgáltatási réteg ajánlott.
>
>Az adattárolással kapcsolatban a standard szintű S0 és az S1 szolgáltatási szint a standard oldal Blobokra kerül. A standard oldal Blobok a merevlemezes (HDD-) alapú tárolóeszközöket használják, és a leghatékonyabb fejlesztéshez, teszteléshez és más, ritkán használt számítási feladatokhoz, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

### <a name="standard-service-tier-continued"></a>Standard szintű szolgáltatási szint (folytatás)

| **Számítási méret** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU-k maximális száma | 200 | 400 | 800 | 1600 | 3000 |
| Foglalt tárterület (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Maximális tárterület (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | N.A. | N.A. | N.A. | N.A. |N.A. |
| Egyidejű feldolgozók maximális száma (kérelem)| 400 | 800 | 1600 | 3200 |6000 |
| Egyidejű munkamenetek maximális száma |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/single/) .

### <a name="premium-service-tier"></a>Prémium szolgáltatási szint

| **Számítási méret** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU-k maximális száma | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Foglalt tárterület (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maximális tárterület (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Memóriában tárolt OLTP-k maximális tárterülete (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Egyidejű feldolgozók maximális száma (kérelem)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Egyidejű munkamenetek maximális száma | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> a további kiépített tárterület miatt felmerülő további költségekkel kapcsolatos további információkért tekintse meg [SQL Database díjszabási lehetőségeit](https://azure.microsoft.com/pricing/details/sql-database/single/) .

<sup>2</sup> 1024 GB-ig 4096 GB-ig, 256 GB-os növekményekben.

> [!IMPORTANT]
> A prémium szinten több mint 1 TB tárterület jelenleg minden régióban elérhető, kivéve a következőket: Kelet-Kína, Észak-Kína, Közép-Németország és Németország északkeleti régiója. Ezekben a régiókban a prémium szintű Storage Max 1 TB-ra van korlátozva.  További információ: [P11-P15 current korlátozások](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).
> [!NOTE]
> A `tempdb` korlátokat lásd: [tempdb korlátok](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis virtuális mag erőforrás-korlátaival kapcsolatban lásd: [önálló adatbázisok erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-single-databases.md)
- A rugalmas készletek virtuális mag erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a virtuális mag beszerzési modell használatával](resource-limits-vcore-elastic-pools.md)
- A rugalmas készletek DTU erőforrás-korlátaival kapcsolatban lásd: [rugalmas készletek erőforrás-korlátai a DTU beszerzési modell használatával](resource-limits-dtu-elastic-pools.md)
- Az Azure SQL felügyelt példányain felügyelt példányok erőforrás-korlátaival kapcsolatban lásd: [SQL felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).
- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- A logikai SQL Server erőforrás-korlátaival kapcsolatos információkért lásd: a [logikai SQL Server erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) a kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért.