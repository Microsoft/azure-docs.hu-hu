---
title: NAT-átjáró létrehozása – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d505c8f363e908a43c751c6253ed0f3a14b3b51
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671539"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>NAT-átjáró létrehozása az Azure CLI-vel

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.71 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internethez való hozzáféréshez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. Az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIP** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt a NAT-átjáróval. Ehhez a forgatókönyvhöz egy nyilvános IP-előtag-erőforrást adunk hozzá, amely bemutatja a következőt:. Az [az Network Public-IP előtag Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) paranccsal hozzon létre egy **myPublicIPprefix** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót az [az Network NAT Gateway Create](/cli/azure/network/nat) nevű **myNATgateway**. A parancs a nyilvános IP- **myPublicIP** és a nyilvános IP-előtag **myPublicIPprefix** is használja. A parancs az üresjárati időkorlátot **10** percre módosítja.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni a virtuális hálózatot.

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal a **myResourceGroupNAT** az [az Network vnet Create](/cli/azure/network/vnet)paranccsal.  A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT szolgáltatás konfigurálása a forrás-alhálózathoz

A virtuális hálózati **myVnet** lévő **mySubnet** a NAT-átjáró egy adott erőforrás- **myNATgateway** az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet)paranccsal való használatára konfigurálja.  Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Mostantól a NAT-átjáró használatával az internetre irányuló összes kimenő forgalom elérhető.  A UDR konfigurálása nem szükséges.

## <a name="create-a-vm-to-use-the-nat-service"></a>Virtuális gép létrehozása a NAT szolgáltatás használatához

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához.  Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését.  A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amelyet a virtuális gép eléréséhez kíván használni.  Az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>NSG létrehozása virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", létre kell hozni egy NSG, amely lehetővé teszi az SSH-hozzáférés bejövő hozzáférését. Az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) paranccsal hozzon létre egy **myNSG** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont közzététele a forrásoldali virtuális gépen

Hozzunk létre egy szabályt a NSG a forrás virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG-SZABÁLYT a **myNSG** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Hálózati adapter létrehozása virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP-címet és a hálózati biztonsági csoportot. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Virtuális gép létrehozása

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Várjon, amíg a virtuális gép üzembe helyezése megkezdődik, majd folytassa a többi lépéssel.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felderítése

Először fel kell deríteni a létrehozott virtuális gép IP-címét. A virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)paranccsal. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy hozzáférjen a virtuális géphez.

### <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépre

Az SSH hitelesítő adatait az előző művelet során a Cloud Shell kell tárolnia.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```bash
ssh <ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatára. 

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrásainak kimerítése további nyilvános IP-cím-erőforrások vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel foglalkozik.


- Tudnivalók az [Azure Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.
> [!div class="nextstepaction"]
