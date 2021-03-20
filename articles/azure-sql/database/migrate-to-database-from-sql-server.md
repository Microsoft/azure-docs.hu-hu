---
title: SQL Server-adatbázisok migrálása az Azure SQL Database-be
description: Ismerkedjen meg a Azure SQL Database SQL Server adatbázis-áttelepítéssel.
keywords: adatbázis-áttelepítés,sql server-adatbázis áttelepítése,adatbázis-áttelepítési eszközök,adatbázis áttelepítése,sql database áttelepítése
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/11/2019
ms.openlocfilehash: fab52fcea03a2f65c868cfac27f8a8cef115b2be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94917647"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server-adatbázisok migrálása az Azure SQL Database-be
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a cikkből megtudhatja, hogyan telepíthet át egy SQL Server 2005-es vagy újabb verziójú adatbázist Azure SQL Databasere. A felügyelt Azure SQL-példányra való áttelepítéssel kapcsolatos információkért lásd: [SQL Server példány áttelepítése az Azure SQL felügyelt példányára](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md). Az áttelepítési lehetőségek és az Azure SQL-re való áttelepítési eszközök kiválasztásával kapcsolatos útmutatásért lásd: [áttelepítés az Azure SQL-be](../migration-guides/index.yml)


## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrálás önálló adatbázisba vagy készletezett adatbázisba

Két elsődleges módszer létezik egy SQL Server 2005-es vagy újabb adatbázis áttelepítésére Azure SQL Databasere. Az első módszer egyszerűbb, azonban jelentős állásidőt igényelhet a migrálás során. A második módszer bonyolultabb, viszont nem jár állásidővel a migrálás közben.

Mindkét esetben biztosítania kell, hogy a forrásadatbázis kompatibilis legyen Azure SQL Database a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)használatával. A SQL Database a [szolgáltatás paritása](features-comparison.md) SQL Serverekkel közelíthető meg, a kiszolgálói szintű és az adatbázison átívelő műveletekkel kapcsolatos problémák kivételével. A [részben támogatott vagy nem támogatott funkciókra](transact-sql-tsql-differences-sql-server.md) támaszkodó adatbázisokat és alkalmazásokat [némileg át kell alakítani](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) ezen inkompatibilitások kijavítása érdekében, még mielőtt áttelepíthetné az SQL Server-adatbázist.

> [!NOTE]
> Nem SQL Server-adatbázis (beleértve a Microsoft Access-, Sybase-, MySQL Oracle- és DB2-adatbázisokat) az Azure SQL Database-be történő áttelepítéséhez lásd [az SQL Server áttelepítési segédet](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/) ismertető cikket.

## <a name="method-1-migration-with-downtime-during-the-migration"></a>1. módszer: Állásidővel járó migrálás

 Ezzel a módszerrel áttelepítheti az áttelepítést egy vagy készletezett adatbázisba, ha bizonyos állásidőt szeretne biztosítani, vagy egy éles adatbázis tesztelési célú áttelepítését végzi későbbi áttelepítésre. Oktatóanyagért lásd: [SQL Server-adatbázis migrálása](../../dms/tutorial-sql-server-to-azure-sql.md).

