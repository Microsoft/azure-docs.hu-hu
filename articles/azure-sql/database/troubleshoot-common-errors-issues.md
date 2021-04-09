---
title: Az Azure SQL Database gyakori csatlakozási problémáinak hibaelhárítása
description: Útmutatást nyújt a Azure SQL Database kapcsolódási problémáinak elhárításához és az egyéb Azure SQL Database vagy az Azure SQL felügyelt példányok adott problémáinak megoldásához
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: ec61f2c67576d6e144d8d4bb7e8ecaaa157db0a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233372"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>A kapcsolódási problémák és a Azure SQL Database és az Azure SQL felügyelt példányával kapcsolatos egyéb hibák elhárítása
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Hibaüzenetek jelenhetnek meg, ha a Azure SQL Database vagy az Azure SQL felügyelt példányának kapcsolata meghiúsul. Ezeket a kapcsolódási problémákat az újrakonfigurálás, a tűzfalbeállítások, a kapcsolat időkorlátja, a helytelen bejelentkezési adatok, illetve az [alkalmazás-tervezési](develop-overview.md) folyamat során az ajánlott eljárások és tervezési irányelvek alkalmazása okozhatja. Emellett, ha elérte a Azure SQL Database vagy SQL felügyelt példányok erőforrásainak maximális korlátját, akkor már nem tud csatlakozni.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Átmeneti hibák hibaüzenetei (40197, 40613 és egyebek)

Az Azure-infrastruktúra az SQL Database szolgáltatásban fellépő nagy mértékű terhelés esetén dinamikusan újra tudja konfigurálni a kiszolgálókat.  Ez a dinamikus viselkedés azt okozhatja, hogy az ügyfélprogram elveszíti a kapcsolatát az adatbázissal vagy a példánnyal. Az ilyen típusú hiba feltételének neve *átmeneti hiba*. Az adatbázis-újrakonfigurálási események egy tervezett esemény (például egy szoftverfrissítés) vagy egy nem tervezett esemény (például egy folyamat összeomlása vagy terheléselosztás) miatt történnek. A legtöbb újrakonfigurálási esemény általában rövid életű, és legfeljebb 60 másodpercnél rövidebb idő alatt elvégezhető. Ezek az események azonban esetenként hosszabb időt vehetnek igénybe, például ha egy nagy tranzakció hosszan futó helyreállítást okoz. A következő táblázat felsorolja azokat az átmeneti hibákat, amelyeket az alkalmazások fogadhatnak SQL Databasehoz való csatlakozáskor.

