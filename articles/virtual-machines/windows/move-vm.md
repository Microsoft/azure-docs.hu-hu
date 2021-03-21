---
title: Windowsos VM-erőforrás áthelyezése az Azure-ban
description: Windows rendszerű virtuális gép áthelyezése egy másik Azure-előfizetésre vagy-erőforráscsoporthoz a Resource Manager-alapú üzemi modellben.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555244"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Windows rendszerű virtuális gép áthelyezése más Azure-előfizetésbe vagy erőforráscsoportba
Ez a cikk bemutatja, hogyan helyezhet át egy Windows rendszerű virtuális gépet (VM) Az erőforráscsoportok vagy előfizetések között. Az előfizetések közötti váltás akkor lehet hasznos, ha eredetileg létrehozott egy virtuális gépet egy személyes előfizetésben, és most át szeretné helyezni a vállalata előfizetését, hogy folytassa a munkáját. Nem kell leállítania a virtuális gépet, hogy át lehessen helyezni, és az áthelyezés során továbbra is futnia kell.

> [!IMPORTANT]
>Az áthelyezés részeként új erőforrás-azonosítók jönnek létre. A virtuális gép áthelyezése után frissítenie kell az eszközöket és a parancsfájlokat az új erőforrás-azonosítók használatára.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Virtuális gép áthelyezése a PowerShell használatával

Ha át szeretne helyezni egy virtuális gépet egy másik erőforráscsoporthoz, győződjön meg arról, hogy az összes függő erőforrást is át kell helyeznie. Az egyes erőforrások erőforrás-azonosítójával rendelkező lista lekéréséhez használja a [Get-AzResource](/powershell/module/az.resources/get-azresource) parancsmagot.

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Az előző parancs kimenetével létrehozhat egy vesszővel elválasztott erőforrás-azonosítókat a [Move-AzResource](/powershell/module/az.resources/move-azresource) , hogy az egyes erőforrások áthelyezhetők legyenek a célhelyre.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Az erőforrások másik előfizetésbe való áthelyezéséhez adja meg a **-DestinationSubscriptionId** paramétert.

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Ha a rendszer arra kéri, hogy erősítse meg, hogy szeretné-e áthelyezni a megadott erőforrásokat, adja meg az **Y** értéket a megerősítéshez.

## <a name="next-steps"></a>Következő lépések
Több különböző típusú erőforrást is áthelyezhet az erőforráscsoportok és az előfizetések között. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
