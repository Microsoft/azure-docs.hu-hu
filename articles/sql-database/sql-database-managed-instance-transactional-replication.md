---
title: Az Azure SQL Database tranzakciós replikáció |} A Microsoft Docs"
description: Ismerje meg az SQL Server tranzakciós replikációját használatával egyetlen készletbe vont, és a példány az Azure SQL Database-adatbázisok.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: bcbdd5fd8395cb0a47038595127e9b20118bdf1b
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147710"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Egyetlen, a tranzakciós replikáció készletezett és adatbázisokat az Azure SQL Database-példány

Tranzakciós replikáció funkciója az Azure SQL Database és SQL Server, amely lehetővé teszi, hogy az adatok replikálása az Azure SQL Database egy táblából vagy a táblák helyezi a távoli adatbázis SQL-kiszolgáló. Ez a funkció lehetővé teszi több táblájából különböző adatbázisok szinkronizálása.

## <a name="when-to-use-transactional-replication"></a>Tranzakciós replikáció használata

Tranzakciós replikáció a következő esetekben hasznos:
- Egy vagy több táblát egy adatbázisban végrehajtott módosítások közzététele, és terjessze azokat egy vagy több SQL Server- vagy Azure SQL-adatbázisok, amelyek a módosítások az előfizetett.
- Ne több elosztott adatbázisok Szinkronizáltsági állapotával.
- Adatbázisok migrálása SQL Server vagy a felügyelt példány egy másik adatbázisba folyamatosan tegye közzé a módosításokat.

## <a name="overview"></a>Áttekintés

A legfontosabb összetevők, a tranzakciós replikáció a következő képen látható:  

![a replikáció az SQL Database szolgáltatással](media/replication-to-sql-database/replication-to-sql-database.png)

A **közzétevő** egy olyan példányt vagy a kiszolgálót, amely közzéteszi az egyes táblák (cikk) végzett módosításokat, ha a frissítéseket küld a Terjesztőn. Közzé minden olyan Azure SQL adatbázis egy helyszíni SQL Serverről az SQL Server következő verziói által támogatott:

