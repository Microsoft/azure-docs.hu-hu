---
title: Mi az egyetlen adatbázis?
description: Ismerje meg a Azure SQL Database önálló adatbázis-erőforrásának típusát.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/08/2019
ms.openlocfilehash: 8f92fe8e4a4ebbc2d970bf28e415859249b9f67c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343319"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Mi a Azure SQL Database önálló adatbázisa?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az önálló adatbázis erőforrástípus egy adatbázist hoz létre Azure SQL Database saját erőforrásaival, és a [kiszolgáló](logical-servers.md)használatával felügyeli. Egyetlen adatbázis esetében minden adatbázis elkülönített és hordozható. Mindegyik saját szolgáltatási réteggel rendelkezik a [DTU-alapú vásárlási modellen](service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellen](service-tiers-vcore.md) belül, valamint egy garantált számítási mérettel.

> [!IMPORTANT]
> Az önálló adatbázis az Azure SQL Database egyik erőforrás-típusa. A másik a [rugalmas készletek](elastic-pool-overview.md).

## <a name="dynamic-scalability"></a>Dinamikus skálázhatóság

Az első alkalmazást egy kis méretű, különálló adatbázison is létrehozhatja, alacsony áron, a kiszolgáló nélküli számítási rétegben, vagy a kiépített számítási szinten kis számítási mérettel. A [számítási vagy szolgáltatási szintet](single-database-scale.md) manuálisan vagy programozottan változtathatja meg a megoldás igényeinek megfelelően. Úgy módosíthatja a teljesítményt, hogy az nem jár leállással az alkalmazás vagy az ügyfelek számára. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre, és lehetővé teszi, hogy csak azokért az erőforrásokért fizessen, amelyekre és amikor szüksége van.

## <a name="single-databases-and-elastic-pools"></a>Önálló adatbázisok és rugalmas készletek

Az erőforrás-megosztáshoz egyetlen adatbázis helyezhető be vagy ki egy [rugalmas készletbe](elastic-pool-overview.md) . Számos vállalkozás és alkalmazás számára elegendő az, ha önálló adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. A rugalmas készletek megoldást jelentenek erre a problémára. A koncepció egyszerű. A teljesítményadatokat egy készlethez kell hozzárendelni, és nem egy önálló adatbázishoz, hanem a készlet kollektív teljesítmény-erőforrásaiért kell fizetnie, nem pedig egyetlen adatbázis teljesítményére.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A beépített [Teljesítményfigyelő](performance-guidance.md) és [riasztási eszközöket](alerts-insights-configure-portal.md)a teljesítmény-minősítésekkel együtt használva használhatja. Ezek lehetővé teszik az aktuális vagy a projekthez kapcsolódó teljesítményigényeken alapuló fel- vagy leskálázás hatásainak gyors kiértékelését. Emellett a SQL Database a könnyebb monitorozás érdekében [mérőszámokat és erőforrás-naplókat is képes kibocsátani](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) .

## <a name="availability-capabilities"></a>Rendelkezésre állás

Az önálló adatbázisok és a rugalmas készletek számos rendelkezésre állási jellemzőt biztosítanak. További információ: [rendelkezésre állási jellemzők](sql-database-paas-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-különbségek

Az alkalmazások által használt legtöbb Transact-SQL-szolgáltatás teljes mértékben támogatott Microsoft SQL Server és Azure SQL Database egyaránt. Például az alapvető SQL-összetevők, például az adattípusok, a operátorok, a karakterláncok, a aritmetikai, a logikai és a kurzor függvények azonos módon működnek SQL Server és SQL Database. Van azonban néhány, a DDL (adatdefiníciós nyelv) és a DML (adatmanipulációs nyelv) elemeinek néhány T-SQL-eltérése, ami csak részben támogatott T-SQL-utasításokat és-lekérdezéseket eredményez (amit a cikk későbbi részében tárgyalunk).

Emellett vannak olyan szolgáltatások és szintaxisok is, amelyek nem támogatottak, mert Azure SQL Database úgy van kialakítva, hogy elkülönítse a szolgáltatásokat a főadatbázis és az operációs rendszer függőségeitől. Ennek megfelelően a legtöbb kiszolgálói szintű tevékenység nem megfelelő a SQL Database számára. A T-SQL-utasítások és-beállítások nem érhetők el, ha kiszolgálói szintű beállításokat konfigurálnak, az operációs rendszer összetevőinek konfigurálását vagy a fájlrendszer konfigurációjának megadását. Ha ilyen képességekre van szükség, a megfelelő alternatíva gyakran elérhető más módon, SQL Database vagy egy másik Azure-szolgáltatásból vagy-szolgáltatásból.

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Databasera](transact-sql-tsql-differences-sql-server.md).

## <a name="security"></a>Biztonság

SQL Database számos [beépített biztonsági és megfelelőségi](security-overview.md) funkciót kínál, amelyek segítségével az alkalmazás megfelel a különböző biztonsági és megfelelőségi követelményeknek.

> [!IMPORTANT]
> A Azure SQL Database számos megfelelőségi szabványnak megfelelő minősítéssel rendelkezik. További információkért tekintse meg a [Microsoft Azure adatvédelmi központot](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), ahol megtalálhatja a SQL Database megfelelőségi minősítések legfrissebb listáját.

## <a name="next-steps"></a>Következő lépések

- Egyetlen adatbázis gyors megkezdéséhez kezdje az [egyetlen adatbázis rövid útmutatóját](quickstart-content-reference-guide.md).
- A SQL Server-adatbázisok Azure-ba történő áttelepítésével kapcsolatos információkért lásd: [áttelepítés Azure SQL Databasere](migrate-to-database-from-sql-server.md).
- A támogatott funkciókkal kapcsolatos tudnivalókat lásd: [Funkciók](features-comparison.md).
