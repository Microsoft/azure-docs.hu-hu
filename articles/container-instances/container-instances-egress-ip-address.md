---
title: Statikus kimenő IP-cím konfigurálása
description: Konfigurálja az Azure Firewallt és a felhasználó által megadott útvonalakat Azure Container Instances, amelyek a tűzfal nyilvános IP-címét használják a bejövő és a bejövő forgalomhoz
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: a03c59652b9409d54bbe63c63a31fdd2228ac34e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878685"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Egyetlen nyilvános IP-cím konfigurálása a tárolócsoport kimenő és bejövő forgalmához

Ha külső [](container-instances-container-groups.md) IP-címmel hoz létre tárolócsoportot, a külső ügyfelek az IP-cím használatával férhetnek hozzá a csoportban található tárolókhoz. Egy böngésző például hozzáférhet egy tárolóban futó webalkalmazáshoz. Jelenleg azonban a tárolócsoport más IP-címet használ a kimenő forgalomhoz. Ez a bejövő IP-cím nem programozott módon van elérhetővé téve, így a tárolócsoport figyelése és az ügyfél tűzfalszabályainak konfigurálása összetettebb.

Ez a cikk a tárolócsoport konfigurálásának lépéseit egy olyan virtuális hálózatban, amely [integrálva](container-instances-virtual-network-concepts.md) van a [Azure Firewall.](../firewall/overview.md) A tárolócsoport és a tűzfalszabályok felhasználó által megadott útvonalának beállításával irányíthatja és azonosíthatja a tárolócsoportból származó és oda befelé tartó forgalmat. A tárolócsoport bejövő és bejövő forgalom a tűzfal nyilvános IP-címét használja. Egyetlen bejövő IP-címet több, a virtuális hálózat alhálózatán üzembe helyezett tárolócsoport is Azure Container Instances.

Ebben a cikkben az Azure CLI használatával hozhatja létre az ehhez a forgatókönyvhöz szükséges erőforrásokat:

* A virtuális hálózat delegált alhálózatán üzembe helyezett [tárolócsoportok](container-instances-vnet.md) 
* A hálózaton üzembe helyezett Azure-tűzfal statikus nyilvános IP-címmel
* Felhasználó által megadott útvonal a tárolócsoportok alhálózatán
* NAT-szabály tűzfal bejövő forgalomhoz és egy alkalmazásszabály a bejövő forgalomhoz

Ezután ellenőrizheti a példa tárolócsoportok bejövő és ki- és bejövő ját a tűzfalon keresztül.

## <a name="deploy-aci-in-a-virtual-network"></a>Az ACI üzembe helyezése virtuális hálózaton

Általában már van egy Azure-beli virtuális hálózata, amelyben üzembe helyezhet egy tárolócsoportot. Bemutatási célból a következő parancsok létrehoznak egy virtuális hálózatot és alhálózatot a tárolócsoport létrehozásakor. Az alhálózat delegálva van a Azure Container Instances. 

A tárolócsoport egy kis méretű webalkalmazást futtat a `aci-helloworld` rendszerképből. Ahogy a dokumentáció más cikkei is mutatják, ez a kép egy statikus HTML-oldalt Node.js webalkalmazást tartalmaz.

Ha szüksége van rá, először hozzon létre egy Azure-erőforráscsoportot [az az group create paranccsal.][az-group-create] Például:

```azurecli
az group create --name myResourceGroup --location eastus
```

Az alábbi példaparancsok egyszerűsítése érdekében használjon környezeti változót az erőforráscsoport nevéhez:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Hozza létre a tárolócsoportot [az az container create paranccsal:][az-container-create]

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
> Állítsa be a értékét az alhálózatban szükséges `--subnet address-prefix` IP-címtérhez. A legkisebb támogatott alhálózat a /29, amely nyolc IP-címet biztosít. Egyes IP-címek az Azure számára vannak fenntartva.

Egy későbbi lépésben való használathoz szerezze be a tárolócsoport magánhálózati IP-címét az [az container show][az-container-show] parancs futtatásával:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Hálózati Azure Firewall üzembe helyezése

A következő szakaszokban az Azure CLI használatával helyezhet üzembe egy Azure-tűzfalat a virtuális hálózaton. Háttérinformációkért [lásd: Oktatóanyag: Üzembe helyezés és konfigurálás Azure Firewall a Azure Portal.](../firewall/deploy-cli.md)

Először az [az network vnet subnet create][az-network-vnet-subnet-create] használatával adjon hozzá egy AzureFirewallSubnet nevű alhálózatot a tűzfalhoz. Az alhálózat kötelező neve  az AzureFirewallSubnet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Az alábbi [Azure CLI-parancsokkal](../firewall/deploy-cli.md) hozzon létre egy tűzfalat az alhálózaton.

