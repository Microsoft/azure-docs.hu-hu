---
title: PowerShell & REST API-k dedikált SQL-készlethez (korábban SQL DW)
description: A legfontosabb PowerShell-parancsmagok a (korábban SQL DW) dedikált SQL-készlethez a Azure Synapse Analytics beleértve az adatbázisok szüneteltetési és folytatási mikéntjét.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 1f00f470fb0aa8ac98b431c6fc9428f501b553ed
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566443"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>A PowerShell & REST API-kat kínál a dedikált SQL-készlethez (korábbi nevén SQL DW-hez) a Azure Synapse Analytics 

Számos dedikált SQL-készlet felügyeleti feladat kezelhető akár parancsmagok Azure PowerShell, akár REST API-k használatával.  Az alábbiakban néhány példát talál arra, hogyan automatizálhatja a dedikált SQL-készlet (korábban SQL DW) gyakori feladatait PowerShell-parancsokkal.  Néhány jó REST-példáért tekintse meg a Skálázhatóság kezelése a [REST-sel cikket.](sql-data-warehouse-manage-compute-rest-api.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Első lépések a Azure PowerShell parancsmagokkal

1. Nyissa meg a Windows PowerShellt.
2. A PowerShell-parancssorban futtassa ezeket a parancsokat a Azure Resource Manager és válassza ki az előfizetését.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Adattárház szüneteltetése – példa

Szüneteltetheti a "Database02" nevű adatbázist, amely a "Server01" nevű kiszolgálón van üzemeltetve.  A kiszolgáló egy "ResourceGroup1" nevű Azure-erőforráscsoportban található.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Ez a példa egy változat, amely a lekért objektumot [a Suspend-AzSqlDatabase parancsra frissíti.](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Ennek eredményeképpen az adatbázis fel van függesztve. Az utolsó parancs megjeleníti az eredményeket.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Példa adatraktár-indításra

A "Database02" nevű adatbázis működésének folytatása a "Server01" nevű kiszolgálón. A kiszolgáló egy "ResourceGroup1" nevű erőforráscsoportban található.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Ez a példa egy "Database02" nevű adatbázist a "Server01" nevű kiszolgálóról ad vissza, amely egy "ResourceGroup1" nevű erőforráscsoportban található. A lekért objektumot a [Resume-AzSqlDatabase adatbázishoz irányitja.](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Vegye figyelembe, hogy ha a kiszolgáló foo.database.windows.net, használja a "foo" nevet a -ServerName paraméterként a PowerShell-parancsmagok között.

## <a name="other-supported-powershell-cmdlets"></a>Egyéb támogatott PowerShell-parancsmagok

Ezeket a PowerShell-parancsmagokat a Azure Synapse Analytics támogatja.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Következő lépések

További PowerShell-példákért lásd:

* [Adattárház létrehozása a PowerShell használatával](create-data-warehouse-powershell.md)
* [Adatbázis visszaállítása](sql-data-warehouse-restore-points.md)

A PowerShell-parancsmagokkal automatizálható egyéb feladatokért [lásd: Azure SQL Database parancsmagok.](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Nem minden Azure SQL Database parancsmag támogatott az Azure Synapse Analytics adattárházhoz. A REST-sel automatizálható feladatok listájáért lásd: [Operations for Azure SQL Database.](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
