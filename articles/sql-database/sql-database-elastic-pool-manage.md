---
title: Rugalmas készletek kezelése – Azure SQL Database | Microsoft Docs
description: Azure SQL rugalmas készletek létrehozása és kezelése.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 6b2a2029dec917672d6f4ee8db24aed60e4f5bc5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568657"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Rugalmas készletek kezelése Azure SQL Database

A rugalmas készlettel meghatározhatja, hogy a rugalmas készlet hány erőforrást igényel az adatbázisok munkaterhelésének kezeléséhez, valamint az egyes készletezett adatbázisok erőforrásainak mennyiségét.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: Rugalmas készletek és készletezett adatbázisok kezelése

A készlet minden beállítása egy helyen található: a **készlet konfigurálása** panel. Itt talál egy rugalmas készletet a portálon, majd kattintson a **Készlet beállítása** elemre a panel felső részén vagy a bal oldali erőforrás menüjében.

Innen az alábbi módosítások bármely kombinációját elvégezheti, és egy kötegbe mentheti őket:

1. A készlet szolgáltatási szintjeinek módosítása
2. A teljesítmény (DTU vagy virtuális mag) és a tárterület méretezése felfelé vagy lefelé
3. Adatbázisok hozzáadása vagy eltávolítása a készletből
4. A készletekben lévő adatbázisok minimális (garantált) és maximális teljesítménybeli korlátjának beállítása
5. Tekintse át a díjszabást, és tekintse meg a számla módosításait az új beállítások eredményeképpen

