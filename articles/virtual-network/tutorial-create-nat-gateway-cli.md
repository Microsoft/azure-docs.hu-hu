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
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 60436b8d4a0f338f4ece59ad4cd11c14c9e4c352
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762640"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure CLI használatával

Ez az oktatóanyag bemutatja, hogyan használhatja Azure Virtual Network NAT szolgáltatást. Létre fog hozni egy NAT-átjárót, amely kimenő kapcsolatot biztosít egy Azure-beli virtuális gép számára. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy NAT-átjárót, és társítsa a virtuális hálózathoz.
> * Csatlakozzon a virtuális géphez, és ellenőrizze a NAT IP-címét.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot az **eastus2 helyen:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

Ebben a szakaszban létrehozunk egy NAT-átjárót és a támogató erőforrásokat.

### <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása

Az internet eléréséhez egy vagy több nyilvános IP-címre van szükség a NAT-átjáróhoz. Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy **myPublicIP** nevű nyilvános IP-címerőforrást a **myResourceGroupNAT erőforrásban.** 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

Hozzon létre egy globális Azure NAT-átjárót [az az network nat gateway create használatával.](/cli/azure/network/nat#az_network_nat_gateway_create) A parancs eredménye létrehoz egy **myNATgateway** nevű átjáróerőforrást, amely a **myPublicIP** nyilvános IP-címet használja. Az üresjárati időkorlát 10 percre van beállítva.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) nevű alhálózattal a **myResourceGroup** erőforráscsoportban. A virtuális hálózat IP-címterülete **a következő: 10.1.0.0/16.** A virtuális hálózaton belüli alhálózat a **következő: 10.1.0.0/24.**

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

Hozzon létre Azure Bastion **myBastionHost** nevű gazdagépet a virtuális gép eléréséhez. 

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy Azure Bastion alhálózatot.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára [az az network public-ip create segítségével.](/cli/azure/network/public-ip#az_network_public_ip_create) 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

A [megerősített gazdagép létrehozásához használja](/cli/azure/network/bastion#az_network_bastion_create) az az network bastion create használhatja. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-szolgáltatás konfigurálása a forrás alhálózathoz

A **myVnet** virtuális hálózat **mySubnet** forrás alhálózatát úgy konfiguráljuk, hogy a **myNATgateway** NAT-átjáró-erőforrást használja az [az network vnet subnet update frissítéssel.](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) Ez a parancs aktiválja a NAT-szolgáltatást a megadott alhálózaton.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Az internetes célhelyre irányuló összes kimenő forgalom mostantól a NAT-átjárót használja.  Nem szükséges UDR-t konfigurálni.


## <a name="virtual-machine"></a>Virtuális gép

Ebben a szakaszban egy virtuális gépet fog létrehozni a NAT-átjáró teszteléséhez a kimenő kapcsolat nyilvános IP-címének ellenőrzéséhez.

Hozza létre a virtuális gépet az [az vm create gombra.](/cli/azure/vm#az_vm_create)

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

Várja meg, amíg a virtuális gép létrehozása befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="test-nat-gateway"></a>NAT-átjáró tesztelése

Ebben a szakaszban a NAT-átjárót fogjuk tesztelni. Először a NAT-átjáró nyilvános IP-címét derítjük fel. Ezután csatlakozunk a teszt virtuális géphez, és ellenőrizzük a kimenő kapcsolatot a NAT-átjárón keresztül.
    
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. Keresse meg a NAT-átjáró nyilvános IP-címét az **Áttekintés képernyőn.** A **bal oldali menüben** válassza a Minden szolgáltatás, majd a Minden erőforrás **lehetőséget,** végül pedig a **myPublicIP elemet.**

2. Jegyezze fel a nyilvános IP-címet:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT-átjáró nyilvános IP-címének felderítése" border="true":::

3. Válassza **a Minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **Minden** erőforrás lehetőséget, majd az erőforrások listájából válassza ki a **myResourceGroupNAT** erőforráscsoportban található **myVM** elemet.

4. Az Áttekintés **lapon** válassza a **Csatlakozás,** majd a **Bastion lehetőséget.**

5. Kattintson a kék **Bastion használata gombra.**

6. Adja meg a virtuális gép létrehozása során megadott felhasználónevet és jelszót.

7. Nyissa **Internet Explorer** a **myTestVM-et.**

8. Írja **https://whatsmyip.com** be a címet a címsorba.

9. Ellenőrizze, hogy a megjelenített IP-cím megegyezik-e az előző lépésben feljegyzett NAT-átjáró címével:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer külső kimenő IP-cím megjelenítése" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Virtual Network NAT:
> [!div class="nextstepaction"]
> [Virtual Network NAT áttekintés](nat-overview.md)
