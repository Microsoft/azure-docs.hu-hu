---
title: Webes forgalom irányítása az URL-cím alapján – Azure CLI
description: Ebből a cikkből megtudhatja, hogyan irányíthatja át a webes forgalmat az URL-cím alapján az Azure CLI-vel megadott méretezhető kiszolgálókon.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8e8fed99fe0b1de52d2e2d0018dfd8867b54b63b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566520"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Webes forgalom irányítása az URL-cím alapján az Azure CLI használatával

A webes forgalmat felügyelő rendszergazdaként a feladatai közé tartozik annak a biztosítása, hogy az ügyfelek és a felhasználók minél gyorsabban érhessék el a szükséges információkat. Ennek az egyik módja, ha a különböző típusú webes forgalmakat különböző kiszolgáló-erőforrásokra irányítja. Ez a cikk bemutatja, hogyan állíthatja be és konfigurálhatja az Azure CLI-vel Application Gateway útválasztást az alkalmazás különböző típusú adatforgalmához. Az útválasztás ezután URL-cím alapján különböző kiszolgálókészletekre irányítja a forgalmat.

![URL-útválasztási példa](./media/tutorial-url-route-cli/scenario.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Erőforráscsoport létrehozása a szükséges hálózati erőforrások számára
* Hálózati erőforrások létrehozása
* Alkalmazásátjáró létrehozása az alkalmazásból érkező forgalom számára
* Kiszolgálókészletek és útválasztásai szabályok meghatározása a különböző típusú forgalmakhoz
* Méretezési csoport létrehozása az egyes készletekhez azok automatikus méretezésének megvalósításához
* Teszt futtatása annak ellenőrzéséhez, hogy a forgalom különféle típusai a megfelelő készletekre irányulnak-e

Ha szeretné, ezt az eljárást [Azure PowerShell](tutorial-url-route-powershell.md) vagy a [Azure Portal](create-url-route-portal.md)használatával végezheti el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az `az group create` paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az `az network vnet create` paranccsal. Ezután adja hozzá a háttérkiszolgálók által használt *myBackendSubnet* nevű alhálózatot az `az network vnet subnet create` paranccsal. Hozza létre a *myAGPublicIPAddress* nevű nyilvános IP-címet az `az network public-ip create` paranccsal.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>Az alkalmazásátjáró létrehozása URL-címleképezéssel

Az `az network application-gateway create` paranccsal hozzon létre egy alkalmazásátjárót *myAppGateway* néven. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az Application Gateway hozzá van rendelve a *myAGSubnet* és a *myAGPublicIPAddress*.

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

 Az alkalmazásátjáró létrehozása több percig is eltarthat. Az alkalmazásátjáró létrehozását követően a következő új funkciók láthatóak:


|Szolgáltatás  |Leírás  |
|---------|---------|
|appGatewayBackendPool     |Az alkalmazásátjáróknak rendelkezniük kell legalább egy háttércímkészlettel.|
|appGatewayBackendHttpSettings     |Megszabja, hogy a kommunikációhoz a rendszer egy HTTP-protokollt és a 80-as portot használja.|
|appGatewayHttpListener     |Az appGatewayBackendPool készlethez társított alapértelmezett figyelő.|
|appGatewayFrontendIP     |Hozzárendeli a myAGPublicIPAddress IP-címet az appGatewayHttpListener figyelőhöz.|
|rule1     |Az appGatewayHttpListener figyelőhöz rendelt alapértelmezett útválasztási szabály.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Kép- és videó-háttérkészletek, valamint egy port hozzáadása

Adja hozzá az alkalmazásátjáróhoz az *imagesBackendPool* és a *videoBackendPool* nevű háttérkészleteket az `az network application-gateway address-pool create` paranccsal. Adja hozzá az előtérportot a készletekhez az `az network application-gateway frontend-port create` paranccsal.

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
  --name port8080
```

### <a name="add-a-backend-listener"></a>Háttérfigyelő hozzáadása

Adja hozzá a forgalom irányításához szükséges *backendListener* nevű háttérfigyelőt az `az network application-gateway http-listener create` paranccsal.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>URL-útvonaltérkép hozzáadása

Az URL-útvonaltérképek biztosítják, hogy adott URL-címek adott háttérkészletekre legyenek irányítva. Hozza létre az *imagePathRule* és a *videoPathRule* nevű URL-útvonaltérképeket az `az network application-gateway url-path-map create` és az `az network application-gateway url-path-map rule create` paranccsal.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
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
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

Az útválasztási szabály az URL-címtérképeket a létrehozott figyelőhöz társítja. Adjon hozzá egy *rule2* nevű szabályt az `az network application-gateway rule create` paranccsal.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a cikkben három virtuálisgép-méretezési csoportot hoz létre, amelyek támogatják a létrehozott három háttér-készletet. A *myvmss1*, *myvmss2* és *myvmss3* nevű méretezési csoportokat hozza létre. Minden méretezési csoport két virtuálisgép-példányt tartalmaz, amelyeken az NGINX-et telepíti.

```azurecli-interactive
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
    --admin-username azureuser \
    --admin-password Azure123456! \
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

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az az network public-ip show parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például:, `http://40.121.222.19` , `http://40.121.222.19:8080/images/test.htm` , vagy `http://40.121.222.19:8080/video/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx.png)

Módosítsa az URL-címet http:// &lt; IP-cím &gt; : 8080/images/test.html értékre, és cserélje le az IP &lt; -cím IP-címét &gt; , és az alábbi példához hasonlóan kell megjelennie:

![Tesztképek URL-címe az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

Módosítsa az URL-címet a http:// &lt; IP-cím &gt; : 8080/video/test.html értékre, és cserélje le az IP &lt; -cím IP-címét &gt; , és az alábbi példához hasonlóan kell megjelennie.

![Tesztvideó URL-címe az alkalmazásátjáróban](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

[Alkalmazásátjáró létrehozása URL-alapú átirányítással](./tutorial-url-redirect-cli.md)
