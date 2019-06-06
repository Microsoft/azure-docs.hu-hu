---
title: PowerShell példa-figyelő-méretezési csoport – rugalmas készlet – Azure SQL Database |} A Microsoft Docs
description: Az Azure PowerShell példaszkript az monitorozása és skálázása az Azure SQL Database rugalmas készlet
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 4c72324f4dcbcda313b7f6d1e34470c884822d59
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729315"
---
# <a name="use-powershell-to-monitor-and-scale-an-elastic-pool-in-azure-sql-database"></a>Az Azure SQL Database egy rugalmas készlet monitorozása és skálázása a PowerShell használatával

Ez a PowerShell-példaszkript egy rugalmas készlet teljesítmény-mérőszámait monitorozza, egy magasabb számítási mérethez skálázza a készletet, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=17-18 "Monitor and scale a single SQL Database")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítsa el az erőforráscsoportot és az ahhoz kapcsolódó összes erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Egy SQL Database-kiszolgálót, amelyen egy önálló adatbázist vagy rugalmas készletet hoz létre. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Egy rugalmas készletet hoz létre. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy önálló adatbázis vagy az adatbázis egy rugalmas készletben. |
| [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) | Megjeleníti az adatbázis méretkihasználtsági adatait.|
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Mérőszámalapú riasztási szabályt ad hozzá vagy frissít. |
| [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) | Frissíti a rugalmas készlet tulajdonságait |
| [Add-AzMetricAlertRule](/powershell/module/az.monitor/add-azmetricalertrule) | Beállít egy riasztási szabályt a DTU-k jövőbeni automatikus monitorozásához. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
