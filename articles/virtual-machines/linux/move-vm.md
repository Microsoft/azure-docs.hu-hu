---
title: Virtuális gép áthelyezése az Azure CLI használatával
description: Helyezze át a virtuális gépet egy másik Azure-előfizetésbe vagy-erőforráscsoporthoz az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 7dbe06a9f2fff8abf59adbdfc9e41055c85e8f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889297"
---
# <a name="move-a-vm-to-another-subscription-or-resource-group"></a>Virtuális gép áthelyezése másik előfizetésre vagy erőforráscsoport-csoportba
Ebből a cikkből megtudhatja, hogyan helyezhet át egy virtuális gépet (VM) Az erőforráscsoportok vagy előfizetések között. A virtuális gépek előfizetések közötti áthelyezése akkor lehet hasznos, ha létrehozott egy virtuális gépet egy személyes előfizetésben, és most át szeretné helyezni a céges előfizetésbe.

> [!IMPORTANT]
>Az áthelyezés részeként új erőforrás-azonosítók jönnek létre. A virtuális gép áthelyezése után frissítenie kell az eszközöket és a parancsfájlokat az új erőforrás-azonosítók használatára.
>


## <a name="use-the-azure-cli-to-move-a-vm"></a>Virtuális gép áthelyezése az Azure CLI használatával


Mielőtt áthelyezi a virtuális gépet az Azure CLI-vel, meg kell győződnie arról, hogy a forrás-és a cél-előfizetések ugyanazon a bérlőn belül találhatók. Annak a megadásához, hogy mindkét előfizetés ugyanazzal a bérlői AZONOSÍTÓval rendelkezik-e, használja az [az Account show](/cli/azure/account)lehetőséget.

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Ha a forrás-és a cél-előfizetéshez tartozó bérlői azonosítók nem egyeznek meg, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , és helyezze át az erőforrásokat egy új bérlőre.

A virtuális gép sikeres áthelyezéséhez át kell helyeznie a virtuális gépet és annak összes támogató erőforrását. Az az [Resource List](/cli/azure/resource) parancs használatával listázhatja az erőforráscsoport összes erőforrását és azonosítóját. Ez segít a parancs kimenetének egy fájlba való átirányításában, így az azonosítókat később is másolhatja és beillesztheti.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```
A `table` kimenet nem érhető el, ha használja `--interactive` . Módosítsa a kimenetet egy másik lehetőségre, például: `json` .

Ha egy virtuális gépet és erőforrásait egy másik erőforráscsoporthoz szeretné áthelyezni, használja [az az erőforrás áthelyezése](/cli/azure/resource)lehetőséget. Az alábbi példa bemutatja, hogyan helyezheti át a virtuális gépet és a szükséges leggyakoribb erőforrásokat. Használja az **-IDS** paramétert, és adja át az áthelyezni kívánt erőforrások azonosítóinak vesszővel tagolt listáját (szóközök nélkül).

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Ha át szeretné helyezni a virtuális gépet és annak erőforrásait egy másik előfizetésbe, adja hozzá a **--Destination-subscriptionId** paramétert a cél előfizetésének megadásához.

Ha a rendszer arra kéri, hogy erősítse meg, hogy szeretné-e áthelyezni a megadott erőforrásokat, adja meg az **Y** értéket a megerősítéshez.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Következő lépések
Több különböző típusú erőforrást is áthelyezhet az erőforráscsoportok és az előfizetések között. További információ: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
