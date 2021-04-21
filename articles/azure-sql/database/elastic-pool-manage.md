---
title: Rugalmas készletek kezelése
description: Rugalmas készletek Azure SQL Database és kezelése a Azure Portal, a PowerShell, az Azure CLI, a Transact-SQL (T-SQL) és a Rest API használatával.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 555b18a7edbc3cc00cd7dcaf7f4897a4608d52e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787196"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Rugalmas készletek kezelése a Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A rugalmas készletekkel meghatározhatja, hogy a rugalmas készlet mennyi erőforrást igényel az adatbázisok számítási feladatainak kezeléséhez, valamint az egyes rugalmas készletbe adott adatbázisokhoz szükséges erőforrások mennyiségét.

## <a name="azure-portal"></a>Azure Portal

Minden készletbeállítás egy helyen található: a Készlet **konfigurálása panelen.** Ide úgy jut el, hogy megkeres egy  rugalmas készletet a Azure Portal majd kattintson a Készlet konfigurálása elemre a panel tetején vagy a bal oldali erőforrásmenüben.

Innen a következő módosítások bármilyen kombinációját egyetlen kötegbe mentheti:

1. A készlet szolgáltatási rétegének módosítása
2. A teljesítmény (DTU vagy virtuális magok) és a tárterület felfelé vagy lefelé skálázása
3. Adatbázisok hozzáadása vagy eltávolítása a készlethez vagy a készletből
4. Minimális (garantált) és maximális teljesítménykorlát beállítása a készletekben található adatbázisokhoz
5. Tekintse át a költségösszegzést a számla módosításainak megtekintéséhez az új beállítások eredményeként

