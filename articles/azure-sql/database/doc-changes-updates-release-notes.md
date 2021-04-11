---
title: Újdonságok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerkedjen meg a Azure SQL Database & SQL felügyelt példányának új szolgáltatásaival és dokumentációs funkcióival.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: sstein
ms.openlocfilehash: 9827a40b2ebc91c17ad7b5457259b8d82565edee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640082"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>A Azure SQL Database & SQL felügyelt példányának újdonságai
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk a jelenleg nyilvános előzetes verzióban elérhető, Azure SQL Database és az Azure SQL felügyelt példányának funkcióit sorolja fel. SQL Database és SQL felügyelt példányok frissítései és tökéletesítései: [SQL Database & SQL felügyelt példányok szolgáltatásának frissítései](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseihez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="whats-new"></a>Újdonságok

A Azure SQL Database és az Azure SQL felügyelt példányának dokumentációja külön szakaszokra oszlik. Azt is frissítettük, hogyan hivatkozunk felügyelt példányra *Azure SQL Database felügyelt példányról* az *Azure SQL felügyelt* példányára.

Ezt azért hajtottuk végre, mert bizonyos funkciók és funkciók nagy mértékben változnak egyetlen adatbázis és egy felügyelt példány között, és egyre nagyobb kihívást jelent a Azure SQL Database és az Azure SQL felügyelt példánya között az egyes megosztott cikkekben található összetett árnyalatok ismertetése.

A különböző Azure SQL-termékek közötti pontosítás egyszerűsíti és egyszerűsíti az Azure-beli SQL Server adatbázismotor használatának folyamatát, függetlenül attól, hogy egyetlen felügyelt adatbázis a Azure SQL Databaseban, egy teljes körűen felügyelt példány, amely több adatbázist üzemeltet az Azure SQL felügyelt példányain, vagy az ismerős helyszíni SQL Server terméket, amely az Azure-beli virtuális gépen üzemel.

Vegye figyelembe, hogy ez a folyamat folyamatban van, és nem minden cikket frissítették még. Például a Transact-SQL (T-SQL) utasítások, a tárolt eljárások és a Azure SQL Database és az Azure SQL felügyelt példánya között megosztott számos szolgáltatás dokumentációja még nem fejeződött be, ezért köszönjük türelmét, hogy továbbra is tisztázza a tartalmat. 

Ez a táblázat a terminológia változásának gyors összehasonlítását biztosítja: 


|**Új kifejezés**  | **Előző kifejezés**  |**Magyarázat** |
|---------|---------|---------|
|**Felügyelt Azure SQL-példány** | *Felügyelt példány* Azure SQL Database| Az Azure SQL felügyelt példánya a saját terméke az Azure SQL családon belül, nem csupán a Azure SQL Databaseon belüli üzembe helyezési lehetőség. | 
|**Azure SQL Database**|*Önálló adatbázis* Azure SQL Database| Ha nincs kifejezetten megadva, a terméknév Azure SQL Database a rugalmas készletbe telepített önálló adatbázisokat és adatbázisokat is tartalmazza. |
|**Azure SQL Database**|*Rugalmas készlet* Azure SQL Database| Ha nincs kifejezetten megadva, a terméknév Azure SQL Database a rugalmas készletbe telepített önálló adatbázisokat és adatbázisokat is tartalmazza.  |
|**Azure SQL Database** |Azure SQL Database | Bár a kifejezés változatlan marad, az már csak az önálló adatbázisra és a rugalmas készletekre vonatkozik, és nem tartalmazza a felügyelt példányt. |
| **Azure SQL**| N/A | Ez az Azure-ban elérhető SQL Server adatbázismotor-termékek családját jelenti: Azure SQL Database, Azure SQL felügyelt példány és Azure-beli virtuális gépeken SQL Server. | 

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Szolgáltatás | Részletek |
| ---| --- |
| Rugalmas adatbázis-feladatok (előzetes verzió) | További információ: [rugalmas feladatok létrehozása, konfigurálása és kezelése](elastic-jobs-overview.md). |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés áttekintése](elastic-query-overview.md). |
| Rugalmas tranzakciók | [Elosztott tranzakciók felhőalapú adatbázisok között](elastic-transactions-overview.md). |
| A Azure Portal lekérdezés-szerkesztője |További információ: [a Azure Portal SQL Query Editor használata az adatok összekapcsolásához és lekérdezéséhez](connect-query-portal.md).|
|SQL Analytics|További információ: [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

| Szolgáltatás | Részletek |
| ---| --- |
| [Elosztott tranzakciók](/azure/azure-sql/database/elastic-transactions-overview) | Elosztott tranzakciók a felügyelt példányok között. |
| [Példánykészletek](/azure/sql-database/sql-database-instance-pools) | A kisebb SQL-példányok felhőbe való áttelepíthető kényelmes és költséghatékony módszer. |
| [Példány szintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)](/sql/t-sql/statements/create-login-transact-sql) | Hozzon létre példány-szintű bejelentkezéseket a [külső szolgáltatói utasítás létrehozási azonosítójával](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . |
| [Tranzakciós replikáció](../managed-instance/replication-transactional-overview.md) | Replikálja a táblák módosításait a felügyelt SQL-példányok, SQL Database vagy SQL Server más adatbázisaiba. Vagy frissítse a táblázatokat, ha egyes sorok módosulnak az SQL felügyelt példányának más példányain vagy SQL Server. További információ: [replikáció konfigurálása az Azure SQL felügyelt példányában](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Fenyegetések észlelése |További információ: [a veszélyforrások észlelésének konfigurálása az Azure SQL felügyelt példányában](../managed-instance/threat-detection-configure.md).|
| Biztonsági mentés hosszú távú megőrzése | További információ: a [hosszú távú biztonsági mentések megőrzésének konfigurálása az Azure SQL felügyelt példányain](../managed-instance/long-term-backup-retention-configure.md), amely jelenleg korlátozott nyilvános előzetes verzióban érhető el. |

---

## <a name="new-features"></a>Új funkciók

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL felügyelt példány H2 2019-frissítések

- A [szolgáltatással segített alhálózat-konfiguráció](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) biztonságos és kényelmes módszer az alhálózati konfiguráció kezelésére, ahol az adatforgalom szabályozása mellett az SQL felügyelt példány biztosítja a felügyeleti forgalom zavartalan áramlását.
- A [transzparens adattitkosítás (TDE) és a bring your own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) lehetővé teszi a REST-alapú adatvédelem (BYOK) használatát, és lehetővé teszi a szervezetek számára a kulcsok és adatok elkülönítését a felügyeleti feladatok elvégzésére.
- Az [automatikus feladatátvételi csoportok](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) lehetővé teszik, hogy az elsődleges példány összes adatbázisát egy másik régió másodlagos példányára replikálja.
- A [globális nyomkövetési jelzők](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) lehetővé teszik az SQL felügyelt példányok viselkedésének konfigurálását.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL felügyelt példány H1 2019 frissítése

A következő funkciók engedélyezve vannak az SQL felügyelt példány üzembe helyezési modelljében a H1 2019-ben:
  - <a href="/azure/azure-sql/managed-instance/resource-limits">Azure havi Kredittel</a> rendelkező előfizetések támogatása a Visual Studio-előfizetőknek és a megnövekedett [regionális korlátoknak](../managed-instance/resource-limits.md#regional-resource-limitations)megfelelően.
  - A <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> sharepoint 2016 és a sharepoint 2019 </a> és a <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central támogatása. </a>
  - Hozzon létre egy felügyelt példányt <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">példány szintű rendezéssel</a> és tetszőleges <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">időzónával</a> .
  - A felügyelt példányok már védettek a [beépített tűzfallal](../managed-instance/management-endpoint-verify-built-in-firewall.md).
  - Az SQL felügyelt példányának konfigurálása [nyilvános végpontok](../managed-instance/public-endpoint-configure.md)használatára, [proxy felülbírálási](connectivity-architecture.md#connection-policy) csatlakozás a jobb hálózati teljesítmény eléréséhez, <a href="https://aka.ms/four-cores-sql-mi-update"> 4 virtuális mag a Gen5</a> , illetve a <a href="/azure/azure-sql/database/automated-backups-overview">biztonsági másolatok megőrzésének beállítása akár 35 napra</a> az időponthoz való visszaállításhoz. A [biztonsági másolatok hosszú távú megőrzése](long-term-retention-overview.md) (akár 10 évig) jelenleg nyilvános előzetes verzióban érhető el.  
  - Az új funkciók lehetővé teszik, hogy az <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">adatbázist egy másik adatközpontba állítsa be a PowerShell használatával</a>, az [adatbázis átnevezésével](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), a [virtuális fürt törlésével](../managed-instance/virtual-cluster-delete.md).
  - Az új beépített [példány-közreműködő szerepkör](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) lehetővé teszi a vám (SOD) megfelelőségének a biztonsági elvekkel való elválasztását, valamint a vállalati szabványoknak való megfelelést.
  - Az SQL felügyelt példánya a következő Azure Government régiókban érhető el a GA (US Gov Texas, US Gov Arizona) és a Észak-Kína 2 és Kelet-Kína 2 között. A következő nyilvános régiókban érhető el: Közép-Ausztrália, Közép-Ausztrália, Dél-Brazília, Dél-Franciaország, Egyesült Arab Emírségek, Észak-Európa, Dél-Afrika, Dél-Afrika, Nyugat-Európa.

## <a name="known-issues"></a>Ismert problémák

|Probléma  |Felderített dátum  |Állapot  |Feloldás dátuma  |
|---------|---------|---------|---------|
|[Az eljárás sp_send_dbmail átmenetileg meghiúsulhat @query a paraméter használatakor](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Jan 2021|Megkerülő megoldás||
|[Az elosztott tranzakciók a felügyelt példány kiszolgálói megbízhatósági csoportból való eltávolítása után hajthatók végre.](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|TOT 2020|Megkerülő megoldás||
|[Az elosztott tranzakciók nem hajthatók végre a felügyelt példány skálázási művelete után](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|TOT 2020|Megkerülő megoldás||
|[bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [](/sql/t-sql/functions/openrowset-transact-sql) Az Azure SQL-ben és `BACKUP` / `RESTORE` a felügyelt példányban található OpenRowset nem használhatja az Azure ad-beli felügyelet identitását az Azure Storage-ban való hitelesítéshez|Sep 2020|Megkerülő megoldás||
|[Az egyszerű szolgáltatásnév nem fér hozzá az Azure AD-hez és a AKV](#service-principal-cannot-access-azure-ad-and-akv)|Augusztus 2020|Megkerülő megoldás||
|[A manuális biztonsági mentés ELLENŐRZŐÖSSZEG nélküli visszaállítása sikertelen lehet](#restoring-manual-backup-without-checksum-might-fail)|2020. május|Feloldva|2020. június|
|[Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése esetén](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020. május|Feloldva|2020. június|
|[Nem az SQL felügyelt példányára vonatkozó engedélyek vannak alkalmazva az erőforráscsoporthoz](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Feb 2020|Feloldva|November 2020|
|[Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül](#limitation-of-manual-failover-via-portal-for-failover-groups)|Jan 2020|Megkerülő megoldás||
|[Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Megkerülő megoldás||
|[Az SQL-ügynök feladatait az ügynök folyamatának újraindítása okozhatja](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Feloldva|Mar 2020|
|[Az Azure AD-bejelentkezések és a felhasználók nem támogatottak a SSDT](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|November 2019|Nincs Áthidaló megoldás||
|[A memóriában tárolt OLTP-korlátok nincsenek alkalmazva](#in-memory-oltp-memory-limits-are-not-applied)|TOT 2019|Megkerülő megoldás||
|[Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|TOT 2019|Megkerülő megoldás||
|[A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Sep 2019|Megkerülő megoldás||
|[Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Sep 2019|Megkerülő megoldás||
|[A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Augusztus 2019|Megkerülő megoldás||
|[Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott](#impersonation-of-azure-ad-login-types-is-not-supported)|Jul 2019|Nincs Áthidaló megoldás||
|[@query a paraméter nem támogatott sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|Apr 2019|Feloldva|Jan 2021|
|[A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Nincs Áthidaló megoldás||
|[A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ](#temporary-database-is-used-during-restore-operation)||Megkerülő megoldás||
|[A TEMPDB szerkezete és tartalma újra létrejön](#tempdb-structure-and-content-is-re-created)||Nincs Áthidaló megoldás||
|[Kis méretű adatbázisfájlok esetén a tárterület nagyobb](#exceeding-storage-space-with-small-database-files)||Megkerülő megoldás||
|[Az adatbázis neve helyett GUID-értékek láthatók](#guid-values-shown-instead-of-database-names)||Megkerülő megoldás||
|[A hibanapló nem marad meg](#error-logs-arent-persisted)||Nincs Áthidaló megoldás||
|[Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Megkerülő megoldás|Mar 2020|
|[A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Megkerülő megoldás||
|Az adatbázis konzisztencia-ellenőrzése a DBCC CHECKDB UTASÍTÁST használatával nem történt meg az Azure-beli Blob Storage adatbázis visszaállítása után.||Feloldva|November 2019|
|Az adott időponthoz tartozó adatbázis-visszaállítás üzletileg kritikus rétegről általános célú szintjére nem lesz sikeres, ha a forrásadatbázis memóriában tárolt OLTP objektumokat tartalmaz.||Feloldva|TOT 2019|
|Az adatbázis levelezési funkciója külső (nem Azure-beli) levelezési kiszolgálókkal biztonságos kapcsolatok használatával||Feloldva|TOT 2019|
|A felügyelt SQL-példányok nem támogatják a foglalt adatbázisokat.||Feloldva|Augusztus 2019|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Az eljárás sp_send_dbmail átmenetileg meghiúsulhat @query a paraméter használatakor

Az eljárás sp_send_dbmail átmenetileg sikertelen lehet `@query` , ha a paramétert használja. Ha ezt a problémát tapasztalja, a sp_send_dbmail eljárások másodpercenkénti végrehajtása hibával `Msg 22050, Level 16, State 1` és üzenettel meghiúsul `Failed to initialize sqlcmd library with error number -2147467259` . A hiba megfelelő megjelenítéséhez az eljárást a paraméterhez a 0 értékkel kell meghívni `@exclude_query_output` , ellenkező esetben a rendszer nem propagálja a hibát.
Ezt a problémát egy olyan ismert hiba okozta, amellyel a sp_send_dbmail a megszemélyesítést és a kapcsolatok készletezését használja.
A probléma megkerüléséhez csomagolja ki az e-mailek küldését egy olyan újrapróbálkozási logikába, amely a kimeneti paraméterre támaszkodik `@mailitem_id` . Ha a végrehajtás meghiúsul, akkor a paraméter értéke NULL lesz, ami azt jelzi, hogy sp_send_dbmailt még egyszer kell meghívni az e-mail sikeres elküldéséhez. Íme egy példa erre az újrapróbálkozási logikára.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Az elosztott tranzakciók a felügyelt példány kiszolgálói megbízhatósági csoportból való eltávolítása után hajthatók végre.

A [kiszolgálói megbízhatósági csoportok](../managed-instance/server-trust-group-overview.md) olyan felügyelt példányok közötti megbízhatósági kapcsolat létesítésére szolgálnak, amelyek az [Elosztott tranzakciók](./elastic-transactions-overview.md)végrehajtásának előfeltételei. Miután eltávolította a felügyelt példányt a kiszolgálói megbízhatósági csoportból, vagy törli a csoportot, lehetséges, hogy az elosztott tranzakciókat is végre tudja hajtani. Megkerülő megoldással megoldható, hogy az elosztott tranzakciók le vannak tiltva, és hogy a felügyelt példányon a [felhasználó által kezdeményezett manuális feladatátvétel](../managed-instance/user-initiated-failover.md) történik.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Az elosztott tranzakciók nem hajthatók végre a felügyelt példány skálázási művelete után

A szolgáltatási réteg vagy virtuális mag módosítását tartalmazó felügyelt példány skálázási műveletei a kiszolgáló megbízhatósági csoportjának beállításait alaphelyzetbe állítják, és letiltják az [Elosztott tranzakciók](./elastic-transactions-overview.md)futtatását. Áthidaló megoldásként törölje és hozzon létre új [kiszolgálói megbízhatósági csoportot](../managed-instance/server-trust-group-overview.md) Azure Portalon.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>A BULK INSERT és a BACKUP/Restore utasítások nem használhatnak felügyelt identitást az Azure Storage eléréséhez

A tömeges beszúrási, biztonsági mentési és VISSZAÁLLÍTÁSi utasítások, valamint a OPENROWSET függvény nem használható `DATABASE SCOPED CREDENTIAL` felügyelt identitással az Azure Storage-ban való hitelesítéshez. Áthidaló megoldásként váltson át a közös HOZZÁFÉRÉSű aláírás-hitelesítésre. Az alábbi példa nem fog működni az Azure SQL-ben (az adatbázis és a felügyelt példány esetében is):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Megkerülő megoldás**: használjon [megosztott hozzáférési aláírást a tárolóba való hitelesítéshez](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage).

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Az egyszerű szolgáltatásnév nem fér hozzá az Azure AD-hez és a AKV

Bizonyos esetekben előfordulhat, hogy probléma merült fel az Azure AD-és Azure Key Vault-(AKV-) szolgáltatások elérésére használt egyszerű szolgáltatással. Ennek eredményeképpen a probléma hatással van az Azure AD-hitelesítés és az átlátható adatbázis-titkosítás (TDE) használatára az SQL felügyelt példányával. Ez egy átmeneti kapcsolódási probléma lehet, vagy nem futtathatók olyan utasítások, mint például a LOGIN/felhasználó létrehozása külső SZOLGÁLTATÓtól, illetve a bejelentkezés/felhasználó futtatása. Előfordulhat, hogy a TDE beállítása az ügyfél által felügyelt kulccsal új, felügyelt Azure SQL-példányon bizonyos esetekben nem működik.

**Áthidaló megoldás**: Ha meg szeretné akadályozni, hogy ez a probléma a frissítési parancsok végrehajtása előtt a FELÜGYELt SQL-példányon történjen, vagy ha már tapasztalta ezt a problémát, lépjen a Azure Portalra, és nyissa meg az SQL felügyelt példány [Active Directory rendszergazdai](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal)paneljét. Ellenőrizze, hogy látható-e a következő hibaüzenet: "a felügyelt példánynak szüksége van egy egyszerű szolgáltatásnév Azure Active Directory eléréséhez. Kattintson ide egy egyszerű szolgáltatásnév létrehozásához. Ha ezt a hibaüzenetet észlelte, kattintson rá, és kövesse a hiba elhárításához szükséges lépésenkénti útmutatót.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>A manuális biztonsági mentés ELLENŐRZŐÖSSZEG nélküli visszaállítása sikertelen lehet

Bizonyos körülmények között a felügyelt példányon az ELLENŐRZŐÖSSZEG nélküli használatával készített adatbázisok manuális biztonsági mentését nem lehet visszaállítani. Ilyen esetekben próbálja meg újra visszaállítani a biztonsági mentést, amíg nem sikerül.

**Megkerülő megoldás**: a felügyelt példányokon lévő adatbázisok manuális biztonsági mentését az ellenőrzőösszeg szolgáltatással engedélyezheti.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése esetén

Bizonyos esetekben a meglévő feladatok módosítása, letiltása vagy engedélyezése miatt az ügynök nem válaszol. A probléma automatikusan az észleléskor lesz enyhítve, ami az ügynök folyamatának újraindítását eredményezi.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Nem az SQL felügyelt példányára vonatkozó engedélyek vannak alkalmazva az erőforráscsoporthoz

Ha az SQL felügyelt példány közreműködői Azure-szerepkörét egy erőforráscsoport (RG) alkalmazza, a rendszer nem alkalmazza az SQL felügyelt példányra, és nincs hatással.

**Megkerülő megoldás**: állítson be egy SQL felügyelt példány közreműködői szerepkört a felhasználók számára az előfizetési szinten.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Feladatátvételi csoportok manuális feladatátvételének korlátozása a portálon keresztül

Ha egy feladatátvételi csoport különböző Azure-előfizetések vagy-erőforráscsoportok példányain terjed ki, a manuális feladatátvétel nem indítható el a feladatátvételi csoport elsődleges példányán.

**Áthidaló megoldás**: kezdeményezzen feladatátvételt a portálon keresztül a Geo-másodlagos példányon.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez

Ha nem sysadmin bejelentkezési adatokat ad hozzá bármely [SQL Agent rögzített adatbázis-szerepkörhöz](/sql/ssms/agent/sql-server-agent-fixed-database-roles), létezik egy olyan probléma, amelyben explicit végrehajtási engedélyeket kell adni a bejelentkezések fő tárolt eljárásaihoz. A probléma előfordulásakor a "végrehajtás engedély megtagadva az objektumon <object_name> (Microsoft SQL Server, hiba: 229)" hibaüzenet jelenik meg.

**Megkerülő megoldás**: Miután hozzáadta a bejelentkezéseket egy SQL Agent rögzített adatbázis-szerepkörhöz (SQLAgentUserRole, SQLAgentReaderRole vagy sqlagentoperatorrole indíthatják), a szerepkörökhöz hozzáadott összes bejelentkezéshez hajtsa végre az alábbi T-SQL-parancsfájlt, hogy explicit módon adjon végrehajtási engedélyeket a felsorolt tárolt eljárásoknak.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Az SQL-ügynök feladatait az ügynök folyamatának újraindítása okozhatja

**(Megoldva a 2020. márciusi)** Az SQL Agent egy új munkamenetet hoz létre minden alkalommal, amikor elindít egy feladatot, és fokozatosan növeli a memória-felhasználást. Ha el szeretné kerülni a belső memória korlátját, ami letiltja az ütemezett feladatok végrehajtását, az ügynök folyamata újraindul, miután a memória-felhasználás eléri a küszöbértéket. Ez a művelet az újraindítás pillanatában futó feladatok végrehajtásának megszakításához vezethet.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memóriában tárolt OLTP-korlátok nincsenek alkalmazva

A üzletileg kritikus szolgáltatási réteg bizonyos esetekben nem fogja helyesen alkalmazni a [memóriára optimalizált objektumok maximális memóriára vonatkozó korlátozásait](../managed-instance/resource-limits.md#in-memory-oltp-available-space) . A felügyelt SQL-példányok lehetővé tehetik, hogy a számítási feladatok nagyobb memóriát használjanak a memóriában tárolt OLTP-műveletekhez, ami hatással lehet a példány rendelkezésre állására és stabilitására. Előfordulhat, hogy a korlátokat elérő memóriabeli OLTP-lekérdezések nem lesznek azonnal sikertelenek. Ezt a problémát hamarosan megjavítja a rendszer. A memóriában lévő OLTP memóriát használó lekérdezések hamarabb sikertelenek lesznek, ha elérik a [határértékeket](../managed-instance/resource-limits.md#in-memory-oltp-available-space).

**Áthidaló megoldás**: [figyelje a MEMÓRIÁban tárolt OLTP](../in-memory-oltp-monitor-space.md) a [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) használatával, hogy a munkaterhelés ne használja a rendelkezésre álló memóriát. Növelje a memória korlátait, amelyek a virtuális mag számától függenek, vagy optimalizálja a számítási feladatokat kevesebb memória használatára.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Helytelen hiba történt a nem üres fájl eltávolítására tett kísérlet során.

A SQL Server és az SQL felügyelt példánya nem [engedi, hogy a felhasználó eldobjon egy nem üres fájlt](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Ha nem üres adatfájlt próbál meg eltávolítani egy utasítás használatával `ALTER DATABASE REMOVE FILE` , a `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` rendszer nem küldi el azonnal a hibát. Az SQL felügyelt példánya továbbra is megpróbálja eldobni a fájlt, és 30 perc elteltével sikertelen lesz a művelet `Internal server error` .

**Áthidaló megoldás**: távolítsa el a fájl tartalmát a `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` parancs használatával. Ha ez az egyetlen fájl a fájlkiszolgálón, akkor törölnie kell az ehhez a fájlhoz társított táblából vagy partícióból származó adatokból a fájl zsugorítása előtt, és szükség esetén be kell töltenie ezeket az adatok egy másik táblába/partícióba.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatási szintek módosítása és a példány-létrehozási műveletek blokkolva vannak az adatbázis-visszaállítás folyamatban

A folyamatos `RESTORE` utasítás, az adatáttelepítési szolgáltatás áttelepítési folyamata és az időponthoz tartozó beépített visszaállítás letiltja a szolgáltatási szintek vagy a meglévő példány átméretezésének frissítését, és új példányokat hoz létre, amíg a visszaállítási folyamat be nem fejeződik. 

A visszaállítási folyamat letiltja ezeket a műveleteket a felügyelt példányokon és példány-készleteken abban az alhálózatban, amelyben a visszaállítási folyamat fut. A példányok példányai nem érintettek. A szolgáltatási szintű műveletek létrehozása vagy módosítása nem fog sikerülni vagy időtúllépést okozni. A visszaállítási folyamat befejezése vagy megszakítása után folytatódnak.

**Áthidaló megoldás**: Várjon, amíg a visszaállítási folyamat befejeződik, vagy szakítsa meg a visszaállítási folyamatot, ha a létrehozási vagy frissítési szolgáltatási rétegbeli művelet magasabb prioritással rendelkezik.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Előfordulhat, hogy a üzletileg kritikus szolgáltatási szinten újra kell konfigurálni a Resource Governor a feladatátvétel után

A [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) funkció, amely lehetővé teszi a felhasználói munkaterheléshez rendelt erőforrások korlátozását, a feladatátvétel vagy a felhasználó által kezdeményezett változás (például a maximális virtuális mag vagy a példányok maximális mérete) módosítását helytelenül osztályozhatja.

**Megkerülő megoldás**: futtassa `ALTER RESOURCE GOVERNOR RECONFIGURE` rendszeresen vagy egy SQL-ügynök feladatának részeként, amely VÉGREHAJTja az SQL-feladatot, amikor a példány elindul, ha [Resource Governort](/sql/relational-databases/resource-governor/resource-governor)használ.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>A szolgáltatási réteg frissítése után újra kell inicializálni a több adatbázis Service Broker párbeszédpaneleit.

Az adatbázisok közötti Service Broker párbeszédpanelek nem teszik elérhetővé az üzeneteket más adatbázisokban lévő szolgáltatásoknak a szolgáltatási réteg módosítása után. Az üzenetek *nem vesznek* el, és a küldő várólistáján találhatók. Az SQL felügyelt példányaiban a virtuális mag vagy a példány tárolási méretének változása esetén a `service_broke_guid` [sys. Databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézetet fogja megváltoztatni az összes adatbázisra vonatkozóan. A `DIALOG` más adatbázisban található Service Broker szolgáltatásra hivatkozó [BEGIN Dialog](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) utasítással létrehozott minden olyan üzenet, amely nem kézbesíti az üzeneteket a célként megadott szolgáltatásnak.

**Megkerülő megoldás**: állítson le minden olyan tevékenységet, amely több adatbázison Service Broker párbeszédet használ a szolgáltatási réteg frissítése előtt, majd újrainicializálja őket. Ha a szolgáltatási rétegek megváltozása után megmaradnak a fennmaradó üzenetek, olvassa el a forrás-üzenetsor üzeneteit, és küldje el újra a cél várólistára.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott

`EXECUTE AS USER` `EXECUTE AS LOGIN` A következő Azure Active Directory-(Azure ad-) rendszerbiztonsági tag vagy a használatával történő megszemélyesítés nem támogatott:
-   Aliast használó Azure AD-felhasználók. Ebben az esetben a következő hibaüzenetet adja vissza: `15517` .
- Azure AD-alapú bejelentkezések és felhasználók Azure AD-alkalmazásokon vagy-egyszerű szolgáltatásokon alapuló felhasználók. Ebben az esetben a következő hibaüzeneteket adja vissza: `15517` és `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query a paraméter nem támogatott sp_send_db_mail

A `@query` [sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) eljárásban szereplő paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a Geo-feladatátvétel után

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban lévő adatbázison, akkor az SQL felügyelt példányának rendszergazdájának törölnie kell a régi elsődleges összes kiadványt, és újra kell konfigurálnia azokat az új elsődlegesen a feladatátvételt követően egy másik régióba. További információ: [replikáció](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Az Azure AD-bejelentkezések és a felhasználók nem támogatottak a SSDT

SQL Server Data Tools nem támogatja teljes mértékben az Azure AD-bejelentkezéseket és-felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>A VISSZAÁLLÍTÁSi művelet során a rendszer ideiglenes adatbázist használ

Ha egy adatbázis a felügyelt SQL-példányban van helyreállítva, a visszaállítási szolgáltatás először egy üres adatbázist hoz létre a kívánt névvel a példányon a név lefoglalásához. Némi idő elteltével ez az adatbázis el lesz dobva, és a rendszer elindítja a tényleges adatbázis visszaállítását. 

A *visszaállítási* állapotban lévő adatbázis neve helyett véletlenszerű GUID-értékkel fog rendelkezni. A visszaállítási folyamat befejeződése után az ideiglenes név az utasításban megadott kívánt névre lesz módosítva `RESTORE` . 

A kezdeti fázisban a felhasználók hozzáférhetnek az üres adatbázishoz, és akár táblákat is létrehozhatnak, vagy betölthetnek egy adatkészletet ebben az adatbázisban. Ezt az ideiglenes adatbázist a rendszer elveti, ha a visszaállítási szolgáltatás elindítja a második fázist.

**Megkerülő megoldás**: ne nyissa meg a visszaállítani kívánt adatbázist, amíg nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>A TEMPDB szerkezete és tartalma újra létrejön

Az `tempdb` adatbázis mindig 12 adatfájlra van bontva, és a fájl szerkezete nem módosítható. A fájlok maximális mérete nem módosítható, és az új fájlok nem adhatók hozzá a következőhöz: `tempdb` . `Tempdb` a rendszer mindig üres adatbázisként hozza létre újra, amikor a példány elindul vagy feladatátvételt végez, és a folyamatban lévő módosításokat `tempdb` nem őrzi meg a rendszer.

### <a name="exceeding-storage-space-with-small-database-files"></a>Kis méretű adatbázisfájlok esetén a tárterület nagyobb

`CREATE DATABASE`a, `ALTER DATABASE ADD FILE` és `RESTORE DATABASE` az utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage-korlátot.

A felügyelt SQL-példányok minden általános célú példánya akár 35 TB tárterületet foglal le az Azure Premium lemezterülethez. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez nem használt lemezterülete nem számít fel díjat, de az Azure Premium-lemezek teljes mérete nem haladhatja meg a 35 TB-ot. Bizonyos esetekben előfordulhat, hogy egy felügyelt példány, amely nem igényel 8 TB-ot, a belső töredezettség miatt túllépheti a 35 TB-os Azure-korlátot a tárterületen.

Előfordulhat például, hogy az SQL felügyelt példány általános célú példánya egy 4 TB-os lemezre helyezett, 1,2 TB-os méretű fájllal rendelkezik. Az is előfordulhat, hogy a 248-es fájlok mindegyike 1 GB, amely külön 128 GB-os lemezekre van helyezve. Ebben a példában:

- A lefoglalt tárterület teljes mérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példányon lévő adatbázisok teljes lefoglalt területe 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt szemlélteti, hogy bizonyos körülmények között a fájlok adott eloszlása miatt a felügyelt SQL-példányok egy példánya elérheti a csatolt Azure Premium-lemez számára fenntartott 35 TB-os korlátot, ha nem várható.

Ebben a példában a meglévő adatbázisok továbbra is működőképesek maradnak, és bármilyen probléma nélkül növekednek, amíg új fájlok nem lettek hozzáadva. Új adatbázisok nem hozhatók létre és nem állíthatók vissza, mert nincs elég hely az új lemezmeghajtók számára, még akkor is, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlát-korlátját. Az ebben az esetben visszaadott hiba nem egyértelmű.

[A fennmaradó fájlok számát](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) a rendszernézetek használatával is meghatározhatja. Ha eléri ezt a korlátot, próbálja meg [üresen hagyni, és töröljön néhány kisebb fájlt a DBCC SHRINKFILE utasítás használatával](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) , vagy váltson a [üzletileg kritikus szintjére, amely nem rendelkezik ezzel a korláttal](../managed-instance/resource-limits.md#service-tier-characteristics).

### <a name="guid-values-shown-instead-of-database-names"></a>Az adatbázis neve helyett GUID-értékek láthatók

A rendszernézetek, a teljesítményszámlálók, a hibaüzenetek, a Xevent típusú eseményekhez és a hibanapló-bejegyzések a tényleges adatbázis neve helyett GUID-adatbázis-azonosítókat jelenítenek meg. Ne használja ezeket a GUID azonosítókat, mert azokat a rendszer a későbbiekben a tényleges adatbázis-nevekkel cseréli le.

**Megkerülő megoldás**: a sys. Databases nézetet használva oldja fel a tényleges adatbázis nevét a fizikai adatbázis nevéből, a GUID adatbázis-azonosítók formájában megadva:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>A hibanapló nem marad meg

Az SQL felügyelt példányában elérhető naplók nem maradnak meg, és a méreteik nem szerepelnek a maximális tárolási korlátban. Előfordulhat, hogy a naplók automatikusan törlődnek, ha a feladatátvétel történik. Előfordulhat, hogy rések vannak a hibák naplózási előzményeiben, mert az SQL felügyelt példánya többször is át lett helyezve több virtuális gépre.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Ugyanazon példányon belül két adatbázis tranzakciós hatóköre nem támogatott

**(Megoldva a 2020. márciusi)** A `TransactionScope` .net-osztály nem működik, ha két lekérdezést küldenek ugyanazon példányon belül két adatbázisnak ugyanazon tranzakció hatókörében:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Áthidaló megoldás (2020 márciusa óta nem szükséges)**: a [SqlConnection. ChangeDatabase (string)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) használatával használjon másik adatbázist kapcsolati környezetben ahelyett, hogy két kapcsolatot kellene használnia.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók időnként nem hivatkozhatnak helyi IP-címekre.

Az SQL felügyelt példányában lévő CLR-modulok és a társított kiszolgálók vagy az aktuális példányra hivatkozó elosztott lekérdezések néha nem tudják feloldani a helyi példányok IP-címét. Ez a hiba átmeneti probléma.

**Megkerülő megoldás**: ha lehetséges, használjon egy CLR-modul környezeti kapcsolatait.

## <a name="updates"></a>Frissítések

SQL Database frissítéseinek és tökéletesítésének listáját lásd: [SQL Database szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=sql-database).

Az összes Azure-szolgáltatás frissítéséhez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="contribute-to-content"></a>Tartalmi közreműködés

Az Azure SQL-dokumentációhoz való hozzájárulásért tekintse meg a [docs közreműködői útmutatóját](/contribute/).
