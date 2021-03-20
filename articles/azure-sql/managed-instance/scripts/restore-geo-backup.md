---
title: 'PowerShell: a Geo-biztonsági mentés visszaállítása az Azure SQL felügyelt példányaihoz'
description: Azure PowerShell példa parancsfájlt egy Azure SQL felügyelt példány-adatbázis visszaállítására egy geo-redundáns biztonsági mentésből.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 07/03/2019
ms.openlocfilehash: f04e4b5a44dccdc3aaeabe6b4144836b0be7354c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790780"
---
# <a name="use-powershell-to-restore-an-azure-sql-managed-instance-database-to-another-geo-region"></a>Az Azure SQL felügyelt példány-adatbázisának visszaállítása a PowerShell használatával egy másik földrajzi régióba

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl például visszaállítja egy Azure SQL felügyelt példány adatbázisát egy távoli földrajzi régióból (Geo-visszaállítás).  

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz Azure PowerShell 1.4.0 vagy újabb rendszerre van szükség. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

```azurepowershell-interactive
# Connect-AzAccount
# The SubscriptionId in which to create these objects
$SubscriptionId = '<put subscription_id here>'
# Set the information for your managed instance
$SourceResourceGroupName = "myResourceGroup-$(Get-Random)"
$SourceInstanceName = "myManagedInstance-$(Get-Random)"
$SourceDatabaseName = "myInstanceDatabase-$(Get-Random)"

# Set the information for your destination managed instance
$TargetResourceGroupName = "myTargetResourceGroup-$(Get-Random)"
$TargetInstanceName = "myTargetManagedInstance-$(Get-Random)"
$TargetDatabaseName = "myTargetInstanceDatabase-$(Get-Random)"

Connect-AzAccount
Set-AzContext -SubscriptionId $SubscriptionId

$backup = Get-AzSqlInstanceDatabaseGeoBackup `
-ResourceGroupName $SourceResourceGroupName `
-InstanceName $SourceInstanceName `
-Name $SourceDatabaseName

$backup | Restore-AzSqlInstanceDatabase -FromGeoBackup `
-TargetInstanceDatabaseName $TargetDatabaseName `
-TargetInstanceName $TargetInstanceName `
-TargetResourceGroupName $TargetResourceGroupName

```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $TargetResourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/New-AzResourceGroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Get-AzSqlInstanceDatabaseGeoBackup](/powershell/module/az.sql/Get-AzSqlInstanceDatabaseGeoBackup) | Egy SQL felügyelt példány adatbázisának geo-redundáns biztonsági mentését hozza létre. |
| [Visszaállítás – AzSqlInstanceDatabase](/powershell/module/az.sql/Restore-AzSqlInstanceDatabase) | Létrehoz egy adatbázist a felügyelt SQL-példányról a Geo biztonsági mentésből. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

További információ a PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/).

A Azure SQL Database PowerShell-parancsfájlokkal kapcsolatos további mintákat [Azure SQL Database PowerShell-parancsfájlokban](../../database/powershell-script-content-guide.md)találhat.