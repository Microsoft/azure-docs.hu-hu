---
title: Karbantartási értesítések leküldése a CLI használatával
description: Megtekintheti az Azure-ban futó virtuális gépek karbantartási értesítéseit, és önkiszolgáló karbantartást kezdhet az Azure CLI használatával.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777868"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tervezett karbantartási értesítések kezelése az Azure CLI használatával

**Ez a cikk a Linux és Windows rendszerű virtuális gépekre vonatkozik.**

A CLI használatával láthatja, hogy mikor vannak ütemezve a virtuális gépek [karbantartásra.](maintenance-notifications.md) A tervezett karbantartási információk az [az vm get-instance-view oldalon érhetők el.](/cli/azure/vm#az_vm_get_instance_view)
 
A rendszer csak akkor ad vissza karbantartási információkat, ha karbantartást terveznek. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Kimenet
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Karbantartás kezdése

A következő hívás elindítja a karbantartást egy virtuális gépen, ha true `IsCustomerInitiatedMaintenanceAllowed` (igaz) érték van beállítva.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is vannak a klasszikus üzembe helyezési modellel üzembe helyezett, örökölt virtuális gépek, a klasszikus Azure CLI használatával lekérdezheti a virtuális gépeket, és karbantartást kezdeményezhet.

A következő beírásával győződjön meg arról, hogy a megfelelő módban van a klasszikus virtuális gépekkel való munkához:

```
azure config mode asm
```

A myVM nevű virtuális gép karbantartási állapotának *lekért* mezőbe írja be a következőt:

```
azure vm show myVM 
``` 

A *myService* szolgáltatásban és a *myDeployment* üzemelő példányban található *myVM* nevű klasszikus virtuális gép karbantartásának elkezdéséhez írja be a következőt:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Következő lépések

A tervezett karbantartást a következő portálon [Azure PowerShell](maintenance-notifications-powershell.md) [is:](maintenance-notifications-portal.md).
