---
title: Tárolócsoport statikus IP-címe
description: Tárolócsoport létrehozása egy virtuális hálózatban, és Azure Application Gateway használata statikus előtér-IP-cím elérhetővé teszi egy tárolóba helyezni a webalkalmazást
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: de9e06b457a9ea5485fe268bd2b7cf206f0a6c0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790940"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Statikus IP-cím elérhetővé téve egy tárolócsoport számára

Ez a cikk egy módját mutatja be a [](container-instances-container-groups.md) tárolócsoport statikus, nyilvános IP-címeinek egy Azure [Application Gateway használatával való elérhetővé tevésére.](../application-gateway/overview.md) Kövesse ezeket a lépéseket, ha statikus belépési pontra van szüksége egy külső tárolóban futó alkalmazáshoz, amely a Azure Container Instances. 

Ebben a cikkben az Azure CLI használatával hozza létre az ehhez a forgatókönyvhöz szükséges erőforrásokat:

* Azure-beli virtuális hálózat
* Egy, a virtuális hálózaton [üzembe helyezett tárolócsoport,](container-instances-vnet.md) amely egy kis méretű webalkalmazást helyez üzembe
* Egy nyilvános előtere IP-címmel, egy listenerrel, amely egy webhelyet az átjárón, valamint egy útvonalat a háttértároló-csoporthoz

Amíg az Application Gateway fut, és a tárolócsoport stabil magánhálózati IP-címet biztosít a hálózat delegált alhálózatán, a tárolócsoport ezen a nyilvános IP-címen érhető el.

> [!NOTE]
> Az Azure az átjáró építési és rendelkezésre álló ideje, valamint a feldolgozott adatok mennyisége alapján díjat kell fizetni az Alkalmazásátjáróért. Lásd [a díjszabást.](https://azure.microsoft.com/pricing/details/application-gateway/)

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Általában előfordulhat, hogy már rendelkezik Azure-beli virtuális hálózattal. Ha még nem tudja, hozzon létre egyet az alábbi példaparancsok szerint. A virtuális hálózatnak külön alhálózatra van szüksége az Application Gatewayhez és a tárolócsoporthoz.

Ha szüksége van rá, hozzon létre egy Azure-erőforráscsoportot. Például:

```azureci
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot [az az network vnet create paranccsal.][az-network-vnet-create] Ez a parancs létrehozza a *myAGSubnet* alhálózatot a hálózaton.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Az [az network vnet subnet create paranccsal][az-network-vnet-subnet-create] hozzon létre egy alhálózatot a háttértároló-csoporthoz. Itt a neve *myACISubnet.*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Az [az network public-ip create paranccsal][az-network-public-ip-create] hozzon létre egy statikus nyilvános IP-erőforrást. Egy későbbi lépésben ez a cím lesz konfigurálva az Application Gateway előtereként.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tárolócsoport létrehozása

A következő [az container create futtatásával][az-container-create] hozzon létre egy tárolócsoportot az előző lépésben konfigurált virtuális hálózatban. 

A csoport a *myACISubnet* alhálózatban van üzembe helyezni, és egyetlen *appcontainer* nevű példányt tartalmaz, amely leküldi a `aci-helloworld` rendszerképet. Ahogy a dokumentáció más cikkei is mutatják, ez a kép egy statikus HTML-oldalt Node.js webalkalmazást tartalmaz. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Sikeres üzembe helyezés esetén a tárolócsoporthoz magánhálózati IP-cím lesz rendelve a virtuális hálózaton. Futtassa például a következő [az container show parancsot][az-container-show] a csoport IP-címének lekérésére:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Az eredmény ehhez hasonlóan fog kinézni: `10.0.2.4`.

Egy későbbi lépésben való használatra mentse az IP-címet egy környezeti változóba:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Ha a tárolócsoport le van állítva, elindult vagy újraindult, a tárolócsoport magánhálózati IP-címe változhat. Ha ez történik, frissítenie kell az Application Gateway konfigurációját.

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy Application Gatewayt a virtuális hálózaton az Application Gateway rövid [útmutató lépéseit követve.](../application-gateway/quick-create-cli.md) A következő [az network application-gateway create parancs][az-network-application-gateway-create] létrehoz egy nyilvános előtere IP-címmel és a háttértárolócsoporthoz tartó útvonalat tartalmazó átjárót. Az átjáró [Application Gateway az](../application-gateway/index.yml) átjáró beállításaival kapcsolatos részletekért tekintse meg az átjárók dokumentációját.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Az Alkalmazásátjáró létrehozása az Azure-ban akár 15 percet is igénybe vehet. 

## <a name="test-public-ip-address"></a>Nyilvános IP-cím tesztelése
  
Most tesztelheti az Application Gateway mögötti tárolócsoportban futó webalkalmazáshoz való hozzáférést.

Futtassa [az az network public-ip show parancsot][az-network-public-ip-show] az átjáró előtere nyilvános IP-címének lekérésére:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A kimenet a következő nyilvános IP-címhez hasonló: `52.142.18.133` .

A sikeresen konfigurált futó webalkalmazás megtekintéséhez nyissa meg az átjáró nyilvános IP-címét a böngészőben. A sikeres hozzáférés a következő hasonló:

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Következő lépések

* Tekintse meg a [gyorsindítási sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) egy olyan tárolócsoport létrehozásához, amely egy WordPress-tárolópéldányt tartalmaz háttérkiszolgálóként egy Application Gateway mögött.
* Az Application Gatewayt is konfigurálhatja tanúsítvánnyal az SSL-megszakításhoz. Tekintse meg [az áttekintést](../application-gateway/ssl-overview.md) és az [oktatóanyagot.](../application-gateway/create-ssl-portal.md)
* A forgatókönyvtől függően fontolja meg más Azure-beli terheléselosztási megoldások Azure Container Instances. Az adatforgalom például [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) tárolópéldányok és régiók közötti elosztására használható. Lásd ezt a [blogbejegyzést:](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
