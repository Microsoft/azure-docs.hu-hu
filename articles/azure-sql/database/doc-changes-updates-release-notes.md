---
title: Újdonságok
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg az új funkciókat és dokumentációs fejlesztéseket a Azure SQL Database & SQL Managed Instance.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: d0522fe2c8b6d6b623903a720e6c8e760bd6aa92
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602093"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>A Azure SQL Database & SQL Managed Instance újdonsága
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk Azure SQL Database és Azure SQL Managed Instance nyilvános előzetes verzióban elérhető funkciókat sorolja fel. A SQL Database és SQL Managed Instance frissítésekkel és fejlesztésekkel kapcsolatos további SQL Database & SQL Managed Instance [lásd:](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseit és fejlesztéseiért lásd: [Szolgáltatásfrissítések.](https://azure.microsoft.com/updates)

## <a name="whats-new"></a>Újdonságok

A dokumentáció Azure SQL Database és Azure SQL Managed Instance szakaszokra oszlik. Frissítettük azt is, hogyan hivatkozunk egy felügyelt példányra a felügyelt Azure SQL Database *a* *Azure SQL Managed Instance.*

Ezt azért megtettük, mert egyes funkciók és funkciók nagy mértékben eltérnek az önálló adatbázisok és a felügyelt példányok között, és egyre nagyobb kihívást jelent az Azure SQL Database és a Azure SQL Managed Instance közötti összetett különbségek magyarázata az egyes megosztott cikkekben.

A különböző Azure SQL-termékek tisztázása leegyszerűsíti és leegyszerűsíti az Azure-beli SQL Server-adatbázismotorral való munkát, legyen szó egyetlen felügyelt adatbázisról az Azure SQL Database-ban, egy teljes körű felügyelt példányról, amely több adatbázist üzemeltet az Azure SQL Managed Instance-ban, vagy az Azure-beli virtuális gépen üzemeltetett, jól ismert helyszíni SQL Server-termék.

Vegye figyelembe, hogy ez egy folyamatban lévő munka, és még nem minden cikk lett frissítve. A Transact-SQL- (T-SQL-) utasítások dokumentációja, a tárolt eljárások és a Azure SQL Database és a Azure SQL Managed Instance között megosztott számos funkció még nem teljes, ezért köszönjük türelmét a tartalom tisztázása során. 

Ez a táblázat a terminológia változásának gyors összehasonlítását tartalmazza: 


|**Új kifejezés**  | **Előző időszak**  |**Magyarázat** |
|---------|---------|---------|
|**Felügyelt Azure SQL-példány** | Azure SQL Database *példány*| Azure SQL Managed Instance egy saját termék a Azure SQL termékcsaládon belül, nem csak egy üzembe helyezési lehetőség a Azure SQL Database. | 
|**Azure SQL Database**|Azure SQL Database adatbázis *létrehozása*| Ha nincs explicit módon megadva, a terméknév Azure SQL Database magában foglalja a rugalmas készletben üzembe helyezett adatbázisokat és adatbázisokat is. |
|**Azure SQL Database**|Azure SQL Database *készlet*| Ha nincs kifejezetten másként megadva, a terméknév Azure SQL Database magában foglalja a rugalmas készletben üzembe helyezett adatbázisokat és adatbázisokat is.  |
|**Azure SQL Database** |Azure SQL Database | Bár a kifejezés nem egyezik, mostantól csak az egy adatbázisra és rugalmas készletre vonatkozó üzemelő példányra vonatkozik, és nem tartalmazza a felügyelt példányt. |
| **Azure SQL**| N/A | Ez az Azure-SQL Server elérhető adatbázismotor-termékek családját jelenti: Azure SQL Database, Azure SQL Managed Instance és SQL Server Azure-beli virtuális gépeken. | 

## <a name="features-in-public-preview"></a>Nyilvános előzetes verziójú funkciók

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| Szolgáltatás | Részletek |
| ---| --- |
| Rugalmas adatbázis-feladatok (előzetes verzió) | További információ: Rugalmas feladatok [létrehozása, konfigurálása és kezelése.](elastic-jobs-overview.md) |
| Rugalmas lekérdezések | További információ: [Rugalmas lekérdezések áttekintése.](elastic-query-overview.md) |
| Rugalmas tranzakciók | [Elosztott tranzakciók a felhőalapú adatbázisokban.](elastic-transactions-overview.md) |
| Lekérdezésszerkesztő a Azure Portal |További információ: A Azure Portal [SQL-lekérdezésszerkesztőjének használata adatok csatlakoztatására és lekérdezésére.](connect-query-portal.md)|
|SQL Analytics|További információ: [Azure SQL Analytics.](../../azure-monitor/insights/azure-sql.md)|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

| Szolgáltatás | Részletek |
| ---| --- |
| [Elosztott tranzakciók](/azure/azure-sql/database/elastic-transactions-overview) | Elosztott tranzakciók felügyelt példányok között. |
| [Példánykészletek](/azure/sql-database/sql-database-instance-pools) | Kényelmes és költséghatékony módja a kisebb SQL-példányok felhőbe való áttelepítésének. |
| [Példányszintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)](/sql/t-sql/statements/create-login-transact-sql) | Példányszintű bejelentkezések létrehozása CREATE [LOGIN FROM EXTERNAL PROVIDER utasítással.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) |
| [Tranzakciós replikáció](../managed-instance/replication-transactional-overview.md) | Replikálja a táblák módosításait más adatbázisokba a SQL Managed Instance, SQL Database vagy SQL Server. A táblákat akkor is frissítheti, ha egyes sorokat más példányokon SQL Managed Instance vagy SQL Server. További információ: [Replikálás konfigurálása a Azure SQL Managed Instance.](../managed-instance/replication-between-two-instances-configure-tutorial.md) |
| Fenyegetések észlelése |További információ: [A fenyegetésészlelés konfigurálása a Azure SQL Managed Instance.](../managed-instance/threat-detection-configure.md)|
| Biztonsági mentés hosszú távú megőrzése | További információ: [Configure long-term back up retention in Azure SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md), which is currently in limited public preview .. |

---

## <a name="new-features"></a>Új funkciók

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL Managed Instance H2 2019 frissítései

- [A szolgáltatás által biztosított](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) alhálózat-konfiguráció biztonságos és kényelmes módja annak az alhálózati konfigurációnak, amelyben Ön szabályozhatja az adatforgalmat, miközben a SQL Managed Instance biztosítja a felügyeleti forgalom zavartalan áramlását.
- [transzparens adattitkosítás (TDE) az Bring Your Own Key (BYOK)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) segítségével lehetővé teszi a byOK(bring-your-own-key) (saját kulcs használata) forgatókönyvet az adatok védelméhez, és lehetővé teszi a szervezetek számára, hogy elkülönítsék a kulcsok és adatok felügyeleti feladatait.
- [Az automatikus feladatátvételi csoportok](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) lehetővé teszik, hogy az elsődleges példány összes adatbázisát egy másik régióban található másodlagos példányra replikálja.
- [A globális nyomkövetési jelzők](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) segítségével konfigurálhatja a SQL Managed Instance viselkedését.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL Managed Instance H1 2019 frissítései

A H1 2019-ben SQL Managed Instance alábbi funkciók vannak engedélyezve az üzembe helyezési modellben:
  - Havi Azure-kreditet támogató előfizetések Visual Studio <a href="/azure/azure-sql/managed-instance/resource-limits">előfizetők és</a> a [megnövekedett regionális korlátok.](../managed-instance/resource-limits.md#regional-resource-limitations)
  - A <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 és a SharePoint 2019, </a> valamint a <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central támogatása. </a>
  - Hozzon létre egy felügyelt példányt <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">példányszintű rendezéssel</a> és egy ön által választott időzónával. <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/"></a>
  - A felügyelt példányok mostantól beépített [tűzfallal vannak védve.](../managed-instance/management-endpoint-verify-built-in-firewall.md)
  - Konfigurálja a SQL Managed Instance nyilvános végpontok [](connectivity-architecture.md#connection-policy) [használatára,](../managed-instance/public-endpoint-configure.md)proxy-felülbírálási kapcsolatra a jobb hálózati teljesítmény érdekében, 4 virtuális mag a <a href="https://aka.ms/four-cores-sql-mi-update">Gen5</a> hardvergeneráción vagy a Biztonsági <a href="/azure/azure-sql/database/automated-backups-overview">másolatok megőrzésének konfigurálása 35</a> napig időponthoz időben történő visszaállításhoz. [A biztonsági másolatok](long-term-retention-overview.md) hosszú távú megőrzése (legfeljebb 10 év) jelenleg nyilvános előzetes verzióban érhető el.  
  - Az új funkciók lehetővé teszik az adatbázis georedúnizálását egy másik adatközpontba a <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell</a> [használatával,](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)az adatbázis átnevezése és a virtuális [fürt törlése használatával.](../managed-instance/virtual-cluster-delete.md)
  - Az új [](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) beépített példány-közreműködői szerepkör lehetővé teszi a biztonsági alapelveknek és a vállalati szabványoknak való megfelelőség elkülönítését a feladatnak (SoD).
  - SQL Managed Instance a következő régiókban Azure Government az ga (US Gov Texas, US Gov Arizona) és a Kína 2. északi régiója és Kína 2. keleti régiója. A következő nyilvános régiókban is elérhető: Ausztrália középső régiója, Ausztrália 2. középső régiója, Dél-Brazília, Dél-Franciaország, Egyesült Arab Emírségek középső régiója, Egyesült Arab Emírségek északi régiója, Dél-Afrika északi régiója, Dél-Afrika nyugati régiója.

## <a name="known-issues"></a>Ismert problémák

|Probléma  |Felderített dátum  |Állapot  |Feloldás dátuma  |
|---------|---------|---------|---------|
|[A kapcsolattípus módosítása nincs hatással a feladatátvételi csoport végponton keresztüli kapcsolatokra](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|2021. január|Áthidaló megoldással rendelkezik||
|[A sp_send_dbmail a paraméter használata esetén átmeneti hiba @query lehet](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|2021. január|Áthidaló megoldással rendelkezik||
|[Az elosztott tranzakciók végrehajthatók, miután eltávolította a felügyelt példányt a kiszolgálói megbízhatósági csoportból](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020. október|Áthidaló megoldással rendelkezik||
|[Az elosztott tranzakciók nem hajthatóak végre a felügyelt példány skálázásának művelete után](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020. október|Áthidaló megoldással rendelkezik||
|[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [A felügyelt példányban Azure SQL openROWSET](/sql/t-sql/functions/openrowset-transact-sql) és utasítása nem használhatja az `BACKUP` / `RESTORE` Azure AD Manage Identityt az Azure Storage-ban való hitelesítéshez|2020. szeptember|Áthidaló megoldással rendelkezik||
|[A szolgáltatásnév nem tud hozzáférni az Azure AD-hez és az AKV-hez](#service-principal-cannot-access-azure-ad-and-akv)|2020. aug.|Áthidaló megoldással rendelkezik||
|[A manuális biztonsági mentés ELLENŐRZŐUM nélküli visszaállítása sikertelen lehet](#restoring-manual-backup-without-checksum-might-fail)|2020. május|Feloldva|2020. június|
|[Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése esetén](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020. május|Feloldva|2020. június|
|[Az erőforráscsoportra nem alkalmazott engedélyek a SQL Managed Instance](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020. február|Feloldva|2020. nov.|
|[A portálon keresztüli manuális feladatátvétel korlátozása feladatátvételi csoportok esetén](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020. január|Áthidaló megoldással rendelkezik||
|[Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez](#in-memory-oltp-memory-limits-are-not-applied)|2019. dec.|Áthidaló megoldással rendelkezik||
|[Az SQL Agent-feladatok az ügynökfolyamat újraindításával szakíthatóak meg](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019. dec.|Feloldva|2020. március|
|[Az SsdT nem támogatja az Azure AD-bejelentkezéseket és -felhasználókat](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019. nov.|Nincs áthidaló megoldás||
|[A memóriában való OLTP memóriakorlátok nincsenek alkalmazva](#in-memory-oltp-memory-limits-are-not-applied)|2019. október|Áthidaló megoldással rendelkezik||
|[Nem üres fájl eltávolításakor a rendszer hibás hibát adott vissza](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019. október|Áthidaló megoldással rendelkezik||
|[A szolgáltatásszint módosítása és a példány létrehozása műveleteket a folyamatban lévő adatbázis-visszaállítás blokkolja](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019. szeptember|Áthidaló megoldással rendelkezik||
|[Resource Governor a üzletileg kritikus szolgáltatási szinten, előfordulhat, hogy a feladatátvétel után újra kell konfigurálni](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019. szeptember|Áthidaló megoldással rendelkezik||
|[Az adatbázisközi Service Broker párbeszédpaneleket újra kell indítani a szolgáltatási szint frissítése után](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019. aug.|Áthidaló megoldással rendelkezik||
|[Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott](#impersonation-of-azure-ad-login-types-is-not-supported)|2019. július|Nincs áthidaló megoldás||
|[@query A paraméter nem támogatott a sp_send_db_mail](#-parameter-not-supported-in-sp_send_db_mail)|2019. április|Feloldva|2021. január|
|[A tranzakciós replikációt újra kell konfigurálni a geo-feladatátvétel után](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019. március|Nincs áthidaló megoldás||
|[A RESTORE művelet ideiglenes adatbázist használ](#temporary-database-is-used-during-restore-operation)||Áthidaló megoldással rendelkezik||
|[Újra létrejön a TEMPDB szerkezete és tartalma](#tempdb-structure-and-content-is-re-created)||Nincs áthidaló megoldás||
|[A tárterület túllépése kis méretű adatbázisfájlokkal](#exceeding-storage-space-with-small-database-files)||Áthidaló megoldással rendelkezik||
|[Adatbázisnevek helyett megjelenő GUID-értékek](#guid-values-shown-instead-of-database-names)||Áthidaló megoldással rendelkezik||
|[A hibanaplók nincsenek megőrzve](#error-logs-arent-persisted)||Nincs áthidaló megoldás||
|[Az egy példányon belüli két adatbázis tranzakciós hatóköre nem támogatott](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Áthidaló megoldással rendelkezik|2020. március|
|[A CLR-modulok és a csatolt kiszolgálók néha nem hivatkoznak helyi IP-címre](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Áthidaló megoldással rendelkezik||
|Az adatbázis-konzisztencia nem ellenőrizhető a DBCC CHECKDB használatával, miután visszaállította az adatbázist a Azure Blob Storage.||Feloldva|2019. nov.|
|Ha a forrásadatbázis memóriában lévő OLTP-objektumokat tartalmaz, üzletileg kritikus-ről egy általános célú szintre való időponthoz időben való visszaállítás nem fog sikerülni.||Feloldva|2019. október|
|Database mail funkció biztonságos kapcsolatot használó külső (nem Azure-beli) levelezési kiszolgálókkal||Feloldva|2019. október|
|A nem támogatott tartalmazott adatbázisok SQL Managed Instance||Feloldva|2019. aug.|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>A kapcsolattípus módosítása nincs hatással a feladatátvételi csoport végponton keresztüli kapcsolatokra

Ha egy példány egy automatikus feladatátvételi csoportban vesz [](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) [részt,](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)a példány kapcsolattípusának módosítása nem lép életbe a feladatátvételi csoport listener végpontja által létrehozott kapcsolatokra.

**Megkerülő** megoldás: Az automatikus feladatátvételi csoport elejtés újbóli létrehozása a kapcsolat típusának módosítására ad megoldást.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>A sp_send_dbmail a paraméter használata esetén átmeneti hiba @query lehet

A sp_send_dbmail a paraméter használata esetén átmeneti sikertelen `@query` lehet. Ha ez a probléma jelentkezik, az eljárás minden második végrehajtása sp_send_dbmail a következő `Msg 22050, Level 16, State 1` hibaüzenettel `Failed to initialize sqlcmd library with error number -2147467259` meghiúsul: . A hiba megfelelő láthatóása érdekében az eljárást a paraméter alapértelmezett 0 értékével kell meghívni, ellenkező esetben a hiba propagálása `@exclude_query_output` nem történik meg.
Ezt a problémát egy ismert hiba okozza, amely azzal kapcsolatos, sp_send_dbmail hogyan használja a megszemélyesítést és a kapcsolatkészletezést.
A probléma megoldásához burkolt kódot kell csomagolni az e-mailek elküldését egy újrapróbálkozási logikába, amely a kimeneti paraméterre `@mailitem_id` támaszkodik. Ha a végrehajtás sikertelen, a paraméter értéke NULL lesz, ami azt sp_send_dbmail, hogy a rendszer még egyszer meg kell hívatni egy e-mail sikeres elküldését. Lássunk egy példát erre az újrapróbálkozási logikára.
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

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Az elosztott tranzakciók végrehajthatók, miután eltávolította a felügyelt példányt a kiszolgálói megbízhatósági csoportból

[A kiszolgálói megbízhatósági csoportok](../managed-instance/server-trust-group-overview.md) segítségével megbízhatósági kapcsolatot lehet létesíteni a felügyelt példányok között, ami előfeltétele az elosztott [tranzakciók nek.](./elastic-transactions-overview.md) Miután eltávolította a felügyelt példányt a kiszolgálói megbízhatósági csoportból, vagy törölte a csoportot, továbbra is végrehajthat elosztott tranzakciókat. Íme egy áthidaló megoldás, amely segítségével győződjön meg [](../managed-instance/user-initiated-failover.md) arról, hogy az elosztott tranzakciók le vannak tiltva, és ez a felügyelt példányon a felhasználó által kezdeményezett manuális feladatátvétel.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Az elosztott tranzakciók nem hajthatóak végre a felügyelt példány skálázása után

A felügyelt példány skálázhatósági műveletei, amelyek tartalmazzák a szolgáltatásszint vagy a virtuális magok számának megváltoztatását, visszaállítják a kiszolgálói megbízhatósági csoport beállításait a háttéren, és letiltják az elosztott [tranzakciók futtatását.](./elastic-transactions-overview.md) Áthidaló megoldásként törölje és hozza létre az új [kiszolgálói megbízhatósági](../managed-instance/server-trust-group-overview.md) csoportot a Azure Portal.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT és BACKUP/RESTORE utasítások nem használhatnak felügyelt identitást az Azure Storage eléréséhez

A tömeges beszúrási, BACKUP- és RESTORE-utasítások, valamint az OPENROWSET függvény nem használható felügyelt identitással az `DATABASE SCOPED CREDENTIAL` Azure Storage-ban való hitelesítéshez. Áthidaló megoldásként váltson a KÖZÖS HOZZÁFÉRÉSŰ JOGOSULTSÁGALÁÍRÁSos hitelesítésre. Az alábbi példa nem működik a Azure SQL (az adatbázison és a felügyelt példányon is):

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Megkerülő** megoldás: A [tárolóban való hitelesítéshez használjon közös hozzáférésű jogosultsági adatokat.](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>A szolgáltatásnév nem tud hozzáférni az Azure AD-hez és az AKV-hez

Bizonyos körülmények között probléma lehet az Azure AD és a Azure Key Vault (AKV) szolgáltatások eléréséhez használt szolgáltatásnévvel. Ennek eredményeképpen ez a probléma hatással van az Azure AD-hitelesítésre és a transzparens adatbázis-titkosításra (TDE) a SQL Managed Instance. Ez időszakos csatlakozási problémaként vagy olyan utasítások futtatásának nem lehetséges, mint a CREATE LOGIN/USER FROM EXTERNAL PROVIDER vagy az EXECUTE AS LOGIN/USER. Előfordulhat, hogy a TDE beállítása ügyfél által felügyelt kulccsal egy új Azure SQL Managed Instance bizonyos körülmények között nem is fog működni.

**Megkerülő** megoldás: Ha meg szeretné akadályozni, hogy ez a probléma a SQL Managed Instance-ban a frissítési parancsok végrehajtása előtt forduljon elő, vagy ha már tapasztalt ilyen problémát a frissítési parancsok után, Azure Portal a SQL Managed Instance [Active Directory eléréséhez.](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal) Ellenőrizze, hogy látja-e a következő hibaüzenetet: "A felügyelt példánynak szolgáltatásnévre van szüksége a Azure Active Directory. Kattintson ide a szolgáltatásnév létrehozásához". Ha ezt a hibaüzenetet észlelte, kattintson rá, és kövesse a megadott részletes utasításokat a hiba megoldásáig.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>A manuális biztonsági mentés ELLENŐRZŐUM nélküli visszaállítása meghiúsulhat

Bizonyos körülmények között előfordulhat, hogy a felügyelt példányon, ELLENŐRZŐUM nélkül készült adatbázisok manuális biztonsági mentése sikertelen lehet. Ilyen esetekben próbálja meg újra visszaállítani a biztonsági mentést, amíg sikerrel nem jár.

**Megkerülő** megoldás: Manuális biztonsági mentés készítése felügyelt példányok adatbázisairól a CHECKSUM engedélyezése esetén.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Az ügynök nem válaszol a meglévő feladatok módosítása, letiltása vagy engedélyezése után

Bizonyos körülmények között egy meglévő feladat módosítása, letiltása vagy engedélyezése azt okozhatja, hogy az ügynök nem válaszol. A probléma észleléskor automatikusan mérsékelhető, ami az ügynökfolyamat újraindítását eredményezi.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Az erőforráscsoportra nem alkalmazott engedélyek a SQL Managed Instance

Ha a SQL Managed Instance Azure-szerepkört egy erőforráscsoportra (RG) alkalmazza, a rendszer nem alkalmazza a SQL Managed Instance, és nincs hatása.

**Megkerülő** megoldás: Állítson be SQL Managed Instance közreműködői szerepkört a felhasználók számára az előfizetés szintjén.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>A portálon keresztüli manuális feladatátvétel korlátozása feladatátvételi csoportok esetén

Ha egy feladatátvételi csoport több Példányra is átível különböző Azure-előfizetések vagy erőforráscsoportok esetében, a manuális feladatátvétel nem indítható el a feladatátvételi csoportban lévő elsődleges példányról.

**Áthidaló** megoldás: Kezdeményezze a feladatátvételt a portálon keresztül a földrajzi másodlagos példányról.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>Az SQL-ügynök szerepköreinek kifejezett EXECUTE engedélyekre van szükségük a nem sysadmin (nem rendszergazdai) bejelentkezésekhez

Ha nem rendszergazdai bejelentkezéseket ad hozzá az [SQL Agent](/sql/ssms/agent/sql-server-agent-fixed-database-roles)rögzített adatbázis-szerepköreihez, akkor olyan probléma áll fenn, amely miatt explicit EXECUTE engedélyeket kell adni a fő tárolt eljárásoknak ahhoz, hogy ezek a bejelentkezések működjön. Ha ez a probléma merül fel, a következő hibaüzenet jelenik meg: "A rendszer megtagadta az EXECUTE engedélyt az objektumon <object_name> (Microsoft SQL Server, Hiba: 229)" üzenet jelenik meg.

**Megkerülő** megoldás: Miután hozzáadta a bejelentkezéseket az SQL Agent rögzített adatbázis-szerepkörhöz (SQLAgentUserRole, SQLAgentReaderRole vagy SQLAgentOperatorRole), hajtsa végre az alábbi T-SQL-szkriptet, hogy explicit módon adjon EXECUTE engedélyeket a felsorolt tárolt eljárásokhoz.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>Az SQL Agent-feladatok az ügynökfolyamat újraindításával szakíthatóak meg

**(2020 márciusában megoldva)** Az SQL Agent minden egyes feladat munkához létrehoz egy új munkamenetet, ezzel fokozatosan növeli a memóriafelhasználást. Ha el szeretné kerülni a belső memóriakorlátot, amely blokkolná az ütemezett feladatok végrehajtását, az ügynökfolyamat újraindul, amint a memóriafelhasználás eléri a küszöbértéket. Ez az újraindítás pillanatában futó feladatok végrehajtásának megszakítását eredményezheti.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>A memóriában való OLTP memóriakorlátok nincsenek alkalmazva

A üzletileg kritikus szolgáltatási szint bizonyos esetekben [](../managed-instance/resource-limits.md#in-memory-oltp-available-space) nem alkalmazza megfelelően a memóriaoptimalált objektumok maximális memóriakorlátját. SQL Managed Instance engedélyezheti, hogy a számítási feladat több memóriát használjon a memória OLTP-műveleteihez, ami befolyásolhatja a példány rendelkezésre állását és stabilitását. Előfordulhat, hogy a memória OLTP-lekérdezései, amelyek elérik a korlátokat, nem fognak azonnal meghiúsulni. Ez a probléma hamarosan ki lesz javítva. A több memóriában elérhető OLTP memóriát használó lekérdezések hamarabb meghiúsulnak, ha elérik a [korlátokat.](../managed-instance/resource-limits.md#in-memory-oltp-available-space)

**Megkerülő** megoldás: Figyelje a memóriában található [OLTP-tárhelyhasználatot](../in-memory-oltp-monitor-space.md) [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) annak érdekében, hogy a számítási feladat ne használja a rendelkezésre álló memóriánál többet. Növelje a virtuális magok számától függő memóriakorlátokat, vagy optimalizálja a számítási feladatot kevesebb memória használatára.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Nem üres fájl eltávolításakor a rendszer hibás hibát adott vissza

SQL Server és SQL Managed Instance nem teszik lehetővé, hogy a felhasználó eldobja a nem [üres fájlokat.](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites) Ha egy utasítással próbál eltávolítani egy nem régi adatfájlt, a hibát a rendszer nem `ALTER DATABASE REMOVE FILE` `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` fogja azonnal visszaadni. SQL Managed Instance megpróbálja elejteni a fájlt, és a művelet 30 perc után meghiúsul a `Internal server error` következővel: .

**Megkerülő** megoldás: Távolítsa el a fájl tartalmát az `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` paranccsal. Ha ez az egyetlen fájl a fájlcsoportban, akkor törölnie kell az adatokat a fájlcsoporthoz társított táblából vagy partícióból, mielőtt zsugorodna, és opcionálisan betölti ezeket az adatokat egy másik táblába/partícióba.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>A szolgáltatásszint módosítása és a példány létrehozása műveleteket a folyamatban lévő adatbázis-visszaállítás blokkolja

A Folyamatban lévő utasítás, a Data Migration Service migrálási folyamata és a beépített időponthoz igazítású visszaállítás letiltja a szolgáltatási szint frissítését vagy a meglévő példány átméretezését, valamint új példányok létrehozását a visszaállítási folyamat `RESTORE` befejezéséig. 

A visszaállítási folyamat blokkolja ezeket a műveleteket a felügyelt példányok és példánykészletek számára ugyanazon az alhálózaton, ahol a visszaállítási folyamat fut. A példánykészletek példányai nem érintettek. A szolgáltatásszint-létrehozási vagy -módosítási műveletek nem fognak meghiúsulni vagy időkorrekciót létrehozni. A visszaállítási folyamat befejezése vagy megszakítása után folytatódnak.

**Megkerülő** megoldás: Várja meg, amíg a visszaállítási folyamat befejeződik, vagy állítsa le a visszaállítási folyamatot, ha a létrehozási vagy frissítési szolgáltatási szint művelete magasabb prioritással rendelkezik.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Resource Governor a üzletileg kritikus szolgáltatási szinten, előfordulhat, hogy a feladatátvétel után újra kell konfigurálni

Előfordulhat, hogy a [Resource Governor-szolgáltatás,](/sql/relational-databases/resource-governor/resource-governor) amely lehetővé teszi a felhasználói számítási feladathoz rendelt erőforrások korlátozását, helytelenül besorolhat bizonyos felhasználói számítási feladatokat a feladatátvétel vagy a szolgáltatási szint felhasználó által kezdeményezett módosítása (például a virtuális magok maximális méretének módosítása vagy a példányok maximális tárterületének módosítása) után.

**Megkerülő** megoldás: Futtassa rendszeresen vagy egy OLYAN SQL Agent-feladat részeként, amely végrehajtja az SQL-feladatot, amikor a példány elindul, ha `ALTER RESOURCE GOVERNOR RECONFIGURE` Resource Governor. [](/sql/relational-databases/resource-governor/resource-governor)

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Az adatbázisközi Service Broker párbeszédpaneleket újra kell indítani a szolgáltatási szint frissítése után

Az adatbázisközi Service Broker a szolgáltatási szint módosítása után a párbeszédpanelek nem kézbesítik tovább az üzeneteket más adatbázisokban a szolgáltatásoknak. Az üzenetek nem *elvesznek,* és a küldő üzenetsorában találhatók. Ha a virtuális magok vagy a példányok tárterületének mérete SQL Managed Instance megváltozik, akkor a `service_broke_guid` [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) nézetben az összes adatbázis értéke módosul. A BEGIN DIALOG utasítással létrehozott, más adatbázisokban található Service Brokersre hivatkozó párbeszédpanelek nem kézbesítenek üzeneteket `DIALOG` a célszolgáltatásnak. [](/sql/t-sql/statements/begin-dialog-conversation-transact-sql)

**Megkerülő** megoldás: A szolgáltatási szint frissítése előtt állítsa le az adatbázisközi párbeszédeket Service Broker használó tevékenységeket, majd ezt követően újra kell indítani őket. Ha vannak még kézbesítetlen üzenetek a szolgáltatási szint módosítása után, olvassa be az üzeneteket a forrás-üzenetsorból, és küldje el őket újra a cél üzenetsorba.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Az Azure AD bejelentkezési típusok megszemélyesítése nem támogatott

A vagy a használatával az alábbi Azure Active Directory `EXECUTE AS USER` (Azure AD) rendszerbiztonsági tagokkal való megszemélyesítés `EXECUTE AS LOGIN` nem támogatott:
-   Aliasnevű Azure AD-felhasználók. Ebben az esetben a következő hiba jelenik meg: `15517` .
- Azure AD-bejelentkezések és -felhasználók Azure AD-alkalmazások vagy -szolgáltatásnév alapján. Ebben az esetben a következő hibákat ad vissza: `15517` és `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query paraméter nem támogatott a sp_send_db_mail

Az `@query` eljárás [](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) sp_send_db_mail paraméter nem működik.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>A tranzakciós replikációt újra kell konfigurálni a geo-feladatátvétel után

Ha a tranzakciós replikáció engedélyezve van egy automatikus feladatátvételi csoportban található adatbázison, a SQL Managed Instance-rendszergazdának el kell tisztítania a régi elsődleges adatbázis összes kiadványát, és újra kell konfigurálnia azokat az új elsődleges adatbázison, miután feladatátvétel történt egy másik régióba. További információ: [Replikáció.](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>Az SsdT nem támogatja az Azure AD-bejelentkezéseket és -felhasználókat

SQL Server Data Tools nem támogatják teljes mértékben az Azure AD-bejelentkezéseket és -felhasználókat.

### <a name="temporary-database-is-used-during-restore-operation"></a>A RESTORE művelet ideiglenes adatbázist használ

Amikor egy adatbázist a SQL Managed Instance, a visszaállítási szolgáltatás először létrehoz egy üres adatbázist a kívánt névvel a név példányon való lefoglalásához. Egy idő után a rendszer eldobja ezt az adatbázist, és a tényleges adatbázis visszaállítása meg fog elindulni. 

A Visszaállítás állapotban van *az* adatbázis neve helyett ideiglenesen egy véletlenszerű GUID-érték lesz. A visszaállítási folyamat befejezése után az ideiglenes név az utasításban megadott kívánt névre `RESTORE` módosul. 

A kezdeti fázisban a felhasználó hozzáférhet az üres adatbázishoz, és akár táblákat is létrehozhat, vagy adatokat tölthet be ebben az adatbázisban. Ez az ideiglenes adatbázis akkor lesz eldobva, amikor a visszaállítási szolgáltatás elindítja a második fázist.

**Megkerülő** megoldás: Ne fér hozzá a visszaállított adatbázishoz, amíg azt nem látja, hogy a visszaállítás befejeződött.

### <a name="tempdb-structure-and-content-is-re-created"></a>Újra létrejön a TEMPDB-struktúra és a tartalom

Az `tempdb` adatbázis mindig 12 adatfájlra van felosztva, és a fájlstruktúra nem módosítható. A fájlonkénti maximális méret nem módosítható, és nem lehet új fájlokat hozzáadni a `tempdb` fájlhoz. `Tempdb` A mindig üres adatbázisként jön létre, amikor a példány elindul vagy átveszi a adatokat, és a-ban végrehajtott módosítások `tempdb` nem maradnak meg.

### <a name="exceeding-storage-space-with-small-database-files"></a>Tárterület túllépése kis adatbázisfájlokkal

`CREATE DATABASE`, `ALTER DATABASE ADD FILE` és `RESTORE DATABASE` utasítások sikertelenek lehetnek, mert a példány elérheti az Azure Storage korlátját.

A általános célú példányok SQL Managed Instance legfeljebb 35 TB tárterületet foglalnak le az Azure Premium Disk-tárhely számára. Minden adatbázisfájl külön fizikai lemezre kerül. A lemezek mérete 128 GB, 256 GB, 512 GB, 1 TB vagy 4 TB lehet. A lemez fel nem használt tárhelye nem számít fel díjat, de az Azure Premium Disk-lemezek teljes mérete nem haladhatja meg a 35 TB-t. Bizonyos esetekben a belső töredezettség miatt előfordulhat, hogy egy felügyelt példány mérete meghaladja a tárterület méretére vonatkozó 35 TB-os azure-korlátot, amely nem szükséges 8 TB-hoz.

Például a általános célú példánya SQL Managed Instance egy 1,2 TB méretű, nagy méretű fájlt is elhelyezhet egy 4 TB-os lemezen. Emellett 248 fájl is lehet benne, amelyek 1 GB méretűek, és külön 128 GB-os lemezekre vannak helyezve. Ebben a példában:

- A lefoglalt lemez teljes tárterületmérete 1 x 4 TB + 248 x 128 GB = 35 TB.
- A példány adatbázisai számára fenntartott teljes terület 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Ez a példa azt mutatja be, hogy bizonyos körülmények között a fájlok egy adott elosztása miatt az SQL Managed Instance egy példánya elérheti a csatolt Prémium szintű Azure-lemez számára fenntartott 35 TB-os korlátot, amikor várhatóan nem erre számít.

Ebben a példában a meglévő adatbázisok továbbra is működnek, és probléma nélkül növekedhetnek, ha nem adnak hozzá új fájlokat. Új adatbázisok nem állíthatók létre vagy állíthatók vissza, mert nincs elegendő hely az új lemezmeghajtók számára, még akkor sem, ha az összes adatbázis teljes mérete nem éri el a példány méretkorlátját. Az ebben az esetben visszaadott hiba nem egyértelmű.

A [rendszernézetek segítségével](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) azonosíthatja a fennmaradó fájlok számát. Ha eléri ezt a korlátot, próbálja meg kiüríteni és törölni néhány kisebb fájlt a [DBCC SHRINKFILE](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) utasítással, vagy váltson át a [üzletileg kritikus-szintre,](../managed-instance/resource-limits.md#service-tier-characteristics)amely nem tartalmazza ezt a korlátot.

### <a name="guid-values-shown-instead-of-database-names"></a>Adatbázisnevek helyett megjelenő GUID-értékek

Számos rendszernézet, teljesítményszámláló, hibaüzenet, XEvents és hibanapló-bejegyzés a tényleges adatbázisnevek helyett GUID-adatbázisazonosítókat jelenít meg. Ne támaszkodjon ezekre a GUID-azonosítókra, mert a jövőben tényleges adatbázisnevekre cseréljük őket.

**Megkerülő** megoldás: A sys.databases nézet használatával oldja fel az adatbázis tényleges nevét a fizikai adatbázis nevéből, guid adatbázis-azonosítók formájában megadva:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>A hibanaplók nincsenek megőrzve

A tárfiókban elérhető hibanaplók SQL Managed Instance nem maradnak meg, és a méretüket nem tartalmazza a maximális tárhelykorlát. Feladatátvétel esetén előfordulhat, hogy a hibanaplók automatikusan törlődnek. A hibanaplók előzményei között lehetnek hézagok, SQL Managed Instance több virtuális gépen is áthelyezték.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Az ugyanazon példányon belüli két adatbázis tranzakciós hatóköre nem támogatott

**(2020 márciusában megoldva)** A .NET osztálya nem működik, ha két lekérdezést küld két adatbázisnak ugyanazon a példányon belül, ugyanabban a `TransactionScope` tranzakciós hatókörben:

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

**Áthidaló megoldás (2020** márciusa óta nem szükséges): Az [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) használatával egy másik adatbázist használhat kapcsolati környezetben két kapcsolat használata helyett.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>A CLR-modulok és a csatolt kiszolgálók néha nem hivatkoznak helyi IP-címre

A helyi SQL Managed Instance és csatolt kiszolgálók CLR-moduljai, illetve az aktuális példányra hivatkozó elosztott lekérdezések néha nem oldják fel a helyi példány IP-címét. Ez a hiba átmeneti probléma.

**Megkerülő** megoldás: Használjon környezeti kapcsolatokat egy CLR-modulban, ha lehetséges.

## <a name="updates"></a>Frissítések

A frissítésekkel és fejlesztésekkel kapcsolatos SQL Database lásd: SQL Database [szolgáltatásfrissítések.](https://azure.microsoft.com/updates/?product=sql-database)

Az összes Azure-szolgáltatás frissítéseit és fejlesztéseiért lásd: [Szolgáltatásfrissítések.](https://azure.microsoft.com/updates)

## <a name="contribute-to-content"></a>Tartalmi közreműködés

A dokumentációban való Azure SQL lásd: [Docs-szerkesztői útmutató.](/contribute/)
