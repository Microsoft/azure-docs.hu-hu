---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure CLI
titlesuffix: Azure Virtual Network
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatot az Azure CLI használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások kommunikációját egymással és az internettel.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3f4cd0a09c64c8c89116bf3a7dec40bae9f05f71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199067"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Gyors útmutató: Virtuális hálózat létrehozása az Azure CLI-vel

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy privát módon kommunikáljanak egymással és az internettel. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután kapcsolódhat a virtuális gépekhez az internetről, és magánjellegű módon kommunikálhat az új virtuális hálózaton keresztül.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Ez a példa létrehoz egy **CreateVNetQS-RG** nevű erőforráscsoportot a **Eastus** helyen:

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) paranccsal. Ez a példa létrehoz egy **myVNet** nevű alapértelmezett virtuális hálózatot egy **alapértelmezett** nevű alhálózattal:

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

Ha az SSH-kulcsok még nem találhatók meg az alapértelmezett kulcs helyén, a parancs létrehozza azokat. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. 

A `--no-wait` beállítás a háttérben hozza létre a virtuális gépet. Folytassa a következő lépéssel. 

Ez a példa egy **myVM1** nevű virtuális gépet hoz létre:

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

Az `--no-wait` előző lépésben használt beállítást használta. Ezt megteheti, és létrehozhatja a **myVM2** nevű második virtuális gépet.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI kimeneti üzenet

A virtuális gépek létrehozása néhány percet vesz igénybe. Miután az Azure létrehozta a virtuális gépeket, az Azure CLI a következőhöz hasonló kimenetet ad vissza:

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

A nyilvános IP- **myVM2** lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Ebben a parancsban cserélje le a parancsot a `<publicIpAddress>` **myVM2** virtuális gép nyilvános IP-címére:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A **myVM2** és a **myVM1** virtuális gépek közötti privát kommunikáció megerősítéséhez írja be a következő parancsot:

```bash
ping myVM1 -c 4
```

Négy választ fog kapni a *10.0.0.4*.

Lépjen ki az SSH-munkamenetből a **myVM2** virtuális géppel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban: 

* Létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. 
* Egy virtuális géphez kapcsolódott az internetről, és a két virtuális gép között magántulajdonban kommunikál.

A virtuális gépek közötti magánhálózati kommunikáció nem korlátozott a virtuális hálózatokban. 

A következő cikkből megtudhatja, hogyan konfigurálhat különböző típusú virtuálisgép-hálózati kommunikációt:
> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)