A következő lista az egyetlen vagy készletezett adatbázis SQL Server adatbázis-áttelepítésének általános munkafolyamatát tartalmazza ezzel a módszerrel. A felügyelt SQL-példányra való áttelepítéssel kapcsolatban lásd: [áttelepítés az SQL felügyelt példányára](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![VSSSDT áttelepítési ábra](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. A [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)legújabb verziójának használatával [mérje](/sql/dma/dma-assesssqlonprem) fel az adatbázis kompatibilitását.
2. A szükséges javításokat Transact-SQL szkriptekként készítse elő.
3. Végezze el az áttelepített forrásadatbázis tranzakciós szempontból konzisztens másolatát, vagy állítson le új tranzakciókat a forrás-adatbázisból az áttelepítés során. Az utóbbi lehetőség végrehajtásának módszerei közé tartozik az Ügyfélkapcsolat letiltása vagy az [adatbázis-pillanatkép](/sql/relational-databases/databases/create-a-database-snapshot-transact-sql)létrehozása. Az áttelepítés után a tranzakciós replikáció használatával frissítheti az áttelepített adatbázisokat az áttelepítéshez szükséges kivágási pont utáni változásokkal. Lásd: [a Migrálás tranzakciós áttelepítés használatával](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Helyezze üzembe a Transact-SQL szkripteket a javítások alkalmazásához az adatbázis másolatán.
5. [Telepítse át](/sql/dma/dma-migrateonpremsql) az adatbázis másolatát egy új adatbázisba Azure SQL Database a Data Migration Assistant használatával.

> [!NOTE]
> A DMA használata helyett használhat BACPAC-fájlt is. Lásd: [BACPAC-fájl importálása egy új adatbázisba Azure SQL Databaseban](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Az adatátviteli teljesítmény optimalizálása migrálás közben

Az alábbi lista az importálási teljesítmény optimalizálására vonatkozó javaslatokat tartalmaz.

- Válassza ki a legmagasabb szolgáltatási szintet és számítási méretet, amelyet a költségkeret az átvitel teljesítményének maximalizálására tesz lehetővé. A migrálás után vertikális leskálázással pénzt takaríthat meg.
- Csökkentse a BACPAC-fájl és a cél adatközpont közötti távolságot.
- Az AutoStatisztika letiltása az áttelepítés során
- Particionálja a táblákat és az indexeket.
- Vesse el, majd a folyamat befejezése után hozza létre újra az indexelt nézeteket.
- Távolítsa el a ritkán lekérdezett korábbi adatlekérdezéseket egy másik adatbázisba, és telepítse át ezeket az előzményeket egy külön adatbázisba Azure SQL Database. Ezután lekérdezheti ezeket az előzményadatokat a [rugalmas lekérdezések](elastic-query-overview.md) használatával.

### <a name="optimize-performance-after-the-migration-completes"></a>A teljesítmény optimalizálása a migrálás befejezése után

A migrálás befejezése után végezzen teljes vizsgálatot a [statisztikák frissítéséhez](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="method-2-use-transactional-replication"></a>2. módszer: Tranzakciós replikáció használata

Ha nem engedheti meg, hogy az áttelepítést követően eltávolítsa a SQL Server adatbázist az éles környezetből, SQL Server tranzakciós replikációt is használhat áttelepítési megoldásként. A módszer használatának feltétele, hogy a forrásadatbázisnak meg kell felelnie a [tranzakciós replikáció követelményeinek](./replication-to-sql-database.md), valamint kompatibilisnek kell lennie az Azure SQL Database-szel is. További információ a mindig bekapcsolt SQL-replikációról: [replikáció konfigurálása always on rendelkezésre állási csoportokhoz (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

A megoldás használatához az adatbázist Azure SQL Database előfizetőként kell konfigurálnia az áttelepíteni kívánt SQL Server-példányra. A tranzakciós replikáció terjesztője szinkronizálja az adatokat szinkronizálni kívánt adatbázisból (a közzétevőből), eközben új tranzakciók továbbra is történnek.

A tranzakciós replikáció során az adatai vagy sémája minden változása megjelenik az adatbázisban Azure SQL Databaseban. Ha a szinkronizálás befejeződött, és készen áll az áttelepítésre, módosítsa az alkalmazások kapcsolódási karakterláncát úgy, hogy azok az adatbázisra mutassanak. Amikor a tranzakciós replikáció elvégezte az összes hátralévő forrásadatbázis-módosítást, valamint az összes alkalmazása az Azure-adatbázisra mutat, eltávolíthatja a tranzakciós replikációt. A Azure SQL Database adatbázisa mostantól az éles rendszer.

 ![SeedCloudTR-diagram](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> A tranzakciós replikációval a forrásadatbázis részhalmazát is migrálhatja. Az Azure SQL Database-be replikált kiadvány a replikált adatbázis tábláinak részhalmaza is lehet. Minden replikált tábla esetén korlátozhatja az adatokat a sor- és/vagy oszlopok meghatározott részhalmazaira.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrálás az SQL Database-re a tranzakciós replikáció használatával – munkafolyamat

> [!IMPORTANT]
> A SQL Server Management Studio legújabb verziójának használatával szinkronizálhatja az Azure-ban és a SQL Database-ban lévő frissítéseket. Az SQL Server Management Studio régebbi verzióiban az SQL Database nem állítható be előfizetőként. [Az SQL Server Management Studio frissítése](/sql/ssms/download-sql-server-management-studio-ssms).

1. Terjesztés beállítása
   - [SQL Server Management Studio (SSMS) használatával](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL használatával](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Kiadvány létrehozása
   - [SQL Server Management Studio (SSMS) használatával](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Transact-SQL használatával](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Előfizetés létrehozása
   - [SQL Server Management Studio (SSMS) használatával](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Transact-SQL használatával](/sql/relational-databases/replication/create-a-push-subscription/)

Tippek és különbségek az SQL Database-be való migráláshoz

- Helyi terjesztő használata
  - Ennek hatására a-kiszolgáló teljesítményére hatással lehet.
  - Ha a teljesítményre kifejtett hatás mértéke nem elfogadható, használhat másik kiszolgálót, ez azonban megnehezíti a kezelést és az adminisztrációt.
- Pillanatképmappa kiválasztásakor győződjön meg arról, hogy a mappában az összes replikálni kívánt tábla BCP-je elfér.
- A pillanatkép-létrehozás addig zárolja a társított táblákat, amíg a művelet be nem fejeződik, ezért megfelelően ütemezze a pillanatképet.
- Az Azure SQL Database csak a leküldéses előfizetéseket támogatja. Előfizetőket csak a forrásadatbázisból adhat hozzá.

## <a name="resolving-database-migration-compatibility-issues"></a>Adatbázis-migrálás kompatibilitási problémáinak megoldása

Számos kompatibilitási probléma merülhet fel, amelyek a forrásadatbázis SQL Server verziójától és az áttelepíteni kívánt adatbázis összetettségének függvényében is előfordulhatnak. Az SQL Server korábbi verziói több kompatibilitási problémával rendelkeznek. A következő forrásanyagokat érdemes használnia a keresőmotorokban végzett internetes keresés mellett:

- [Az Azure SQL Database-ben nem támogatott SQL Server-adatbázisfunkciók](transact-sql-tsql-differences-sql-server.md)
- [Megszűnt adatbázismotor-funkció az SQL Server 2016-ban](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server)
- [Megszűnt adatbázismotor-funkció az SQL Server 2014-ben](/sql/database-engine/discontinued-database-engine-functionality-in-sql-server?viewFallbackFrom=sql-server-2014)
- [Megszűnt adatbázismotor-funkció az SQL Server 2012-ben](/previous-versions/sql/sql-server-2012/ms144262(v=sql.110))
- [Megszűnt adatbázismotor-funkció az SQL Server 2008 R2-ben](/previous-versions/sql/sql-server-2008-r2/ms144262(v=sql.105))
- [Megszűnt adatbázismotor-funkció az SQL Server 2005-ben](/previous-versions/sql/sql-server-2005/ms144262(v=sql.90))

Az interneten való keresés és ezen erőforrások használata mellett a [Microsoft Q&a kérdéses oldalt Azure SQL Database](/answers/topics/azure-sql-database.html) vagy [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya lehetővé teszi, hogy a meglévő SQL Server-példányokat és annak adatbázisait minimálisan, kompatibilitási problémák nélkül áttelepítse. Lásd: [Mi az a felügyelt példány](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja a Tempdb-adatbázis felhasználását a migrálás közben](/archive/blogs/azuresqlemea/lesson-learned-10-monitoring-tempdb-usage).
- Az Azure SQL EMEA Engineers blogon lévő szkripttel [monitorozhatja az adatbázis tranzakciósnapló-területét a migrálás időtartama alatt](/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files).
- További információ a UTC idő kezeléséről a migrálás után: [Alapértelmezett időzóna módosítása a saját időzónájára](/archive/blogs/azuresqlemea/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone).
- További információ az adatbázis alapértelmezett nyelvének migrálás utáni módosításáról: [Az Azure SQL Database alapértelmezett nyelvének módosítása](/archive/blogs/azuresqlemea/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database).
