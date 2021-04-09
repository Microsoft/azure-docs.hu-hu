---
title: Azure SQL Server replikáció Azure SQL Database
description: A leküldéses előfizetőként Azure SQL Database is konfigurálhat egy egyirányú tranzakciós vagy pillanatképes replikációs topológiában.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780954"
---
# <a name="replication-to-azure-sql-database"></a>Replikálás Azure SQL Databasere
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az Azure SQL Databaset leküldéses előfizetőként konfigurálhatja egy egyirányú tranzakciós vagy pillanatképes replikációs topológiában.

> [!NOTE]
> Ez a cikk a [tranzakciós replikáció](/sql/relational-databases/replication/transactional/transactional-replication) használatát ismerteti Azure SQL Databaseban. Nem kapcsolódik az [aktív geo-replikáláshoz](./active-geo-replication-overview.md), amely egy Azure SQL Database funkció, amely lehetővé teszi az egyes adatbázisok teljes olvasható replikáinak létrehozását.

## <a name="supported-configurations"></a>Támogatott konfigurációk
  
- Azure SQL Database csak SQL Server közzétevő és terjesztő leküldéses előfizetője lehet.  
- A közzétevőként és/vagy terjesztőként működő SQL Server példány lehet a helyszíni, az [Azure SQL felügyelt példányát](../managed-instance/instance-create-quickstart.md) [futtató SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)példánya, vagy a [felhőben egy Azure-beli virtuális gépen futó SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)példánya. 
- A terjesztési adatbázis és a replikációs ügynökök nem helyezhetők el Azure SQL Database adatbázisán.  
- A [Pillanatkép](/sql/relational-databases/replication/snapshot-replication) és az [egyirányú tranzakciós](/sql/relational-databases/replication/transactional/transactional-replication) replikáció támogatott. A társ-társ tranzakciós replikáció és az egyesítéses replikáció nem támogatott.

### <a name="versions"></a>Verziók  

Azure SQL Database-adatbázisba való sikeres replikáláshoz SQL Server közzétevőknek és forgalmazóknak a következő verziók egyikét kell használnia:

A SQL Server következő verziói támogatják a SQL Server-adatbázisok bármely Azure SQL Database való közzétételét:

- SQL Server 2016 és újabb
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) vagy [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) vagy [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Ha nem támogatott verzióval konfigurálja a replikálást, a hiba szám MSSQL_REPL20084 (a folyamat nem tudott csatlakozni az előfizetőhöz.) és a MSSQL_REPL40532 (nem nyitható meg \<name> a bejelentkezés által kért kiszolgáló. A bejelentkezés sikertelen.)  

A Azure SQL Database összes funkciójának használatához a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) és a [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)legújabb verzióit kell használnia.  

### <a name="types-of-replication"></a>Replikálási típusok

A replikáció különböző [típusú](/sql/relational-databases/replication/types-of-replication):

