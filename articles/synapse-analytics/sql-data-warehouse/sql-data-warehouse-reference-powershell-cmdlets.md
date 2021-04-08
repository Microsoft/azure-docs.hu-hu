---
title: PowerShell & REST API-k dedikált SQL-készlethez (korábban SQL DW)
description: Top PowerShell-parancsmagok a dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben, beleértve az adatbázisok szüneteltetését és folytatását.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 83e6082025f068e91a3d531f052b746870ffd57a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584108"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>PowerShell & REST API-k a dedikált SQL-készlethez (korábban SQL DW) az Azure szinapszis Analyticsben 

Számos dedikált SQL Pool felügyeleti feladat felügyelhető Azure PowerShell parancsmagok vagy REST API-k használatával.  Az alábbiakban néhány példát láthat arra, hogyan használhatók a PowerShell-parancsok a dedikált SQL-készlet (korábban SQL DW) gyakori feladatainak automatizálására.  Néhány jó REST-példákért tekintse meg a [méretezhetőség és a REST kezelése](sql-data-warehouse-manage-compute-rest-api.md)című cikket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Ismerkedés a Azure PowerShell-parancsmagokkal

1. Nyissa meg a Windows PowerShellt.
2. A PowerShell parancssorába futtassa ezeket a parancsokat, hogy bejelentkezzen a Azure Resource Managerba, és válassza ki az előfizetését.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Az adatraktár szüneteltetése – példa

Szüneteltesse a "Database02" nevű adatbázist egy "Kiszolgalo01" nevű kiszolgálón.  A kiszolgáló egy "ResourceGroup1" nevű Azure-erőforráscsoport.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Egy változat, ez a példa a beolvasott objektumot [felfüggeszti a AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Ennek eredményeképpen a rendszer szünetelteti az adatbázist. Az utolsó parancs az eredményeket jeleníti meg.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Az adatraktár indítása – példa

A "Database02" nevű, "Kiszolgalo01" nevű kiszolgálón futtatott adatbázis folytatásának folytatása A kiszolgálót egy "ResourceGroup1" nevű erőforráscsoport tárolja.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Egy változat, ez a példa egy "Database02" nevű adatbázist kér le egy "Kiszolgalo01" nevű kiszolgálóról, amelyet egy "ResourceGroup1" nevű erőforráscsoport tárol. A lekért objektumot a [folytatáshoz AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Vegye figyelembe, hogy ha a kiszolgáló foo.database.windows.net, a PowerShell-parancsmagokban használja az "ize" kifejezést a-ServerName értékre.

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok

Ezek a PowerShell-parancsmagok az Azure szinapszis Analytics-adattárházban támogatottak.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Visszaállítás – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Folytatás – AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Felfüggesztés – AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Következő lépések

További PowerShell-példákat a következő témakörben talál:

* [Adattárház létrehozása a PowerShell használatával](create-data-warehouse-powershell.md)
* [Adatbázis-visszaállítás](sql-data-warehouse-restore-points.md)

A PowerShell-lel automatizálható egyéb feladatokért lásd: [Azure SQL Database parancsmagok](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nem minden Azure SQL Database parancsmag támogatott az Azure szinapszis Analytics-adattárházban. A REST-sel automatizálható feladatok listáját a [Azure SQL Database műveleteit](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)ismertető témakörben tekintheti meg.
