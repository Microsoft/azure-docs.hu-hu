---
title: Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer – Azure CLI
description: Megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer az Azure CLI használatával.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518209"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer az Azure CLI használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek 

- A virtuálisgép-méretezési csoport üzembe helyezéséhez az előfizetésben egy meglévő standard SKU Load Balancer szükséges.

- A virtuálisgép-méretezési csoporthoz Azure-Virtual Network szükséges.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési csoport üzembe helyezése meglévő Load balancerrel

Cserélje le a zárójelben lévő értékeket a konfigurációban található erőforrások nevére.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Az alábbi példa egy virtuálisgép-méretezési csoport üzembe helyezését mutatja be:

- **MyVMSS** nevű virtuálisgép-méretezési csoport
- Azure Load Balancer nevű **myLoadBalancer**
- **MyBackendPool** nevű terheléselosztó-háttérbeli készlet
- **MyVnet** nevű Azure Virtual Network
- **MySubnet** nevű alhálózat
- **MyResourceGroup** nevű erőforráscsoport
- A virtuálisgép-méretezési csoporthoz tartozó Ubuntu Server-rendszerkép

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> A méretezési csoport létrehozása után a háttér-port nem módosítható a terheléselosztó állapot-mintavételi eljárása által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapot-mintavételt az Azure virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapotot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuálisgép-méretezési csoport üzembe helyezését egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési csoportokról és a Load balancerről további információt a következő témakörben talál:

- [Mi az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
                                