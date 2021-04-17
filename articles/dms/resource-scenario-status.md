---
title: Adatbázis migrációs forgatókönyvének állapota
titleSuffix: Azure Database Migration Service
description: Ismerje meg a szolgáltatás által támogatott migrálási forgatókönyvek Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 6c1a0853dc59b2e2ceabfd47d81aac364a2b5716
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589431"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Az áttelepítési forgatókönyvek állapota, amelyet a Azure Database Migration Service

Azure Database Migration Service úgy lett kialakítva, hogy támogassa a különböző migrálási forgatókönyveket (forrás-/célpárok) az offline (egyszer) és az online (folyamatos szinkronizálás) migráláshoz. A forgatókönyv által biztosított lefedettség Azure Database Migration Service idő alatt megnnyúsodik. Az új forgatókönyveket rendszeresen hozzáadjuk. Ez a cikk a Azure Database Migration Service által jelenleg támogatott migrálási forgatókönyveket és az egyes forgatókönyvek állapotát (privát előzetes verzió, nyilvános előzetes verzió vagy általánosan elérhető) azonosítja.

## <a name="offline-versus-online-migrations"></a>Offline és online migrálás

A Azure Database Migration Service offline vagy online migrálást is lehet. Offline *migrálás* esetén az alkalmazás állásideja a migrálás elindulásával egy időben kezdődik. Az állásidőnek az új környezetbe való átvágáshoz szükséges időre való korlátozásához a migrálás befejezésekor használjon *online migrálást.* Javasoljuk, hogy tesztelje az offline migrálást annak megállapításához, hogy elfogadható-e az állásidő; Ha nem, akkor online migrálást is kell.

## <a name="migration-scenario-support"></a>Migrálási forgatókönyv támogatása

Az alábbi táblázatok a támogatott migrálási forgatókönyveket mutatják be a Azure Database Migration Service.

> [!NOTE]
> Ha az alább támogatottként felsorolt forgatókönyvek nem jelennek meg a felhasználói felületen, további információért forduljon az [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) aliashoz.

> [!IMPORTANT]
> A privát előzetes verzióban jelenleg Azure Database Migration Service összes forgatókönyv megtekintéséhez tekintse meg a [DMS előzetes verzió webhelyét.](https://aka.ms/dms-preview)

### <a name="offline-one-time-migration-support"></a>Offline (egyszeres) migrálás támogatása

Az alábbi táblázat az Azure Database Migration Service áttelepítések támogatását mutatja be.

| Cél  | Forrás | Támogatás | Állapot |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | FE |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL | X |  |
|   | Oracle | X |   |
| **Azure SQL virtuális gép** | SQL Server | ✔ | FE |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure DB** | MySQL | X |   |
|   | RDS MySQL | X |   |
| **Azure DB for PostgreSQL – Egyetlen kiszolgáló** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |
| **Azure DB for PostgreSQL – rugalmas skálázás (Citus)** | PostgreSQL | X |
|  | RDS PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Online (folyamatos szinkronizálás) migrálás támogatása

Az alábbi táblázat bemutatja, Azure Database Migration Service online migrálások támogatottak.

| Cél  | Forrás | Támogatás | Állapot |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | X |  |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL DB MI** | SQL Server | ✔ | FE |
|   | RDS SQL | X |  |
|   | Oracle | X |  |
| **Azure SQL virtuális gép** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | FE |
| **MySQL-hez készült Azure DB** | MySQL | ✔ | FE |
|   | RDS MySQL | ✔ | FE |
| **Azure DB for PostgreSQL – Egyetlen kiszolgáló** | PostgreSQL | ✔ | FE |
|   | Azure DB for PostgreSQL – Egyetlen kiszolgáló | ✔ | FE |
|   | RDS PostgreSQL | ✔ | FE |
|   | Oracle | ✔ | Nyilvános előzetes verzió (2021. május 1. után elalasztható) |
| **Azure DB for PostgreSQL – rugalmas skálázás (Citus)** | PostgreSQL | ✔ | FE |
|   | RDS PostgreSQL | ✔ | FE |

> [!IMPORTANT]
> Az "Oracle to Azure Database for PostgreSQL" migrálási forgatókönyv (jelenleg előzetes verzióban érhető el) 2021. május 1. után már nem lesz elérhető. Továbbra is biztosítunk támogatást alternatív eszközök (például Ora2pg) segítségével, és a legjobb migrálási élményt biztosítjuk az Oracle-ről PostgreSQL-re történő migráláshoz. A migrálásra vonatkozó ajánlott eljárásokért lásd: [Oracle Azure Database for PostgreSQL áttelepítési útmutató.](https://aka.ms/OracletoPGguide)


## <a name="next-steps"></a>Következő lépések

A szolgáltatások és Azure Database Migration Service áttekintését lásd: Mi [a Azure Database Migration Service.](dms-overview.md)
