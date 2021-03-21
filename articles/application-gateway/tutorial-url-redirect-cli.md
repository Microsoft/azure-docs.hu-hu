---
title: 'Oktatóanyag: URL-elérésiút-alapú átirányítás a parancssori felület használatával'
titleSuffix: Azure Application Gateway
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Application Gateway-t URL-alapú átirányított forgalommal az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: aa247d868e19831b3e6f1c3f68e605fb209a0664
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218315"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Oktatóanyag: Alkalmazásátjáró létrehozása URL-alapú átirányítással az Azure CLI használatával

Az Azure CLI használatával [URL-alapú útválasztási szabályokat](tutorial-url-route-cli.md) konfigurálhat [alkalmazásátjárók](./overview.md) létrehozásakor. Ebben az oktatóanyagban háttérkészleteket hoz létre [virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/overview.md) használatával. Ezután URL-útválasztási szabályokat hoz létre, amelyek biztosítják a webes forgalom átirányítását a megfelelő háttérkészletekre.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és útválasztási szabályok hozzáadása
> * Virtuálisgép-méretezési csoportok létrehozása a háttérkészletekhez

A következő példában a 8080-as és a 8081-es portról egyaránt érkezik webhelyforgalom, amely ugyanazokra a háttérkészletekre lesz átirányítva:

![URL-útválasztási példa](./media/tutorial-url-redirect-cli/scenario.png)

Igény szerint az oktatóanyag az [Azure PowerShell](tutorial-url-redirect-powershell.md) használatával is elvégezhető.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

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

A myAppGateway nevű Application Gateway létrehozásához használja az [az Network Application-Gateway Create](/cli/azure/network/application-gateway) paranccsal. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az Application Gateway hozzá van rendelve a korábban létrehozott *myAGSubnet* és *myPublicIPAddress* .

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

 Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozását követően a következő új funkciók láthatóak:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.


### <a name="add-backend-pools-and-ports"></a>Háttérkészletek és portok hozzáadása

Az *imagesBackendPool* és a *videoBackendPool* nevű háttércímkészleteket az [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool) paranccsal adhatja hozzá az alkalmazásátjáróhoz. Az előtérbeli portokat az [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) paranccsal adhatja hozzá a készletekhez. 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool

az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Figyelők és szabályok hozzáadása

### <a name="add-listeners"></a>Figyelők hozzáadása

Adja hozzá a forgalom irányításához szükséges *backendListener* és *redirectedListener* nevű háttérfigyelőket az [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener) paranccsal.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Alapértelmezett URL-útvonaltérkép hozzáadása

Az URL-cím elérési útja, hogy az adott URL-címek adott háttér-készletekbe legyenek irányítva. Létrehozhatja az *imagePathRule* és a *videoPathRule* nevű URL-útvonaltérképet az [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) és az [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule) paranccsal.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule

az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Átirányítási konfiguráció hozzáadása

A figyelő átirányítását az [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) paranccsal konfigurálhatja.

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Átirányítási URL-útvonaltérkép hozzáadása

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Útválasztási szabályok hozzáadása

Az útválasztási szabályok az URL-útvonaltérképeket a létrehozott figyelőkhöz társítják. A *defaultRule* és a *redirectedRule* nevű szabályt az [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) paranccsal adhatja hozzá.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a példában három virtuálisgép-méretezési csoportot hoz létre, amelyek támogatják a három létrehozott háttérkészletet. A *myvmss1*, *myvmss2* és *myvmss3* nevű méretezési csoportokat hozza létre. Minden méretezési csoport két virtuálisgép-példányt tartalmaz, amelyeken az NGINX-et telepíti.

Cserélje le a \<azure-user> és a \<password> nevet az Ön által választott felhasználónévre és jelszóra.

```azurecli
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username <azure-user> \
    --admin-password <password> \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>Az NGINX telepítése

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például:,,, `http://40.121.222.19` `http://40.121.222.19:8080/images/test.htm` `http://40.121.222.19:8080/video/test.htm` vagy `http://40.121.222.19:8081/images/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

Módosítsa az URL-címet http:// &lt; IP-cím &gt; : 8080/images/test.html értékre, és cserélje le az IP &lt; -cím IP-címét &gt; , és az alábbi példához hasonlóan kell megjelennie:

![Tesztképek URL-címe az alkalmazásátjáróban](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

Módosítsa az URL-címet a http:// &lt; IP-cím &gt; : 8080/video/test.html értékre, és cserélje le az IP &lt; -cím IP-címét &gt; , és az alábbi példához hasonlóan kell megjelennie:

![Tesztvideó URL-címe az alkalmazásátjáróban](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Most változtassa meg az URL-címet a http:// &lt; IP-cím &gt; : 8081/images/test.htm értékre, és cserélje le az IP &lt; -cím IP-címét &gt; , és a http:// &lt; IP-cím &gt; : 8080/images című témakörben tekintse át az átirányított forgalmat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg részletesebben az alkalmazásátjárók lehetőségeit](./overview.md)