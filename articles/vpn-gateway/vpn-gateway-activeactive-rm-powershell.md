---
title: Aktív-aktív S2S Azure VPN Gateway kapcsolatok konfigurálása
description: Ez a cikk bemutatja, hogyan konfigurálhatja az aktív-aktív kapcsolatokat az Azure VPN Gateway Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89440779"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Aktív-aktív S2S VPN-kapcsolatok konfigurálása Azure VPN Gateway-átjárókkal

Ez a cikk bemutatja, hogyan hozhat létre aktív-aktív létesítmények közötti és VNet VNet kapcsolatokat a Resource Manager-alapú üzemi modell és a PowerShell használatával. A Azure Portalban aktív-aktív átjáró is konfigurálható.

## <a name="about-highly-available-cross-premises-connections"></a>A nagyszámú létesítmények közötti kapcsolatok
Ha magas rendelkezésre állást szeretne biztosítani a létesítmények közötti és VNet kapcsolatokhoz, több VPN-átjárót kell üzembe helyeznie, és több párhuzamos kapcsolatot kell létesítenie a hálózatok és az Azure között. A kapcsolódási lehetőségek és a topológia áttekintéséhez tekintse meg a [létesítmények közötti, valamint a VNet és a VNet közötti kapcsolatot](vpn-gateway-highlyavailable.md) .

Ez a cikk útmutatást nyújt az aktív-aktív létesítmények közötti VPN-kapcsolat beállításához és a két virtuális hálózat közötti aktív-aktív kapcsolat létrehozásához.

