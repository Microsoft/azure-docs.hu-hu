---
title: 'PowerShell: felügyelt példány létrehozása'
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk egy Azure PowerShell példa parancsfájlt tartalmaz egy felügyelt példány létrehozásához.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: af4add4f14457ea3cbf2acf88fb8d1cc11409c7a
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220907"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>Felügyelt példány létrehozása a PowerShell használatával

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl például egy felügyelt példányt hoz létre egy dedikált alhálózaton egy új virtuális hálózaton belül. Emellett egy útválasztási táblázatot és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A szkript sikeres futtatása után a felügyelt példány a virtuális hálózatról vagy egy helyszíni környezetből is elérhető. Lásd: az Azure-beli [virtuális gép konfigurálása Azure SQL Database felügyelt példányhoz való kapcsolódáshoz](../connect-vm-instance-configure.md) , valamint [pont – hely kapcsolat konfigurálása a helyszíni Azure SQL felügyelt példányhoz](../point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](../resource-limits.md#supported-regions) és [támogatott előfizetési típusok](../resource-limits.md#supported-subscription-types).

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz Azure PowerShell 1.4.0 vagy újabb rendszerre van szükség. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-script"></a>Példaszkript

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja. Az alábbi táblázatban szereplő használattal és egyéb parancsokkal kapcsolatos további információkért kattintson a parancsokra vonatkozó dokumentációra mutató hivatkozásokra.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja.
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Virtuális hálózatot hoz létre. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Alhálózati konfigurációt rendel egy virtuális hálózathoz. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Lekér egy virtuális hálózatot egy erőforráscsoportban. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Beállítja egy virtuális hálózat céljának állapotát. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Beolvas egy alhálózatot egy virtuális hálózatban. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | A virtuális hálózatban lévő alhálózat konfigurációjának cél állapotát konfigurálja. |
| [Új – AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Létrehoz egy útválasztási táblázatot. |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Útválasztási táblák beolvasása. |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Beállítja egy útválasztási tábla céljának állapotát. |
| [Új – AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Létrehoz egy felügyelt példányt. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/azure/overview).

Az Azure SQL felügyelt példányaihoz további PowerShell-szkriptek is megtalálhatók az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md).
