---
title: 'Gyors útmutató: Linux rendszerű virtuális gép létrehozása az Azure CLI használatával'
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan hozhat létre Linux rendszerű virtuális gépet az Azure CLI használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: 2d66064167741b59766763f4fc1313409de1e649
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801056"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Gyors útmutató: Linux virtuális gép létrehozása az Azure CLI-vel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe az Azure CLI segítségével Linux rendszerű virtuális gépeket (VM-eket) az Azure-ban. Ebben az oktatóanyagban az Ubuntu 16.04 LTS-t fogjuk telepíteni. A virtuális gép működés közbeni bemutatásához SSH-val fog csatlakozni hozzá, és az NGINX-webkiszolgálót fogja telepíteni.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngésző lapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is megnyithatja. Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.30-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal.

A következő példa létrehoz egy *myVM* nevű virtuális gépet, és hozzáad egy *azureuser* nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter használatával automatikusan létrejön egy SSH-kulcs, és elhelyezhető az alapértelmezett kulcs helyén ( *~/.ssh*). Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő kimeneti példa azt mutatja be, hogy a virtuális gép létrehozási művelete sikeres volt.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Vegye észere a saját `publicIpAddress` címét a virtuális gép kimenetében. A következő lépésekben ez a cím használható a virtuális gép eléréséhez.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára

Alapértelmezés szerint csak SSH-kapcsolatok vannak megnyitva, amikor Linux virtuális gépet hoz létre az Azure-ban. Használja az [az vm open-port](/cli/azure/vm) parancsot a 80-as TCP-port NGINX webkiszolgálóhoz történő megnyitásához:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

A virtuális géphez a szokásos módon kapcsolódhat SSH-val. Cserélje le **publicIpAddress** kifejezést a virtuális gép nyilvános IP-címére, amint az a virtuális gép előző kimenetében szerepel:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az NGINX-webkiszolgálót. Frissítse a csomag forrásait, majd telepítse a legújabb NGINX-csomagot.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Ha elkészült, az SSH-munkamenetből való kilépéshez írja be a következőt: `exit`.

## <a name="view-the-web-server-in-action"></a>A webkiszolgáló működésének ellenőrzése

Egy tetszőleges böngésző használatával megtekintheti az alapértelmezett NGINX-kezdőlapot. Webcímként használja a virtuális gép nyilvános IP-címét. A következő példában az alapértelmezett NGINX-webhely látható:

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalom számára, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