Ha még nincs telepítve, adja hozzá a tűzfalbővítményt az Azure CLI-hez [az az extension add paranccsal:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

Hozza létre a tűzfalerőforrásokat:

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

Frissítse a tűzfal konfigurációját [az az network firewall update paranccsal:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Az [az network firewall ip-config list paranccsal][az-network-firewall-ip-config-list] szerezze be a tűzfal magánhálózati IP-címét. Ezt a magánhálózati IP-címet egy későbbi parancs használja.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Az [az network public-ip show][az-network-public-ip-show] paranccsal szerezze be a tűzfal nyilvános IP-címét. Ezt a nyilvános IP-címet egy későbbi parancs használja.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Felhasználó által megadott útvonal meghatározása az ACI-alhálózaton

Definiálhat egy használat által meghatározott útvonalat az ACI-alhálózaton, hogy átirányítsa a forgalmat az Azure Firewallhoz. További információ: [Hálózati forgalom útválasztása.](../virtual-network/tutorial-create-route-table-cli.md) 

### <a name="create-route-table"></a>Útválasztási táblázat létrehozása

Először futtassa a következő [az network route-table create parancsot][az-network-route-table-create] az útvonaltábla létrehozásához. Hozza létre az útvonaltáblát ugyanabban a régióban, mint a virtuális hálózat.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Útvonal létrehozása

Futtassa [az az network-route-table route create futtatásával][az-network-route-table-route-create] hozzon létre egy útvonalat az útvonaltáblában. A forgalom tűzfalra való útválasztáshoz állítsa a következő ugrás típusát a következőre, és adja meg a tűzfal magánhálózati `VirtualAppliance` IP-címét a következő ugrás címeként.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Útválasztási táblázat társítása ACI-alhálózathoz

Futtassa [az az network vnet subnet update parancsot,][az-network-vnet-subnet-update] hogy társítsa az útválasztási táblázatot az Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Tűzfalszabályok konfigurálása

Alapértelmezés szerint a Azure Firewall (blokkok) tiltja a bejövő és kimenő forgalmat. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>NAT-szabály konfigurálása tűzfalon az ACI-alhálózathoz

Hozzon létre [egy NAT-szabályt](../firewall/rule-processing.md) a tűzfalon, amely lefordítja és szűri a bejövő internetes forgalmat a korábban a hálózaton elindított alkalmazástárolóra. Részletekért lásd: Bejövő internetes forgalom szűrése Azure Firewall [DNAT segítségével](../firewall/tutorial-firewall-dnat.md)

Hozzon létre egy NAT-szabályt és -gyűjteményt [az az network firewall nat-rule create paranccsal:][az-network-firewall-nat-rule-create]

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

Szükség szerint adjon hozzá NAT-szabályokat az alhálózat más IP-címeinek forgalmának szűréséhez. Például az alhálózatban található más tárolócsoportok IP-címeket tehetnek elérhetővé a bejövő forgalom számára, vagy más belső IP-címek rendelhetők hozzá a tárolócsoporthoz újraindítás után.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Kimenő alkalmazásszabály létrehozása a tűzfalon

Futtassa a következő [az network firewall application-rule create parancsot][az-network-firewall-application-rule-create] egy kimenő szabály létrehozásához a tűzfalon. Ez a mintaszabály engedélyezi a hozzáférést az FQDN-Azure Container Instances delegált `checkip.dyndns.org` alhálózatról. A webhely HTTP-hozzáférése egy későbbi lépésben lesz használva a hálózati forgalom IP-címének Azure Container Instances.

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

## <a name="test-container-group-access-through-the-firewall"></a>Tárolócsoport hozzáférésének tesztelése a tűzfalon keresztül

A következő szakaszok ellenőrzik, hogy a Azure Container Instances számára delegált alhálózat megfelelően van-e konfigurálva az Azure-tűzfal mögött. Az előző lépések az alhálózatra érkező és az alhálózatból kimenő forgalmat is a tűzfalon keresztül irányítják.

### <a name="test-ingress-to-a-container-group"></a>Tárolócsoport bejövő forgalomának tesztelése

Tesztelje a virtuális hálózaton futó *appcontainer* bejövő hozzáférését a tűzfal nyilvános IP-címének tallózással. Korábban a nyilvános IP-címet a következő változóban tárolta$FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

A kimenet a következőhöz hasonló:

```console
52.142.18.133
```

Ha a tűzfal NAT-szabálya megfelelően van konfigurálva, a következőt fogja látni, amikor beírja a tűzfal nyilvános IP-címét a böngészőben:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Tallózás a tűzfal nyilvános IP-címére":::

### <a name="test-egress-from-a-container-group"></a>Tárolócsoportból származó forgalom tesztelése


Telepítse a következő mintatárolót a virtuális hálózaton. A futtatásakor egyetlen HTTP-kérést küld a szolgáltatásnak, amely megjeleníti a küldő `http://checkip.dyndns.org` IP-címét (a bejövő forgalom IP-címét). Ha a tűzfalon az alkalmazásszabály megfelelően van konfigurálva, a rendszer visszaadja a tűzfal nyilvános IP-címét.

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

Tekintse meg a tárolónaplókat, és ellenőrizze, hogy az IP-cím megegyezik-e a tűzfal nyilvános IP-címével.

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

Ebben a cikkben tárolócsoportokat fog beállítani egy Azure-tűzfal mögötti virtuális hálózatban. Konfigurált egy felhasználó által megadott útvonalat, valamint NAT- és alkalmazásszabályokat a tűzfalon. Ezzel a konfigurációval egyetlen statikus IP-címet állíthat be a bejövő és a Azure Container Instances.

A forgalom kezelésével és az Azure-erőforrások [](../firewall/index.yml) védelmével kapcsolatos további információkért tekintse meg a Azure Firewall dokumentációját.



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/network/firewall#az_network_firewall_update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/network/firewall/application-rule#az_network_firewall_application_rule_create
[az-network-firewall-nat-rule-create]: /cli/azure/network/firewall/nat-rule#az_network_firewall_nat_rule_create
