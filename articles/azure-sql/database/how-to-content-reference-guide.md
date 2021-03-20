---
title: A &-tartalom kezelése hivatkozás konfigurálása
description: Megtalálhatja a Azure SQL Database konfigurálását és kezelését megtanító tartalomra mutató hivatkozást.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791766"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Tartalmi referenciák konfigurálása és kezelése – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben a cikkben a különböző útmutatókra, parancsfájlokra és magyarázatokra mutató hivatkozásokat talál, amelyek segíthetnek a Azure SQL Database kezelésében és konfigurálásában. 

## <a name="load-data"></a>Adatok betöltése

- [Migrálás az SQL Database-be](migrate-to-database-from-sql-server.md)
- Megtudhatja, hogyan [kezelheti SQL Database az áttelepítés után](manage-data-after-migrating-to-database.md).
- [Adatbázis másolása](database-copy.md)
- [Adatbázis importálása BACPAC-ból](database-import.md)
- [Adatbázis exportálása BACPAC-ba](database-export.md)
- [Adatok betöltése a BCP használatával](../load-from-csv-with-bcp.md)
- [Adatok betöltése az ADF használatával](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Funkciók konfigurálása

- [Azure Active Directory (Azure AD) hitelesítésének konfigurálása](authentication-aad-configure.md)
- [Feltételes hozzáférés konfigurálása](conditional-access-configure.md)
- [Többtényezős Azure AD-hitelesítés](authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication konfigurálása](authentication-mfa-ssms-configure.md)
- [Ideiglenes adatmegőrzési szabály konfigurálása](temporal-tables-retention-policy.md)
- [A TDE konfigurálása BYOK megoldással](transparent-data-encryption-byok-configure.md)
- [TDE BYOK-kulcsok váltása](transparent-data-encryption-byok-key-rotation.md)
- [A TDE-védő eltávolítása](transparent-data-encryption-byok-remove-tde-protector.md)
- [Memóriabeli OLTP konfigurálása](../in-memory-oltp-configure.md)
- [Azure Automation konfigurálása](automation-manage.md)
- [Konfigurálja a tranzakciós replikálást](replication-to-sql-database.md) az adatbázisok közötti dátum replikálásához.
- A [veszélyforrások észlelésének konfigurálásával](threat-detection-configure.md) Azure SQL Database azonosíthatók a gyanús tevékenységek, például az SQL-injektálás vagy a gyanús helyekről való hozzáférés.
- A bizalmas adatok védelme érdekében [konfigurálja a dinamikus adatmaszkolást](dynamic-data-masking-configure-portal.md) .
- A [biztonsági másolatok megőrzésének konfigurálása](long-term-backup-retention-configure.md) egy adatbázis számára, hogy a biztonsági mentések megmaradjanak az Azure Blob Storage-on. 
- [Konfigurálja a Geo-replikálást](active-geo-replication-overview.md) , hogy az adatbázis egy másik régióban maradjon.
- [Konfigurálja a Geo-replikák biztonságát](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Az adatbázis monitorozása és finomhangolása

- [Manuális hangolás](performance-guidance.md)
- [A teljesítmény monitorozása DMV-kkel](monitoring-with-dmvs.md)
- [A teljesítmény monitorozása a lekérdezéstár használatával](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Engedélyezze az automatikus hangolást](automatic-tuning-enable.md) , hogy Azure SQL Database optimalizálja a számítási feladatok teljesítményét.
- Az [automatikus hangolással kapcsolatos e-mail-értesítések engedélyezése](automatic-tuning-email-notifications-configure.md) a hangolási javaslatokkal kapcsolatos információk beszerzéséhez.
- [Teljesítménnyel kapcsolatos javaslatok alkalmazása](database-advisor-find-recommendations-portal.md) és az adatbázis optimalizálása.
- [Riasztásokat hozhat létre](alerts-insights-configure-portal.md) Azure SQL Databaseről érkező értesítések beszerzéséhez.
- [Kapcsolódási hibák elhárítása](troubleshoot-common-errors-issues.md) , ha az alkalmazások és az adatbázis közötti kapcsolódási problémákat tapasztal. [A kapcsolódási problémákhoz Resource Health](resource-health-to-troubleshoot-connectivity.md)is használhatja.
- [Teljesítményproblémák elhárítása az Intelligent Insights segítségével](intelligent-insights-troubleshoot-performance.md)
- [Kezelheti](file-space-manage.md) a tárhelyet a tárterület-használat figyeléséhez az adatbázisban.
- [Az Intelligent Insights diagnosztikai naplójának használata](intelligent-insights-use-diagnostics-log.md)
- [Memóriabeli OLTP területének monitorozása](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Bővített események

- [Bővített események](xevent-db-diff-from-svr.md)
- [Kiterjesztett események tárolása az Event file-ban](xevent-code-event-file.md)
- [Kiterjesztett események tárolása gyűrűs pufferbe](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Lekérdezések elosztott adatai

- A [vertikálisan particionált adatforrásokat](elastic-query-getting-started-vertical.md) több adatbázis között kérdezheti le.
- A kibővített [adatrétegek közötti jelentés](elastic-query-horizontal-partitioning.md).
- [Lekérdezés különböző sémákkal rendelkező táblák között](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Adatszinkronizálás

- [SQL-adatszinkronizálás](sql-data-sync-data-sql-server-sql-database.md)
- [Adatszinkronizálási ügynök](sql-data-sync-agent-overview.md)
- [Sémaváltozások replikálása](sql-data-sync-update-sync-schema.md)
- [Monitorozás az OMS-szel](./monitor-tune-overview.md)
- [Ajánlott eljárások az adatszinkronizáláshoz](sql-data-sync-best-practices.md)
- [Az adatszinkronizálás hibaelhárítása](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Rugalmas adatbázis-feladatok

- [Létrehozás és kezelés](elastic-jobs-powershell-create.md) Elastic Database feladatok a PowerShell használatával.
- [Létrehozás és kezelés](elastic-jobs-tsql-create-manage.md) Feladatok Elastic Database a Transact-SQL használatával.
- [Migrálás a régi rugalmas feladatokból](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Adatbázis horizontális skálázása

- [Rugalmas adatbázis-ügyfél függvénytárának frissítése](elastic-scale-upgrade-client-library.md).
- [Szilánkokra osztott alkalmazás létrehozása](elastic-scale-get-started.md).
- [Horizontálisan szilánkokra bontott adathalmazok lekérdezése](elastic-query-getting-started.md)
- [Több szegmensből álló lekérdezések](elastic-scale-multishard-querying.md)futtatása.
- [Helyezze át a szilánkokra osztott adatátvitelt](elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurálja a biztonságot](elastic-scale-split-merge-security-configuration.md) az adatbázis-szegmensekben.
- [Adjon hozzá egy](elastic-scale-add-a-shard.md) szegmenst az aktuális adatbázis-szegmensek jelenlegi készletéhez.
- [Kijavítottuk a szilánkok térképének problémáit](elastic-database-recovery-manager.md).
- A [szilánkokra osztott adatbázis migrálása](elastic-convert-to-use-elastic-tools.md).
- [Számlálók létrehozása](elastic-database-perf-counters.md)
- Az Entity Framework használatával lekérdezheti a szilánkokra osztott [adatkereteket](elastic-scale-use-entity-framework-applications-visual-studio.md) .
- A [felhasználható keretrendszer használatával](elastic-scale-working-with-dapper.md) lekérdezheti a szilánkokra bontott adatkereteket.

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolódás](connect-query-content-reference-guide.md#libraries)
- [Spark-összekötő használata](spark-connector.md)
- [Alkalmazás hitelesítése](application-authentication-get-client-id-keys.md)
- [Kötegelt feldolgozás használata a jobb teljesítmény érdekében](../performance-improve-use-batching.md)
- [Csatlakoztatási útmutató](troubleshoot-common-connectivity-issues.md)
- [DNS-aliasok](dns-alias-overview.md)
- [DNS-alias beállítása PowerShell](dns-alias-powershell-create.md)
- [Portok – ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C és C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Tervezés vészhelyreállításra](designing-cloud-solutions-for-disaster-recovery.md)
- [Tervezés rugalmas készletekhez](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Tervezés alkalmazásfrissítésekhez](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Több-bérlős szoftveres (SaaS) alkalmazások tervezése

- [SaaS-kialakítási minták](saas-tenancy-app-design-patterns.md)
- [SaaS-videóindexelő](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-alkalmazásbiztonság](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Következő lépések

- További információ [Az Azure SQL felügyelt példányaival kapcsolatos útmutatókról](../managed-instance/how-to-content-reference-guide.md)