---
title: 'A helyszíni hálózat összekapcsolása egy Azure-beli virtuális hálózattal: helyek közötti VPN: PowerShell'
description: A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a PowerShell segítségével.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: cherylmc
ms.openlocfilehash: 85ea3855b13350901d85701e9bca8d87ff6632c3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778804"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Helyek közötti VPN-kapcsolattal rendelkező virtuális hálózat létrehozása a PowerShell használatával

Ez a cikk bemutatja, hogyan használhatja a PowerShellt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a VNet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="running-powershell-locally"></a>A PowerShell helyi futtatása

Ha helyben kívánja telepíteni és használni a PowerShellt, telepítse az Azure Resource Manager PowerShell-parancsmagok legújabb verzióját. A PowerShell-parancsmagok gyakran frissülnek, és általában frissítenie kell a PowerShell-parancsmagokat a legújabb szolgáltatásfunkciók eléréséhez. Ha nem frissíti a PowerShell-parancsmagokat, a megadott értékek sikertelenek lehetnek. 

A használt verzió megkereséséhez futtassa a Get-Module-ListAvailable az parancsot. Ha frissítésre van szükség, olvassa el az [Azure PowerShell-modul telepítését ismertető](/powershell/azure/install-az-ps) szakaszt. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.
Ha helyileg futtatja a PowerShellt, akkor a "AzAccount" parancsot is futtatnia kell a kapcsolat létrehozásához az Azure-ban.


### <a name="example"></a>Példaértékek

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="VNet"></a>1. hozzon létre egy virtuális hálózatot és egy átjáró-alhálózatot

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. Virtuális hálózat létrehozásakor győződjön meg róla, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel. 

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon, egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amit kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>
>

### <a name="about-the-gateway-subnet"></a>Az átjáró alhálózatának ismertetése

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>Virtuális hálózat és átjáró-alhálózat létrehozása

Ebben a példában egy virtuális hálózatot és egy átjáróalhálózatot hozunk létre. Ha már rendelkezik virtuális hálózattal, amelyhez hozzá szeretne adni egy átjáróalhálózatot, lásd: [Átjáróalhálózat hozzáadása már létrehozott virtuális hálózathoz](#gatewaysubnet).

Hozzon létre egy erőforráscsoportot:

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

Hozza létre a virtuális hálózatot.

1. Állítsa be a változókat.

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. Hozza létre a virtuális hálózatot.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="gatewaysubnet"></a>Átjáró-alhálózat hozzáadása már létrehozott virtuális hálózathoz

Ennek a szakasznak a lépéseit akkor kövesse, ha már rendelkezik virtuális hálózattal, de még hozzá kell adnia egy átjáró-alhálózatot.

1. Állítsa be a változókat.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. Hozza létre az átjáró-alhálózatot.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. Állítsa be a konfigurációt.

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## 2. <a name="localnet"> </a>a helyi hálózati átjáró létrehozása

A helyi hálózati átjáró (LNG) általában a helyszíni helyre vonatkozik. Nem ugyanaz, mint a virtuális hálózati átjáró. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. A helyszíni hálózat módosításakor az előtagok egyszerűen frissíthetők.

Használja a következő értékeket:

* A helyszíni VPN-eszköz IP-címe: *GatewayIPAddress*.
* A helyszíni címtér az *AddressPrefix*.

Helyi hálózati átjáró hozzáadása egyetlen címelőtaggal:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

Helyi hálózati átjáró hozzáadása több címelőtaggal:

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

Helyi hálózati átjáró IP-cím előtagjainak módosítása:

A helyi hálózati átjáró előtagjai bizonyos esetekben változnak. Az IP-címelőtagok módosításának lépései attól függően változnak, hogy előzőleg létre lett-e hozva VPN Gateway-kapcsolat. Lásd a jelen cikk [Helyi hálózati átjáró IP-címelőtagjainak módosítása](#modify) című szakaszát.

## <a name="PublicIP"></a>3. nyilvános IP-cím kérése

Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. 

A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím megváltozik, miután hozzá lett rendelve a VPN-átjáróhoz. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

Kérjen egy nyilvános IP-címet, amelyet a virtuális hálózatban a VPN Gateway-hez társít.

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>4. az átjáró IP-címzési konfigurációjának létrehozása

Az átjáró konfigurációja meghatározza az alhálózatot (a "GatewaySubnet") és a használni kívánt nyilvános IP-címet. A következő példa használatával hozza létre az átjáró konfigurációját.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>5. a VPN-átjáró létrehozása

Hozza létre a virtuális hálózat VPN-átjáróját.

Használja a következő értékeket:

* A webhelyek közötti konfiguráció *-GatewayType* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. Más átjáró-konfigurációk például -GatewayType ExpressRoute típus alkalmazását igényelhetik.
* A *-VpnType* típus a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). További információk a VPN-átjárótípusokról: [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md).
* Válassza ki az átjáró használni kívánt termékváltozatát. Egyes termékváltozatok konfigurációs korlátokkal rendelkeznek. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ha hibaüzenetet kap a -GatewaySku paraméterrel kapcsolatban a VPN-átjáró létrehozásakor, győződjön meg arról, hogy telepítette a PowerShell-parancsmagok legújabb verzióját.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

A parancs futtatása után az átjáró létrehozása akár 45 percet is igénybe vehet.

## <a name="ConfigureVPNDevice"></a>6. a VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következő elemek szükségesek:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A virtuális hálózati átjáró nyilvános IP-címének a PowerShell használatával történő megkereséséhez használja a következő példát. Ebben a példában a VNet1GWPIP annak a nyilvános IP-cím-erőforrásnak a neve, amelyet egy korábbi lépésben hozott létre.

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>7. a VPN-kapcsolat létrehozása

Ezután hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a VPN-eszköz között. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. A megosztott kulcsnak meg kell egyeznie a VPN-eszköze konfigurálásakor használt értékkel. Vegye figyelembe, hogy helyek közötti kapcsolat esetében a „-ConnectionType” értéke: **IPsec**.

1. Állítsa be a változókat.
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. Hozza létre a kapcsolatot.
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

A kapcsolat rövid időn belül létrejön.

## <a name="toverify"></a>8. a VPN-kapcsolat ellenőrzése

A VPN-kapcsolat ellenőrzése többféleképpen is történhet.

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>Helyi hálózati átjáró IP-címelőtagjainak módosítása

Ha a helyszínre átirányítani kívánt IP-címelőtagok módosulnak, módosíthatja a helyi hálózati átjárót. Két utasításcsoportot adtunk meg. Ezek közül aszerint választhat, hogy az átjárókapcsolat létre lett-e már hozva. Ha ezeket a példákat használja, módosítsa az értékeket úgy, hogy megfeleljenek a környezetének.

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Helyi hálózati átjáró IP-címének módosítása

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Következő lépések

*  Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* A helyek közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [helyek közötti VPN-kapcsolat létrehozását](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) ismertető cikkben talál.
* A virtuális hálózatok közötti VPN-kapcsolatok Azure Resource Manager-sablonok használatával történő létrehozásáról további információt a [HBase-georeplikáció üzembe helyezését](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) ismertető cikkben talál.
