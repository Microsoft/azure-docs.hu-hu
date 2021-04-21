---
title: Útválasztási beállítás konfigurálása nyilvános IP-címhez az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozhat létre nyilvános IP-címet internetes forgalom-útválasztási beállításokkal az Azure CLI használatával.
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
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776554"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Útválasztási beállítás konfigurálása nyilvános IP-címhez az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja az útválasztási beállításokat internetszolgáltatói hálózaton **keresztül** (internetes beállítás) egy nyilvános IP-címhez az Azure CLI használatával. Miután létrehozta a nyilvános IP-címet, a következő Azure-erőforrásokkal társíthatja az internetre irányuló bejövő és kimenő forgalomhoz:

* Virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal elérhető terheléseltöltő
* Application Gateway
* Azure Firewall

Alapértelmezés szerint a nyilvános IP-címek útválasztási beállítása a Microsoft globális hálózata az összes Azure-szolgáltatáshoz, és bármely Azure-szolgáltatáshoz társítható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot az **USA keleti Azure-régiójában:**

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Hozzon létre egy nyilvános  IP-címet internettípusú útválasztási beállítással az [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create)paranccsal, az alább látható formátumban.

A következő parancs egy új nyilvános IP-címet hoz **létre** internetes útválasztási beállítással az USA keleti **Azure-régiójában.**

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
>  Az útválasztási beállítás jelenleg csak az IPV4 nyilvános IP-címeket támogatja.

A fentiekben létrehozott nyilvános IP-címet társíthatja [egy Windows vagy](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Linux rendszerű [virtuális](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) géphez. Használja az oktatóanyag oldalán található CLI-szakaszt: [Nyilvános IP-cím](associate-public-ip-address-vm.md#azure-cli) társítása egy virtuális géphez a nyilvános IP-cím a virtuális géphez való társítására. A fentiekben létrehozott nyilvános IP-címet [](../load-balancer/load-balancer-overview.md)társíthatja egy Azure Load Balancer-val is, ha hozzárendeli azt a terheléselosztási **előtere konfigurációhoz.** A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd.

## <a name="next-steps"></a>Következő lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben.](routing-preference-overview.md) 
- [Konfigurálja egy virtuális gép útválasztási beállítását az Azure CLI használatával.](configure-routing-preference-virtual-machine-cli.md)
