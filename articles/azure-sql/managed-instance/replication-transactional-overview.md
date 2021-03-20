---
title: Tranzakciós replikáció
titleSuffix: Azure SQL Managed Instance
description: Tudnivalók a SQL Server tranzakciós replikálás használatáról az Azure SQL felügyelt példányaival (előzetes verzió).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225343"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Tranzakciós replikáció az Azure SQL felügyelt példányaival (előzetes verzió)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A tranzakciós replikáció az Azure SQL felügyelt SQL Server példányának egyik funkciója, amely lehetővé teszi az adatok replikálását az Azure SQL felügyelt példányain lévő táblákból vagy egy SQL Server-példányból a távoli adatbázisokra helyezett táblákba. Ez a funkció lehetővé teszi több tábla szinkronizálását különböző adatbázisokban. 

A tranzakciós replikáció jelenleg nyilvános előzetes verzióban érhető el a felügyelt SQL-példányhoz. 

## <a name="overview"></a>Áttekintés

A tranzakciós replikálás használatával leküldheti az Azure SQL felügyelt példányain végrehajtott módosításokat a következőre:

- Egy SQL Server-adatbázis helyszíni vagy Azure-beli virtuális gépen
- Egy adatbázis a Azure SQL Database
- Példány-adatbázis az Azure SQL felügyelt példányában

  > [!NOTE]
  > Az Azure SQL felügyelt példányok összes funkciójának használatához a [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) és a [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióját kell használnia.

### <a name="components"></a>Összetevők

A tranzakciós replikáció legfontosabb összetevői a **közzétevő**, a **terjesztő** és az **előfizető**, ahogyan az a következő képen látható:  

![replikálás SQL Database](./media/replication-transactional-overview/replication-to-sql-database.png)

| Szerepkör | Azure SQL Database | Felügyelt Azure SQL-példány |
| :----| :------------- | :--------------- |
| **Publisher** | Nem | Igen |
| **Terjesztő** | Nem | Igen|
| **Lekéréses előfizető** | Nem | Igen|
| **Leküldéses előfizető**| Igen | Yes|
| &nbsp; | &nbsp; | &nbsp; |

A **közzétevő** közzéteszi az egyes táblákon (cikkeken) végrehajtott módosításokat úgy, hogy elküldi a frissítéseket a terjesztőnek. A közzétevő lehet egy Azure SQL felügyelt példány vagy egy SQL Server példány.

A **terjesztő** begyűjti a cikkek változásait egy közzétevőtől, és elosztja azokat az előfizetőknek. A terjesztő lehet egy Azure SQL felügyelt példány vagy egy SQL Server-példány (bármely verzió, ameddig a közzétevő verziója megegyezik vagy annál nagyobb.)

Az **előfizető** a közzétevőn végrehajtott módosításokat fogadja. Egy SQL Server példány és az Azure SQL felügyelt példánya leküldéses és lekéréses előfizetők is lehetnek, bár a lekéréses előfizetések nem támogatottak, ha a terjesztő egy Azure SQL felügyelt példány, és az előfizető nem. Azure SQL Database-adatbázis csak leküldéses előfizető lehet.

Az Azure SQL felügyelt példánya a következő SQL Server-verziók előfizetői számára is képes támogatni:

- SQL Server 2016 és újabb verziók
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) vagy [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) vagy [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Az olyan SQL Server más verziói esetében, amelyek nem támogatják az Azure-ban lévő objektumok közzétételét, a [republishing](/sql/relational-databases/replication/republish-data) adatmódszer használatával áthelyezheti az adatátvitelt a SQL Server újabb verzióiba.
   > - Ha egy régebbi verzió használatával próbál meg konfigurálni a replikálást, a hiba szám MSSQL_REPL20084 (a folyamat nem tudott csatlakozni az előfizetőhöz.) és a MSSQ_REPL40532 (a bejelentkezés által kért kiszolgáló nem nyitható meg \<name> . A bejelentkezés sikertelen.)

### <a name="types-of-replication"></a>Replikálási típusok

A replikáció különböző [típusú](/sql/relational-databases/replication/types-of-replication):

| Replikálás | Azure SQL Database | Felügyelt Azure SQL-példány |
| :----| :------------- | :--------------- |
| [**Normál tranzakciós**](/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizetőként) | Yes |
| [**Pillanatkép**](/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizetőként) | Yes|
| [**Replikálás egyesítése**](/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Egyenrangú**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| [**Kétirányú**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Frissíthető előfizetések**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Támogatási mátrix

  A felügyelt Azure SQL-példány tranzakciós replikációs támogatási mátrixa ugyanaz, mint SQL Server.
  
| **Publisher**   | **Terjesztő** | **Előfizető** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>A következő esetekben használja

A tranzakciós replikáció a következő esetekben hasznos:

- Közzéteheti az adatbázis egy vagy több táblájából származó módosításokat, és egy vagy több, SQL Server példányban lévő vagy a változásokra előfizetett Azure SQL Database adatbázisba terjesztheti őket.
- Több elosztott adatbázis tartása szinkronizált állapotban.
- Az adatbázisok migrálása egy SQL Server példányból vagy egy Azure SQL felügyelt példányból egy másik adatbázisba a módosítások folyamatos közzétételekor.

### <a name="compare-data-sync-with-transactional-replication"></a>Adatszinkronizálás összehasonlítása tranzakciós replikációval

| Kategória | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | – Aktív-aktív támogatás<br/>– A helyszíni és a Azure SQL Database közötti kétirányú irányítás | – Alacsonyabb késés<br/>– Tranzakciós konzisztencia<br/>-Meglévő topológia újrafelhasználása az áttelepítés után |
| Hátrányok | – Nincs tranzakciós konzisztencia<br/>– Nagyobb teljesítményre gyakorolt hatás | -Nem lehet közzétenni Azure SQL Database <br/>– Magas karbantartási díj |

## <a name="common-configurations"></a>Gyakori konfigurációk

Általánosságban a közzétevőnek és a terjesztőnek a felhőben vagy a helyszínen kell lennie. A következő konfigurációk vannak támogatva:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Közzétevő helyi terjesztővel a felügyelt SQL-példányon

![Önálló példány Közzétevőként és terjesztőként](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

A közzétevő és a terjesztő egyetlen SQL felügyelt példányon belül van konfigurálva, és a módosításokat egy másik, felügyelt SQL-példányra, SQL Database vagy SQL Server példányra terjeszti.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Közzétevő távoli terjesztővel a felügyelt SQL-példányon

Ebben a konfigurációban egy felügyelt példány közzétesz egy másik, felügyelt SQL-példányra helyezett terjesztőt, amely számos forrás SQL felügyelt példányt képes kiszolgálni, és a módosításokat egy vagy több, Azure SQL Database, Azure SQL felügyelt példányon vagy SQL Servern lévő célpontra terjesztheti.

![A közzétevő és a terjesztő külön példányai](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

A közzétevő és a terjesztő két felügyelt példányon van konfigurálva. Ehhez a konfigurációhoz bizonyos korlátozások vonatkoznak:

- Mindkét felügyelt példány ugyanazon a vNet található.
- Mindkét felügyelt példány ugyanazon a helyen található.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Helyszíni közzétevő/terjesztő távoli előfizetővel

![Azure SQL Database előfizetőként](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

Ebben a konfigurációban a Azure SQL Database vagy az Azure SQL felügyelt példányának egyik adatbázisa előfizető. Ez a konfiguráció támogatja a helyszíni rendszerről az Azure-ba való áttelepítést. Ha az előfizető Azure SQL Database adatbázisa, leküldéses módban kell lennie.  

## <a name="requirements"></a>Követelmények

- SQL-hitelesítés használata a replikációs résztvevők közötti kapcsolathoz.
- A replikáció által használt munkakönyvtárhoz használjon Azure Storage-fiók megosztását.
- Az Azure-fájlmegosztás eléréséhez nyissa meg az alhálózati biztonsági szabályok 445-es TCP kimenő portját.
- Nyissa meg a 1433-as TCP kimenő portot, ha az SQL felügyelt példánya a közzétevő/terjesztő, és az előfizető nem. Előfordulhat, hogy módosítania kell az SQL felügyelt példányának NSG kimenő biztonsági szabályát a `allow_linkedserver_outbound` esetében a 1433 **-as** portra vonatkozóan `virtualnetwork` `internet` .
- Helyezze el a közzétevőt és a terjesztőt a felhőben, vagy mindkettőt a helyszínen.
- Konfigurálja a VPN-társítást a replikációs résztvevők virtuális hálózatai között, ha a virtuális hálózatok eltérőek.

> [!NOTE]
> Ha a kimenő hálózati biztonsági csoport (NSG) 445-es portja le van tiltva, ha a terjesztő egy Azure SQL felügyelt példány-adatbázis, és az előfizető a helyszínen van, akkor a 53-es hiba léphet fel egy Azure Storage-fájlhoz való csatlakozáskor. A probléma megoldásához [frissítse a VNET NSG](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) .

## <a name="with-failover-groups"></a>Feladatátvételi csoportokkal

Az [aktív geo-replikáció](../database/active-geo-replication-overview.md) nem támogatott az SQL felügyelt példányainak tranzakciós replikációval való használata esetén. Az aktív geo-replikáció helyett használjon [automatikus feladatátvételi csoportokat](../database/auto-failover-group-overview.md), de vegye figyelembe, hogy a kiadványt [manuálisan kell törölni](transact-sql-tsql-differences-sql-server.md#replication) az elsődleges felügyelt példányból, majd újra létre kell hozni a másodlagos SQL felügyelt példányon a feladatátvétel után.

Ha a földrajzi replikáció engedélyezve van a **közzétevő** vagy a **terjesztő** SQL felügyelt példányán egy [feladatátvételi csoportban](../database/auto-failover-group-overview.md), akkor az SQL felügyelt példányának rendszergazdájának törölnie kell az összes kiadványt a régi elsődleges gépen, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően. Ebben a forgatókönyvben a következő tevékenységek szükségesek:

1. Állítsa le az adatbázison futó összes replikációs feladatot, ha vannak ilyenek.
1. Az előfizetés metaadatainak eldobása a közzétevőtől a következő parancsfájl futtatásával a közzétevő adatbázisán:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Az előfizetés metaadatainak eldobása az előfizetőtől. Futtassa a következő parancsfájlt az előfizetői SQL felügyelt példányának előfizetési adatbázisán:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Az összes replikációs objektum kényszerített eldobása a közzétevőtől a közzétett adatbázisban a következő parancsfájl futtatásával:

   ```sql
   EXEC sp_removedbreplication
   ```

1. A régi terjesztő elsődleges SQL felügyelt példányból való kényszerített eldobása (ha a feladatátvételt egy régi elsődlegesnek kell visszaadnia, amely a terjesztőt használta). Futtassa a következő parancsfájlt a régi terjesztő SQL felügyelt példányának Master adatbázisában:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Ha a Geo-replikáció engedélyezve van egy feladatátvételi csoport **előfizetői** példányán, a kiadványt úgy kell konfigurálni, hogy az előfizető által felügyelt példányhoz kapcsolódjon a feladatátvételi csoport figyelő-végponthoz. Feladatátvétel esetén a felügyelt példány rendszergazdája a következő művelettől függ, hogy milyen típusú feladatátvétel történt:

- Adatvesztés nélküli feladatátvétel esetén a replikáció a feladatátvételt követően továbbra is működni fog.
- Adatvesztéssel rendelkező feladatátvétel esetén a replikáció is működik. A rendszer újra replikálja az elveszett módosításokat.
- Adatvesztéssel rendelkező feladatátvétel esetén az adatvesztés azonban a terjesztési adatbázis megőrzési idején kívül esik, az SQL felügyelt példányának rendszergazdájának újra kell inicializálnia az előfizetés-adatbázist.

## <a name="next-steps"></a>Következő lépések

A tranzakciós replikáció konfigurálásával kapcsolatos további információkért tekintse meg a következő oktatóanyagokat:

- [Replikáció konfigurálása egy SQL felügyelt példány közzétevője és előfizetője között](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Replikáció konfigurálása egy felügyelt SQL-példány közzétevője, az SQL felügyelt példány-terjesztője és SQL Server előfizető között](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Hozzon létre egy kiadványt](/sql/relational-databases/replication/publish/create-a-publication).
- [Hozzon létre egy leküldéses előfizetést](/sql/relational-databases/replication/create-a-push-subscription) , amely a kiszolgáló nevét használja előfizetőként (például `N'azuresqldbdns.database.windows.net` a (z) Azure SQL Database nevű adatbázist a céladatbázisként (például **AdventureWorks**). )

## <a name="see-also"></a>Lásd még  

- [Replikálás SQL felügyelt példánnyal és feladatátvételi csoporttal](transact-sql-tsql-differences-sql-server.md#replication)
- [Replikáció az SQL Database-be](../database/replication-to-sql-database.md)
- [Replikálás felügyelt példányra](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Kiadvány létrehozása](/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](/sql/relational-databases/replication/initialize-a-subscription)
