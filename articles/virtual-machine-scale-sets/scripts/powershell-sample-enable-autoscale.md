---
title: Azure PowerShell minták – gazdagép-alapú autoskálázás engedélyezése
description: Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre, illetve gazdagépalapú mérőszámok használatával automatikusan elvégzi a méretezést, amely a CPU terhelésének változásaihoz igazodik.
author: ju-shim
ms.author: jushiman
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 9530f34ee919547049df06fb0974971c3ba4b2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079625"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Virtuálisgép-méretezési csoport automatikus méretezése a PowerShell-lel
Ez a szkript egy Windows Server 2016 rendszert futtató virtuálisgép-méretezési csoportot hoz létre, illetve gazdagépalapú mérőszámok használatával automatikusan elvégzi a méretezést, amely a CPU terhelésének változásaihoz igazodik.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Példaszkript


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
Az alábbi paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése
A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Új – AzVmss](/powershell/module/az.compute/new-azvmss) | Létrehozza a virtuálisgép-méretezési csoportot és az összes támogató erőforrást, beleértve a virtuális hálózatot, a terheléselosztót és a NAT-szabályokat. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Lekéri a virtuálisgép-méretezési csoport adatait. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Egy virtuálisgép-bővítményt ad hozzá az egyéni szkriptekhez egy alapszintű webalkalmazás telepítéséhez. |
| [Frissítés – AzVmss](/powershell/module/az.compute/update-azvmss) | Frissíti a virtuálisgép-méretezési csoport modelljét a virtuálisgép-bővítmény alkalmazására. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Lekéri a terheléselosztó által a felhasználóhoz hozzárendelt nyilvános IP-cím adatait. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |

## <a name="next-steps"></a>Következő lépések
Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

