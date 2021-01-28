---
title: A hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz – Azure CLI
description: Ebből a cikkből megtudhatja, hogyan korlátozhatja és korlátozhatja az Azure-erőforrások, például az Azure Storage és a Azure SQL Database hálózati hozzáférését az Azure CLI-t használó virtuális hálózati szolgáltatás-végpontokkal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5c23259a9cb1f3c409ad437a1e91151b5f401129
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946287"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>A virtuális hálózati szolgáltatásbeli végpontokkal való hálózati hozzáférés korlátozása az Azure CLI használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz, és az ebben a cikkben létrehozott összes többi erőforrást. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy alhálózattal rendelkező virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet)paranccsal.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

A szolgáltatási végpontokat csak olyan szolgáltatások esetében engedélyezheti, amelyek támogatják a szolgáltatási végpontokat. Azure-helyen elérhető szolgáltatás-végpontok által használható szolgáltatások megtekintése az [az Network vnet List-Endpoint-Services](/cli/azure/network/vnet). A következő példa a *eastus* régióban elérhető szolgáltatás-végpontok számára engedélyezett szolgáltatások listáját adja vissza. A visszaadott szolgáltatások listája idővel növekszik, mivel egyre több Azure-szolgáltatás lesz engedélyezve a szolgáltatás végpontja.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Hozzon létre egy további alhálózatot a virtuális hálózatban az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet)paranccsal. Ebben a példában a *Microsoft. Storage* szolgáltatáshoz tartozó szolgáltatási végpont jön létre az alhálózathoz: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. A következő példa egy *myNsgPrivate* nevű hálózati biztonsági csoportot hoz létre.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Társítsa a hálózati biztonsági csoportot a *privát* alhálózathoz az [az Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet). A következő példa a *myNsgPrivate* hálózati biztonsági csoportot társítja a *privát* alhálózathoz:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Hozzon létre biztonsági szabályokat az [az Network NSG Rule Create](/cli/azure/network/nsg/rule)paranccsal. Az alábbi szabály lehetővé teszi a kimenő hozzáférést az Azure Storage szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Minden hálózati biztonsági csoport több [alapértelmezett biztonsági szabályt](./network-security-groups-overview.md#default-security-rules)tartalmaz. Az alábbi szabály felülbírál egy alapértelmezett biztonsági szabályt, amely lehetővé teszi a kimenő hozzáférést az összes nyilvános IP-címhez. A `destination-address-prefix "Internet"` beállítás megtagadja a kimenő hozzáférést az összes nyilvános IP-címhez. Az előző szabály felülbírálja ezt a szabályt a magasabb prioritás miatt, ami lehetővé teszi az Azure Storage nyilvános IP-címeinek elérését.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

A következő szabály lehetővé teszi, hogy bárhonnan bejövő SSH-forgalom legyen az alhálózatra. Ez a szabály felülbírálja azon alapértelmezett biztonsági szabályokat, amelyek elutasítanak minden bejövő forgalmat az internetről. Az SSH engedélyezve van az alhálózat számára, hogy a kapcsolat tesztelhető legyen egy későbbi lépésben.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. A cikk további része egy Azure Storage-fiók hálózati hozzáférésének korlátozására szolgáló lépéseket mutat be példaként.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy Azure Storage-fiókot az [az Storage Account Create](/cli/azure/storage/account)paranccsal. Cserélje le `<replace-with-your-unique-storage-account-name>` az karaktert az összes Azure-helyen található egyedi névre, amely 3-24 karakter hosszúságú, és csak számokat és kisbetűket használ.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A Storage-fiók létrehozása után a Storage-fiókhoz tartozó kapcsolati karakterláncot az [az Storage Account show-kapcsolat-string](/cli/azure/storage/account)értékkel rendelkező változóba kell beolvasni. A kapcsolódási karakterlánc a fájlmegosztás egy későbbi lépésben való létrehozására szolgál.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Tekintse meg a változó tartalmát, és jegyezze fel a kimenetben visszaadott **AccountKey** értékét, mert azt egy későbbi lépésben használják.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy fájlmegosztást a Storage-fiókban az [az Storage Share Create](/cli/azure/storage/share)paranccsal. Egy későbbi lépésben a rendszer csatlakoztatja a fájlmegosztást a hálózati hozzáférés megerősítéséhez.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Minden hálózati hozzáférés megtagadása egy Storage-fiókhoz

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Ha korlátozni szeretné a hozzáférést a kiválasztott hálózatokra, módosítsa az alapértelmezett műveletet az az [Storage Account Update](/cli/azure/storage/account) *utasítással* . Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

Engedélyezze a hálózati hozzáférést a Storage-fiókhoz a *magánhálózati* alhálózatról az [az Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule)paranccsal.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *nyilvános* alhálózaton az [az VM Create](/cli/azure/vm)paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a visszaadott kimenet **publicIpAddress** . Ez a címe egy későbbi lépésben a virtuális gép internetről való elérésére szolgál.

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A létrehozás után jegyezze fel a visszaadott kimenet **publicIpAddress** . Ez a címe egy későbbi lépésben a virtuális gép internetről való elérésére szolgál.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

SSH-t a *myVmPrivate* virtuális gépre. Cserélje le a helyére *\<publicIpAddress>* a *myVmPrivate* virtuális gép nyilvános IP-címét.

```bash 
ssh <publicIpAddress>
```

Mappa létrehozása csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Csatlakoztassa az Azure-fájlmegosztást a létrehozott címtárhoz. A következő parancs futtatása előtt cserélje le a parancsot `<storage-account-name>` a fiók nevére és a megjelenő `<storage-account-key>` kulccsal a [Storage-fiók létrehozása](#create-a-storage-account)lehetőségre.

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Ekkor megjelenik a `user@myVmPrivate:~$` kérdés. Az Azure-fájlmegosztás sikeresen csatlakoztatva lett a */mnt/MyAzureFileShare*-hez.

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal más nyilvános IP-címekhez:

```bash
ping bing.com -c 4
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Lépjen ki az SSH-munkamenetből a *myVmPrivate* virtuális gépre.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A következő parancs használatával hozzon létre egy SSH-munkamenetet a *myVmPublic* virtuális géppel. Cserélje le a `<publicIpAddress>` t a *myVmPublic* virtuális gép nyilvános IP-címére: 

```bash 
ssh <publicIpAddress>
```

Könyvtár létrehozása csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Kísérelje meg az Azure-fájlmegosztás csatlakoztatását a létrehozott könyvtárba. Ez a cikk azt feltételezi, hogy telepítette az Ubuntu legújabb verzióját. Ha az Ubuntu korábbi verzióit használja, tekintse [meg a Linux csatlakoztatása](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakört, amely további utasításokat tartalmaz a fájlmegosztás csatlakoztatásával kapcsolatban. A következő parancs futtatása előtt cserélje le a parancsot `<storage-account-name>` a fiók nevére és a megjelenő `<storage-account-key>` kulcsra a [Storage-fiók létrehozása](#create-a-storage-account)területen:

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

A hozzáférés megtagadva, és hibaüzenet jelenik meg `mount error(13): Permission denied` , mivel a *myVmPublic* virtuális gép a *nyilvános* alhálózaton belül van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Lépjen ki az SSH-munkamenetből a *myVmPublic* virtuális gépre.

A számítógépről próbálja meg megtekinteni a Storage-fiókban lévő megosztásokat az [az Storage Share List](/cli/azure/storage/share)paranccsal. Cserélje `<account-name>` le `<account-key>` a és a elemet a Storage-fiók nevére és kulcsára a [Storage-fiók létrehozásakor](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

A hozzáférés megtagadva, és a *kérelem nem jogosult a művelet elvégzésére* , mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *privát* alhálózatán található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure) paranccsal távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuális hálózati alhálózat szolgáltatási végpontját engedélyezte. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. További információt a [virtuális hálózatok összekapcsolásával](tutorial-connect-virtual-networks-cli.md)foglalkozó témakörben talál.
