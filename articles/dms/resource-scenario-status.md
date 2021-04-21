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
ms.openlocfilehash: f06c1a055b2f08259bf5318afe3c969d763bbbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818619"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>A migrálási forgatókönyvek állapota, amelyet a Azure Database Migration Service

Azure Database Migration Service úgy lett kialakítva, hogy támogassa a különböző migrálási forgatókönyveket (forrás-/célpárok) az offline (egyszer) és az online (folyamatos szinkronizálás) migráláshoz. A forgatókönyv által biztosított Azure Database Migration Service az idő alatt megnnyosodnak. Az új forgatókönyveket rendszeresen hozzáadjuk. Ez a cikk a jelenleg támogatott migrálási forgatókönyveket Azure Database Migration Service és az egyes forgatókönyvek állapotát (privát előzetes verzió, nyilvános előzetes verzió vagy általánosan elérhető).

## <a name="offline-versus-online-migrations"></a>Offline és online migrálás

A Azure Database Migration Service offline vagy online migrálást is lehet. Offline *migrálás* esetén az alkalmazás állásideja a migrálás elindulásával egy időben kezdődik. Online migrálás használatával korlátozhatja az állásidőt az új környezetre való átálláshoz szükséges *időre.* Javasoljuk, hogy tesztelje az offline migrálást annak megállapításához, hogy elfogadható-e az állásidő; Ha nem, akkor online migrálást is kell.

## <a name="migration-scenario-status"></a>Migrálási forgatókönyv állapota

A szolgáltatás által támogatott migrálási forgatókönyvek Azure Database Migration Service idővel változhatnak. A forgatókönyveket általában először a privát előzetes **verzióban adták ki.** A privát előzetes verzió után a forgatókönyv állapota nyilvános előzetes **verzióra változik.** Azure Database Migration Service felhasználók közvetlenül a felhasználói felületről próbálnak ki migrálási forgatókönyveket nyilvános előzetes verzióban. Nincs szükség regisztrációra.  Előfordulhat azonban, hogy a nyilvános előzetes verzió migrálási forgatókönyvei nem minden régióban érhetők el, és a végleges kiadás előtt további módosításokon eshetnek át. A nyilvános előzetes verzió után a forgatókönyv állapota általánosan **elérhetővé változik.** Az általánosan elérhető (GA) a végleges kiadási állapot, és a funkció teljes, és minden felhasználó számára elérhető.

## <a name="migration-scenario-support"></a>Migrálási forgatókönyv támogatása

Az alábbi táblázatok a támogatott migrálási forgatókönyveket mutatják be a Azure Database Migration Service.

> [!NOTE]
> Ha az alább támogatottként felsorolt forgatókönyvek nem jelennek meg a felhasználói felületen, további információért forduljon az [Ask Azure Database Migrations (Azure Database-migrálások](mailto:AskAzureDatabaseMigrations@service.microsoft.com) lekérdezése) aliashoz.

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
| **MySQL-hez készült Azure DB** | MySQL | ✔ |   |
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
> Az "Oracle to Azure Database for PostgreSQL" migrálási forgatókönyv (jelenleg előzetes verzióban érhető el) 2021. május 1. után már nem lesz elérhető. Továbbra is biztosítunk támogatást alternatív eszközök (például Ora2pg) segítségével, és a legjobb migrálási élményt biztosítjuk az Oracle-ről PostgreSQL-re történő migráláshoz. A migrálás ajánlott eljárásait lásd: [Oracle Azure Database for PostgreSQL áttelepítési útmutató.](https://aka.ms/OracletoPGguide)


## <a name="next-steps"></a>Következő lépések

A szolgáltatások és Azure Database Migration Service áttekintését lásd: Mi [a Azure Database Migration Service.](dms-overview.md)
