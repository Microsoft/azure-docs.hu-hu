---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure CLI
titlesuffix: Azure Virtual Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot az Azure CLI használatával. A virtuális hálózatok lehetővé teszi, hogy az Azure-erőforrások kommunikáljanak egymással és az internettel.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 407207c0dcb6270f08fb511a01e6e4e835b9fab9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776752"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Gyors útmutató: Virtuális hálózat létrehozása az Azure CLI-vel

A virtuális hálózatok lehetővé teszik, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak egymással és az internettel. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozik a virtuális gépekhez az internetről, és privát módon kommunikál az új virtuális hálózaton keresztül.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat gazdagépeként. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Ez a példa létrehoz egy **CreateVNetQS-rg nevű** erőforráscsoportot az **Eastus** helyen:

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Ez a példa létrehoz egy **myVNet** nevű alapértelmezett virtuális hálózatot egy default nevű **alhálózattal:**

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. 

Ha az SSH-kulcsok még nem léteznek az alapértelmezett kulcshelyen, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. 

A `--no-wait` beállítás a háttérben hozza létre a virtuális gépet. Folytathatja a következő lépéssel. 

Ez a példa létrehoz egy **myVM1 nevű virtuális gépet:**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Az előző `--no-wait` lépésben a kapcsolót használta. Létrehozhatja a második, **myVM2 nevű virtuális gépet.**

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="azure-cli-output-message"></a>Az Azure CLI kimeneti üzenete

A virtuális gépek létrehozása néhány percet vesz igénybe. Miután az Azure létrehozza a virtuális gépeket, az Azure CLI a következő kimenetet adja vissza:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>Virtuális gép nyilvános IP-címe

A **myVM2** nyilvános IP-cím lekért neveként használja [az az network public-ip show címet:](/cli/azure/network/public-ip#az_network_public_ip_show)

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Ebben a parancsban cserélje le `<publicIpAddress>` a helyére a **myVM2** virtuális gép nyilvános IP-címét:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A **myVM2 és a myVM1** virtuális gépek közötti privát kommunikáció megerősítéséhez írja be a következő parancsot: 

```bash
ping myVM1 -c 4
```

Négy választ fog kapni a *következőtől: 10.0.0.4*.

Lépjen ki a myVM2 virtuális gép **SSH-munkamenetből.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) parancs használatával eltávolíthatja az erőforráscsoportot és az összes erőforrását:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban: 

* Létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. 
* Csatlakozott egy virtuális géphez az internetről, és privát módon kommunikált a két virtuális gép között.

A virtuális gépek közötti privát kommunikáció korlátlan a virtuális hálózatokon. 

A virtuálisgép-hálózati kommunikáció különböző típusainak konfigurálásával kapcsolatos további információkért olvassa el a következő cikket:
> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)
