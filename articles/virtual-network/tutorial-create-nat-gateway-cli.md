---
title: 'Oktatóanyag: NAT-átjáró létrehozása – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: 'Első lépések: NAT-átjáró létrehozása az Azure CLI használatával.'
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102663331"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure CLI használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy NAT-átjárót, és társítsa a virtuális hálózathoz.
> * Kapcsolódjon a virtuális géphez, és ellenőrizze a NAT IP-címét.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

Ebben a szakaszban a NAT-átjárót és a támogató erőforrásokat fogjuk létrehozni.

### <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása

Az Internet eléréséhez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. Az [az Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) paranccsal hozzon létre egy **myPublicIP** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Hozzon létre egy globális Azure NAT-átjárót az [az Network NAT Gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create)paranccsal. A parancs eredménye egy **myNATgateway** nevű átjáró-erőforrást hoz létre, amely a nyilvános IP- **myPublicIP** használja. Az Üresjárati időkorlát 10 percre van beállítva.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy **myVnet** nevű virtuális hálózatot mySubnet az  [Network vnet Create](/cli/azure/network/vnet#az_network_vnet_create) nevű alhálózattal a **myResourceGroup** erőforráscsoporthoz. A virtuális hálózat IP-címe **10.1.0.0/16**. A virtuális hálózaton belüli alhálózat **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Hozzon létre egy **myBastionHost** nevű Azure Bastion-gazdagépet a virtuális gép eléréséhez. 

Hozzon létre egy Azure-beli megerősített alhálózatot [az az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára az [az Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create)paranccsal. 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

A megerősített gazdagép létrehozásához használja az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT szolgáltatás konfigurálása a forrás-alhálózathoz

A virtuális hálózati **myVnet** lévő **mySubnet** a NAT-átjáró egy adott erőforrás- **myNATgateway** az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)paranccsal való használatára konfigurálja. Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Mostantól a NAT-átjáró használatával az internetre irányuló összes kimenő forgalom elérhető.  A UDR konfigurálása nem szükséges.


## <a name="virtual-machine"></a>Virtuális gép

Ebben a szakaszban létre fog hozni egy virtuális gépet a NAT-átjáró teszteléséhez a kimenő kapcsolatok nyilvános IP-címének ellenőrzéséhez.

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Várjon, amíg befejeződik a virtuális gép létrehozása, mielőtt továbblép a következő szakaszra.

## <a name="test-nat-gateway"></a>NAT-átjáró tesztelése

Ebben a szakaszban teszteljük a NAT-átjárót. Először a NAT-átjáró nyilvános IP-címét fogjuk felderíteni. Ezután csatlakozik a teszt virtuális géphez, és ellenőrizze a kimenő kapcsolatot a NAT-átjárón keresztül.
    
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. Keresse meg a NAT-átjáró nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myPublicIP** lehetőséget.

2. Jegyezze fel a nyilvános IP-címet:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="A NAT-átjáró nyilvános IP-címének felderítése" border="true":::

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM** , amely a **myResourceGroupNAT** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM**.

8. Írja be **https://whatsmyip.com** a címsorba.

9. Ellenőrizze, hogy a megjelenő IP-cím megegyezik-e az előző lépésben feljegyzett NAT-átjáró címével:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Külső kimenő IP-címet mutató Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések

Az Azure Virtual Network NAT-ról további információt a következő témakörben talál:
> [!div class="nextstepaction"]
> [Virtual Network NAT áttekintése](nat-overview.md)
