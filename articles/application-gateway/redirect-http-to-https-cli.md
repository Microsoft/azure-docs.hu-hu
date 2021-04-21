---
title: HTTP–HTTPS átirányítás a CLI használatával
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre HTTP–HTTPS átirányítást, és hogyan adhat hozzá tanúsítványt a TLS-megszakításhoz az Azure CLI használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: e66eca305433a89496f72aac667512efd418a369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784766"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Alkalmazásátjáró létrehozása HTTP–HTTPS átirányítással az Azure CLI használatával

Az Azure CLI használatával létrehozhat egy [Application Gatewayt](overview.md) egy tanúsítvánnyal a TLS/SSL megszüntetéséhez. Az útválasztási szabályok a HTTP-forgalmat az alkalmazásátjáró HTTPS-portjára irányítják át. Ebben a példában egy [](../virtual-machine-scale-sets/overview.md) virtuálisgép-méretezési készletet is létrehoz az alkalmazásátjáró háttérkészlete számára, amely két virtuálisgép-példányt tartalmaz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Önaláírt tanúsítvány létrehozása
* Hálózat beállítása
* Alkalmazásátjáró létrehozása a tanúsítvánnyal
* Figyelési és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálni kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt és egy PFX-fájlt hoz létre az openssl paranccsal.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Adjon meg olyan értékeket, amelyek a tanúsítvány szempontjából jelentéssel bírnak. Elfogadhatja az alapértelmezett értékeket.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Adja meg a tanúsítványhoz tartozó jelszót. Ebben a példában az *Azure123456!* jelszót használjuk.

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
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Az [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. 

Az alkalmazásátjáró a korábban létrehozott *myAGSubnet* alhálózathoz és *myAGPublicIPAddress* IP-címhez lesz rendelve. Ebben a példában társítja a létrehozott tanúsítványt és annak jelszavát az alkalmazásátjáró létrehozásakor. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="add-a-listener-and-redirection-rule"></a>Figyelési és átirányítási szabály hozzáadása

### <a name="add-the-http-port"></a>A HTTP-port hozzáadása

Az [az network application-gateway frontend-port create használatával](/cli/azure/network/application-gateway/frontend-port#az_network-application_gateway_frontend_port_create) hozzáadhatja a HTTP-portot az alkalmazásátjáróhoz.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>A HTTP- figyelő hozzáadása

Az [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) használatával hozzáadhatja a *myListener* nevű listenert az alkalmazásátjáróhoz.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Az átirányítási konfiguráció hozzáadása

Adja hozzá a HTTP–HTTPS átirányítási konfigurációt az alkalmazásátjáróhoz [az az network application-gateway redirect-config create használatával.](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create)

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Az útválasztási szabály hozzáadása

Adja hozzá a *rule2 nevű* útválasztási szabályt az átirányítási konfigurációval az [alkalmazásátjáróhoz az az network application-gateway rule create használatával.](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create)

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy *myvmss* nevű virtuálisgép-méretezési készletet hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz és az *appGatewayBackendPool* készlethez vannak rendelve. A méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal hozhatja létre.

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

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használhatja az [az network public-ip show](/cli/azure/network/public-ip) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Biztonsági figyelmeztetés](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos NGINX-webhely a következő példához hasonlóan jelenik meg:

![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Következő lépések

- [Application Gateway létrehozása belső átirányítással az Azure CLI használatával](redirect-internal-site-cli.md)