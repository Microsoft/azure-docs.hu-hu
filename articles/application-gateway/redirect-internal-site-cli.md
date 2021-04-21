---
title: Belső átirányítás a CLI használatával
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre olyan alkalmazásátjárót, amely a belső webes forgalmat a megfelelő készletre irányítja át az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: c4aa8927422d1f95a642e1d7c383aebc03a4930e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775780"
---
# <a name="create-an-application-gateway-with-internal-redirection-using-the-azure-cli"></a>Application Gateway létrehozása belső átirányítással az Azure CLI használatával

Az Azure CLI használatával konfigurálhatja a [webes forgalom átirányítását](multiple-site-overview.md) egy [Alkalmazásátjáró létrehozásakor.](overview.md) Ebben az oktatóanyagban egy háttérkészletet fog definiálni egy virtuálisgép-méretezési készlet használatával. Ezután a saját tartományai alapján konfigurálhatja a figyelőket és szabályokat, hogy a webes forgalom a megfelelő készlethez érkezzen. Ez az oktatóanyag feltételezi, hogy Ön több tartományt is használ, és példákat használ a *www \. contoso.com* és *a www \. contoso.org.*

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* A hálózat beállítása
* Application Gateway létrehozása
* Figyelők és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása a háttérkészletből
* CNAME rekord létrehozása a tartományban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Hozza létre a *myVNet* nevű virtuális hálózatot és a *myAGSubnet* nevű alhálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Ezután hozzáadhatja a kiszolgálók háttérkészletéhez szükséges *myBackendSubnet* nevű alhálózatot [az az network vnet subnet create használatával.](/cli/azure/network/vnet/subnet) Hozza létre a *myAGPublicIPAddress* elnevezésű nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) paranccsal.

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
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az [az network application-gateway create](/cli/azure/network/application-gateway) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.


## <a name="add-listeners-and-rules"></a>Figyelők és szabályok hozzáadása 

A figyelő ahhoz szükséges, hogy az alkalmazásátjáró megfelelően irányíthassa a forgalmat a háttérkészlethez. Ebben az oktatóanyagban két figyelőt hoz létre a két tartományhoz. Ebben a példában a figyelők a www és a *www \.* contoso.com *tartományhoz \.* contoso.org.

Adja hozzá a forgalom irányításához szükséges háttérfigyelőket az [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) paranccsal.

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoComListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com
az network application-gateway http-listener create \
  --name contosoOrgListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.org   
  ```

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá az alkalmazásátjárón az [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create)használatával az átirányítási konfigurációt, amely a www *\. consoto.org-ről* küld forgalmat a *www \. contoso.com* listenerjének.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name orgToCom \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener contosoListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-routing-rules"></a>Útválasztási szabályok hozzáadása

A szabályok a létrehozásuk sorrendjében vannak feldolgozva, és a forgalom az alkalmazásátjárónak küldött URL-címnek megfelelő első szabály használatával lesz irányítva. Ha például egy adott porton egy alapszintű figyelőt használó és egy többhelyes figyelőt használó szabály is aktív, a többhelyes figyelővel rendelkező szabályt az alapszintű figyelővel rendelkező elé kell venni, hogy a többhelyes szabály a várakozásnak megfelelően működjön. 

Ebben a példában két új szabályt hoz létre, és törli a létrehozott alapértelmezett szabályt.  A szabályt az [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create) paranccsal adhatja hozzá.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoComRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoComListener \
  --rule-type Basic \
  --address-pool appGatewayBackendPool
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoOrgRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoOrgListener \
  --rule-type Basic \
  --redirect-config orgToCom
az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása

Ebben a példában létrehoz egy virtuálisgép-méretezési csoport, amely támogatja a létrehozott háttérkészletet. A létrehozott méretezési csoport neve *myvmss,* és két virtuálisgép-példányt tartalmaz, amelyekre az NGINX-et telepíti.

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

Futtassa ezt a parancsot a rendszerhéjablakban:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-cname-record-in-your-domain"></a>CNAME rekord létrehozása a tartományban

Az alkalmazásátjáró nyilvános IP-címmel történő létrehozása után lekérheti a DNS-címet, és a segítségével létrehozhat egy CNAME rekordot a tartományban. Az alkalmazásátjáró DNS-címét az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) paranccsal kérheti le. Másolja a DNSSettings *fqdn* értékét, és használja a létrehozandó CNAME rekord értékeként. Az A rekordok használata nem javasolt, mivel a virtuális IP-cím változhat az alkalmazásátjáró újraindításakor.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Adja meg a tartománya nevét a böngésző címsorában. Például: http: \/ /www.contoso.com.

![Contoso webhely tesztelése az alkalmazásátjáróban](./media/redirect-internal-site-cli/application-gateway-nginxtest.png)

Módosítsa a címet a másik tartományra, például http: /www.contoso.org, és látnia kell, hogy a forgalom vissza lett irányítva a \/ www \. contoso.com.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> * A hálózat beállítása
> * Application Gateway létrehozása
> * Figyelők és átirányítási szabály hozzáadása
> * Virtuálisgép-méretezési csoport létrehozása a háttérkészletből
> * CNAME rekord létrehozása a tartományban
