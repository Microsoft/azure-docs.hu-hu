---
title: Közelségi elhelyezési csoport létrehozása az Azure CLI használatával
description: Ismerje meg, hogyan lehet közelségi elhelyezési csoportokat létrehozni és használni az Azure-beli virtuális gépekhez.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771442"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Virtuális gépek telepítése közelségi elhelyezési csoportokba az Azure CLI használatával

Ahhoz, hogy a virtuális gépeket a lehető legnagyobb késéssel érjük el, egy közelségi elhelyezési csoportban kell üzembe [helyezni őket.](../co-location.md#proximity-placement-groups)

A közelségi elhelyezési csoport olyan logikai csoportosítás, amely az Azure-beli számítási erőforrások egymás fizikai közelében helyezkedik el. A közelségi elhelyezési csoportok olyan számítási feladatokhoz hasznosak, ahol a kis késés követelmény.


## <a name="create-the-proximity-placement-group"></a>Közelségi elhelyezési csoport létrehozása
Hozzon létre egy közelségi elhelyezési csoportot a [`az ppg create`](/cli/azure/ppg#az_ppg_create) használatával. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Közelségi elhelyezési csoportok listából

Az összes közelségi elhelyezési csoportot listába sorolhatja [az az ppg list használatával.](/cli/azure/ppg#az_ppg_list)

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a közelségi elhelyezési csoportban az [új az vm használatával.](/cli/azure/vm#az_vm_create)

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

A virtuális gépet a közelségi elhelyezési csoportban az az ppg show használatával [láthatja.](/cli/azure/ppg#az_ppg_show)

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
Rendelkezésre állási csoportot a közelségi elhelyezési csoportban is létrehozhat. Használja ugyanazt a paramétert az `--ppg` [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) paraméterrel egy rendelkezésre állási csoport létrehozásához, és a rendelkezésre állási csoportban található összes virtuális gép ugyanabban a közelségi elhelyezési csoportban lesz létrehozva.

## <a name="scale-sets"></a>Méretezési csoportok

Méretezési csoportot a közelségi elhelyezési csoportban is létrehozhat. Használja ugyanazt `--ppg` a paramétert [az az vmss create](/cli/azure/vmss#az_vmss_create) paraméterrel egy méretezési csoport létrehozásához, és az összes példány ugyanabban a közelségi elhelyezési csoportban jön létre.

## <a name="next-steps"></a>Következő lépések

További információ a közelségi elhelyezési [csoportokhoz](/cli/azure/ppg) elérhető Azure CLI-parancsokról.