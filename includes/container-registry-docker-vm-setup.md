---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773466"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Tesztelési célokra használjon Docker-kompatibilis Ubuntu virtuális gépet az Azure Container Registry eléréséhez. A regisztrációs Azure Active Directory való hitelesítéshez telepítse az [Azure CLI-t][azure-cli] is a virtuális gépre. Ha már rendelkezik Azure-beli virtuális géppel, hagyja ki ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális géphez és a tároló-beállításjegyzékhez is. Ez a beállítás leegyszerűsíti a tisztítást a végén, de nem szükséges. Ha külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa [az az group create futtatását.][az-group-create] Az alábbi példa feltételezi, hogy környezeti változókat adott meg az erőforráscsoport nevéhez és a beállításjegyzék helyéhez:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Most telepítsen egy alapértelmezett Ubuntu Azure virtuális gépet [az az vm create használatával.][az-vm-create] Az alábbi példa egy *myDockerVM* nevű virtuális gépet hoz létre.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Amikor a parancs befejeződik, jegyezze fel az `publicIpAddress` Azure CLI által megjelenített üzenetet. Ezzel a címmel SSH-kapcsolatokat létesít a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futtatása után létesítsen SSH-kapcsolatot a virtuális géphez. Cserélje *le a publicIpAddress* helyére a virtuális gép nyilvános IP-címét.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsokat a Docker ubuntus virtuális gépre való telepítéséhez:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális gépen:

```bash
sudo docker run -it hello-world
```

Kimenet:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Az Azure CLI összetevő telepítése

Az Azure CLI ubuntus virtuális gépre való telepítéséhez kövesse az Azure CLI telepítése az [apt](/cli/azure/install-azure-cli-apt) használatával részben található lépéseket. Például:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Lépjen ki az SSH-kapcsolatból.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group