![Rugalmas készlet konfigurációs panelje](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modulhoz lesz. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek.

Az alábbi PowerShell-SQL Database segítségével rugalmas készleteket és rugalmas készletbe Azure PowerShell adatbázisokat hozhat létre és kezelhet. Ha telepítenie vagy frissítenie kell a PowerShellt, tekintse meg az Install Azure PowerShell module (A PowerShell [telepítése) Azure PowerShell modult.](/powershell/azure/install-az-ps) A rugalmas készlet kiszolgálóinak létrehozásához és kezeléséhez lásd: [Kiszolgálók létrehozása és kezelése.](logical-servers.md) Tűzfalszabályok létrehozásához és kezeléséhez lásd: Tűzfalszabályok létrehozása és [kezelése a PowerShell használatával.](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)

> [!TIP]
> A PowerShell-példaszk szkriptekért lásd: Rugalmas készletek létrehozása és adatbázisok áthelyezése készletek és készletek között [a készletből a PowerShell](scripts/move-database-between-elastic-pools-powershell.md) használatával, illetve a [Rugalmas SQL-készlet](scripts/monitor-and-scale-pool-powershell.md)figyelése és méretezése a Azure SQL Database.
>

| Parancsmag | Leírás |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Létrehoz egy rugalmas készletet.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Lekérte a rugalmas készleteket és azok tulajdonságértékét.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Módosítja egy rugalmas készlet tulajdonságait Például a **StorageMB** tulajdonsággal módosíthatja egy rugalmas készlet maximális tárterületét.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Töröl egy rugalmas készletet.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Lekért műveletek állapota egy rugalmas készleten|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázist kér le.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist rugalmas készletekbe, rugalmas készletekből, illetve rugalmas készletek között.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Eltávolít egy adatbázist.|

> [!TIP]
> Ha a portál vagy a PowerShell-parancsmagok használatával egyszerre csak egy adatbázist hoz létre, több adatbázist is létrehozhat egy rugalmas készletben. A rugalmas készletbe történő létrehozás automatizálásával lásd: [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Ha rugalmas készleteket SQL Database az [Azure CLI-val,](/cli/azure)használja a következő Azure [CLI-SQL Database](/cli/azure/sql/db) parancsokat. A [Cloud Shell-lel](../../cloud-shell/overview.md) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI-példaszk szkriptekért lásd: Adatbázis áthelyezése a SQL Database-ban rugalmas SQL-készletben, illetve Rugalmas SQL-készlet méretezése az Azure CLI használatával [a Azure SQL Database.](scripts/scale-pool-cli.md) [](scripts/move-database-between-elastic-pools-cli.md)
>

| Parancsmag | Leírás |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Létrehoz egy rugalmas készletet.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Egy kiszolgálón található rugalmas készletek listáját adja vissza.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Egy rugalmas készletben található adatbázisok listáját adja vissza.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Emellett tartalmazza az elérhető készlet DTU-beállításait, a tárterület korlátait és az adatbázis-beállításokat. A részletesség csökkentése érdekében a rendszer alapértelmezés szerint elrejti a további tárolási korlátokat és adatbázis-beállításokat.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Frissíti a rugalmas készletet.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Törli a rugalmas készletet.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Az alábbi T-SQL-parancsokkal adatbázisokat hozhat létre és mozgathat meglévő rugalmas készletekben, vagy információt SQL Database rugalmas készletről a Transact-SQL használatával. Ezeket a parancsokat a Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával használhatja, amely képes csatlakozni egy kiszolgálóhoz és Transact-SQL-parancsokat átadni. A tűzfalszabályok T-SQL használatával való létrehozásáról és kezelésről lásd: [Tűzfalszabályok kezelése a Transact-SQL használatával.](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)

> [!IMPORTANT]
> Nem hozhat létre, frissíthet vagy törölhet rugalmas Azure SQL Database a Transact-SQL használatával. Hozzáadhat vagy eltávolíthat adatbázisokat egy rugalmas készletből, és DMV-kkel adatokat adhat vissza a meglévő rugalmas készletekről.
>

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. Új adatbázis létrehozásához csatlakoztatnia kell a master adatbázist.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Adatbázis áthelyezése rugalmas készletekbe, rugalmas készletekből vagy készletek között.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|A kiszolgáló összes rugalmas készletének erőforrás-használati statisztikáit adja vissza. Minden rugalmas készlethez egy sor tartozik minden 15 másodperces jelentéskészítési ablakhoz (percenként négy sor). Ez magában foglalja a processzor, az I/O, a napló, a tárhasználat és az egyidejű kérés-/munkamenet-használatot a készlet összes adatbázisa számára.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Egy adatbázis kiadását (szolgáltatási szint), szolgáltatási célkitűzést (tarifacsomag) és rugalmas készlet nevét adja vissza ,ha van ilyen, SQL Database vagy Azure Synapse Analytics. Ha be van jelentkezve a master adatbázisba egy kiszolgálón, az összes adatbázisra vonatkozó adatokat ad vissza. A Azure Synapse Analytics a master adatbázishoz kell csatlakoztatnia.|

## <a name="rest-api"></a>REST API

Rugalmas készletek és rugalmas SQL Database adatbázisok létrehozásához és kezeléséhez használja ezeket a REST API kéréseket.

| Parancs | Leírás |
| --- | --- |
|[Rugalmas készletek – Létrehozás vagy frissítés](/rest/api/sql/elasticpools/createorupdate)|Létrehoz egy új rugalmas készletet, vagy frissíti a meglévő rugalmas készletet.|
|[Rugalmas készletek – Törlés](/rest/api/sql/elasticpools/delete)|Törli a rugalmas készletet.|
|[Rugalmas készletek – Lekért](/rest/api/sql/elasticpools/get)|Lekért egy rugalmas készletet.|
|[Rugalmas készletek – Listás kiszolgáló szerint](/rest/api/sql/elasticpools/listbyserver)|Egy kiszolgálón található rugalmas készletek listáját adja vissza.|
|[Rugalmas készletek – Frissítés](/rest/api/sql/2020-11-01-preview/elasticpools/update)|Frissíti a meglévő rugalmas készletet.|
|[Rugalmas készlet tevékenységei](/rest/api/sql/elasticpoolactivities)|Rugalmas készlet tevékenységeit adja vissza.|
|[Rugalmas készlet adatbázis-tevékenységei](/rest/api/sql/elasticpooldatabaseactivities)|Egy rugalmas készleten belüli adatbázisokon végzett tevékenységet ad vissza.|
|[Adatbázisok – Létrehozás vagy frissítés](/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissíti a meglévő adatbázist.|
|[Adatbázisok – Lekért](/rest/api/sql/databases/get)|Lekért egy adatbázist.|
|[Adatbázisok – Lista rugalmas készlet alapján](/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben található adatbázisok listáját adja vissza.|
|[Adatbázisok – Listás kiszolgáló szerint](/rest/api/sql/databases/listbyserver)|Egy kiszolgálón található adatbázisok listáját adja vissza.|
|[Adatbázisok – Frissítés](/rest/api/sql/databases/update)|Frissíti a meglévő adatbázist.|

## <a name="next-steps"></a>Következő lépések

* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](saas-tenancy-app-design-patterns.md) című részt.
* A rugalmas készleteket használó SaaS-oktatóanyagért lásd: Bevezetés a [Wingtip SaaS-alkalmazásba.](saas-dbpertenant-wingtip-app-overview.md)
