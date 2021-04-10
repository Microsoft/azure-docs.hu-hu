---
title: Webalkalmazási tűzfal engedélyezése – Azure CLI
description: Megtudhatja, hogyan korlátozhatja a webes forgalmat egy Application Gateway webalkalmazási tűzfallal az Azure CLI használatával.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/29/2021
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: d53f4b640154e4d7b02115d5043b37f6bb6e89ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731139"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Webalkalmazási tűzfal engedélyezése az Azure CLI használatával

Egy [webalkalmazási tűzfallal](ag-overview.md) (WAF) korlátozhatja az Application Gateway forgalmát. A WAF [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-szabályokkal védi az alkalmazást. Ezek a szabályok olyan támadások ellen nyújtanak védelmet, mint az SQL-injektálás, a Cross-Site Scripting támadások és a munkamenet-eltérítések.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

 * A hálózat beállítása
 * Alkalmazásátjáró létrehozása engedélyezett WAF-fel
 * Virtuálisgép-méretezési csoport létrehozása
 * Tárfiók létrehozása és diagnosztika konfigurálása

![Webalkalmazási tűzfal – példa](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Ha szeretné, az eljárást [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md)használatával végezheti el.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozzon létre egy *myResourceGroupAG* nevű Azure-erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása

A virtuális hálózat és annak alhálózatai biztosítják az alkalmazásátjáró és az ahhoz tartozó erőforrások hálózati kapcsolatát. Hozzon létre egy *myVNet* nevű virtuális hálózatot és egy *myAGSubnet* nevű alhálózatot. Ezután hozzon létre egy *myAGPublicIPAddress* nevű nyilvános IP-címet.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>Alkalmazásátjáró létrehozása webalkalmazási tűzfallal

Az [az network application-gateway create](/cli/azure/network/application-gateway) paranccsal létrehozhatja a *myAppGateway* nevű alkalmazásátjárót. Amikor létrehoz egy alkalmazásátjárót az Azure CLI használatával, olyan konfigurációs információkat kell megadnia, mint a kapacitás, a termékváltozat és a HTTP-beállítások. Az Application Gateway hozzá van rendelve a *myAGSubnet* és a *myAGPublicIPAddress*.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Az alkalmazásátjáró létrehozása néhány percig is eltarthat. Az alkalmazásátjáró létrehozása után a következő új funkcióit láthatja:

- *appGatewayBackendPool* – Az Application Gatewayeknek legalább egy háttércímkészlettel kell rendelkezniük.
- *appGatewayBackendHttpSettings* – Meghatározza, hogy a kommunikációhoz a rendszer a 80-as portot és egy HTTP-protokollt használ.
- *appGatewayHttpListener* – Az *appGatewayBackendPool* készlethez társított alapértelmezett figyelő.
- *appGatewayFrontendIP* – Hozzárendeli a *myAGPublicIPAddress* IP-címet az *appGatewayHttpListener* figyelőhöz.
- *rule1* – Az *appGatewayHttpListener* elemmel társított alapértelmezett útválasztási szabály.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely két kiszolgálót biztosít a háttérkészlet számára az alkalmazásátjáróban. A méretezési csoportban lévő virtuális gépek a *myBackendSubnet* alhálózathoz vannak rendelve. A méretezési csoportot az [az vmss create](/cli/azure/vmss#az-vmss-create) paranccsal hozhatja létre.

A \<username> \<password> futtatása előtt cserélje le a és az értékeket az értékekre.

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username <username> \
  --admin-password <password> \
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
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Tárfiók létrehozása és diagnosztika konfigurálása

Ebben a cikkben az Application Gateway egy Storage-fiók használatával tárolja az adatgyűjtési és-megelőzési célokat. Az adatok rögzítéséhez Azure Monitor naplókat vagy Event hub-t is használhat. 

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy *myagstore1* nevű tárfiókot az [az storage account create](/cli/azure/storage/account#az-storage-account-create) paranccsal.

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>Diagnosztika konfigurálása

Konfigurálja a diagnosztikát az adatok az ApplicationGatewayAccessLog, az ApplicationGatewayPerformanceLog és az ApplicationGatewayFirewallLog naplóba rögzítéséhez. Cserélje le `<subscriptionId>` az értékét az előfizetés-azonosítóra, majd konfigurálja a diagnosztikát az [az monitor diagnosztikai-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create)paranccsal.

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazásátjáró nyilvános IP-címének lekéréséhez használja az [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) parancsot. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Az alap URL-cím tesztelése az alkalmazásátjáróban](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Következő lépések

[Webalkalmazási tűzfalszabályok testreszabása](application-gateway-customize-waf-rules-portal.md)
