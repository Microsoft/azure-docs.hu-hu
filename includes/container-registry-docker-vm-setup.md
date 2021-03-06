---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244764"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker-kompatibilis virtuális gép létrehozása

Tesztelési célból használjon Docker-kompatibilis Ubuntu virtuális gépet az Azure Container Registry eléréséhez. Ha Azure Active Directory hitelesítést kíván használni a beállításjegyzékben, telepítse az [Azure CLI][azure-cli] -t is a virtuális gépre. Ha már rendelkezik Azure-beli virtuális géppel, ugorja át ezt a létrehozási lépést.

Használhatja ugyanazt az erőforráscsoportot a virtuális géphez és a tároló-beállításjegyzékhez is. Ez a beállítás leegyszerűsíti a tisztítást a végén, de nem kötelező. Ha úgy dönt, hogy külön erőforráscsoportot hoz létre a virtuális géphez és a virtuális hálózathoz, futtassa az [az Group Create][az-group-create]parancsot. Az alábbi példa feltételezi, hogy beállította a környezeti változókat az erőforráscsoport neve és a beállításjegyzék helye számára:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Most helyezzen üzembe egy alapértelmezett Ubuntu Azure-beli virtuális gépet az [az VM Create][az-vm-create]paranccsal. A következő példa egy *myDockerVM* nevű virtuális gépet hoz létre.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs befejeződik, jegyezze fel az `publicIpAddress` Azure CLI által megjelenített adatmennyiséget. Ezzel a címtől SSH-kapcsolatokat hozhat a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futása után létesítsen SSH-kapcsolatokat a virtuális géppel. Cserélje le a *publicIpAddress* -t a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa az alábbi parancsokat a Docker telepítéséhez az Ubuntu virtuális gépen:

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

Az Azure CLI az Ubuntu rendszerű virtuális gépen való telepítéséhez kövesse az Azure CLI az [apt-vel](/cli/azure/install-azure-cli-apt) való telepítésének lépéseit. Például:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Lépjen ki az SSH-kapcsolatban.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group