---
title: Egyetlen adatbázis figyelése és méretezése a PowerShell használatával Azure SQL Database
description: Egy Azure PowerShell példa parancsfájl használatával figyelheti és méretezheti az egyes adatbázisokat Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 04c19ca8fbdaed85225b5af128c72d393e5350e8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573252"
---
# <a name="use-powershell-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Egyetlen adatbázis figyelése és méretezése a PowerShell használatával Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a PowerShell-parancsfájl egy adott adatbázis teljesítmény-mérőszámait figyeli, méretezi azt egy magasabb számítási méretre, és riasztási szabályt hoz létre az egyik teljesítmény-mérőszámon.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az az PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=15-16 "Monitor and scale single database")]

> [!NOTE]
> A metrikák teljes listáját lásd: a [metrikák támogatottak](../../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases).
> [!TIP]
> Az adatbázis-műveletek állapotának lekéréséhez használja a [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) -t, és a [stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity) használatával szakítsa meg az adatbázis-frissítési műveletet.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy kiszolgálót, amely egyetlen adatbázist vagy rugalmas készletet üzemeltet. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Megjeleníti az adatbázis méretkihasználtsági adatait.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Frissíti az adatbázis tulajdonságait, vagy áthelyezi az adatbázist a rugalmas készletek között, illetve azok között. |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Riasztási szabályt állít be a mérőszámok jövőbeli automatikus figyeléséhez. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/).

A PowerShell-parancsfájlok további mintái [Azure PowerShell parancsfájlokban](../powershell-script-content-guide.md)találhatók.
