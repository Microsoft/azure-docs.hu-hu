---
title: A Container Group statikus IP-címe
description: Hozzon létre egy tároló csoportot egy virtuális hálózatban, és egy Azure Application Gateway használatával tegye elérhetővé egy statikus előtérbeli IP-címet egy tárolós webalkalmazáshoz
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 0131780fdb04a71837d5ae9bf5498bf2bd499f8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035053"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Statikus IP-cím közzététele egy tároló csoport számára

Ebből a cikkből megtudhatja, hogyan tehet elérhetővé egy statikus, nyilvános IP-címet egy [tároló-csoport](container-instances-container-groups.md) számára egy Azure [Application Gateway](../application-gateway/overview.md)használatával. Kövesse az alábbi lépéseket, ha statikus belépési pontra van szüksége egy olyan külső tárolós alkalmazáshoz, amely Azure Container Instances fut. 

Ebben a cikkben az Azure CLI használatával hozza létre a forgatókönyv erőforrásait:

* Azure-beli virtuális hálózat
* Egy kisméretű webalkalmazást üzemeltető [virtuális hálózaton](container-instances-vnet.md) üzembe helyezett Container Group
* Egy nyilvános előtérbeli IP-címmel rendelkező Application Gateway, egy webhelynek az átjárón való üzemeltetésére szolgáló figyelő, valamint a háttérbeli tároló csoport útvonala

Mindaddig, amíg az Application Gateway fut, és a tároló csoport egy stabil magánhálózati IP-címet tesz közzé a hálózat delegált alhálózatában, a tároló csoport elérhető ezen a nyilvános IP-címen.

> [!NOTE]
> Az Azure-díjak az átjáró üzembe helyezése és rendelkezésre állása, valamint az általa feldolgozott adatok mennyisége alapján az Application Gateway számára. Tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Egy tipikus esetben előfordulhat, hogy már rendelkezik Azure-beli virtuális hálózattal. Ha még nem rendelkezik ilyennel, hozzon létre egyet a következő példában látható parancsokkal. A virtuális hálózatnak külön alhálózatokra van szüksége az Application Gateway és a Container csoport számára.

Ha szüksége van erre, hozzon létre egy Azure-erőforráscsoportot. Például:

```azureci
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot az az [Network vnet Create][az-network-vnet-create] paranccsal. Ez a parancs létrehozza a *myAGSubnet* alhálózatot a hálózaton.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Az az [Network vnet subnet Create][az-network-vnet-subnet-create] paranccsal hozzon létre egy alhálózatot a háttér-tároló csoport számára. Itt a neve *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Egy statikus nyilvános IP-erőforrás létrehozásához használja az az [Network Public-IP Create][az-network-public-ip-create] parancsot. Egy későbbi lépésben ez a címe az Application Gateway elejeként van konfigurálva.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Tárolócsoport létrehozása

A következő az [Container Create][az-container-create] paranccsal hozzon létre egy tároló csoportot az előző lépésben konfigurált virtuális hálózaton. 

A csoport a *myACISubnet* alhálózaton van üzembe helyezve, és a *appcontainer* nevű egyetlen példányt tartalmaz, amely lekéri a `aci-helloworld` lemezképet. Ahogy az a dokumentáció más cikkeiben is látható, ez a rendszerkép egy olyan Node.js írt kisméretű webalkalmazást csomagol, amely egy statikus HTML-oldalt szolgál ki. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

A sikeres üzembe helyezést követően a tároló csoport egy magánhálózati IP-címet kap a virtuális hálózaton. Például futtassa a következőt az [Container show][az-container-show] paranccsal a csoport IP-címének lekéréséhez:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Az eredmény ehhez hasonlóan fog kinézni: `10.0.2.4`.

Egy későbbi lépésben való használathoz mentse az IP-címet egy környezeti változóban:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Ha a tároló csoport leáll, elindult vagy újraindult, a tároló csoport privát IP-címének módosítása változhat. Ha ez történik, frissítenie kell az Application Gateway konfigurációját.

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Hozzon létre egy Application Gateway-t a virtuális hálózaton az [Application Gateway](../application-gateway/quick-create-cli.md)rövid útmutatójának lépéseit követve. A következő az [Network Application-Gateway Create][az-network-application-gateway-create] paranccsal létrehoz egy átjárót egy nyilvános ELŐTÉRBELI IP-címmel és egy, a háttér-tároló csoportra mutató útvonallal. Az átjáró beállításaival kapcsolatos részletekért tekintse meg az [Application Gateway dokumentációját](../application-gateway/index.yml) .

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


Az Azure az Application Gateway létrehozásához akár 15 percet is igénybe vehet. 

## <a name="test-public-ip-address"></a>Nyilvános IP-cím tesztelése
  
Most tesztelheti a hozzáférést az Application Gateway mögött található Container csoportban futó webalkalmazáshoz.

Az átjáró nyilvános IP-címének lekéréséhez futtassa az az [Network Public-IP show][az-network-public-ip-show] parancsot:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

A kimenet egy nyilvános IP-cím, amely a következőhöz hasonló: `52.142.18.133` .

Ha a sikeres konfigurálás után szeretné megtekinteni a futó webalkalmazást, navigáljon az átjáró nyilvános IP-címére a böngészőben. A sikeres hozzáférés a következőhöz hasonló:

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Következő lépések

* Megtekintheti a gyors üzembe helyezési [sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) , amely egy, a WordPress Container-példánnyal rendelkező tároló csoportot hoz létre egy Application Gateway mögötti háttér-kiszolgálóként.
* Az alkalmazás-átjárót a tanúsítvány használatával is konfigurálhatja az SSL-lezáráshoz. Tekintse meg az [áttekintést](../application-gateway/ssl-overview.md) és az [oktatóanyagot](../application-gateway/create-ssl-portal.md).
* A forgatókönyvtől függően érdemes lehet más Azure-terheléselosztási megoldásokat használni a Azure Container Instances. Például az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) használatával terjesztheti a forgalmat több tároló példányban és több régióban is. Tekintse meg ezt a [blogbejegyzést](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