### <a name="list-of-transient-fault-error-codes"></a>Az átmeneti hibák hibakódjának listája

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 4060 |16 |A bejelentkezés által kért "%. &#x2a;ls" adatbázis nem nyitható meg. A bejelentkezés sikertelen volt. További információ: [4000 – 4999. hibák](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |A szolgáltatás hibát észlelt a kérelem feldolgozásakor. Próbálkozzon újra. Hibakód:% d.<br/><br/>Ez a hibaüzenet akkor jelenik meg, ha a szolgáltatás szoftver vagy hardveres frissítés, hardverhiba vagy bármilyen más feladatátvételi probléma miatt leáll. A 40197-es hiba üzenetében beágyazott hibakód (% d) további információkat nyújt a hiba vagy a feladatátvételi típusról. Néhány példa a hibakódokra a 40197-es hiba üzenetében található, 40020, 40143, 40166 és 40540.<br/><br/>Az újracsatlakozás automatikusan csatlakozik az adatbázis egy kifogástalan állapotú példányához. Az alkalmazásnak meg kell fognia a 40197-es hibát, be kell jelentkeznie a (z) "% d" beágyazott hibakódra az üzenetben a hibaelhárításhoz, és újra kell csatlakoznia SQL Database, amíg az erőforrások elérhetővé válnak, és a kapcsolat újból létrejön. További információért tekintse meg az [átmeneti hibákkal](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults) foglalkozó cikket.|
| 40501 |20 |A szolgáltatás jelenleg foglalt. Próbálja megismételni a kérést 10 másodperc múlva. Incidens azonosítója:% ls. Kód:% d. További információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md).|
| 40613 |17 |A (z)%. &#x2a;ls kiszolgáló "%. &#x2a;ls" adatbázisa jelenleg nem érhető el. Később próbálja megismételni a kapcsolatokat. Ha a probléma továbbra is fennáll, forduljon az ügyfél-támogatási szolgálathoz, és adja meg nekik a (z)%. &#x2a;ls munkamenet-nyomkövetési AZONOSÍTÓját.<br/><br/> Ez a hiba akkor fordulhat elő, ha már létezik egy meglévő dedikált rendszergazdai kapcsolódás (DAC) az adatbázishoz. További információért tekintse meg az [átmeneti hibákkal](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults) foglalkozó cikket.|
| 49918 |16 |A kérelem nem dolgozható fel. Nincs elég erőforrás a kérelem feldolgozásához.<br/><br/>A szolgáltatás jelenleg foglalt. Próbálkozzon újra a kéréssel. További információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md). |
| 49919 |16 |A létrehozási vagy frissítési kérelem nem dolgozható fel. Túl sok létrehozási vagy frissítési művelet van folyamatban a (z) "% ld" előfizetéshez.<br/><br/>A szolgáltatás foglalt, több létrehozására vagy frissítésre vonatkozó kérelmet dolgoz fel az előfizetéshez vagy a kiszolgálóhoz. A kérések jelenleg le vannak tiltva az erőforrás-optimalizáláshoz. Lekérdezés [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a függőben lévő műveletekhez. Várjon, amíg a függőben lévő létrehozási vagy frissítési kérelmek befejeződik, vagy törölje a függőben lévő kérelmek valamelyikét, majd ismételje meg a kérést. További információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md). |
| 49920 |16 |A kérelem nem dolgozható fel. Túl sok művelet van folyamatban a (z) "% ld" előfizetéshez.<br/><br/>A szolgáltatás foglalt több kérést dolgoz fel ehhez az előfizetéshez. A kérések jelenleg le vannak tiltva az erőforrás-optimalizáláshoz. A műveleti állapot lekérdezési [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Várjon, amíg a függőben lévő kérelmek befejeződik, vagy törölje a függőben lévő kérelmek valamelyikét, majd ismételje meg a kérést. További információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md). |
| 4221 |16 |A "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING" hosszú várakozás miatt nem sikerült bejelentkezni a Read-másodlagosra. A replika nem érhető el a bejelentkezéshez, mert a rendszer a replikák újrahasznosítása során a folyamatban lévő tranzakciók esetében hiányzik a sorok verziószáma. A probléma megoldásához visszaállíthatja vagy véglegesítheti az aktív tranzakciókat az elsődleges replikán. Ennek az állapotnak az előfordulásait a hosszú írási tranzakciók elkerülésével lehet csökkenteni. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Az átmeneti kapcsolódási problémák megoldásának lépései

