---
title: Dedikált SQL-készlet biztonságossá tétele (korábban SQL DW)
description: Tippek a dedikált SQL-készlet (korábban SQL DW) biztonságossá tételéhez és az Azure szinapszis Analytics-megoldások fejlesztéséhez.
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: ad19c976cceab76d6eb0dbfbea5840d9764bffec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092948"
---
# <a name="secure-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Dedikált SQL-készlet (korábban SQL DW) védelme az Azure szinapszis Analytics szolgáltatásban

> [!div class="op_single_selector"]
>
> * [Biztonsági áttekintés](sql-data-warehouse-overview-manage-security.md)
> * [Hitelesítés](sql-data-warehouse-authentication.md)
> * [Titkosítás (portál)](sql-data-warehouse-encryption-tde.md)
> * [Titkosítás (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Ez a cikk végigvezeti a dedikált SQL-készlet (korábban SQL DW) biztonságossá tételének alapjain. Ez a cikk bemutatja, hogyan használhatja az erőforrásokat a hozzáférés korlátozásához, az adatok védelméhez és a figyelési tevékenységekhez a dedikált SQL-készlet (korábban SQL DW) használatával.

## <a name="connection-security"></a>Kapcsolatbiztonság

A kapcsolatbiztonság azt jelenti, hogy hogyan korlátozza és védi az adatbázis kapcsolatait a tűzfalszabályok és a csatlakozástitkosítás használatával.

A tűzfalszabályok használatát mind a [logikai SQL-kiszolgáló](../../azure-sql/database/logical-servers.md) , mind az adatbázisai használják, hogy elutasítja a kapcsolódási kísérleteket a nem jóváhagyott IP-címekről. Ha engedélyezni szeretné az alkalmazás vagy az ügyfélszámítógép nyilvános IP-címének kapcsolatait, először létre kell hoznia egy kiszolgálói szintű tűzfalszabály-szabályt a Azure Portal, REST API vagy a PowerShell használatával.

Az ajánlott eljárás az, hogy a lehető legnagyobb mértékben korlátozza a kiszolgálói szintű tűzfalon keresztül engedélyezett IP-címtartományt.  A dedikált SQL-készlet (korábban SQL DW) helyi számítógépről való eléréséhez győződjön meg arról, hogy a tűzfal a hálózaton és a helyi számítógépen lehetővé teszi a kimenő kommunikációt a 1433-es TCP-porton.  

A dedikált SQL-készlet (korábbi nevén SQL DW) kiszolgálói szintű IP-tűzfalszabályok használatára szolgál. Az adatbázis-szintű IP-tűzfalszabályok nem támogatottak. További információ: [Azure SQL Database tűzfalszabályok](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

A dedikált SQL-készlet (korábbi nevén SQL DW) kapcsolatai alapértelmezés szerint titkosítva vannak.  A rendszer figyelmen kívül hagyja a kapcsolódási beállítások módosítását a titkosítás letiltásához.

## <a name="authentication"></a>Hitelesítés

A hitelesítés azt jelenti, hogy hogyan igazolja az identitását az adatbázishoz való kapcsolódáskor. A dedikált SQL-készlet (korábban SQL DW) jelenleg a felhasználónévvel és jelszóval SQL Server hitelesítést, valamint a Azure Active Directoryt támogatja.

Amikor létrehozta a kiszolgálót az adatbázishoz, a "kiszolgálói rendszergazda" bejelentkezési azonosítót adta meg felhasználónévvel és jelszóval. Ezeknek a hitelesítő adatoknak a használatával a hitelesítő adatokat az adott kiszolgálón található adatbázis-tulajdonosként vagy a "dbo" SQL Server hitelesítésen keresztül végezheti el.

Az ajánlott eljárás szerint azonban a szervezet felhasználóinak egy másik fiókot kell használniuk a hitelesítéshez. Így korlátozhatja az alkalmazás számára biztosított engedélyeket, és csökkentheti a kártékony tevékenységek kockázatát abban az esetben, ha az alkalmazás kódja sebezhető az SQL-injektálási támadásokkal szemben.

SQL Server hitelesített felhasználó létrehozásához kapcsolódjon a kiszolgálón található **Master** adatbázishoz a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy új kiszolgálói bejelentkezést.  Érdemes létrehozni egy felhasználót is a Master adatbázisban. A felhasználók a Master szolgáltatásban való létrehozása lehetővé teszi, hogy a felhasználó egy adatbázis nevének megadása nélkül jelentkezzen be a SSMS-hez hasonló eszközökkel.  Azt is lehetővé teszi, hogy az Object Explorer használatával megtekintsék a kiszolgálókon lévő összes adatbázist.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ezután kapcsolódjon a **DEDIKÁLT SQL-készlethez (korábban SQL DW)** a kiszolgáló-rendszergazdai bejelentkezéssel, és hozzon létre egy adatbázis-felhasználót a létrehozott kiszolgálói bejelentkezés alapján.

```sql
-- Connect to the database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ahhoz, hogy a felhasználó engedélyt nyújtson további műveletek elvégzésére, például bejelentkezések létrehozására vagy új adatbázisok létrehozására, rendelje hozzá a felhasználót a `Loginmanager` `dbmanager` Master adatbázis és a szerepkörökhöz.

További információ ezekről a további szerepkörökről és a SQL Database való hitelesítésről: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseokban](../../azure-sql/database/logins-create-manage.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  A Azure Active Directory használatával való csatlakozással kapcsolatos további információkért lásd: [csatlakozás Azure Active Directory-hitelesítés használatával](sql-data-warehouse-authentication.md).

## <a name="authorization"></a>Engedélyezés

Az engedélyezés arra utal, hogy a hitelesítés és a csatlakozás után egy adatbázison belül mit tehet. Az engedélyezési jogosultságokat a szerepkör-tagságok és engedélyek határozzák meg. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak. A szerepkörök kezeléséhez használhatja a következő tárolt eljárásokat:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A kapcsolódáshoz használt kiszolgálói rendszergazdai fiók a db_owner szerepkör tagja, így teljes körű engedélyekkel rendelkezik az adott adatbázisban. Tartsa meg ezt a fiókot a sémafrissítések üzembe helyezéséhez és egyéb felügyeleti műveletekhez. Használja kevesebb engedéllyel rendelkező „ApplicationUser” fiókot, ha az alkalmazásból kíván csatlakozni az adatbázishoz az alkalmazás által minimálisan igényelt engedélyekkel.

Lehetőség van arra, hogy tovább korlátozza, hogy a felhasználók milyen műveleteket végezhetnek el az adatbázison belül:

* A részletes [engedélyek](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) segítségével szabályozhatja, hogy mely műveleteket végezheti el az egyes oszlopokon, táblákon, nézeteken, sémán, eljárásokon és más objektumokon az adatbázisban. Használjon részletes engedélyeket a leghatékonyabb szabályozáshoz, és adja meg a szükséges minimális engedélyeket.
* A db_datareader és db_datawritertól eltérő [adatbázis-szerepköröket](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) is felhasználhat nagyobb teljesítményű alkalmazás-felhasználói fiókok vagy kevésbé hatékony felügyeleti fiókok létrehozásához. A beépített rögzített adatbázis-szerepkörök egyszerű módot biztosítanak az engedélyek megadására, de a szükségesnél több engedélyt is biztosítanak.
* A [tárolt eljárások](/sql/relational-databases/stored-procedures/stored-procedures-database-engine?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) segítségével korlátozhatja az adatbázisban végezhető műveleteket.

A következő példa olvasási hozzáférést biztosít egy felhasználó által definiált sémához.

```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Az adatbázisok és kiszolgálók Azure Portal vagy a Azure Resource Manager API használatával történő kezeléséhez a portál felhasználói fiókjának szerepkör-hozzárendelései vannak vezérelve. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="encryption"></a>Titkosítás

A transzparens adattitkosítás (TDE) segít megvédeni a kártékony tevékenységek fenyegetését azáltal, hogy titkosítja és visszafejti az adatait a nyugalmi állapotban. Az adatbázis titkosításakor a társított biztonsági másolatok és a tranzakciós naplófájlok az alkalmazások módosítása nélkül titkosítva lesznek. A TDE egy teljes adatbázis tartalmát titkosítja egy szimmetrikus kulcs – az adatbázis-titkosítási kulcs – használatával.

SQL Database az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgáló tanúsítványa minden kiszolgáló esetében egyedi. A Microsoft automatikusan elforgatja ezeket a tanúsítványokat legalább 90 naponta. A használt titkosítási algoritmus az AES-256. A TDE általános ismertetését lásd: [transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Az adatbázis titkosítása a [Azure Portal](sql-data-warehouse-encryption-tde.md) vagy a [T-SQL](sql-data-warehouse-encryption-tde-tsql.md)használatával végezhető el.

## <a name="next-steps"></a>Következő lépések

További részletek és példák a Warehouse-hoz való csatlakozásra különböző protokollokkal: [Csatlakozás DEDIKÁLT SQL-készlethez (korábban SQL DW)](sql-data-warehouse-connect-overview.md).
