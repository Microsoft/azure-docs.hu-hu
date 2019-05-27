---
title: Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre az Azure CLI-vel egy Azure Application Gatewayt, amellyel egy háttérkészlet virtuális gépeibe irányíthatja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a4f6cc2af7b9e044e5a72767898f876932fbf973
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133950"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Gyors útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure CLI-vel

Ez a rövid útmutató bemutatja, hogyan hozzon létre egy application gateway az Azure CLI használatával.  Az application gateway létrehozása után tesztelje, hogy ellenőrizze, hogy azok megfelelően működnek. Az Azure Application Gatewayjel az alkalmazás webes forgalom az erőforrásoknál figyelői hozzárendelése portokat, szabályok és hozzáadunk erőforrásokat egy háttérkészlet közvetlen. Az egyszerűség kedvéért a jelen cikk egy egyszerű beállítás nyilvános előtérbeli IP-cím, egy alapszintű figyelő gazdagéphez az application gateway-en egyetlen hely, a háttérkészlet és a egy egyszerű kérelem-útválasztási szabály használható két virtuális gépet használ.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-cli"></a>Azure CLI

Ha helyi telepítése és használata a parancssori Felületet, futtassa az Azure CLI 2.0.4-es vagy újabb. A verzió megkereséséhez futtassa **az--verzió**. Telepítésekor vagy verziófrissítésekor kapcsolatos információkért lásd: [Azure CLI telepítése]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Erőforráscsoport

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoportba foglalhat. Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create). 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Szükséges hálózati erőforrások 

Az Azure-hoz az erőforrások közötti kommunikációt, hogy hozzon létre egy virtuális hálózat szükséges.  Az application gateway alhálózatának csak az application Gateway-átjárókon is tartalmazhat. Egyéb erőforrások nem engedélyezettek.  Hozzon létre egy új alhálózatot az Application Gateway számára, vagy használjon egy meglévőt. Ebben a példában két alhálózat ebben a példában hoz létre: egyet az application gateway, a másik pedig a háttérkiszolgálókhoz. Az előtérbeli IP-címét az Application Gateway nyilvános vagy privát kell a használati eset megfelelően konfigurálhatja. Ebben a példában választjuk ki egy nyilvános előtérbeli IP-címet.

A virtuális hálózatot és alhálózatot létrehozni, használjon [az network vnet létrehozása](/cli/azure/network/vnet#az-network-vnet-create). Futtatás [az network public-ip létrehozása](/cli/azure/network/public-ip) nyilvános IP-cím létrehozásához.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

### <a name="backend-servers"></a>Háttérkiszolgálók

Háttérbeli hálózati adapterek, a virtual machine scale sets, nyilvános IP-címek állhat, belső IP-címek, teljesen minősített neve (FQDN), és több-bérlős háttéralkalmazások hasonlóan az Azure App Service-ben. Ebben a példában két virtuális gépet a háttérkiszolgálókhoz való használata az application gateway az Azure-hoz létre. Az IIS-et is telepíti, győződjön meg arról, hogy az Azure sikeresen létrejött-e az application gateway a virtuális gépeket.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Telepítse a [NGINX-webkiszolgálót](https://docs.nginx.com/nginx/) az alkalmazás ellenőrzéséhez a virtuális gépek átjáró sikeresen létrejött. A cloud-init konfigurációs fájl használatával telepíti az nginx-et és a egy "Hello World" Node.js-alkalmazás futtatása Linux rendszerű virtuális gépen. Cloud-Init használatával kapcsolatos további információkért lásd: [Cloud-init támogatása az Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Az Azure Cloud shellben, másolja és illessze be a következő konfigurációs fájlba *cloud-init.txt*. Adja meg *szerkesztő cloud-init.txt* a fájl létrehozásához.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Hozza létre a hálózati interfészeket az [az network nic create](/cli/azure/network/nic#az-network-nic-create) paranccsal. A virtuális gépek létrehozására használhatja [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Application Gateway létrehozása

Hozzon létre egy application gateway használatával [az network application-gateway létrehozása](/cli/azure/network/application-gateway). Egy application gateway az Azure CLI-vel való létrehozásakor megadhatja a konfigurációs adatokat, például a kapacitás, a Termékváltozat és a HTTP beállításait. Az Azure magánhálózati IP-címet a hálózati adapterek ezt követően hozzáadja az application Gateway háttérkészlet-kiszolgálóként.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Az application gateway létrehozása akár 30 percet is igénybe vehet. A létrehozást követően a következő beállításokat is megtekintheti a **beállítások** szakaszában a **az Application gateway** oldalon:

- **appGatewayBackendPool**: Található a **háttérkészletek** lapot. Azt adja meg a szükséges háttérkészlethez.
- **appGatewayBackendHttpSettings**: Található a **HTTP-beállítások** lapot. Meghatározza, hogy az application gateway 80-as portot és a HTTP protokollt használja a kommunikációhoz.
- **appGatewayHttpListener**: Található a **figyelői lap**. Azt adja meg a társított alapértelmezett figyelő **appGatewayBackendPool**.
- **appGatewayFrontendIP**: Található a **előtérbeli IP-konfigurációk** lapot. Rendeli hozzá a *myAGPublicIPAddress* való **appGatewayHttpListener**.
- **rule1**: Található a **szabályok** lapot. Azt adja meg az alapértelmezett útválasztási szabályt, amely társítva van **appGatewayHttpListener**.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az Azure nem szükséges egy NGINX-webkiszolgálót az application gateway létrehozása, telepítése, ebben a rövid, ellenőrizze, hogy az Azure sikeresen létrejött-e az application gateway. Az új application gateway nyilvános IP-címét használja [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.
    
![Az alkalmazásátjáró tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

Amikor frissíti a böngészőben, megtekintheti a második virtuális gép nevét. Érvényes válasz ellenőrzi, hogy az application gateway létrehozása sikeresen megtörtént, és képes sikeresen csatlakozott a háttérszolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az application gateway segítségével létrehozott erőforrásokat, használja a [az csoport törlése](/cli/azure/group#az-group-delete) paranccsal eltávolítható az erőforráscsoport. Az erőforráscsoport eltávolításával is eltávolítja az application gateway és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése alkalmazásátjáróval az Azure CLI használatával](./tutorial-manage-web-traffic-cli.md)

