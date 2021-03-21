---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 046a4bc9abb936ca6f9fcecd0f660a723edb092b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80116992"
---
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. A parancs emellett az *azureuser* nevet állítja be rendszergazdai felhasználónévként. Később ezt a nevet fogja használni a virtuális géphez való csatlakozáshoz. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím a később lépésekben használható a virtuális gép eléréséhez.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Alapértelmezés szerint kizárólag SSH-kapcsolatok engedélyezettek az Azure-ban üzembe helyezett, Linux rendszerű virtuális gépeken. Mivel ez a virtuális gép webkiszolgáló lesz, meg kell nyitnia a 80-as portot az internet irányából. A kívánt port megnyitásához használja az az [VM Open-port](/cli/azure/vm) parancsot.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val


Ha még nem ismeri a virtuális gépéhez tartozó nyilvános IP-címet, futtassa az [az network public-ip list](/cli/azure/network/public-ip) parancsot. Erre az IP-címre több későbbi lépésben is szüksége lesz.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Helyettesítse be a virtuális gépe tényleges nyilvános IP-címét. Ebben a példában az IP-cím a következő: *40.68.254.142*. Az *azureuser* a virtuális gép létrehozásakor beállított rendszergazdai felhasználónév.

```bash
ssh azureuser@40.68.254.142
```

