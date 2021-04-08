---
title: A &-tartalom kezelése hivatkozás konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Az Azure SQL felügyelt példányának konfigurálását és kezelését bemutató dokumentáció.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779764"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Az Azure SQL felügyelt példányának tartalmi referenciája
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebben a cikkben az Azure SQL felügyelt példányának kezeléséhez és konfigurálásához segítséget nyújtó különféle útmutatókra, parancsfájlokra és magyarázatokra mutató hivatkozásokat talál.

## <a name="load-data"></a>Adatok betöltése

- [Migrálás az Azure SQL felügyelt példányára](migrate-to-instance-from-sql-server.md): az ajánlott áttelepítési folyamat és az Azure SQL felügyelt példányra való áttelepítéshez szükséges eszközök ismertetése.
- TDE-tanúsítvány átirányítása [Az Azure SQL felügyelt példányára](tde-certificate-migrate.md): ha a SQL Server-adatbázist transzparens adattitkosítással (TDE) védik, át kell telepítenie azt a tanúsítványt, amelyet az SQL felügyelt példánya használhat az Azure-ban visszaállítani kívánt biztonsági mentés visszafejtéséhez.
- [Adatbázis importálása BACPAC-ból](../database/database-import.md)
- [Adatbázis exportálása BACPAC-ba](../database/database-export.md)
- [Adatok betöltése a BCP használatával](../load-from-csv-with-bcp.md)
- [Adatok betöltése az Azure Data Factoryvel](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Hálózati konfiguráció

- [Alhálózat méretének meghatározása](vnet-subnet-determine-size.md): mivel az alhálózat nem méretezhető át az SQL felügyelt példányának telepítése után, ki kell számítania, hogy milyen IP-címtartományt kell megadni az alhálózatra telepítendő felügyelt példányok számához és típusaihoz. 
- [Hozzon létre egy új VNet és alhálózatot](virtual-network-subnet-create-arm-template.md): konfigurálja a virtuális hálózatot és az alhálózatot a [hálózati követelményeknek](connectivity-architecture-overview.md#network-requirements)megfelelően. 
- [Meglévő VNet és alhálózat konfigurálása](vnet-existing-add-subnet.md): Ellenőrizze a hálózati követelményeket, és konfigurálja a meglévő virtuális hálózatot és alhálózatot az SQL felügyelt példányának telepítéséhez. 
- [Egyéni DNS konfigurálása](custom-dns-configure.md): konfigurálja az egyéni DNS-t úgy, hogy külső erőforrás-hozzáférést biztosítson az egyéni tartományokhoz az SQL által felügyelt példányból egy csatolt, db levelezési profilok használatával. 
- [Hálózati konfiguráció szinkronizálása](azure-app-sync-network-configuration.md): frissítse a hálózati konfigurációs tervet, ha nem tud kapcsolatot létesíteni az [alkalmazás Azure-beli virtuális hálózattal való integrálását](../../app-service/web-sites-integrate-with-vnet.md)követően.
- [A felügyeleti végpont IP-címének megkeresése](management-endpoint-find-ip-address.md): határozza meg azt a nyilvános végpontot, amelyet a FELÜGYELt SQL-példány felügyeleti célokra használ. 
- [Beépített tűzfalbeállítások ellenőrzése](management-endpoint-verify-built-in-firewall.md): Ellenőrizze, hogy az SQL felügyelt példánya csak a szükséges portokon és más beépített tűzfalszabályok esetében engedélyezi a forgalmat. 
- [Alkalmazások csatlakoztatása](connect-application-instance.md): megismerheti az alkalmazások SQL felügyelt példányhoz való csatlakoztatásának különböző mintáit.

## <a name="feature-configuration"></a>Szolgáltatás konfigurációja

- [Azure AD-hitelesítés konfigurálása](../database/authentication-aad-configure.md)
- [Feltételes hozzáférés konfigurálása](../database/conditional-access-configure.md)
- [Többtényezős Azure AD-hitelesítés](../database/authentication-mfa-ssms-overview.md)
- [Többtényezős hitelesítés konfigurálása](../database/authentication-mfa-ssms-configure.md)
- [Időbeli adatmegőrzési szabályzat konfigurálása](../database/temporal-tables-retention-policy.md)
- [A TDE konfigurálása BYOK megoldással](../database/transparent-data-encryption-byok-configure.md)
- [TDE BYOK-kulcsok váltása](../database/transparent-data-encryption-byok-key-rotation.md)
- [TDE-védő eltávolítása](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Memóriabeli OLTP konfigurálása](../in-memory-oltp-configure.md)
- [Azure Automation konfigurálása](../database/automation-manage.md)
- A [tranzakciós replikáció](replication-between-two-instances-configure-tutorial.md) lehetővé teszi az adatok replikálását a felügyelt példányok között, illetve a helyszíni SQL Server és az SQL felügyelt példányai között, és fordítva.
- A [fenyegetések észlelésének konfigurálása](threat-detection-configure.md) – a [fenyegetések észlelése](../database/threat-detection-overview.md) egy beépített Azure SQL felügyelt példány funkciója, amely különböző lehetséges támadásokat, például SQL-injektálást vagy gyanús helyekről való hozzáférést észlel. 
- A [riasztások létrehozása](alerts-create.md) lehetővé teszi a riasztások beállítását a figyelt metrikák, például a CPU-kihasználtság, a tárterület-felhasználás, a IOPS és az SQL által felügyelt példányok számára. 

## <a name="monitoring-and-tuning"></a>Monitorozás és finomhangolás

- [Manuális hangolás](../database/performance-guidance.md)
- [A teljesítmény monitorozása DMV-kkel](../database/monitoring-with-dmvs.md)
- [A lekérdezési tároló használata a teljesítmény figyeléséhez](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Teljesítményproblémák elhárítása az Intelligent Insights segítségével](../database/intelligent-insights-troubleshoot-performance.md)
- [A Intelligent Insights diagnosztikai napló használata](../database/intelligent-insights-use-diagnostics-log.md)
- [In-Memory OLTP-terület figyelése](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Bővített események

- [Bővített események](../database/xevent-db-diff-from-svr.md)
- [Kiterjesztett események tárolása egy esemény-fájlba](../database/xevent-code-event-file.md)
- [Kiterjesztett események tárolása gyűrűs pufferbe](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Riasztások kezelése

- [Riasztások létrehozása a felügyelt példányhoz](alerts-create.md)

## <a name="operations"></a>Üzemeltetés

- [Felhasználó által, felügyelt SQL-példányon kezdeményezett manuális feladatátvétel](user-initiated-failover.md)

## <a name="develop-applications"></a>Alkalmazások fejlesztése

- [Kapcsolódás](../database/connect-query-content-reference-guide.md#libraries)
- [Spark-összekötő használata](../../cosmos-db/spark-connector.md)
- [Alkalmazás hitelesítése](../database/application-authentication-get-client-id-keys.md)
- [Kötegelt feldolgozás használata a jobb teljesítmény érdekében](../performance-improve-use-batching.md)
- [Csatlakoztatási útmutató](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-aliasok](../database/dns-alias-overview.md)
- [DNS-alias beállítása a PowerShell használatával](../database/dns-alias-powershell-create.md)
- [Portok – ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C és C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Alkalmazások tervezése

- [Tervezés vészhelyreállításra](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Tervezés rugalmas készletekhez](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Tervezés alkalmazásfrissítésekhez](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások tervezése

- [SaaS-kialakítási minták](../database/saas-tenancy-app-design-patterns.md)
- [SaaS-videóindexelő](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS-alkalmazásbiztonság](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Következő lépések

Első lépések: az [SQL felügyelt példányának telepítése](instance-create-quickstart.md).