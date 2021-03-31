---
title: Statikus kimenő IP-cím konfigurálása
description: Az Azure tűzfal és a felhasználó által megadott útvonalak konfigurálása olyan Azure Container Instances munkaterhelésekhez, amelyek a tűzfal nyilvános IP-címét használják a bejövő és kimenő forgalomhoz
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89566568"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Egyetlen nyilvános IP-cím konfigurálása a kimenő és a bejövő forgalom számára egy tároló csoportba

Egy külső elérésű IP-címmel rendelkező [tároló csoport](container-instances-container-groups.md) beállítása lehetővé teszi, hogy a külső ügyfelek az IP-címet használják a csoportba tartozó tárolók eléréséhez. Egy böngészőben például elérheti egy tárolóban futó webalkalmazást. Jelenleg azonban a Container Group egy másik IP-címet használ a kimenő forgalomhoz. A kimenő forgalomhoz tartozó IP-cím nem érhető el programozott módon, ami a tárolók csoportjának felügyeletét és az ügyféloldali tűzfalszabályok konfigurációját összetettebbvé teszi.

Ez a cikk azt ismerteti, hogyan konfigurálható egy tároló csoport a [Azure Firewall](../firewall/overview.md)integrált [virtuális hálózatban](container-instances-virtual-network-concepts.md) . Ha a felhasználó által megadott útvonalat állítja be a tároló csoportba és a tűzfalszabályokba, átirányíthatja és azonosíthatja a tároló csoport felé irányuló és onnan érkező forgalmat. A tároló csoport bejövő és kimenő adatforgalmának használata a tűzfal nyilvános IP-címét használja. Egyetlen kimenő IP-cím is használható több, a virtuális hálózat alhálózatában a Azure Container Instancesra delegált tároló-csoport számára.

Ebben a cikkben az Azure CLI használatával hozza létre a forgatókönyv erőforrásait:

* [A virtuális hálózat](container-instances-vnet.md) delegált alhálózatán üzembe helyezett tároló csoportok 
* Egy statikus nyilvános IP-címmel rendelkező, hálózaton üzembe helyezett Azure-tűzfal
* Egy felhasználó által megadott útvonal a Container groups alhálózatán
* NAT-szabály a tűzfal bejövő forgalmához és egy alkalmazás-szabály a kimenő forgalomhoz

Ezután a tűzfalon keresztül ellenőrizheti a bejövő és a kimenő forgalmat.

## <a name="deploy-aci-in-a-virtual-network"></a>ACI üzembe helyezése virtuális hálózaton

Egy tipikus esetben előfordulhat, hogy már rendelkezik egy Azure-beli virtuális hálózattal, amelyben egy tároló csoportot kíván üzembe helyezni. A következő parancsok bemutató célból létrehoznak egy virtuális hálózatot és alhálózatot a tároló csoport létrehozásakor. Az alhálózat delegálása Azure Container Instances. 

A tároló csoport egy kisméretű webalkalmazást futtat a `aci-helloworld` rendszerképből. Ahogy az a dokumentáció más cikkeiben is látható, ez a rendszerkép egy olyan Node.js írt kisméretű webalkalmazást csomagol, amely egy statikus HTML-oldalt szolgál ki.

Ha szüksége van erre, először hozzon létre egy Azure-erőforráscsoportot az az [Group Create][az-group-create] paranccsal. Például:

```azurecli
az group create --name myResourceGroup --location eastus
```

Az alábbi parancsokra vonatkozó példák leegyszerűsítéséhez használjon környezeti változót az erőforráscsoport neveként:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Hozza létre a tároló csoportot az az [Container Create][az-container-create] paranccsal:

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Állítsa be az `--subnet address-prefix` alhálózat számára szükséges IP-címtartomány értékét. A legkisebb támogatott alhálózat a/29, amely nyolc IP-címet biztosít. Egyes IP-címek az Azure-hoz való használatra vannak fenntartva.

Egy későbbi lépésben való használathoz szerezze be a tároló csoport magánhálózati IP-címét az [az Container show] [az-Container-show] parancs futtatásával:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Azure Firewall üzembe helyezése a hálózaton

A következő szakaszban az Azure CLI használatával helyezhet üzembe egy Azure-tűzfalat a virtuális hálózaton. A háttérben tekintse meg [az oktatóanyag: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](../firewall/deploy-cli.md)című témakört.

Először az az [Network vnet subnet Create][az-network-vnet-subnet-create] paranccsal adjon hozzá egy AzureFirewallSubnet nevű alhálózatot a tűzfalhoz. A AzureFirewallSubnet az alhálózat *kötelező* neve.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Az alábbi [Azure CLI-parancsokkal](../firewall/deploy-cli.md) hozhat létre tűzfalat az alhálózaton.

Ha még nincs telepítve, adja hozzá a tűzfal bővítményt az Azure CLI-hez az az [Extension Add][az-extension-add] paranccsal:

```azurecli
az extension add --name azure-firewall
```

A tűzfal erőforrásainak létrehozása:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Frissítse a tűzfal konfigurációját az az [Network Firewall Update][az-network-firewall-update] paranccsal:

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Szerezze be a tűzfal magánhálózati IP-címét az az [Network Firewall IP-config List][az-network-firewall-ip-config-list] parancs használatával. Ez a magánhálózati IP-cím egy későbbi parancsban van használatban.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Szerezze be a tűzfal nyilvános IP-címét az az [Network Public-IP show][az-network-public-ip-show] paranccsal. Ez a nyilvános IP-cím egy későbbi parancsban van használatban.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Felhasználó által megadott útvonal definiálása ACI-alhálózaton