* [1. rész – az Azure VPN Gateway létrehozása és konfigurálása aktív-aktív módban](#aagateway)
* [2. rész – aktív-aktív létesítmények közötti kapcsolatok létrehozása](#aacrossprem)
* [3. rész – aktív-aktív VNet – VNet kapcsolatok létrehozása](#aav2v)

Ha már rendelkezik egy VPN-átjáróval, a következőket teheti:
* [Meglévő VPN-átjáró frissítése aktív-készenlétről aktív-aktívra, vagy fordítva](#aaupdate)

Kombinálhatja ezeket együtt egy összetettebb, magasan elérhető hálózati topológia létrehozásához, amely megfelel az igényeinek.

> [!IMPORTANT]
> Az aktív-aktív mód minden SKU esetében elérhető az alapszintű kivételével.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>1. rész – aktív-aktív VPN Gateway-átjárók létrehozása és konfigurálása
Az alábbi lépésekkel konfigurálhatja az Azure VPN Gatewayt aktív-aktív módokon. Az aktív-aktív és az aktív-készenléti átjárók közötti fő különbségek:

* Két nyilvános IP-címmel rendelkező átjáró IP-konfigurációt kell létrehoznia
* Be kell állítania a EnableActiveActiveFeature jelzőt
* Az átjáró SKU-jának VpnGw1, VpnGw2, VpnGw3 vagy HighPerformance (örökölt SKU) kell lennie.

A többi tulajdonság megegyezik a nem aktív-aktív átjárókkal. 

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Ha nem szeretné a böngészőben használni a Cloudshellben-t, telepítenie kell a Azure Resource Manager PowerShell-parancsmagokat. A PowerShell-parancsmagok telepítésével kapcsolatos további információkért tekintse meg a [Azure PowerShell áttekintése](/powershell/azure/) című témakört.

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – a VNet1 létrehozása és konfigurálása
#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Ezt a gyakorlatot a változók deklarálásával kezdjük. Ha a "kipróbálás" Cloud Shell használja, automatikusan a fiókjához fog csatlakozni. Ha helyileg használja a PowerShellt, a következő példával csatlakozhat a kapcsolódáshoz:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

Az alábbi példa a gyakorlathoz használt értékekkel deklarálja a változókat. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```azurepowershell-interactive
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-create-a-new-resource-group"></a>2. hozzon létre egy új erőforráscsoportot

Egy új erőforráscsoport létrehozásához használja az alábbi példát:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 létrehozása
Az alábbi minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>2. lépés – a TestVNet1 VPN-átjáró létrehozása aktív-aktív móddal
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. a nyilvános IP-címek és az átjáró IP-konfigurációinak létrehozása
Igényeljen két nyilvános IP-címet a VNet létrehozandó átjáróhoz. Az alhálózatot és az IP-konfigurációkat is meg kell határoznia.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. a VPN-átjáró létrehozása aktív-aktív konfigurációval
Hozza létre a TestVNet1 virtuális hálózati átjáróját. Vegye figyelembe, hogy két GatewayIpConfig-bejegyzés van, a EnableActiveActiveFeature jelző pedig be van állítva. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. az átjáró nyilvános IP-címeinek és a BGP-társ IP-címének beszerzése
Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN Gatewayon. Ez a címe szükséges ahhoz, hogy az Azure VPN Gateway BGP-társként konfigurálja a helyszíni VPN-eszközökhöz.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

A következő parancsmagok segítségével jelenítheti meg a VPN-átjáróhoz lefoglalt két nyilvános IP-címet, valamint a hozzájuk tartozó BGP-társ IP-címeket az egyes átjáró-példányok esetében:

```azurepowershell-interactive
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Az átjáró példányaihoz tartozó nyilvános IP-címek sorrendje és a kapcsolódó BGP-társítási címek azonosak. Ebben a példában a 40.112.190.5 nyilvános IP-címével rendelkező átjáró virtuális gép a 10.12.255.4-t használja a BGP-társi címként, és az átjáró és a 138.91.156.129 a 10.12.255.5-t fogja használni. Erre az információra akkor van szükség, amikor beállítja az aktív-aktív átjáróhoz csatlakozó helyszíni VPN-eszközöket. Az átjáró az alábbi ábrán látható az összes címmel:

![aktív – aktív átjáró](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Az átjáró létrehozása után ezt az átjárót használhatja aktív-aktív létesítmények közötti vagy VNet-VNet kapcsolat létesítéséhez. Az alábbi szakaszokban ismertetjük a gyakorlat befejezésének lépéseit.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>2. rész – aktív-aktív létesítmények közötti kapcsolat létesítése
Létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót a helyszíni VPN-eszköz megjelenítéséhez, valamint egy kapcsolatot az Azure VPN Gateway és a helyi hálózati átjáró összekapcsolásához. Ebben a példában az Azure VPN Gateway aktív-aktív módban van. Ennek eredményeképpen annak ellenére, hogy csak egy helyszíni VPN-eszköz (helyi hálózati átjáró) és egy kapcsolati erőforrás érhető el, az Azure VPN Gateway példányai S2S VPN-alagutakat fognak létrehozni a helyszíni eszközzel.

A folytatás előtt győződjön meg arról, hogy végrehajtotta a gyakorlat [1. részét](#aagateway) .

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – a helyi hálózati átjáró létrehozása és konfigurálása
#### <a name="1-declare-your-variables"></a>1. a változók deklarálása
Ez a gyakorlat továbbra is a diagramon látható konfigurációt fogja felépíteni. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Néhány tudnivaló a helyi hálózati átjáró paraméterekkel kapcsolatban:

* A helyi hálózati átjáró a VPN-átjáróval megegyező vagy eltérő helyen és erőforráscsoporthoz is lehet. Ebben a példában a különböző erőforráscsoportok, de ugyanazon az Azure-helyen láthatók.
* Ha a fent látható módon csak egy helyszíni VPN-eszköz működik, az aktív-aktív kapcsolat BGP protokollal vagy anélkül is működhet. Ez a példa BGP-t használ a létesítmények közötti kapcsolatok esetében.
* Ha a BGP engedélyezve van, a helyi hálózati átjáróhoz deklarálni kívánt előtag a VPN-eszközön található BGP-társ IP-címének állomásneve. Ebben az esetben ez a "10.52.255.253/32" a/32 előtag.
* Emlékeztetőként különböző BGP-ASN kell használnia a helyszíni hálózatok és az Azure-VNet között. Ha ezek megegyeznek, akkor módosítania kell a VNet ASN-t, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédokkal.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. hozza létre a helyi hálózati átjárót a site5 számára
Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez. Hozzon létre egy erőforráscsoportot, ha még nincs létrehozva.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a VNet-átjáró és a helyi hálózati átjáró összekötése
#### <a name="1-get-the-two-gateways"></a>1. a két átjáró beolvasása

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. hozza létre a TestVNet1 site5-kapcsolatban
Ebben a lépésben létrehozza a TestVNet1-ből Site5_1 a "EnableBGP" értékkel rendelkező, $Truere beállított kapcsolatokat.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN-és BGP-paraméterek a helyszíni VPN-eszközhöz
Az alábbi példa azokat a paramétereket sorolja fel, amelyeket a helyszíni VPN-eszköz BGP-konfiguráció szakaszában fog megadni a következő gyakorlathoz:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolatot néhány perc múlva kell létrehozni, és a BGP-társítási munkamenet elindul az IPsec-kapcsolat létrejöttekor. Ez a példa eddig csak egy helyszíni VPN-eszközt konfigurált, ami az alábbi ábrán látható:

![aktív-aktív-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>3. lépés – két helyszíni VPN-eszköz csatlakoztatása az aktív-aktív VPN-átjáróhoz
Ha két VPN-eszköze van ugyanazon a helyszíni hálózaton, akkor a kettős redundancia eléréséhez csatlakoztassa az Azure VPN Gatewayt a második VPN-eszközhöz.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. hozza létre a második helyi hálózati átjárót a site5
Az átjáró IP-címe, a cím előtagja és a BGP-társítási cím a második helyi hálózati átjáró esetében nem lehet átfedésben az előző helyi hálózati átjáróval ugyanahhoz a helyszíni hálózathoz.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. a VNet-átjáró és a második helyi hálózati átjáró összekötése
Hozza létre a TestVNet1-ből Site5_2 a "EnableBGP" értékkel rendelkező, $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN-és BGP-paraméterek a második helyszíni VPN-eszközhöz
Hasonlóképpen, az alábbiakban azokat a paramétereket sorolja fel, amelyeket a második VPN-eszközbe fog írni:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat (alagutak) létrejötte után a rendszer két redundáns VPN-eszközt és alagutat csatlakoztat a helyszíni hálózathoz és az Azure-hoz:

![kettős redundancia – crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>3. rész – aktív-aktív VNet – VNet kapcsolat létrehozása
Ez a szakasz egy aktív-aktív VNet-VNet-kapcsolódást hoz létre a BGP-vel. 

Az alábbi útmutató a fent leírt lépések folytatása. Az 1. [részt](#aagateway) el kell végeznie a TestVNet1 és a BGP-vel való VPN Gateway létrehozásához és konfigurálásához. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – a TestVNet2 és a VPN-átjáró létrehozása
Fontos annak biztosítása, hogy az új virtuális hálózat (TestVNet2) IP-címének mérete ne legyen átfedésben a VNet-tartományokkal.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. Beállíthatja a különböző előfizetések közötti VNet-VNet kapcsolatokat; További részletekért tekintse meg a [VNet-VNet kapcsolat konfigurálását](vpn-gateway-vnet-vnet-rm-ps.md) ismertető témakört. Győződjön meg arról, hogy hozzáadja a "-EnableBgp $True" kifejezést a BGP-t engedélyező kapcsolatok létrehozásakor.

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása
Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```azurepowershell-interactive
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. hozzon létre TestVNet2 az új erőforráscsoporthoz

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. az aktív-aktív VPN Gateway létrehozása a TestVNet2-hez
Igényeljen két nyilvános IP-címet a VNet létrehozandó átjáróhoz. Az alhálózatot és az IP-konfigurációkat is meg kell határoznia.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Hozza létre a VPN-átjárót a AS számmal és a "EnableActiveActiveFeature" jelzővel. Vegye figyelembe, hogy az alapértelmezett ASN-t felül kell bírálnia az Azure VPN Gateway-átjárón. A csatlakoztatott virtuális hálózatok ASN eltérőnek kell lennie a BGP és az átvitel útválasztásának engedélyezéséhez.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés – a TestVNet1 és a TestVNet2-átjárók összekötése
Ebben a példában mindkét átjáró ugyanahhoz az előfizetéshez tartozik. Ezt a lépést ugyanabban a PowerShell-munkamenetben végezheti el.

#### <a name="1-get-both-gateways"></a>1. mindkét átjáró lekérése
Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. mindkét kapcsolat létrehozása
Ebben a lépésben létrehozza a TestVNet1 és a TestVNet2 közötti kapcsolatokat, valamint a TestVNet2 és a TestVNet1 közötti kapcsolatokat.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Ügyeljen arra, hogy mindkét kapcsolat esetében engedélyezze a BGP-t.
> 
> 

A fenti lépések elvégzése után a kapcsolat néhány perc alatt létrejön, és a BGP-társas munkamenet akkor válik elérhetővé, ha a VNet-VNet kapcsolat befejeződött kettős redundanciával:

![aktív-aktív-V2V](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Meglévő VPN-átjáró frissítése

Ha egy aktív-készenléti átjárót aktív-aktívra vált, hozzon létre egy másik nyilvános IP-címet, majd adjon hozzá egy második átjáró IP-konfigurációt. Ez a szakasz segítséget nyújt egy meglévő Azure VPN-átjáró aktív-készenléti állapotból aktív-aktív módra való módosításához, vagy fordítva a PowerShell használatával. Az átjárót a virtuális hálózati átjáró **konfiguráció** lapján lévő Azure Portal is módosíthatja.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Aktív-készenléti átjáró módosítása aktív-aktív átjáróra

Az alábbi példa egy aktív-készenléti átjárót aktív-aktív átjáróra konvertál. 

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Cserélje le a példákhoz használt következő paramétereket a saját konfigurációhoz szükséges beállításokkal, majd deklarálja ezeket a változókat.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

A változók deklarálása után másolhatja és beillesztheti a példát a PowerShell-konzolba.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. hozza létre a nyilvános IP-címet, majd adja hozzá a második átjáró IP-konfigurációját

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. engedélyezze az aktív-aktív üzemmódot, és frissítse az átjárót

Ebben a lépésben engedélyezi az aktív-aktív üzemmódot, és frissíti az átjárót. A példában a VPN-átjáró jelenleg egy örökölt szabványos SKU-t használ. Az aktív-aktív azonban nem támogatja a standard SKU-t. Ha az örökölt SKU-t egy támogatottra (ebben az esetben HighPerformance) szeretné átméretezni, egyszerűen meg kell adnia a használni kívánt örökölt SKU-t.

* Ezzel a lépéssel nem módosítható örökölt SKU az egyik új SKU-ra. Egy örökölt SKU-t csak egy másik támogatott örökölt SKU-ra lehet átméretezni. Például az SKU-t nem lehet standardról VpnGw1-re módosítani (bár a VpnGw1 aktív-aktív), mert a standard egy örökölt SKU, és a VpnGw1 egy aktuális SKU. Az SKU-ra való átméretezéssel és áttelepítéssel kapcsolatos további információkért lásd: [átjáró SKU](vpn-gateway-about-vpngateways.md#gwsku)-i.

* Ha egy aktuális SKU-t szeretne átméretezni, például a VpnGw3 VpnGw1, ezt a lépést megteheti, mert a SKU-ban ugyanabban az SKU-család található. Ehhez a következő értéket kell használnia: ```-GatewaySku VpnGw3```

Ha ezt használja a környezetben, ha nem kell átméreteznie az átjárót, nem kell megadnia a-Gatewaysku paraméterben. Figyelje meg, hogy ebben a lépésben az átjáró objektumot a PowerShellben kell beállítani a tényleges frissítés elindításához. Ez a frissítés 30 – 45 percet is igénybe vehet, még akkor is, ha nem lakik az átjáró.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Aktív-aktív átjáró módosítása aktív-készenléti átjáróra
#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Cserélje le a példákhoz használt következő paramétereket a saját konfigurációhoz szükséges beállításokkal, majd deklarálja ezeket a változókat.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

A változók deklarálása után kérje le az eltávolítani kívánt IP-konfiguráció nevét.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. távolítsa el az átjáró IP-konfigurációját, és tiltsa le az aktív-aktív módot

Ez a példa az átjáró IP-konfigurációjának eltávolítására és az aktív-aktív üzemmód letiltására használható. Figyelje meg, hogy az átjáró objektumot a PowerShellben kell beállítani a tényleges frissítés elindításához.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Ez a frissítés akár 30 – 45 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