![Rugalmas készlet konfigurációjának panelje](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Rugalmas készletek és készletezett adatbázisok kezelése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

SQL Database rugalmas készletek és készletezett adatbázisok Azure PowerShell használatával történő létrehozásához és kezeléséhez használja a következő PowerShell-parancsmagokat. Ha telepítenie vagy frissítenie kell a PowerShellt, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört. A rugalmas készlethez tartozó SQL Database-kiszolgálók létrehozásához és kezeléséhez tekintse meg a [SQL Database kiszolgálók létrehozása és kezelése](sql-database-servers.md)című témakört. A Tűzfalszabályok létrehozásával és kezelésével kapcsolatban lásd: [Tűzfalszabályok létrehozása és kezelése a PowerShell használatával](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell).

> [!TIP]
> A PowerShell-szkriptek esetében lásd: [rugalmas készletek létrehozása és adatbázisok áthelyezése készletek és készlet között a PowerShell használatával](scripts/sql-database-move-database-between-pools-powershell.md) , valamint a [PowerShell használatával egy rugalmas SQL-készlet figyelése és méretezése Azure SQL Databaseban](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| A parancsmag | Leírás |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Rugalmas készletet hoz létre.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Rugalmas készleteket és azok tulajdonságainak értékét kapja meg.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Egy rugalmas készlet tulajdonságainak módosítása például a **StorageMB** tulajdonság használatával módosítható egy rugalmas készlet maximális tárterülete.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Töröl egy rugalmas készletet.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|A rugalmas készletben lévő műveletek állapotának beolvasása|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Egy vagy több adatbázist kér le.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Beállítja egy adatbázis tulajdonságait, vagy áthelyezi a meglévő adatbázist a rugalmas készletek között, illetve azok között.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Eltávolít egy adatbázist.|

> [!TIP]
> A rugalmas készletekben számos adatbázis létrehozása időt vehet igénybe, ha a portál vagy a PowerShell-parancsmagok használatával végezhető el egyszerre egyetlen adatbázis létrehozása. A létrehozás rugalmas készletbe való automatizálásához tekintse meg a következőt: [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Rugalmas készletek és készletezett adatbázisok kezelése

SQL Database rugalmas készletek az [Azure CLI](/cli/azure)-vel való létrehozásához és kezeléséhez használja az alábbi [Azure CLI SQL Database](/cli/azure/sql/db) -parancsokat. A [Cloud Shell-lel](/azure/cloud-shell/overview) futtassa a parancssori felületet a böngészőben, vagy [telepítse](/cli/azure/install-azure-cli) macOS, Linux, illetve Windows rendszeren.

> [!TIP]
> Az Azure CLI példa parancsfájljaihoz lásd: a [CLI használata egy Azure SQL Database-adatbázis áthelyezéséhez egy rugalmas SQL-készletben](scripts/sql-database-move-database-between-pools-cli.md) , és az [Azure CLI használatával MÉRETEZHETŐ egy rugalmas SQL-készlet a Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| A parancsmag | Leírás |
| --- | --- |
|[az SQL rugalmas készlet létrehozása](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Rugalmas készletet hoz létre.|
|[az SQL rugalmas készlet listája](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Egy kiszolgálón lévő rugalmas készletek listáját adja vissza.|
|[az SQL rugalmas-Pool List-adatbázisok](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[az SQL rugalmas-Pool List-Editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|A rendelkezésre álló készlet DTU beállításait, tárolási korlátozásokat és adatbázis-beállításokat is tartalmaz. A részletesség csökkentése érdekében a további tárolási korlátok és az adatbázis-beállítások alapértelmezés szerint rejtve maradnak.|
|[az SQL rugalmas készlet frissítése](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Frissít egy rugalmas készletet.|
|[az SQL rugalmas készlet törlése](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Törli a rugalmas készletet.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Készletezett adatbázisok kezelése

A meglévő rugalmas készletekben lévő adatbázisok létrehozásához és áthelyezéséhez, illetve a Transact-SQL használatával SQL Database rugalmas készlettel kapcsolatos információk visszaküldéséhez használja a következő T-SQL-parancsokat. Ezeket a parancsokat a Azure Portal, a [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), a [Visual Studio Code](https://code.visualstudio.com/docs)vagy bármely más program használatával lehet kiadni, amely csatlakozhat egy Azure SQL Database-kiszolgálóhoz, és átadhatja a Transact-SQL-parancsokat. A tűzfalszabályok T-SQL használatával történő létrehozásához és kezeléséhez lásd: [Tűzfalszabályok kezelése a Transact-SQL használatával](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Azure SQL Database rugalmas készlet nem hozható létre, nem frissíthető és nem törölhető a Transact-SQL használatával. Hozzáadhat vagy eltávolíthat egy rugalmas készletből származó adatbázisokat, és a DMV használatával a meglévő rugalmas készletekre vonatkozó információkat adhat vissza.
>

| Parancs | Leírás |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Új adatbázist hoz létre egy meglévő készletben vagy egyetlen adatbázisként. Új adatbázis létrehozásához csatlakoznia kell a Master adatbázishoz.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Egy adatbázis áthelyezése a, a vagy a rugalmas készletek között.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Töröl egy adatbázist.|
|[sys. elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Az SQL Database-kiszolgáló összes rugalmas készletének erőforrás-használati statisztikáit adja vissza. Minden rugalmas készlethez egy sor tartozik minden 15 másodperces jelentési ablakhoz (percenként négy sor). Ide tartozik a CPU, az IO, a log, a Storage-felhasználás és az egyidejű kérelmek/munkamenetek kihasználtsága a készletben lévő összes adatbázis esetében.|
|[sys. database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|A kiadás (szolgáltatási csomag), a szolgáltatási cél (árképzési csomag) és a rugalmas készlet nevét adja vissza egy Azure SQL Database-adatbázishoz vagy egy Azure SQL Data Warehousehoz. Ha bejelentkezett a főadatbázisba egy Azure SQL Database-kiszolgálón, a visszaadja az összes adatbázisra vonatkozó információt. Azure SQL Data Warehouse esetében csatlakoznia kell a Master adatbázishoz.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: Rugalmas készletek és készletezett adatbázisok kezelése

SQL Database rugalmas készletek és készletezett adatbázisok létrehozásához és kezeléséhez használja ezeket a REST API kérelmeket.

| Parancs | Leírás |
| --- | --- |
|[Rugalmas készletek – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Létrehoz egy új rugalmas készletet, vagy frissít egy meglévő rugalmas készletet.|
|[Rugalmas készletek – törlés](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Törli a rugalmas készletet.|
|[Rugalmas készletek – Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Rugalmas készletet kap.|
|[Rugalmas készletek – kiszolgálók listázása](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Egy kiszolgálón lévő rugalmas készletek listáját adja vissza.|
|[Rugalmas készletek – frissítés](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Frissít egy meglévő rugalmas készletet.|
|[Rugalmas készlettel kapcsolatos tevékenységek](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Rugalmas készlet tevékenységeit adja vissza.|
|[Rugalmas készlet – adatbázis-tevékenységek](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|A rugalmas készletben lévő adatbázisokon belüli tevékenységeket adja vissza.|
|[Adatbázisok – létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist.|
|[Adatbázisok – Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Adatbázis beolvasása.|
|[Adatbázisok – lista rugalmas készlet alapján](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Egy rugalmas készletben lévő adatbázisok listáját adja vissza.|
|[Adatbázisok – kiszolgálók listázása](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Egy kiszolgáló adatbázisainak listáját adja vissza.|
|[Adatbázisok – frissítés](https://docs.microsoft.com/rest/api/sql/databases/update)|Frissít egy meglévő adatbázist.|

## <a name="next-steps"></a>További lépések

* A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md) című részt.
* A rugalmas készleteket használó SaaS-oktatóanyagért tekintse [meg a Wingtip SaaS-alkalmazás bemutatása](sql-database-wtp-overview.md)című témakört.