Definiáljon egy használaton kívüli útvonalat az ACI-alhálózaton, hogy átirányítsa a forgalmat az Azure tűzfalra. További információ: a [hálózati forgalom irányítása](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Útválasztási táblázat létrehozása

Először futtassa az alábbi az [Network Route-Table Create][az-network-route-table-create] parancsot az útválasztási táblázat létrehozásához. Hozza létre az útválasztási táblázatot a virtuális hálózattal megegyező régióban.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Útvonal létrehozása

Futtassa az [az Network-Route-Table Route Create][az-network-route-table-route-create] parancsot az útvonal táblában való létrehozásához. Ha át szeretné irányítani a forgalmat a tűzfalra, állítsa a következő ugrás típusát a értékre `VirtualAppliance` , és adja át a tűzfal magánhálózati IP-címét a következő ugrási címként.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Útválasztási táblázat hozzárendelése ACI-alhálózathoz

Az az [Network vnet subnet Update][az-network-vnet-subnet-update] parancs futtatásával társítsa az útválasztási táblázatot a Azure Container instances delegált alhálózathoz.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Szabályok konfigurálása a tűzfalon

Alapértelmezés szerint a Azure Firewall megtagadja (blokkolja) a bejövő és kimenő forgalmat. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>NAT-szabály konfigurálása a tűzfalon az ACI-alhálózatra

Hozzon létre egy [NAT-szabályt](../firewall/rule-processing.md) a tűzfalon a bejövő internetes forgalom lefordításához és szűréséhez a korábban a hálózatban elindított alkalmazás-tárolóra. Részletekért lásd: [a bejövő internetes forgalom szűrése Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)

Hozzon létre egy NAT-szabályt és-gyűjteményt az az [Network Firewall NAT-Rule Create][az-network-firewall-nat-rule-create] parancs használatával:

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Szükség szerint adja hozzá a NAT-szabályokat az alhálózat más IP-címeire irányuló forgalom szűréséhez. Az alhálózatban lévő más tároló-csoportok például kijelenthetik a bejövő forgalom IP-címeit, vagy más belső IP-címeket is hozzá lehet rendelni a Container csoporthoz az újraindítás után.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Kimenő alkalmazás szabályának létrehozása a tűzfalon

A következő az [Network Firewall Application-Rule Create][az-network-firewall-application-rule-create] paranccsal hozzon létre egy kimenő szabályt a tűzfalon. Ez a szabály lehetővé teszi a hozzáférést az alhálózatról delegált alhálózatról a teljes tartománynévre Azure Container Instances `checkip.dyndns.org` . A helyhez való HTTP-hozzáférést egy későbbi lépésben kell használni, hogy erősítse a kimenő IP-címet Azure Container Instancesról.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Tároló csoport hozzáférésének tesztelése a tűzfalon keresztül

A következő fejezetek azt ellenőrzik, hogy Azure Container Instances delegált alhálózata megfelelően van-e konfigurálva az Azure tűzfal mögött. Az előző lépések a bejövő forgalmat az alhálózatra és a kimenő adatforgalomra is átirányítják a tűzfalon keresztül.

### <a name="test-ingress-to-a-container-group"></a>Bejövő forgalom tesztelése egy tároló csoportba

Tesztelje a virtuális hálózaton futó *appcontainer* bejövő hozzáférését a tűzfal nyilvános IP-címének tallózásával. Korábban a nyilvános IP-címet a (z) $FW _PUBLIC_IP változóban tárolta:

```bash
echo $FW_PUBLIC_IP
```

A kimenet a következőhöz hasonló:

```console
52.142.18.133
```

Ha a tűzfal NAT-szabálya megfelelően van konfigurálva, akkor a tűzfal nyilvános IP-címének megadásakor a következő jelenik meg:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="A tűzfal nyilvános IP-címének megkeresése tallózással":::

### <a name="test-egress-from-a-container-group"></a>Kimenő forgalom tesztelése egy tároló csoportjából


A következő minta tároló üzembe helyezése a virtuális hálózaton. A futtatásakor egyetlen HTTP-kérést küld a szolgáltatásnak `http://checkip.dyndns.org` , amely megjeleníti a küldő IP-címét (a kimenő IP-címet). Ha a tűzfalon az alkalmazásra vonatkozó szabály megfelelően van konfigurálva, a rendszer a tűzfal nyilvános IP-címét adja vissza.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Tekintse meg a tároló naplóit, és ellenőrizze, hogy az IP-cím megegyezik-e a tűzfal nyilvános IP-címével.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

A kimenet a következőhöz hasonló:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Azure tűzfal mögötti virtuális hálózatban állít be tároló csoportokat. Felhasználó által megadott útvonalat, NAT-és alkalmazás-szabályokat konfigurált a tűzfalon. Ennek a konfigurációnak a használatával egyetlen statikus IP-címet állíthat be a bejövő és a kimenő Azure Container Instancesról.

A forgalom kezelésével és az Azure-erőforrások védelmével kapcsolatos további információkért tekintse meg a [Azure Firewall](../firewall/index.yml) dokumentációját.



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