| Replikáció | Azure SQL Database | Felügyelt Azure SQL-példány |
| :----| :------------- | :--------------- |
| [**Normál tranzakciós**](/sql/relational-databases/replication/transactional/transactional-replication) | Igen (csak előfizetőként) | Yes | 
| [**Pillanatkép**](/sql/relational-databases/replication/snapshot-replication) | Igen (csak előfizetőként) | Yes|
| [**Replikálás egyesítése**](/sql/relational-databases/replication/merge/merge-replication) | Nem | Nem|
| [**Egyenrangú**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Nem | Nem|
| [**Kétirányú**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Nem | Igen|
| [**Frissíthető előfizetések**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Nem | Nem|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Megjegyzések

- Csak a Azure SQL Database leküldéses előfizetések támogatottak.  
- A replikáció [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával vagy Transact-SQL-utasítások végrehajtásával konfigurálható a közzétevőn. A replikációt nem konfigurálhatja a Azure Portal használatával.  
- A replikáció csak SQL Server hitelesítési bejelentkezéseket használhat a Azure SQL Databasehoz való kapcsolódáshoz.
- A replikált tábláknak elsődleges kulccsal kell rendelkezniük.  
- Rendelkeznie kell egy meglévő Azure-előfizetéssel.  
- A Azure SQL Database előfizető bármely régióban lehet.  
- SQL Server egyetlen kiadványa képes támogatni Azure SQL Database és SQL Server (helyszíni és SQL Server Azure-beli virtuális gépeken) előfizetők számára.  
- A replikációs felügyeletet, a figyelést és a hibaelhárítást SQL Server kell végrehajtani, nem Azure SQL Database.  
- Csak `@subscriber_type = 0` SQL Database esetén támogatott a **sp_addsubscription** .  
- A Azure SQL Database nem támogatja a kétirányú, az azonnali, a frissíthető vagy a társközi replikálást.

## <a name="replication-architecture"></a>Replikációs architektúra  

![A diagram megjeleníti a replikálási architektúrát Azure SQL Databaseával, amely több előfizetői fürtöt tartalmaz különböző régiókban, valamint helyszíni Azure-beli virtuális gépeket, amelyek közzétevőt, Logread végrehajtható fájlt és a távoli fürtökhöz kapcsolódó terjesztői végrehajtható fájlokat tartalmaznak.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Forgatókönyvek  

### <a name="typical-replication-scenario"></a>Tipikus replikációs forgatókönyv  

1. Tranzakciós replikálási kiadvány létrehozása SQL Server adatbázison.  
2. SQL Server az **új előfizetés varázsló** vagy a Transact-SQL-utasítások használatával hozzon létre egy leküldéses előfizetést a Azure SQL Databaseba.  
3. A Azure SQL Database önálló és készletezett adatbázisaiban a kezdeti adatkészlet olyan pillanatkép, amelyet a pillanatkép-ügynök hozott létre, és amelyet a terjesztési ügynök kioszt és alkalmaz. A felügyelt SQL-példányok közzétevője egy adatbázis biztonsági másolatát is használhatja a Azure SQL Database előfizető kivetéséhez.

### <a name="data-migration-scenario"></a>Adatáttelepítési forgatókönyv  

1. A tranzakciós replikálás használatával replikálhatja a SQL Server-adatbázis adatait a Azure SQL Databaseba.  
2. Az adatbázis-másolat frissítéséhez irányítsa át az ügyfelet vagy a középső rétegbeli alkalmazásokat.  
3. Állítsa le a táblázat SQL Server verziójának frissítését, és távolítsa el a kiadványt.  

## <a name="limitations"></a>Korlátozások

Azure SQL Database-előfizetések esetén a következő beállítások nem támogatottak:

- Fájlcsoportok társításának másolása  
- Táblázat particionálási sémáinak másolása  
- Index particionálási sémáinak másolása  
- Felhasználó által definiált statisztikák másolása  
- Alapértelmezett kötések másolása  
- Szabály-kötések másolása  
- Teljes szöveges indexek másolása  
- XML XSD másolása  
- XML-indexek másolása  
- Másolási engedélyek  
- Térbeli indexek másolása  
- Szűrt indexek másolása  
- Adattömörítési attribútum másolása  
- Ritka oszlop attribútumának másolása  
- FileStream konvertálása maximális adattípusra  
- Hierarchyid konvertálása maximális adattípusra  
- Térbeli érték konvertálása maximális adattípusra  
- Kiterjesztett tulajdonságok másolása  

### <a name="limitations-to-be-determined"></a>Meghatározandó korlátozások

- Rendezés másolása  
- Végrehajtás az SP szerializált tranzakciójában  

## <a name="examples"></a>Példák

Hozzon létre egy kiadványt és egy leküldéses előfizetést. További információkért lásd:
  
- [Kiadvány létrehozása](/sql/relational-databases/replication/publish/create-a-publication)
- [Hozzon létre egy leküldéses előfizetést](/sql/relational-databases/replication/create-a-push-subscription/) , amely a kiszolgáló nevét használja előfizetőként (például **N'azuresqldbdns. database. Windows. net**) és a Azure SQL Database nevet célként megadott adatbázisként (például **AdventureWorks**).  

## <a name="see-also"></a>Lásd még:  

- [Tranzakciós replikáció](../managed-instance/replication-transactional-overview.md)
- [Kiadvány létrehozása](/sql/relational-databases/replication/publish/create-a-publication)
- [Leküldéses előfizetés létrehozása](/sql/relational-databases/replication/create-a-push-subscription/)
- [A replikáció típusai](/sql/relational-databases/replication/types-of-replication)
- [Figyelés (replikálás)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Előfizetés inicializálása](/sql/relational-databases/replication/initialize-a-subscription)