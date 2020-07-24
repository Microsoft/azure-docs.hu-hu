---
title: 'PowerShell: rugalmas készlet hozzáadása automatikus feladatátvételi csoporthoz'
description: Azure PowerShell példa parancsfájlt egy Azure SQL Database rugalmas készlet létrehozásához, adja hozzá egy automatikus feladatátvételi csoporthoz, és tesztelje a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e509fc0f93c60d271409b5453aecdf793cd22ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040603"
---
# <a name="use-powershell-to-add-an-elastic-pool-to-a-failover-group"></a>Rugalmas készlet hozzáadása feladatátvételi csoporthoz a PowerShell használatával
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a Azure PowerShell szkript létrehoz egy adatbázist a Azure SQL Databaseban, hozzáadja azt egy rugalmas készlethez, létrehoz egy feladatátvételi csoportot és teszteli a feladatátvételi teszteket.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az az PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaparancsfájlok

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Adatbázisokat és rugalmas készleteket futtató kiszolgálót hoz létre. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Kiszolgáló szintű tűzfalszabály létrehozása egy kiszolgálóhoz. |
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | létrehoz egy új adatbázist; |
| [Új – AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbázis-készletet hoz létre.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Beállítja egy adatbázis tulajdonságait, vagy áthelyezi egy meglévő adatbázist egy rugalmas készletbe. |
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több adatbázist kér le. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy listázza az adatbázis-feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Egy adatbázis-feladatátvételi csoport feladatátvételét hajtja végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása |

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../powershell-script-content-guide.md).
