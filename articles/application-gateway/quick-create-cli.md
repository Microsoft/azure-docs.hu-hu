---
title: 'Gyors útmutató: webes forgalom közvetlen továbbítása a CLI használatával'
titleSuffix: Azure Application Gateway
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure CLI-t egy olyan Azure-Application Gateway létrehozásához, amely egy háttér-készletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 01/19/2021
ms.author: victorh
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 1a691d8ab212dd436b0dc9f7aafbc19a406b12b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98601779"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI

Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy Application Gateway-t. Ezt követően ellenőrizze, hogy megfelelően működik-e. 

Az Application Gateway az alkalmazás webes forgalmát egy háttér-készlet adott erőforrásaira irányítja. A figyelőket hozzárendelheti a portokhoz, szabályokat hozhat létre, és erőforrásokat adhat hozzá egy háttér-készlethez. Az egyszerűség kedvéért ez a cikk egy egyszerű telepítőt használ egy nyilvános előtéri IP-címmel, egy alapszintű figyelőt, amely egyetlen helyet üzemeltet az Application gatewayben, egy alapszintű kérelem-útválasztási szabályt és két virtuális gépet a háttér-készletben.

A rövid útmutató [Azure PowerShell](quick-create-powershell.md) vagy a [Azure Portal](quick-create-portal.md)használatával is elvégezhető.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Hozzon létre egy erőforráscsoportot a használatával `az group create` . 

A következő példában létrehozunk egy *myResourceGroupAG* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Hálózati erőforrások létrehozása 

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra.  Az Application Gateway-alhálózat csak Application Gateway átjárókat tartalmazhat. Más erőforrások nem engedélyezettek.  Létrehozhat egy új alhálózatot Application Gatewayhoz, vagy használhat egy meglévőt is. Ebben a példában két alhálózatot hoz létre: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára. A Application Gateway előtérbeli IP-címét a használati esetnek megfelelően lehet nyilvános vagy privátként beállítani. Ebben a példában egy nyilvános előtérbeli IP-címet választ.

A virtuális hálózat és az alhálózat létrehozásához használja a következőt: `az network vnet create` . Futtassa `az network public-ip create` a parancsot a nyilvános IP-cím létrehozásához.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>A háttér-kiszolgálók létrehozása

A háttérrendszer rendelkezhet hálózati adapterekkel, virtuálisgép-méretezési csoportokkal, nyilvános IP-címekkel, belső IP-címekkel, teljes tartománynévvel (FQDN) és több-bérlős háttérrel (például Azure App Service). Ebben a példában két virtuális gépet hoz létre, amelyeket háttér-kiszolgálóként használ az Application Gateway számára. Az IIS-t a virtuális gépeken is telepítheti az Application Gateway teszteléséhez.

#### <a name="create-two-virtual-machines"></a>Két virtuális gép létrehozása

Telepítse az NGINX webkiszolgálót a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e. Egy Cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy "Hello World" Node.js alkalmazást Linux rendszerű virtuális gépen. A Cloud-init szolgáltatással kapcsolatos további információkért lásd: [Cloud-init támogatás az Azure-beli virtuális gépekhez](../virtual-machines/linux/using-cloud-init.md).

A Azure Cloud Shell másolja és illessze be a következő konfigurációt egy *cloud-init.txt* nevű fájlba. Adja meg a *szerkesztő cloud-init.txt* a fájl létrehozásához.

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

Hozza létre a hálózati adaptereket `az network nic create` . A virtuális gépek létrehozásához használja a következőt: `az vm create` .

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

Hozzon létre egy Application Gateway-t a használatával `az network application-gateway create` . Amikor az Azure CLI-vel hoz létre egy Application Gateway-t, meg kell adnia a konfigurációs adatokat, például a kapacitást, az SKU-t és a HTTP-beállításokat. Az Azure Ezután hozzáadja a hálózati adapterek magánhálózati IP-címeit az Application Gateway háttérbeli készletében lévő kiszolgálóként.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Az Azure az Application Gateway létrehozásához akár 30 percet is igénybe vehet. A létrehozás után a következő beállításokat tekintheti meg az **Application Gateway** oldal **Beállítások** szakaszában:

- **appGatewayBackendPool**: a háttér- **készletek** lapon található. Meghatározza a szükséges háttérrendszer-készletet.
- **appGatewayBackendHttpSettings**: a http- **Beállítások** lapon található. Azt határozza meg, hogy az Application Gateway a 80-es portot és a HTTP protokollt használja a kommunikációhoz.
- **appGatewayHttpListener**: a **figyelők oldalon** található. Meghatározza a **appGatewayBackendPool** társított alapértelmezett figyelőt.
- **appGatewayFrontendIP**: az ELŐTÉRI **IP-konfigurációk** lapon található. A *myAGPublicIPAddress* a **appGatewayHttpListener**-hez rendeli hozzá.
- **rule1**: a **szabályok** lapon található. Meghatározza a **appGatewayHttpListener** társított alapértelmezett útválasztási szabályt.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Bár az Azure nem igényel NGINX-webkiszolgálót az Application Gateway létrehozásához, ezt a rövid útmutatóban telepítette annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. Az új Application Gateway nyilvános IP-címének lekéréséhez használja a következőt: `az network public-ip show` . 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Másolja és illessze be a nyilvános IP-címet a böngésző címsorába.
    
![Az alkalmazásátjáró tesztelése](./media/quick-create-cli/application-gateway-nginxtest.png)

A böngésző frissítésekor a második virtuális gép nevét kell megjelennie. Ez azt jelzi, hogy az Application Gateway sikeresen létrejött, és csatlakozhat a háttérrel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, az `az group delete` paranccsal törölheti az erőforráscsoportot. Az erőforráscsoport törlésekor az Application Gateway és az összes kapcsolódó erőforrás is törlődik.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

