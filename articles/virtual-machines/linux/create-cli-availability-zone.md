---
title: Zónában lévő virtuális gép létrehozása az Azure CLI-vel
description: Virtuális gép létrehozása rendelkezésre állási zónában az Azure CLI-vel
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 3405468c64a7130bb0d71543403dd4ce5ef9c241
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558372"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-cli"></a>Virtuális gép létrehozása rendelkezésre állási zónában az Azure CLI használatával

Ez a cikk a Linux rendszerű virtuális gépek Azure-beli rendelkezésre állási zónában történő létrehozásához szükséges lépéseket ismerteti az Azure CLI használatával. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

Rendelkezésre állási zóna használatához egy [támogatott Azure-régióban](../../availability-zones/az-region.md) hozza létre a virtuális gépet.

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és bejelentkezett egy Azure-fiókba az [az login](/cli/azure/reference-index)paranccsal.


## <a name="check-vm-sku-availability"></a>A VM-termékváltozatok rendelkezésre állásának ellenőrzése
A virtuális gépek méretének vagy termékváltozatainak rendelkezésre állása régiónként és zónánként eltérhet. Ha fel szeretne készülni a rendelkezésre állási zónák használatára, megtekintheti a virtuális gépek termékváltozatainak listáját Azure-régió és zóna szerint. Ezáltal megfelelő virtuálisgép-méretet választhat, valamint biztosíthatja a zónák közötti rugalmasság kívánt szintjét. További információ a virtuális gépek különböző típusairól és méreteiről: [Virtuálisgép-méretek – áttekintés](../sizes.md).

Az elérhető VM SKU-ket az az [VM List-SKUs](/cli/azure/vm) paranccsal tekintheti meg. Az alábbi példa az *eastus2* régióban található virtuális gépek elérhető termékváltozatait listázza:

```azurecli
az vm list-skus --location eastus2 --output table
```

A kimenet a következő sűrített példához hasonló, amelyben azok a rendelkezésre állási zónák láthatók, amelyekben minden virtuálisgép-méret elérhető:

```output
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.  

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. Ebben a példában egy *myResourceGroupVM* nevű erőforráscsoportot hoznak létre a *eastus2* régióban. Az USA 2. keleti régiója a rendelkezésre állási zónákat támogató Azure-régiók egyike.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Az erőforráscsoport a virtuális gép létrehozásakor vagy módosításakor van megadva, amely ebben a cikkben is látható.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

Egy virtuális gép létrehozásakor több lehetőség is rendelkezésre áll, például az operációsrendszer-lemezkép, a lemezméretezés vagy a rendszergazdai hitelesítő adatok. Ebben a példában egy *myVM* nevű virtuális gépet hozunk létre, amelyen az Ubuntu Server fut. A virtuális gép az *1*. rendelkezésre állási zónában jön létre. Alapértelmezés szerint a virtuális gép *Standard_DS1_v2* méretben jön létre.

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

A virtuális gép létrehozása eltarthat néhány percig. A virtuális gép létrehozása után az Azure CLI a virtuális géppel kapcsolatos adatokat jelenít meg. Jegyezze fel az `zones` értéket, amely arra a rendelkezésre állási zónára utal, amelyben a virtuális gép fut. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>A felügyelt lemez és az IP-cím zónájának megerősítése

Ha a virtuális gép rendelkezésre állási zónában van telepítve, a virtuális gép felügyelt lemeze ugyanabban a rendelkezésre állási zónában jön létre. Alapértelmezés szerint a rendszer a zónában is létrehoz egy nyilvános IP-címet. Az alábbi példák információt kapnak ezekről az erőforrásokról.

Annak ellenőrzéséhez, hogy a virtuális gép felügyelt lemeze szerepel-e a rendelkezésre állási zónában, használja az az [VM show](/cli/azure/vm) parancsot a lemez azonosítójának visszaküldéséhez. Ebben a példában a lemez AZONOSÍTÓját egy későbbi lépésben használt változó tárolja. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Most a felügyelt lemezről kaphat információkat:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```output
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

A *myVM*-ben adja vissza a nyilvános IP-cím erőforrás nevét az az [VM List-IP-Addresss](/cli/azure/vm) paranccsal. Ebben a példában a nevet egy későbbi lépésben használt változó tárolja.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Most lekérheti az IP-címmel kapcsolatos információkat:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

A kimenet azt mutatja, hogy az IP-cím ugyanabban a rendelkezésre állási zónában van, mint a virtuális gép:

```output
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. További információ az Azure-beli virtuális gépek [rendelkezésre állásáról](../availability.md) .