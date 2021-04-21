---
title: Webes forgalom kezelése – Azure CLI
description: Megismerheti, hogyan hozhat létre egy alkalmazásátjárót egy virtuálisgép-méretezési csoporttal a webes forgalom kezelésére az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/20/2019
ms.author: victorh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01df6a12437808aa903046d2923c735ded5067ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772778"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével

Az alkalmazásátjáró kezeli az Ön által fenntartott kiszolgálókra irányuló webes forgalmat, és védelmet biztosít ahhoz. Az Azure CLI használatával létrehozhat egy [alkalmazásátjárót,](overview.md) amely virtuálisgép-méretezési csoportokat [használ](../virtual-machine-scale-sets/overview.md) háttérkiszolgálókhoz. Ebben a példában a méretezési készlet két virtuálisgép-példányt tartalmaz. A méretezési készlet hozzá lesz adva az Application Gateway alapértelmezett háttérkészlethez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózat beállítása
* Application Gateway létrehozása
* Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha szeretné, ezt az eljárást a következővel is [Azure PowerShell.](tutorial-manage-web-traffic-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ezután hozzáadhatja a háttérkiszolgálók számára szükséges *myBackendSubnet* nevű alhálózatot az [az network vnet subnet create](/cli/azure/network/vnet/subnet) paranccsal. Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az [az network application-gateway create](/cli/azure/network/application-gateway) paranccsal hozza létre a *myAppGateway* alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a *korábban létrehozott myAGSubnet* és *myPublicIPAddress* ip-címhez van rendelve. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az Application Gateway létrehozása után a következő új funkciók fognak látni:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz és az *appGatewayBackendPool* készlethez vannak rendelve. A méretezési csoport létrehozásához használja az [az vmss create](/cli/azure/vmss#az_vmss_create) parancsot.

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Az NGINX telepítése

Most telepítheti az NGINX-et a virtuálisgép-méretezési csoportra, hogy tesztelhesse a háttérkészlet HTTP-kapcsolatát.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Következő lépések

[Webes forgalom korlátozása webalkalmazási tűzfallal](../web-application-firewall/ag/tutorial-restrict-web-traffic-cli.md)