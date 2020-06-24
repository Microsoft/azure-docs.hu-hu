---
title: Útválasztási beállítások konfigurálása egy virtuális géphez – Azure CLI
description: Megtudhatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet az útválasztási beállítások megválasztásával az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: d5ea44e7059ae01204dbafd454c187e10f85e4e8
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84707548"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Útválasztási beállítások konfigurálása virtuális géphez az Azure CLI használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek útválasztási beállításait. A virtuális gépről érkező internetes forgalom az INTERNETSZOLGÁLTATÓ hálózatán keresztül lesz átirányítva, ha az **Internet** lehetőséget választja útválasztási preferenciaként. Az alapértelmezett útválasztás a Microsoft globális hálózatán keresztül történik.

Ez a cikk bemutatja, hogyan hozhat létre egy nyilvános IP-címmel rendelkező virtuális gépet, amely az Azure CLI használatával továbbítja a forgalmat a nyilvános interneten keresztül.

> [!IMPORTANT]
> Az útválasztási preferencia jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Az előfizetéshez tartozó funkció regisztrálása
Az útválasztási preferencia szolgáltatás jelenleg előzetes verzióban érhető el. Regisztrálja az előfizetéshez tartozó szolgáltatást a következőképpen:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
1. Ha a Cloud Shell használja, ugorjon a 2. lépésre. Nyisson meg egy parancssori munkamenetet, és jelentkezzen be az Azure-ba `az login` .
2. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiója Azure-régióban:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
A virtuális gépek internetről való eléréséhez létre kell hoznia egy nyilvános IP-címet. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. A következő példa létrehoz egy nyilvános IP-címet az útválasztási preferencia típusú *internetről* az *USA keleti* régiójában:

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

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A virtuális gép üzembe helyezése előtt létre kell hoznia a támogató hálózati erőforrásokat – a hálózati biztonsági csoportot, a virtuális hálózatot és a virtuális hálózati ADAPTERt.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a VNet bejövő és kimenő kommunikációját szabályozó szabályokhoz az [az Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) paranccsal. Az alábbi példa egy *myVNET* nevű virtuális hálózatot hoz létre az alhálózatok *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Hálózati adapter létrehozása

Hozzon létre egy virtuális hálózati adaptert a virtuális GÉPHEZ az [az Network NIC Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)paranccsal. A következő példa egy virtuális hálózati adaptert hoz létre, amelyet a virtuális GÉPHEZ fog csatolni.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) paranccsal. A következő példa egy Windows Server 2019 rendszerű virtuális GÉPET és a szükséges virtuális hálózati összetevőket hozza létre, ha azok még nem léteznek.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az-group-delete) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>További lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md).
- További információ az Azure [-beli nyilvános IP-címekről](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) .
- További információ a [nyilvános IP-címek beállításairól](virtual-network-public-ip-address.md#create-a-public-ip-address).
