---
title: Példák Azure PowerShell parancsfájlokra
description: Azure PowerShell szkriptek segítségével létrehozhat és kezelhet Azure SQL Database és az Azure SQL felügyelt példányának erőforrásait.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 7d732f0e42bc5eef8f72a2b9ce2adee28deb9a67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690970"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Azure PowerShell minták a Azure SQL Database és az Azure SQL felügyelt példányaihoz
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példánya lehetővé teszi az adatbázisok, példányok és készletek konfigurálását a Azure PowerShell használatával.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AZ PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

A következő táblázat az Azure SQL Database-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

|Hivatkozás|Leírás|
|---|---|
|**Önálló adatbázisok és rugalmas készletek létrehozása és konfigurálása**||
| [Egyetlen adatbázis létrehozása és a kiszolgálói szintű tűzfalszabály konfigurálása](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl egyetlen adatbázist hoz létre, és egy kiszolgálói szintű IP-tűzfalszabály konfigurálását végzi. |
| [Rugalmas készletek létrehozása és rugalmas készletbe helyezett adatbázisok áthelyezése](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript rugalmas készleteket hoz létre, áthelyezi a készletezett adatbázisokat, és megváltoztatja a számítási méreteket.|
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis konfigurálása és feladatátvétele az aktív geo-replikáció használatával](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl az aktív geo-replikálást konfigurálja egyetlen adatbázishoz, és feladatátvételt hajt végre a másodlagos replikára. |
| [Készletezett adatbázis konfigurálása és feladatátvétele az aktív geo-replikáció használatával](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl egy rugalmas készletben lévő adatbázis aktív geo-replikálását konfigurálja, és feladatátvételt hajt végre a másodlagos replikára. |
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása egyetlen adatbázishoz](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript létrehoz egy adatbázist és egy feladatátvételi csoportot, hozzáadja az adatbázist a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóra. |
| [Feladatátvevő csoport konfigurálása rugalmas készlethez](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript létrehoz egy adatbázist, hozzáadja azt egy rugalmas készlethez, hozzáadja a rugalmas készletet a feladatátvételi csoporthoz, és teszteli a feladatátvételt a másodlagos kiszolgálóra. |
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl egy adott adatbázis teljesítmény-mérőszámait figyeli, méretezi azt egy magasabb számítási méretre, és riasztási szabályt hoz létre az egyik teljesítmény-mérőszámon. |
| [Rugalmas készlet méretezése](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl figyeli a rugalmas készlet teljesítmény-mérőszámait, méretezi azt magasabb számítási méretre, és riasztási szabályt hoz létre az egyik teljesítmény-mérőszámon. |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl egy adatbázis naplózási és fenyegetés-észlelési házirendjeit konfigurálja. |
| **Adatbázis visszaállítása, másolása és importálása**||
| [Adatbázis visszaállítása](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript visszaállítja az adatbázist egy geo-redundáns biztonsági mentésből, és visszaállítja a törölt adatbázist a legújabb biztonsági mentésre. |
| [Adatbázis másolása új kiszolgálóra](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl másolatot készít egy meglévő adatbázisról egy új kiszolgálón. |
| [Adatbázis importálása bacpac-fájlból](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl egy adatbázist importál Azure SQL Database egy bacpac-fájlból. |
| **Adatok szinkronizálása adatbázisok között**||
| [Adatok szinkronizálása adatbázisok között](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl úgy konfigurálja az adatszinkronizálást, hogy Azure SQL Database több adatbázis között szinkronizáljon. |
| [Adatok helyszíni szinkronizálása az SQL Database és az SQL Server között](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript úgy konfigurálja az adatszinkronizálást, hogy szinkronizáljon egy Azure SQL Database és egy SQL Server helyszíni adatbázis között. |
| [Az SQL Data Sync szinkronizálási sémájának frissítése](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript adatokat ad hozzá a Data Sync szinkronizálási sémához, vagy eltávolítja belőle azokat. |
|||

További információ az [egyadatbázisos Azure POWERSHELL API-](single-database-manage.md#powershell)ról. 

## <a name="azure-sql-managed-instance"></a>[Felügyelt Azure SQL-példány](#tab/managed-instance)

A következő táblázat az Azure SQL felügyelt példányának Azure PowerShell parancsfájljaira mutató hivatkozásokat tartalmaz.

|Hivatkozás|Leírás|
|---|---|
|**Felügyelt példányok létrehozása és konfigurálása**||
| [Felügyelt példány létrehozása és kezelése](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat Azure PowerShell használatával. |
| [Felügyelt példány létrehozása és kezelése a Azure Resource Manager sablon használatával](../managed-instance/create-template-quickstart.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript bemutatja, hogyan hozhat létre és kezelhet felügyelt példányokat a Azure PowerShell és a Azure Resource Manager sablon használatával.|
| [Adatbázis visszaállítása felügyelt példányra egy másik földrajzi régióban](../managed-instance/scripts/restore-geo-backup.md) | Ez a PowerShell-szkript egy adatbázis biztonsági mentését végzi, és visszaállítja egy másik régióba. Ez a katasztrófa-helyreállítási helyzetek felderítésére szolgál. |
| **Transzparens adattitkosítás konfigurálása**||
| [Felügyelt példány transzparens adattitkosításának kezelése a saját kulcsának használatával Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript átlátható adattitkosítást konfigurál egy Bring Your Own Key forgatókönyvben az Azure SQL felügyelt példányaihoz, a Azure Key Vault egyik kulcsának használatával.|
|**Feladatátvételi csoport konfigurálása**||
| [Feladatátvételi csoport konfigurálása felügyelt példányhoz](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-parancsfájl két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd az elsődleges felügyelt példányról a másodlagos felügyelt példányra ellenőrzi a feladatátvételt. |
|||

További információ [Az Azure SQL felügyelt példányaihoz készült PowerShell-parancsmagokról](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>További források

Az ezen a lapon felsorolt példák az Azure SQL-erőforrások létrehozására és kezelésére szolgáló [PowerShell-parancsmagokat](/powershell/module/az.sql/) használják. A lekérdezések futtatásához és számos adatbázis-feladat elvégzéséhez szükséges további parancsmagok a [SQLServer](/powershell/module/sqlserver/) modulban találhatók. További információ: [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).