- Az SQL Server 2019 (előzetes verzió)
- Az SQL Server 2016 SQL 2017-ben
- Az SQL Server 2014 SP1 CU3 vagy nagyobb (12.00.4427)
- Az SQL Server 2014 RTM CU10 (12.00.2556)
- Az SQL Server 2012 SP3 vagy nagyobb (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Egyéb verziói esetén az SQL Server, amelyek nem támogatják a közzétételt az objektumok az Azure-ban, is lehet használni a [adatok újbóli közzététele](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metódus adatok áthelyezése az SQL Server újabb verzióit. 

A **terjesztő** példány vagy-kiszolgálót, amely a cikkekben módosítások gyűjti össze a közzétevő osztja el azokat az előfizetők számára. A terjesztő lehet az Azure SQL Database felügyelt példányába vagy az SQL Server (bármilyen verzió, ahogy azt a hosszú az egyenlő vagy nagyobb, mint a közzétevő verzió). 

A **előfizető** egy olyan példányt, vagy a módosítások a közzétevő a fogadó kiszolgálón. Előfizetők lehetnek önálló, készletezett, és a példány-adatbázisok Azure SQL Database vagy SQL Server-adatbázisokban. Az önálló vagy készletezett adatbázisok előfizető leküldéses előfizetőként kell konfigurálni. 

| Szerepkör | Egyetlen vagy készletezett adatbázisok | Példányok adatbázisai |
| :----| :------------- | :--------------- |
| **Publisher** | Nem | Igen | 
| **Terjesztő** | Nem | Igen|
| **Pull-előfizető** | Nem | Igen|
| **Push előfizetői**| Igen | Igen|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Egy lekéréses előfizetéssel nem támogatott, ha a terjesztőn-példány adatbázis és az előfizető nincs. 

Különböző [replikációtípusok](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikáció | Egyetlen vagy készletezett adatbázisok | Példányok adatbázisai|
| :----| :------------- | :--------------- |
| [**Standard tranzakciós**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizető) | Igen | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizető) | Igen|
| [**Replikációs egyesítés**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Társ-társ**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| [**Kétirányú**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Frissíthető előfizetések**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Próbál egy régebbi verzióját használja, a replikáció konfigurálása számának MSSQL_REPL20084 (a folyamat nem tudott kapcsolódni előfizető.) és MSSQ_REPL40532 hibát eredményez (nem nyitható meg a kiszolgáló \<név > a bejelentkezés által kért. A bejelentkezés sikertelen volt.)
  > - Azure SQL Database összes funkciójának használatához kell használnia a legújabb [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Példányok adatbázisai és a helyszíni rendszerek támogatási mátrix
  A replikációs támogathatóság mátrix, például adatbázisok pedig ugyanaz, mint a helyszíni SQL Server esetében. 
  
  | **Publisher**   | **Terjesztő** | **Előfizető** |
| :------------   | :-------------- | :------------- |
| SQL Server 2017 | SQL Server 2017 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Követelmények

- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között. 
- Egy Azure Storage-fiókot a munkakönyvtárban replikáció által használt megosztást. 
- 445-ös (TCP, kimenő) porton keresztül kell megnyitni a biztonsági szabályok az Azure-fájlmegosztás eléréséhez a felügyelt példány alhálózatára kell. 
- 1433 (TCP, kimenő) porton kell nyitható meg, ha az előfizető a helyszíni, és a közzétevő forgalmazó egy felügyelt példányon.

  >[!NOTE]
  > 53-as hiba találkozhat, amikor csatlakozik egy Azure Storage-fájlt, ha a kimenő hálózati biztonsági csoport (NSG) 445-ös le van tiltva, ha a terjesztőn-példány adatbázis, és az előfizető a helyszíni. [Frissítse a Vnetet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) a probléma megoldásához. 

### <a name="compare-data-sync-with-transactional-replication"></a>Hasonlítsa össze a tranzakciós replikáció adatszinkronizálás

| | Adatszinkronizálás | Tranzakciós replikáció |
|---|---|---|
| Előnyök | – Aktív-aktív támogatása<br/>Kétirányú a helyszíni és az Azure SQL Database között | -Kisebb késés<br/>-Tranzakció-konzisztencia<br/>-Felhasználhatja a meglévő topológiát migrálás után |
| Hátrányai | -5 perc vagy több késés<br/>– Nincs tranzakció-konzisztencia<br/>-Nagyobb teljesítményre gyakorolt hatás | – Nem lehet közzétenni az Azure SQL Database önálló adatbázisok vagy készletezett adatbázis<br/>-Nagy karbantartás költsége |
| | | |

## <a name="common-configurations"></a>Általános beállításai

Általánosságban véve a közzétevő és a terjesztőn kell lennie a felhőben vagy helyszíni. A következő konfigurációk támogatottak: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Felügyelt példány helyi terjesztő közzétevő

![Gyártó és forgalmazó, egyetlen példány](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Gyártó és forgalmazó belül egyetlen felügyelt példány és más felügyelt példány, önálló adatbázis, a készletezett adatbázis definíciófrissítés-terjesztés változások vannak konfigurálva, vagy a helyszíni SQL Server. Ebben a konfigurációban a felügyelt példánynak közzétevő/terjesztő nem konfigurálható a [georeplikációja és automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>A felügyelt példány a távoli terjesztőt közzétevő

Ebben a konfigurációban egy felügyelt példány közzéteszi a módosításokat elhelyezett egy másik felügyelt példányt, amely képes több forrás felügyelt példány szolgál, és a felügyelt példány, önálló adatbázis, a készletezett adatbázis egy vagy több cél változásait terjesztheti a terjesztőhöz vagy Az SQL Server.

![A közzétevő és a Terjesztőn különálló példány](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Gyártó és forgalmazó két felügyelt példány konfigurálása. Ebben a konfigurációban

- Felügyelt példány is ugyanazon a virtuális hálózaton van.
- Felügyelt példány is ugyanazon a helyen vannak.
- Felügyelt példányok üzemeltető közzétett és a terjesztő adatbázist nem lehet [georeplikált automatikus feladatátvételi-csoportok használatával](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Gyártó és forgalmazó a helyszínen az egyetlen, az előfizető készletezett és adatbázis-példány 

![Az Azure SQL DB-előfizetőként](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Ebben a konfigurációban az Azure SQL Database (egyetlen, készletezett és adatbázis-példány) az előfizető. Ez a konfiguráció támogatja az áttelepítés a helyszínről az Azure-bA. Ha az előfizető az önálló vagy készletezett adatbázisok, leküldéses módban kell lennie.  


## <a name="next-steps"></a>További lépések

1. [Konfigurálja a felügyelt példány tranzakciós replikáció](replication-with-sql-database-managed-instance.md). 
1. [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) az előfizetők az Azure SQL Database-kiszolgáló neve használatával (például `N'azuresqldbdns.database.windows.net` és a cél adatbázis az Azure SQL Database nevére (például **Adventureworks**. )



## <a name="see-also"></a>Lásd még:  

- [Replikáció az SQL Database-be](replication-to-sql-database.md)
- [Felügyelt példány történő replikálásához](replication-with-sql-database-managed-instance.md)
- [Kiadvány létrehozása](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetés](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Replikáció típusa](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoring (replikáció)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
