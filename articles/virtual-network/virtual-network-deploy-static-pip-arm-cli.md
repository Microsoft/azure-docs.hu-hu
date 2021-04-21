---
title: Virtuális gép létrehozása statikus nyilvános IP-címmel – Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális gépet statikus nyilvános IP-címmel az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790130"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Statikus nyilvános IP-címmel és virtuális gép létrehozása az Azure CLI használatával

Statikus nyilvános IP-címmel is létrehozhat virtuális gépet. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Dinamikus cím helyett statikus nyilvános IP-cím hozzárendelésével biztosíthatja, hogy a cím soha ne változzon. További információ a statikus [nyilvános IP-címekről:](./public-ip-addresses.md#allocation-method). Ha módosítani szeretné egy meglévő virtuális géphez hozzárendelt nyilvános IP-címet dinamikusról statikusra, vagy magánhálózati IP-címekkel szeretne dolgozni, tekintse meg az IP-címek hozzáadását, módosítását vagy [eltávolítását.](virtual-network-network-interface-addresses.md) A nyilvános IP-címek névleges díjat [](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) [számolnak](https://azure.microsoft.com/pricing/details/ip-addresses)fel, és az előfizetésenként használható nyilvános IP-címek száma korlátozva van.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy a helyi Azure Cloud Shell. A helyi számítógép használatának biztosításához győződjön meg arról, hogy az [Azure CLI telepítve van.](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) A parancs Azure Cloud Shell válassza a **Kipróbálom** lehetőséget a következő parancsmezők jobb felső sarkában. A Cloud Shell bejelentkezik az Azure-ba.

1. Ha a Cloud Shell, ugorjon a 2. lépésre. Nyisson meg egy parancs-munkamenetet, és jelentkezzen be az Azure-ba a `az login` paranccsal.
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot az USA keleti Azure-régiójában:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A `--public-ip-address-allocation=static` beállítás statikus nyilvános IP-címet rendel a virtuális géphez. Az alábbi példa egy ubuntus virtuális gépet hoz létre egy statikus, alapszintű termékváltozatú, *myPublicIpAddress* nevű nyilvános IP-címmel:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Ha a nyilvános IP-címnek standard termékváltozatnak kell lennie, adja hozzá `--public-ip-sku Standard` az előző parancsot. További információ a [nyilvános IP-cím-SKUS-okkal kapcsolatban.](./public-ip-addresses.md#sku) Ha a virtuális gépet hozzáadja egy nyilvános Azure Load Balancer háttérkészlethez, a virtuális gép nyilvános IP-címének termékváltozatának meg kell egyeznie a terheléselosztás nyilvános IP-címének termékváltozatának. Részletekért lásd: [Azure Load Balancer.](../load-balancer/skus.md)

4. Tekintse meg a hozzárendelt nyilvános IP-címet, és győződjön meg arról, hogy statikus, alapszintű termékváltozat-címként lett létrehozva az [az network public-ip show címmel:](/cli/azure/network/public-ip#az_network_public_ip_show)

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Az Azure hozzárendelt egy nyilvános IP-címet abból a régióból, ahol a virtuális gépet létrehozta. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerében. Az operációs rendszer nem tud az Azure nyilvános IP-címről. Bár hozzáadhat magánhálózati IP-címbeállításokat az operációs rendszerhez, javasoljuk, hogy ezt csak szükség esetén, és csak a Magánhálózati IP-cím hozzáadása operációs rendszerhez elolvasása után adja [meg.](virtual-network-network-interface-addresses.md#private)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure-beli nyilvános](./public-ip-addresses.md#public-ip-addresses) IP-címekről
- További információ az összes [nyilvános IP-címbeállításról](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](./private-ip-addresses.md) és a statikus [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#add-ip-addresses) Azure-beli virtuális gépekhez való hozzárendelésével kapcsolatban
- További információ a [Linux és Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális [gépek](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) létrehozásáról
