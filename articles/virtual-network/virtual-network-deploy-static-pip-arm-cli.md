---
title: Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása – Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre statikus nyilvános IP-címmel rendelkező virtuális gépet az Azure parancssori felület (CLI) használatával.
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
ms.openlocfilehash: c4062dd086eeee712376a402da2792352fa3c3ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221342"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Statikus nyilvános IP-címmel rendelkező virtuális gép létrehozása az Azure CLI használatával

Létrehozhat egy statikus nyilvános IP-címmel rendelkező virtuális gépet is. A nyilvános IP-cím lehetővé teszi, hogy az internetről kommunikáljon egy virtuális géppel. Statikus nyilvános IP-címet rendeljen hozzá, nem pedig dinamikus címet, hogy a cím ne legyen módosítva. További információ a [statikus nyilvános IP-címekről](./public-ip-addresses.md#allocation-method). Ha egy meglévő virtuális géphez hozzárendelt nyilvános IP-címet szeretne módosítani dinamikusról statikusra, vagy magánhálózati IP-címekkel szeretne dolgozni, tekintse meg az [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md)című témakört. A nyilvános IP-címek [névleges díjszabással](https://azure.microsoft.com/pricing/details/ip-addresses)rendelkeznek, és az előfizetések által használható nyilvános IP-címek száma [korlátozva](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) van.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A következő lépéseket a helyi számítógépről vagy a Azure Cloud Shell használatával végezheti el. A helyi számítógép használatához győződjön meg arról, hogy az [Azure CLI telepítve](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)van. A Azure Cloud Shell használatához válassza a **kipróbálás** elemet az alábbi parancsok bármelyikének jobb felső sarkában. A Cloud Shell aláírja az Azure-t.

1. Ha a Cloud Shell használja, ugorjon a 2. lépésre. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba `az login` .
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiója Azure-régióban:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A `--public-ip-address-allocation=static` beállítás egy statikus nyilvános IP-címet rendel a virtuális géphez. Az alábbi példa egy *myPublicIpAddress* nevű, statikus, alapszintű SKU nyilvános IP-címmel rendelkező Ubuntu virtuális gépet hoz létre:

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

   Ha a nyilvános IP-címnek standard SKU-nak kell lennie, adja hozzá `--public-ip-sku Standard` az előző parancshoz. További információ a [nyilvános IP-cím SKU-](./public-ip-addresses.md#sku)ról. Ha a virtuális gépet egy nyilvános Azure Load Balancer háttér-készletéhez adja hozzá, akkor a virtuális gép nyilvános IP-címének SKU-jának meg kell egyeznie a terheléselosztó nyilvános IP-címének SKU-jának. Részletekért lásd: [Azure Load Balancer](../load-balancer/skus.md).

4. Tekintse meg a hozzárendelt nyilvános IP-címet, és győződjön meg róla, hogy statikus, alapszintű SKU-címként lett létrehozva az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Az Azure hozzárendelt egy nyilvános IP-címet a virtuális gépet a ben létrehozó régióban használt címekről. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.

> [!WARNING]
> Ne módosítsa az IP-cím beállításait a virtuális gép operációs rendszerén belül. Az operációs rendszer nem ismeri az Azure nyilvános IP-címeit. Bár a magánhálózati IP-címek beállításait az operációs rendszerhez is hozzáadhatja, azt javasoljuk, hogy csak akkor hajtsa végre ezt, ha szükséges, és nem, amíg az olvasó nem [ad hozzá privát IP-címet az operációs rendszerhez](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses)
- További információ az összes [nyilvános IP-cím beállításairól](virtual-network-public-ip-address.md#create-a-public-ip-address)
- További információ a [magánhálózati IP-címekről](./private-ip-addresses.md) és a [statikus magánhálózati IP-](virtual-network-network-interface-addresses.md#add-ip-addresses) címek egy Azure-beli virtuális géphez való hozzárendeléséről
- További információ a [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Windows rendszerű](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek létrehozásáról
