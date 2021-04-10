---
title: Kifejezések jegyzéke
titleSuffix: Azure SQL
description: A Azure SQL Database, az Azure SQL felügyelt példányának és az SQL Azure-beli virtuális gépen való használatának feltételeit tartalmazó Szószedet.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98249652"
---
# <a name="azure-sql-database-glossary-of-terms"></a>A kifejezések szószedetének Azure SQL Database
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Környezet|Időszak|További információ|
|:---|:---|:---|
|Azure-szolgáltatás|Azure SQL Database vagy SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|Vásárlási modell|DTU-alapú vásárlási modell|[DTU-alapú vásárlási modell](database/service-tiers-dtu.md)|
||Virtuálismag-alapú vásárlási modell|[Virtuálismag-alapú vásárlási modell](database/service-tiers-vcore.md)|
|Üzembehelyezési lehetőség |Önálló adatbázis|[Önálló adatbázisok](database/single-database-overview.md)|
||Rugalmas készlet|[Rugalmas készlet](database/elastic-pool-overview.md)|
|Szolgáltatási szint|Alapszintű, standard, prémium, általános célú, nagy kapacitású, üzletileg kritikus|A virtuális mag modellben található szolgáltatási szintekért lásd: [SQL Database szolgáltatási szintek](database/service-tiers-vcore.md#service-tiers). A DTU modellben található szolgáltatási szintekért lásd: [DTU-modell](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Számítási szint|Kiszolgáló nélküli számítástechnika|[Kiszolgáló nélküli számítástechnika](database/service-tiers-vcore.md#compute-tiers)
||Kiépített számítás|[Kiépített számítás](database/service-tiers-vcore.md#compute-tiers)
|Számítási generáció|Gen5, M-sorozat, Fsv2 sorozat, DC sorozat|[Hardver generációi](database/service-tiers-vcore.md#hardware-generations)
|Kiszolgáló entitása| Kiszolgáló |[Logikai SQL Server-kiszolgálók](database/logical-servers.md)|
|Erőforrás típusa|virtuális mag|Egy CPU-mag, amely egyetlen adatbázishoz, rugalmas készlethez van megadva a számítási erőforrás számára. |
||Számítási méret és tárterület mennyisége|A számítási méret a CPU, a memória és az egyéb, nem Storage szolgáltatáshoz kapcsolódó erőforrások maximális mennyisége, amely egyetlen adatbázishoz vagy rugalmas készlethez érhető el.  A tárterület mérete az egyetlen adatbázishoz vagy a rugalmas készlethez rendelkezésre álló tárterület maximális mennyisége. A virtuális mag-modell méretezési lehetőségeiért lásd: [virtuális mag önálló adatbázisok](database/resource-limits-vcore-single-databases.md)és [virtuális mag rugalmas készletek](database/resource-limits-vcore-elastic-pools.md).  (.. /managed-instance/resource-limits.md).  A DTU modell méretezési lehetőségeiért lásd: [DTU önálló adatbázisok](database/resource-limits-dtu-single-databases.md) és [DTU rugalmas készletek](database/resource-limits-dtu-elastic-pools.md).

## <a name="azure-sql-managed-instance"></a>Felügyelt Azure SQL-példány

|Környezet|Időszak|További információ|
|:---|:---|:---|
|Azure-szolgáltatás|Felügyelt Azure SQL-példány|[SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)|
|Vásárlási modell|Virtuálismag-alapú vásárlási modell|[Virtuálismag-alapú vásárlási modell](database/service-tiers-vcore.md)|
|Üzembehelyezési lehetőség |Egyetlen példány|[Egyetlen példány](managed-instance/sql-managed-instance-paas-overview.md)|
||Példány készlete (előzetes verzió)|[Példánykészletek](managed-instance/instance-pools-overview.md)|
|Szolgáltatási szint|Általános célú, üzletileg kritikus|[SQL felügyelt példány szolgáltatási szintjei](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|Számítási szint|Kiépített számítás|[Kiépített számítás](database/service-tiers-vcore.md#compute-tiers)|
|Számítási generáció|Gen5|[Hardver generációi](database/service-tiers-vcore.md#hardware-generations)
|Kiszolgáló entitása|Felügyelt példány vagy példány| N/A, mert az SQL felügyelt példánya maga a kiszolgáló |
|Erőforrás típusa|virtuális mag|A felügyelt SQL-példány számítási erőforrásához biztosított CPU-mag.|
||Számítási méret és tárterület mennyisége|A számítási méret a CPU, a memória és a nem tárolók által az SQL felügyelt példányhoz kapcsolódó egyéb erőforrások maximális mennyisége.  A tárolási méret a felügyelt SQL-példányok számára elérhető maximális tárterület.  A méretezési beállításokhoz az [SQL felügyelt példányain](managed-instance/resource-limits.md). |
