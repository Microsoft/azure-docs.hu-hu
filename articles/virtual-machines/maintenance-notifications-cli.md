---
title: Karbantartási értesítések beszerzése a parancssori felület használatával
description: Megtekintheti az Azure-ban futó virtuális gépek karbantartási értesítéseit, és megkezdheti az önkiszolgáló karbantartást az Azure CLI használatával.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: cd042ce09533cbefe37cb2e4d311a3857e3dfdec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552405"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Tervezett karbantartási értesítések kezeléséhez az Azure CLI használatával

**Ez a cikk a Linux és Windows rendszerű virtuális gépekre is vonatkozik.**

A CLI használatával megtekintheti, hogy mikor ütemezik a virtuális gépek [karbantartását](maintenance-notifications.md). A tervezett karbantartási információk az [az VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view)címről érhetők el.
 
A karbantartási adatokat csak akkor adja vissza a rendszer, ha karbantartási terv van. 

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

## <a name="start-maintenance"></a>Karbantartás indítása

A következő hívás elindítja a karbantartást egy virtuális gépen `IsCustomerInitiatedMaintenanceAllowed` , ha a értéke TRUE (igaz).

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klasszikus üzembe helyezések

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ha továbbra is a klasszikus üzembe helyezési modellel telepített örökölt virtuális gépekkel rendelkezik, a klasszikus Azure CLI használatával lekérdezheti a virtuális gépeket, és megkezdheti a karbantartást.

Győződjön meg arról, hogy a megfelelő módban van, hogy a klasszikus virtuális gép használatával működjön a következő beírásával:

```
azure config mode asm
```

Egy *myVM* nevű virtuális gép karbantartási állapotának lekéréséhez írja be a következőt:

```
azure vm show myVM 
``` 

A *myVM* nevű klasszikus virtuális gép karbantartásának megkezdéséhez a *myService* szolgáltatásban és a *myDeployment* -telepítésben írja be a következőt:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Következő lépések

A tervezett karbantartást a [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.
