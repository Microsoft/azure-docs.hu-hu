---
title: Az Azure Storage-ba irányuló kiszűrése korlátozása – Azure CLI
description: Ebből a cikkből megtudhatja, hogyan korlátozhatja és korlátozhatja a virtuális hálózati adatkiszűrése az Azure Storage-erőforrásokhoz a virtuális hálózati szolgáltatás végpont-házirendjeivel az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 9ce1e320a93a834a938ce95f3931d885d2214faa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98216869"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Az Azure Storage-fiókok kiszűrése az Azure CLI-vel való kezelése virtuális hálózati szolgáltatás végponti házirendjeivel

A Virtual Network szolgáltatás végponti házirendjei lehetővé teszik, hogy az Azure Storage-fiókok hozzáférés-vezérlését a szolgáltatás-végpontokon keresztül egy virtuális hálózaton belül alkalmazza. Ez a legfontosabb feladat a számítási feladatok biztonságossá tétele, a Storage-fiókok kezelése, valamint az adatok kiszűrése engedélyezése.
Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hozzon létre egy virtuális hálózatot, és adjon hozzá egy alhálózatot.
* Szolgáltatás végpontjának engedélyezése az Azure Storage-hoz.
* Hozzon létre két Azure Storage-fiókot, és engedélyezze a hálózati hozzáférést a fent létrehozott alhálózatból.
* Hozzon létre egy szolgáltatás-végponti házirendet, amely lehetővé teszi, hogy csak az egyik Storage-fiókhoz férhessen hozzá.
* Helyezzen üzembe egy virtuális gépet (VM) az alhálózaton.
* Erősítse meg az engedélyezett Storage-fiókhoz való hozzáférést az alhálózaton.
* Győződjön meg arról, hogy a hozzáférés meg van tagadva a nem engedélyezett Storage-fiókhoz az alhálózaton.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

Ebben a példában a *Microsoft. Storage* szolgáltatáshoz tartozó szolgáltatási végpont jön létre a *privát* alhálózathoz: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Az Azure Storage-fiókokhoz való hálózati hozzáférés korlátozása

Ez a szakasz azokat a lépéseket sorolja fel, amelyekkel korlátozható egy Azure Storage-fiók hálózati hozzáférése a virtuális hálózat adott alhálózatáról a szolgáltatási végponton keresztül.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre két Azure Storage-fiókot az [az Storage Account Create](/cli/azure/storage/account)paranccsal.

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

A Storage-fiókok létrehozása után kérje le a Storage-fiókok kapcsolati karakterláncát az [az Storage Account show-kapcsolat-string](/cli/azure/storage/account)változóval. A kapcsolódási karakterlánc a fájlmegosztás egy későbbi lépésben való létrehozására szolgál.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Tekintse meg a változó tartalmát, és jegyezze fel a kimenetben visszaadott **AccountKey** értékét, mert azt egy későbbi lépésben használják.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy fájlmegosztást a Storage-fiókban az [az Storage Share Create](/cli/azure/storage/share)paranccsal. Egy későbbi lépésben a rendszer csatlakoztatja a fájlmegosztást a hálózati hozzáférés megerősítéséhez.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>A Storage-fiókhoz való összes hálózati hozzáférés tiltása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Ha korlátozni szeretné a hozzáférést a kiválasztott hálózatokra, módosítsa az alapértelmezett műveletet az az [Storage Account Update](/cli/azure/storage/account) *utasítással* . Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Hálózati hozzáférés engedélyezése virtuális hálózati alhálózatból

Engedélyezze a hálózati hozzáférést a Storage-fiókhoz a *magánhálózati* alhálózatról az [az Storage Account Network-Rule Add](/cli/azure/storage/account/network-rule)paranccsal.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Házirend alkalmazása az érvényes Storage-fiókhoz való hozzáférés engedélyezéséhez