1. Győződjön meg arról, hogy a [Microsoft Azure szolgáltatás irányítópultján](https://azure.microsoft.com/status) az alkalmazás által jelentett hibák ideje alatt bekövetkezett összes ismert leállás látható.
2. A felhőalapú szolgáltatásokhoz, például a Azure SQL Databasehoz csatlakozó alkalmazásoknak rendszeres újrakonfigurálási eseményeket kell elvárniuk, és az újrapróbálkozási logikát kell végrehajtaniuk a hibák kezeléséhez a felhasználók számára.
3. Mivel az adatbázis megközelíti az erőforrás-korlátozásokat, úgy tűnhet, hogy átmeneti kapcsolódási probléma. Lásd: [erőforrás-korlátok](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Ha a kapcsolódási problémák továbbra is fennállnak, vagy ha az alkalmazás az időtartamot meghaladja a 60 másodpercnél, vagy ha egy adott nap több előfordulását látja a hibával, az Azure-támogatási kérést az [Azure](https://azure.microsoft.com/support/options) támogatási webhelyén, a **támogatás beszerzése** lehetőség választásával kérheti le.

#### <a name="implementing-retry-logic"></a>Újrapróbálkozási logika implementálása

Erősen ajánlott, hogy az ügyfélalkalmazás újra megismételje a logikát, hogy újra létre lehessen hozni a kapcsolatot, miután megadta az átmeneti hibák idejét.  Javasoljuk, hogy az első újrapróbálkozás előtt 5 másodpercig várjon. Az 5 másodpercnél rövidebb idő elteltével próbálkozzon újra a Cloud Service-t érő kockázatokkal. Minden további újrapróbálkozás esetén a késleltetés exponenciálisan növekszik, legfeljebb 60 másodpercig.

Az újrapróbálkozási logikával kapcsolatos Példákért lásd:

- [Rugalmas csatlakozás az SQL-hez a ADO.NET használatával](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Rugalmas csatlakozás az SQL-hez a PHP-vel](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Az átmeneti hibák megoldásával kapcsolatos további információkért tekintse meg az [átmeneti kapcsolódási hibák elhárítása SQL Database](troubleshoot-common-connectivity-issues.md)

A ADO.NET-t használó ügyfelek *blokkolási időszakának* megvitatását a következő témakörben érheti el: [kapcsolatok készletezése (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Hálózattal kapcsolatos vagy példány-specifikus hiba történt a kiszolgálóval létesített kapcsolat létrehozása közben

A probléma akkor fordul elő, ha az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A probléma megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>A kiszolgáló/példány nem található vagy nem érhető el (hiba 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>26. hiba: a megadott kiszolgáló keresésekor hiba történt

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>40-es hiba: nem sikerült megnyitni a kiszolgálót

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>10053-es hiba: átviteli szintű hiba történt a kiszolgáló eredményeinek fogadásakor

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Ezek a problémák akkor fordulnak elő, ha az alkalmazás nem tud csatlakozni a kiszolgálóhoz.

A problémák megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Tűzfallal kapcsolatos problémák miatt nem lehet kapcsolódni a kiszolgálóhoz

### <a name="error-40615-cannot-connect-to--servername-"></a>40615-es hiba: nem lehet csatlakozni < servername >

A probléma megoldásához [konfigurálja a tűzfal beállításait SQL Database a Azure Portal használatával](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>5. hiba: nem lehet csatlakozni a (z) < servername >

A probléma megoldásához ellenőrizze, hogy a 1433-es port nyitva van-e a kimenő kapcsolatok számára az ügyfél és az Internet közötti összes tűzfalon.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nem lehet bejelentkezni a kiszolgálóra (hibák 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>A következő felhasználó bejelentkezése sikertelen volt: "< Felhasználónév >"

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

A probléma megoldásához forduljon a szolgáltatás rendszergazdájához, és adjon meg egy érvényes felhasználónevet és jelszót.

A szolgáltatás rendszergazdája általában a következő lépésekkel adhatja hozzá a bejelentkezési hitelesítő adatokat:

1. Jelentkezzen be a kiszolgálóra SQL Server Management Studio (SSMS) használatával.
2. Futtassa a következő SQL-lekérdezést a Master adatbázisban, és győződjön meg róla, hogy a bejelentkezési név le van tiltva:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins;
   ```

3. Ha a név le van tiltva, engedélyezze a következő utasítással:

   ```sql
   ALTER LOGIN <User name> ENABLE;
   ```

4. Ha az SQL-bejelentkezési Felhasználónév nem létezik, szerkessze és futtassa a következő SQL-lekérdezést új SQL-bejelentkezés létrehozásához:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>';
   GO
   ```

5. A SSMS Object Explorer bontsa ki az **adatbázisok** csomópontot.
6. Válassza ki azt az adatbázist, amelyhez engedélyt kíván adni a felhasználónak.
7. Kattintson a jobb gombbal a **Biztonság** elemre, majd válassza az **új**, **felhasználó** lehetőséget.
8. A generált parancsfájl helyőrzővel, szerkessze és futtassa a következő SQL-lekérdezést:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>;
   GO

   -- Add user to the database owner role
   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>';
   GO
   ```

   A használatával meghatározott `sp_addrolemember` felhasználókat adott adatbázis-szerepkörökhöz is leképezheti.

   > [!NOTE]
   > Azure SQL Database az adatbázis-szerepkör tagságának kezeléséhez vegye figyelembe az újabb [módosítási szerepkör](/sql/t-sql/statements/alter-role-transact-sql) -szintaxist.  

További információ: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>A kapcsolat időtúllépése lejárt hibák

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): a kapcsolat időtúllépése lejárt

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. reSqlClient. SqlException (0x80131904): időtúllépés lejárt

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. adat. Entity. Core. EntityException: az alapul szolgáló szolgáltató nem sikerült a megnyitáskor

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nem lehet kapcsolódni < kiszolgálónévhez >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Ezek a kivételek a kapcsolatok vagy a lekérdezési problémák miatt fordulhatnak elő. Annak ellenőrzéséhez, hogy ezt a hibát a kapcsolódási problémák okozták-e, tekintse meg azt, [hogy a hibát a kapcsolódási probléma okozta](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)-e.

A kapcsolat időtúllépése azért fordul elő, mert az alkalmazás nem tud csatlakozni a kiszolgálóhoz. A probléma megoldásához próbálja meg a [gyakori kapcsolódási problémák elhárítása](#steps-to-fix-common-connection-issues) című szakaszban ismertetett lépéseket (a sorrend szerint).

## <a name="resource-governance-errors"></a>Erőforrás-irányítási hibák

### <a name="error-10928-resource-id-d"></a>10928-es hiba: erőforrás-azonosító:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

A probléma megkerüléséhez próbálkozzon az alábbi módszerek egyikével:

- Ellenőrizze, hogy vannak-e hosszan futó lekérdezések.

  > [!NOTE]
  > Ez egy minimalista megközelítés, amely esetleg nem oldja meg a problémát. A hosszú futású vagy blokkoló lekérdezések hibaelhárításával kapcsolatos részletes információkért lásd: [Azure SQL Database blokkolási problémák megismerése és megoldása](understand-resolve-blocking.md).

1. A következő SQL-lekérdezés futtatásával tekintse meg a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) nézetet a blokkoló kérelmek megtekintéséhez:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

1. Határozza meg a fej-blokkoló **bemeneti pufferét** a [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dinamikus felügyeleti funkciójával, valamint a jogsértő lekérdezés session_idával, például:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

1. A fej-blokkoló lekérdezésének hangolása.

Ha az adatbázis folyamatosan eléri a korlátot a blokkoló és a hosszan futó lekérdezések kezelése ellenére, érdemes lehet egy kiadásra frissíteni a [további erőforrás-kiadásokkal](https://azure.microsoft.com/pricing/details/sql-database/).

Az adatbázis-korlátokkal kapcsolatos további információkért lásd:  [a kiszolgálók erőforrás-korlátainak SQL Database](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>10929-es hiba: erőforrás-azonosító: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>40501-es hiba: a szolgáltatás jelenleg foglalt

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Ez egy hajtómű-szabályozási hiba, amely azt jelzi, hogy túllépik az erőforrás-korlátozásokat.

Az erőforrás-korlátokkal kapcsolatos további információkért lásd: [logikai SQL Server erőforrás-korlátok](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>40544-es hiba: az adatbázis elérte a mérete kvótáját

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ez a hiba akkor fordul elő, amikor az adatbázis elérte a méretre vonatkozó kvótát.

A következő lépések segíthetnek a probléma megkerülésében vagy további lehetőségek megadásában:

1. Az Azure Portal irányítópultjának használatával vizsgálja meg az adatbázis aktuális méretét.

   > [!NOTE]
   > A következő SQL-lekérdezés futtatásával megállapíthatja, hogy mely táblák használják a legtöbb helyet, ezért lehetséges a kitakarításra alkalmas jelöltek futtatása:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Ha az aktuális méret nem haladja meg a kiadásban támogatott maximális méretet, az ALTER DATABASE paranccsal növelheti a MAXSIZE beállítást.
3. Ha az adatbázis már elmúlt a maximálisan támogatott méret a kiadásban, próbálkozzon az alábbi lépésekkel:

   - Normál adatbázis-karbantartási tevékenységek végrehajtása. Például megtisztíthatja a nemkívánatos adatmennyiséget a csonkítás/törlés paranccsal, vagy áthelyezheti az adatvesztést SQL Server Integration Services (SSIS) vagy a tömeges másolási program (BCP) segédprogram használatával.
   - Particionálja vagy törölje az adatvesztést, az indexek eldobását, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat.
   - Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](./single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>40549-es hiba: a munkamenet leáll, mert hosszú ideig futó tranzakció van

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Ha többször is megtapasztalja ezt a hibát, próbálja meg elhárítani a problémát a következő lépésekkel:

1. Tekintse meg a sys.dm_exec_requests nézetet, hogy megjelenjenek-e a total_elapsed_time oszlop magas értékkel rendelkező nyitott munkamenetei. A következő SQL-szkript futtatásával végezze el az ellenőrzés végrehajtását:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Határozza meg a fej-blokkoló **bemeneti pufferét** a [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dinamikus felügyeleti funkciójával, valamint a jogsértő lekérdezés session_idával, például:

   ```sql 
   SELECT * FROM sys.dm_exec_input_buffer (100,0);
   ```

3. A lekérdezés finomhangolása.

    > [!Note]
    > A Azure SQL Database blokkolásának hibaelhárításával kapcsolatos további információkért lásd: [Azure SQL Database blokkolási problémák megismerése és megoldása](understand-resolve-blocking.md).

A lekérdezések kötegelt feldolgozását is érdemes figyelembe venni. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](../performance-improve-use-batching.md).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>40551-es hiba: a munkamenet túlzott TEMPDB-használat miatt megszakadt

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

A probléma megkerüléséhez kövesse az alábbi lépéseket:

1. Módosítsa a lekérdezéseket, hogy csökkentse az ideiglenes tábla területének használatát.
2. Ha már nincs szükség rájuk, dobja el az ideiglenes objektumokat.
3. Táblázatok csonkítása vagy a nem használt táblák eltávolítása.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>40552-es hiba: a munkamenet a tranzakciós napló túlzott kihasználtsága miatt megszakadt

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

A hiba elhárításához próbálkozzon a következő módszerekkel:

- A probléma INSERT, Update vagy DELETE művelet miatt fordulhat elő.
A kötegelt feldolgozás végrehajtásával vagy a több kisebb tranzakcióra való felosztásával próbálja meg csökkenteni a azonnal üzemeltetett sorok számát.
- A probléma az index újraépítési műveletei miatt fordulhat elő. A probléma megkerüléséhez győződjön meg arról, hogy a táblázatban szereplő sorok száma * (bájt + 80-ben frissített mező átlagos mérete) < 2 gigabájt (GB).

  > [!NOTE]
  > Az indexek újraépítése esetén a frissített mező átlagos méretét az indexek átlagos méretével kell helyettesíteni.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>40553-es hiba: a munkamenet túl sok memóriahasználat miatt megszakadt

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

A probléma megkerüléséhez próbálja meg optimalizálni a lekérdezést.

Részletes hibaelhárítási eljárás: a [lekérdezés a felhőben fut?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>További erőforrás-irányítási hibaüzenetek táblája

| Hibakód | Súlyosság | Leírás |
| ---:| ---:|:--- |
| 10928 |20 |Erőforrás-azonosító:% d. Az adatbázis% s korlátja% d, és elérte a következőt:. További információ: [az önálló és a készletezett adatbázisok SQL Database erőforrás-korlátai](resource-limits-logical-server.md).<br/><br/>Az erőforrás-azonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkaszálak esetében az erőforrás-azonosító = 1. A munkamenetek esetében az erőforrás-azonosító = 2.<br/><br/>A hibával és megoldásával kapcsolatos további információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md). |
| 10929 |20 |Erőforrás-azonosító:% d. A (z)% s minimális garancia% d, a maximális korlát% d, az adatbázis jelenlegi használata pedig% d. Azonban a kiszolgáló jelenleg túl elfoglalt ahhoz, hogy támogassa a (z)% d-nál nagyobb kérelmeket ehhez az adatbázishoz. Az erőforrás-azonosító azt az erőforrást jelzi, amely elérte a korlátot. Munkaszálak esetében az erőforrás-azonosító = 1. A munkamenetek esetében az erőforrás-azonosító = 2. További információkért lásd: <br/>&bull;&nbsp; [Logikai SQL Server erőforrás-korlátok](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-alapú korlátok önálló adatbázisokhoz](service-tiers-dtu.md)<br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md). <br/>Ellenkező esetben próbálkozzon újra később. |
| 40544 |20 |Az adatbázis elérte a méretre vonatkozó kvótát. Particionálja vagy törölje az adatvesztést, az indexek eldobását, vagy a lehetséges megoldásokról a dokumentációban tájékozódhat. Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).|
| 40549 |16 |A munkamenet meg lett szakítva, mert hosszú ideig futó tranzakció van. Próbálja meg lerövidíteni a tranzakciót. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](../performance-improve-use-batching.md).|
| 40550 |16 |A munkamenet meg lett szakítva, mert túl sok zárolást szerzett. Próbáljon meg egy tranzakción belül kevesebb sort olvasni vagy módosítani. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](../performance-improve-use-batching.md).|
| 40551 |16 |A munkamenet a túlzott használat miatt megszakadt `TEMPDB` . Próbálja meg módosítani a lekérdezést, hogy csökkentse az ideiglenes tábla területének használatát.<br/><br/>Ha ideiglenes objektumokat használ, az ideiglenes objektumok eldobásával lemezterületet takaríthat meg az `TEMPDB` adatbázisban, miután a munkamenet már nem igényel rájuk. További információ a SQL Database tempdb használatáról: tempdb- [adatbázis SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |A munkamenet meg lett szakítva a tranzakciós naplók túlzott kihasználtsága miatt. Próbáljon meg kevesebb sort módosítani egyetlen tranzakcióban. A kötegelt feldolgozással kapcsolatos további információkért lásd: [a Batch használata az SQL Database alkalmazások teljesítményének javításához](../performance-improve-use-batching.md).<br/><br/>Ha a segédprogram vagy a osztály használatával hajtja végre a tömeges beszúrást `bcp.exe` `System.Data.SqlClient.SqlBulkCopy` , akkor a `-b batchsize` vagy a beállítások használatával `BatchSize` korlátozza az egyes tranzakciókban a kiszolgálóra másolt sorok számát. Ha az utasítással újraépít egy indexet `ALTER INDEX` , próbálja meg használni a `REBUILD WITH ONLINE = ON` kapcsolót. A virtuális mag-vásárlási modell tranzakciós naplójának méretével kapcsolatos információkért lásd: <br/>&bull;&nbsp; [virtuális mag-alapú korlátok önálló adatbázisokhoz](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md)<br/>&bull;Az &nbsp; [Azure SQL felügyelt példányának erőforrás-korlátai](../managed-instance/resource-limits.md).|
| 40553 |16 |A munkamenet túl sok memóriahasználat miatt megszakadt. Próbálja meg módosítani a lekérdezést úgy, hogy kevesebb sort dolgozzon fel.<br/><br/>A `ORDER BY` `GROUP BY` Transact-SQL-kód számának és műveleteinek csökkentése csökkenti a lekérdezés memóriabeli követelményeit. Az adatbázis-méretezéssel kapcsolatban lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Rugalmas készlettel kapcsolatos hibák

A rugalmas készletek létrehozásával és használatával kapcsolatos hibák a következők:

| Hibakód | Súlyosság | Leírás | Javító művelet |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |A rugalmas készlet elérte a tárolási korlátot. A rugalmas készlet tárolási kihasználtsága nem haladhatja meg a (z) (% d) MB-ot. Egy adatbázisba való adatírásra tett kísérlet, ha elérte a rugalmas készlet tárolási korlátját. Az erőforrás-korlátokkal kapcsolatos további információkért lásd: <br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md). <br/> |Ha lehetséges, növelje a rugalmas készlethez való DTU és/vagy a tárterület hozzáadását, csökkentse a rugalmas készletben lévő egyes adatbázisok által használt tárterületet, vagy távolítsa el az adatbázisokat a rugalmas készletből. A rugalmas készlet skálázásával kapcsolatban lásd: [rugalmas készlet erőforrásainak](elastic-pool-scale.md)méretezése. További információ az adatbázisok nem felhasznált területének eltávolításáról: [Azure SQL Database-adatbázisok tárterületének kezelése](file-space-manage.md).|
| 10929 | 16 |A (z)% s minimális garancia% d, a maximális korlát% d, az adatbázis jelenlegi használata pedig% d. Azonban a kiszolgáló jelenleg túl elfoglalt ahhoz, hogy támogassa a (z)% d-nál nagyobb kérelmeket ehhez az adatbázishoz. Az erőforrás-korlátokkal kapcsolatos további információkért lásd: <br/>&bull;&nbsp; [Rugalmas készletek DTU-alapú korlátai](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [rugalmas készletek virtuális mag-alapú korlátai](resource-limits-vcore-elastic-pools.md). <br/> Ellenkező esetben próbálkozzon újra később. DTU/virtuális mag/perc/adatbázis; DTU/virtuális mag-adatbázis maximális száma. Az egyidejű feldolgozók (kérelmek) teljes száma a rugalmas készletben lévő összes adatbázisban, a készlet korlátjának túllépése miatt. |Ha lehetséges, érdemes lehet növelni a rugalmas készlet DTU vagy virtuális mag, hogy növelje a munkavégző korlátot, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40844 | 16 |A (z) "% ls" kiszolgáló "% ls" adatbázisa egy rugalmas készletben lévő "% ls" kiadási adatbázis, és nem lehet folytonos másolási kapcsolat.  |N/A |
| 40857 | 16 |Nem található rugalmas készlet a következő kiszolgálóhoz: "% ls", rugalmas készlet neve: "% ls". A megadott rugalmas készlet nem létezik a megadott kiszolgálón. | Adja meg a rugalmas készlet érvényes nevét. |
| 40858 | 16 |A (z) "% ls" rugalmas készlet már létezik a következő kiszolgálón: "% ls". A megadott rugalmas készlet már létezik a megadott kiszolgálón. | Adja meg az új rugalmas készlet nevét. |
| 40859 | 16 |A rugalmas készlet nem támogatja a (z)% ls szolgáltatási szintet. A megadott szolgáltatási szintet nem támogatja a rugalmas készlet kiépítés. |Adja meg a megfelelő kiadást, vagy hagyja üresen a szolgáltatási szintet, hogy az alapértelmezett szolgáltatási szintet használja. |
| 40860 | 16 |A (z) "% ls" rugalmas készlet és a (z) "% ls" szolgáltatási cél nem érvényes. A rugalmas készlet és a szolgáltatási szintek csak akkor adhatók meg együtt, ha az erőforrástípus a következőként van megadva: "ElasticPool". |A rugalmas készlet és a szolgáltatási szintek helyes kombinációjának meghatározása. |
| 40861 | 16 |A (z)% adatbázis-kiadás *az ls nem lehet eltér a rugalmas készlet szolgáltatási szintjétől, amely a következő:%.* ls ". Az adatbázis kiadása eltér a rugalmas készlet szolgáltatási szintjétől. |Ne határozzon meg olyan adatbázis-kiadást, amely eltér a rugalmas készlet szolgáltatási szintjétől.  Vegye figyelembe, hogy az adatbázis-kiadást nem kell megadni. |
| 40862 | 16 |Ha a rugalmas készlet szolgáltatási célja meg van adva, akkor meg kell adni a rugalmas készlet nevét. A rugalmas készlet szolgáltatási célja nem azonosítja egyedi módon a rugalmas készletet. |Ha a rugalmas készlet szolgáltatási célkitűzését használja, adja meg a rugalmas készlet nevét. |
| 40864 | 16 |A rugalmas készlet DTU legalább (% d) DTU kell lennie a (z)%. * ls szolgáltatási szinten. Kísérlet a minimális korlát alatti rugalmas készlet DTU beállítására. |Próbálja megismételni a rugalmas készlet DTU beállítását legalább a minimális korlátra. |
| 40865 | 16 |A rugalmas készlet DTU nem lehet hosszabb (% d) DTU a (z)%. * ls szolgáltatási szinten. Kísérlet a maximális korlátot meghaladó rugalmas készlet DTU beállítására. |Próbálja meg újra beállítani a rugalmas készlet DTU, hogy ne legyen nagyobb a maximális korlátnál. |
| 40867 | 16 |A (z)%. * ls szolgáltatási szinten a DTU Max/adatbázisnak legalább (% d) kell lennie. A DTU maximális száma a támogatott korlát alatt állítható be. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40868 | 16 |A DTU maximális száma adatbázison nem lehet nagyobb (% d) a (z)%. * ls szolgáltatási szinten. Kísérlet történt a DTU maximális értékének beállítására az adatbázison a támogatott korláton túl. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40870 | 16 |A (z)%. * ls szolgáltatási szinten a DTU minimális száma (% d) nem lehet hosszabb. Kísérlet a DTU minimális értékének beállítására a támogatott korláton túl. | Érdemes lehet a rugalmas készlet szolgáltatási szintjét használni, amely támogatja a kívánt beállítást. |
| 40873 | 16 |Az adatbázisok (% d) és a DTU minimális száma (% d) nem haladhatja meg a rugalmas készlet (% d) DTU. A rugalmas készletben lévő, a rugalmas készlet DTU meghaladó DTU minimális értékének megadására tett kísérlet. | Vegye fontolóra a rugalmas készlet DTU növelését, vagy csökkentse az adatbázis DTU, vagy csökkentse az adatbázisok számát a rugalmas készletben. |
| 40877 | 16 |A rugalmas készlet nem törölhető, ha nem tartalmaz adatbázisokat. A rugalmas készlet egy vagy több adatbázist tartalmaz, ezért nem törölhető. |A törléshez távolítsa el az adatbázisokat a rugalmas készletből. |
| 40881 | 16 |A (z) "%. * ls" rugalmas készlet elérte az adatbázisa számának korlátját.  A rugalmas készlet adatbázisának száma nem haladhatja meg a (z)% d DTU rendelkező rugalmas készletet. Kísérlet történt az adatbázis rugalmas készlethez való létrehozására vagy hozzáadására, ha a rugalmas készlet adatbázisának száma elérte a korlátot. | Ha lehetséges, érdemes lehet növelni a rugalmas készlet DTU, hogy növelje az adatbázis-korlátot, vagy távolítsa el az adatbázisokat a rugalmas készletből. |
| 40889 | 16 |A (z) "%. * ls" rugalmas készlet DTU vagy tárolási korlátja nem csökkenthető, mert nem biztosít elegendő tárterületet az adatbázisai számára. Kísérlet a rugalmas készlet tárolási korlátjának csökkentésére a tárterület-használat alatt. | Vegye figyelembe a rugalmas készletben lévő egyes adatbázisok tárolási használatát, vagy távolítsa el az adatbázisokat a készletből annak érdekében, hogy csökkentse annak DTU vagy tárolási korlátját. |
| 40891 | 16 |A DTU minimális száma (% d) nem haladhatja meg a DTU-adatbázis maximális számát (% d). Kísérlet a DTU minimális értékének megadására, mint az adatbázis-DTU maximális száma. |Győződjön meg arról, hogy a DTU-adatbázis min. száma nem haladja meg a maximális DTU. |
| TBD | 16 |A rugalmas készletben lévő önálló adatbázisok tárolási mérete nem haladhatja meg a (z) "%. * ls" szolgáltatási rétegek rugalmas készlete által megengedett maximális méretet. Az adatbázis maximális mérete meghaladja a rugalmas készlet szolgáltatási szintje által megengedett maximális méretet. |Állítsa be az adatbázis maximális méretét a rugalmas készlet szolgáltatási szintje által megengedett maximális méret korlátain belül. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>A bejelentkezés által kért "Master" adatbázis nem nyitható meg. A bejelentkezés sikertelen

Ez a probléma azért fordul elő, mert a fióknak nincs engedélye a Master adatbázis elérésére. Alapértelmezés szerint azonban SQL Server Management Studio (SSMS) megpróbál csatlakozni a Master adatbázishoz.

A probléma megoldásához kövesse az alábbi lépéseket:

1. A SSMS bejelentkezési képernyőjén válassza a **Beállítások**, majd a **kapcsolatok tulajdonságai** elemet.
2. A **Kapcsolódás az adatbázishoz** mezőben adja meg a felhasználó alapértelmezett adatbázisának nevét az alapértelmezett bejelentkezési adatbázisként, majd válassza a **Kapcsolódás** lehetőséget.

   ![Kapcsolat tulajdonságai](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Erősítse meg, hogy hiba történt-e egy csatlakozási probléma miatt

Annak ellenőrzéséhez, hogy hiba történt-e egy csatlakozási probléma miatt, tekintse át a verem nyomkövetését olyan keretek esetében, amelyek a következőhöz hasonló kapcsolat megnyitására irányuló hívásokat jelenítenek meg (jegyezze fel a **SqlConnection** osztályra mutató hivatkozást):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Ha a kivételt a lekérdezési problémák váltották ki, az alábbihoz hasonló hívási verem jelenik meg (jegyezze fel a **SqlCommand** osztályra mutató hivatkozást). Ebben a helyzetben [a lekérdezések finomhangolása](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

A teljesítmény finomhangolásával kapcsolatos további útmutatásért tekintse meg a következő forrásokat:

- [Az Azure SQL indexek és statisztikák karbantartása](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [A lekérdezés teljesítményének manuális finomhangolása Azure SQL Database](./performance-guidance.md)
- [Teljesítmény-Azure SQL Database figyelése dinamikus felügyeleti nézetek használatával](./monitoring-with-dmvs.md)
- [A lekérdezési tároló üzemeltetése Azure SQL Database](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>A gyakori kapcsolati problémák javítási lépései

1. Győződjön meg arról, hogy a TCP/IP engedélyezve van-e az alkalmazáskiszolgáló ügyfél-protokollja számára. További információ: az [ügyféloldali protokollok konfigurálása](/sql/database-engine/configure-windows/configure-client-protocols). Azokon az alkalmazás-kiszolgálókon, amelyeken nincs telepítve az SQL-eszközök, ellenőrizze, hogy a TCP/IP engedélyezve van-e a **cliconfg.exe** (SQL Server Client Network segédprogram) futtatásával.
2. Ellenőrizze az alkalmazás kapcsolódási karakterláncát, és győződjön meg arról, hogy megfelelően van-e konfigurálva. Győződjön meg például arról, hogy a kapcsolatok karakterlánca a megfelelő portot (1433) és teljes kiszolgálónevet adja meg.
Lásd a [kapcsolatok adatainak beolvasása](./connect-query-ssms.md#get-server-connection-information)című témakört.
3. Próbálja meg növelni a kapcsolat időtúllépési értékét. Legalább 30 másodperces kapcsolati időtúllépés használatát javasoljuk.
4. Tesztelje az alkalmazáskiszolgáló és a Azure SQL Database közötti kapcsolatot a [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), a UDL-fájl, a ping vagy a Telnet használatával. További információ: [kapcsolódási problémák elhárítása](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) és [diagnosztika a kapcsolódási problémákhoz](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Hibaelhárítási lépésként egy másik ügyfélszámítógépen is tesztelheti a kapcsolatot.

5. Ajánlott eljárásként győződjön meg arról, hogy az újrapróbálkozási logika van érvényben. Az újrapróbálkozási logikával kapcsolatos további információkért lásd: [átmeneti hibák és kapcsolódási hibák elhárítása SQL Database](./troubleshoot-common-connectivity-issues.md).

Ha ezek a lépések nem oldják meg a problémát, próbálkozzon a további adatok gyűjtésével, majd forduljon az ügyfélszolgálathoz. Ha az alkalmazás egy felhőalapú szolgáltatás, engedélyezze a naplózást. Ez a lépés a hiba UTC időbélyegzőjét adja vissza. Emellett a SQL Database visszaadja a nyomkövetési azonosítót. A [Microsoft ügyfél-támogatási szolgálata](https://azure.microsoft.com/support/options/) ezeket az információkat használhatja.

A naplózás engedélyezésével kapcsolatos további információkért lásd: a [diagnosztikai naplózás engedélyezése a Azure app Service alkalmazásokban](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Következő lépések

- [Az Azure SQL Database kapcsolati architektúrája](./connectivity-architecture.md)
- [A Azure SQL Database és az Azure szinapszis Analytics hálózati hozzáférés-vezérlés](./network-access-controls-overview.md)