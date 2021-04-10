---
title: A nyilvános IP-cím útválasztási beállításainak konfigurálása az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre egy nyilvános IP-címet egy internetes forgalom útválasztási beállításával az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 2291767c162953f8339fb8cc27e55b96290ef795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101665962"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>A nyilvános IP-cím útválasztási beállításainak konfigurálása az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja az útválasztási beállításokat a nyilvános IP-címekre vonatkozó ISP-hálózaton (**Internet** -beállítás) az Azure CLI használatával. A nyilvános IP-cím létrehozása után a következő Azure-erőforrásokkal társíthatja a bejövő és kimenő adatforgalmat az internethez:

* Virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal rendelkező Load Balancer
* Application Gateway
* Azure Firewall

Alapértelmezés szerint a nyilvános IP-cím útválasztási beállítása az összes Azure-szolgáltatáshoz a Microsoft globális hálózatra van állítva, és bármely Azure-szolgáltatáshoz társítható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az **USA keleti** régiója Azure-régióban:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy nyilvános IP-címet az **internetes** típus útválasztási beállításával az az [Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)paranccsal, az alább látható formátumban.

A következő parancs egy új nyilvános IP-címet hoz létre az **Internet** Routing PREFERENCIA az **USA keleti** régiója Azure-régióban.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Az útválasztási beállítások jelenleg csak IPV4 nyilvános IP-címeket támogatnak.

A fenti létrehozott nyilvános IP-címet egy Windows vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális géppel társíthatja. A CLI szakasz az oktatóanyag oldalon: [nyilvános IP-cím hozzárendelése egy virtuális géphez](associate-public-ip-address-vm.md#azure-cli) , hogy a nyilvános IP-címet a virtuális géphez rendelje. A fent létrehozott nyilvános IP-címet társíthatja egy [Azure Load Balancer](../load-balancer/load-balancer-overview.md)is, ha hozzárendeli a terheléselosztó előtér **-konfigurációjához.** A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd.

## <a name="next-steps"></a>Következő lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md). 
- [Konfigurálja a virtuális gép útválasztási beállításait az Azure CLI használatával](configure-routing-preference-virtual-machine-cli.md).