Az Azure-szolgáltatás végponti házirendjei csak az Azure Storage számára érhetők el. Így a *Microsoft. Storage* ezen az alhálózaton található szolgáltatás-végpontját is engedélyezzük ebben a példában a telepítéshez.

A szolgáltatási végpontokra vonatkozó házirendek a szolgáltatási végpontokon vannak alkalmazva. Első lépésként hozzon létre egy szolgáltatási végponti szabályzatot. Ezután létrehozjuk az ebben a szabályzatban szereplő szabályzat-definíciókat az Azure Storage-fiókok számára az alhálózat jóváhagyásához

Szolgáltatás-végponti szabályzat létrehozása

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Mentse az erőforrás-URI-t az engedélyezett Storage-fiókhoz egy változóban. Az alábbi parancs végrehajtása előtt cserélje le az *\<your-subscription-id>* értéket az előfizetés-azonosító aktuális értékére.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Hozzon létre & adjon hozzá egy szabályzat-definíciót, amely engedélyezi a fenti Azure Storage-fiók számára a szolgáltatási végponti házirendet

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

És frissítse a virtuális hálózati alhálózatot, hogy társítsa azt az előző lépésben létrehozott szolgáltatási végponti szabályzattal.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Az Azure Storage-fiókokhoz való hozzáférés korlátozásának ellenőrzése

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Egy Storage-fiók hálózati hozzáférésének teszteléséhez helyezzen üzembe egy virtuális gépet az alhálózaton.

Hozzon létre egy virtuális gépet a *privát* alhálózaton az [az VM Create](/cli/azure/vm)paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

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

### <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

SSH-t a *myVmPrivate* virtuális gépre. Cserélje le a helyére *\<publicIpAddress>* a *myVmPrivate* virtuális gép nyilvános IP-címét.

```bash 
ssh <publicIpAddress>
```

Mappa létrehozása csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Csatlakoztassa az Azure-fájlmegosztást a létrehozott címtárhoz. Az alábbi parancs végrehajtása előtt cserélje le a *\<storage-account-key>* értéket a *AccountKey* értékre a következőtől: **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Ekkor megjelenik a `user@myVmPrivate:~$` kérdés. Az Azure-fájlmegosztás sikeresen csatlakoztatva lett a */mnt/MyAzureFileShare*-hez.

### <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

Ugyanabból a VM- *myVmPrivate* hozzon létre egy könyvtárat a csatlakoztatási ponthoz:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Próbálja meg csatlakoztatni az Azure-fájlmegosztást a Storage-fiók *notallowedstorageacc* a létrehozott könyvtárba. Ez a cikk azt feltételezi, hogy telepítette az Ubuntu legújabb verzióját. Ha az Ubuntu korábbi verzióit használja, tekintse [meg a Linux csatlakoztatása](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakört, amely további utasításokat tartalmaz a fájlmegosztás csatlakoztatásával kapcsolatban. 

Az alábbi parancs végrehajtása előtt cserélje le a *\<storage-account-key>* értéket a *AccountKey* értékre a következőtől: **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

A hozzáférés megtagadva, és hibaüzenet jelenik meg `mount error(13): Permission denied` , mivel ez a Storage-fiók nem szerepel az alhálózatra alkalmazott szolgáltatási végponti házirend engedélyezési listáján. 

Lépjen ki az SSH-munkamenetből a *myVmPublic* virtuális gépre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure) paranccsal távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy szolgáltatás-végponti házirendet alkalmazott egy Azure Virtual Network szolgáltatási végponton az Azure Storage-ba. Létrehozta az Azure Storage-fiókokat és a korlátozott hálózati hozzáférést csak bizonyos Storage-fiókokhoz (és így másokat is megtagadott) egy virtuális hálózat alhálózatáról. A szolgáltatás-végponti házirendekkel kapcsolatos további tudnivalókért lásd: [szolgáltatás-végponti házirendek áttekintése](virtual-network-service-endpoint-policies-overview.md).