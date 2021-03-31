---
title: 'Helyszíni hálózatok összekapcsolása virtuális hálózattal: helyek közötti VPN: parancssori felület'
description: Hozzon létre egy IPsec helyek közötti VPN Gateway kapcsolatot a helyszíni hálózatból egy Azure-beli virtuális hálózattal a nyilvános interneten keresztül a parancssori felület használatával.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2c59c67eb7b5ae5b26ac5517afba433fe8c028fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104611746"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a parancssori felület használatával

Ez a cikk bemutatja, hogyan használhatja az Azure CLI-t egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:<br>

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
* Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

### <a name="example-values"></a><a name="example"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. kapcsolódás az előfizetéshez

Ha a parancssori felület helyi futtatását választja, kapcsolódjon az előfizetéshez. Ha Azure Cloud Shellt használ a böngészőben, nem kell csatlakoznia az előfizetéséhez. Azure Cloud Shell automatikusan fog összekapcsolást létesíteni. A kapcsolódás után azonban érdemes ellenőrizni, hogy a megfelelő előfizetést használja-e.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a><a name="rg"></a>2. erőforráscsoport létrehozása

A következő példában létrehozunk egy „TestRG1” nevű erőforráscsoportot az „eastus” helyen. Ha már rendelkezik erőforráscsoporttal abban a régióban, ahol létre kívánja hozni a virtuális hálózatát, használhatja azt is.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="3-create-a-virtual-network"></a><a name="VNet"></a>3. virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet az [az network vnet create](/cli/azure/network/vnet) paranccsal. Virtuális hálózat létrehozásakor győződjön meg róla, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel.

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon, egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amit kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>
>

Az alábbi példa létrehoz egy „TestVNet1” nevű virtuális hálózatot és egy „Subnet-1” nevű alhálózatot.

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## <a name="4-create-the-gateway-subnet"></a>4. <a name="gwsub"></a> az átjáró alhálózatának létrehozása


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Az átjáróalhálózat létrehozásához használja az [az network vnet subnet create](/cli/azure/network/vnet/subnet) parancsot.

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="5-create-the-local-network-gateway"></a><a name="localnet"></a>5. a helyi hálózati átjáró létrehozása

A helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. A helyszíni hálózat módosításakor az előtagok egyszerűen frissíthetők.

Használja a következő értékeket:

* A helyszíni VPN-eszköz IP-címe: *--gateway-ip-address*.
* A helyszíni címterek: *--local-address-prefixes*.

Az [az network local-gateway create](/cli/azure/network/local-gateway) paranccsal hozzáadhat egy helyi hálózati átjárót több címelőtaggal:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="6-request-a-public-ip-address"></a><a name="PublicIP"></a>6. nyilvános IP-cím kérése

Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

Az [az network public-ip create](/cli/azure/network/public-ip) paranccsal kérhet dinamikus nyilvános IP-címet.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="7-create-the-vpn-gateway"></a><a name="CreateGateway"></a>7. a VPN-átjáró létrehozása

Hozza létre a virtuális hálózat VPN-átjáróját. A VPN-átjáró létrehozása akár 45 percet vagy többet is igénybe vehet.

Használja a következő értékeket:

* A helyek közötti konfiguráció *--gateway-type* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. További információért lásd: [Átjárótípusok](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* A *--VPN-Type* lehet *útvonalalapú* (más néven dinamikus átjáró néhány dokumentációban), vagy *házirendalapú* (más néven statikus átjáró néhány dokumentációban). A beállítás azon eszköz követelményeire vonatkozik, amelyhez csatlakozik. További információk a VPN-átjárótípusokról: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Válassza ki az átjáró használni kívánt termékváltozatát. Egyes termékváltozatok konfigurációs korlátokkal rendelkeznek. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/network/vnet-gateway) paranccsal. Ha ezt a parancsot a „--no-wait” paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. Ez a paraméter lehetővé teszi, hogy az átjáró a háttérben jöjjön létre. Egy átjáró létrehozása nagyjából 45 percet vesz igénybe.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="8-configure-your-vpn-device"></a><a name="VPNDevice"></a>8. a VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti.   A virtuális hálózati átjáró IP-címét az [az network public-ip list](/cli/azure/network/public-ip) paranccsal keresheti meg. Az olvashatóság érdekében a kimenet táblázatos formában jeleníti meg a nyilvános IP-címek listáját.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="9-create-the-vpn-connection"></a><a name="CreateConnection"></a>9. a VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között. Különösen figyeljen oda a megosztott kulcs értékére, amelynek meg kell egyeznie a VPN-eszköz konfigurált megosztottkulcs-értékével.

Hozza létre a kapcsolatot az [az network vnet-connection create](/cli/azure/network/vpn-connection) paranccsal.

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

A kapcsolat rövid időn belül létrejön.

## <a name="10-verify-the-vpn-connection"></a><a name="toverify"></a>10. a VPN-kapcsolat ellenőrzése

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Ha másik módszerrel kívánja ellenőrizni a kapcsolatot: [VPN Gateway-kapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

Ez a szakasz a helyek közötti konfigurációk használatakor hasznos gyakori parancsokat tartalmazza. A CLI hálózati parancsainak teljes listájáért lásd az [Azure CLI-hálózatkezelést](/cli/azure/network) bemutató cikket.

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](../index.yml).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* Információk a kényszerített bújtatásról: [Információk a kényszerített bújtatásról](vpn-gateway-forced-tunneling-rm.md).
* Információk a magas rendelkezésre állású aktív-aktív kapcsolatokról: [Magas rendelkezésre állású kapcsolatok létesítmények, illetve virtuális hálózatok között](vpn-gateway-highlyavailable.md).
* Az Azure CLI hálózati parancsainak listáját lásd: [Azure CLI](/cli/azure/network).
* A helyek közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [helyek közötti VPN-kapcsolat létrehozását](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) ismertető cikkben talál.
* A virtuális hálózatok közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [HBase-georeplikáció üzembe helyezését](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) ismertető cikkben talál.