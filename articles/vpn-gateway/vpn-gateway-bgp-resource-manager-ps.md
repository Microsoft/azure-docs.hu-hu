---
title: 'Azure VPN Gateway: a BGP konfigurálása: PowerShell'
description: Ez a cikk bemutatja, hogyan konfigurálhatja a BGP-t az Azure VPN Gateway Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659760"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>A BGP konfigurálása Azure VPN Gateway-átjárón a PowerShell használatával
Ez a cikk bemutatja, hogyan engedélyezheti a BGP-t a telephelyek közötti helyek közötti (S2S) VPN-kapcsolaton, valamint egy VNet és VNet kapcsolaton keresztül a Resource Manager-alapú üzemi modell és a PowerShell használatával.



## <a name="about-bgp"></a>A BGP ismertetése
A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. A BGP lehetővé teszi az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.

Lásd: a BGP és az [Azure VPN Gateway közötti áttekintés](vpn-gateway-bgp-overview.md) a BGP előnyeiről, valamint a BGP használatának technikai követelményeiről és szempontjairól.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>A BGP első lépései Azure VPN Gateway-átjárókkal

Ez a cikk végigvezeti a következő feladatok végrehajtásának lépésein:

* [1. rész – a BGP engedélyezése az Azure VPN-átjárón](#enablebgp)
* 2. rész – létesítmények közötti kapcsolat létrehozása BGP-vel
* [3. rész – VNet és VNet közötti kapcsolat létrehozása a BGP-vel](#v2vbgp)

Az utasítások mindegyik része egy alapszintű építőelemet képez, amely lehetővé teszi a BGP hálózati kapcsolaton belüli engedélyezését. Ha mindhárom részben elkészült, a következő ábrán látható módon kell felépíteni a topológiát:

![BGP-topológia](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

A részeket kombinálva egyesítheti az igényeinek megfelelő összetettebb, több ugrást biztosító átviteli hálózatot.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>1. rész – a BGP konfigurálása az Azure VPN Gateway
A konfigurációs lépések az Azure VPN Gateway BGP-paramétereit az alábbi ábrán látható módon állíthatják be:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse a Azure Resource Manager PowerShell-parancsmagokat. További információ a PowerShell-parancsmagok telepítéséről: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/). 

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – a VNet1 létrehozása és konfigurálása
#### <a name="1-declare-your-variables"></a>1. a változók deklarálása
Ebben a gyakorlatban először deklaráljuk a változókat. A következő példában deklaráljuk a változókat a gyakorlat értékei alapján. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. kapcsolódjon az előfizetéshez, és hozzon létre egy új erőforráscsoportot
A Resource Manager-parancsmagok használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. TestVNet1 létrehozása
Az alábbi minta egy TestVNet1 nevű virtuális hálózatot és három alhálózatot hoz létre, amelyek közül az egyik a GatewaySubnet, az egyik a FrontEnd, a másik pedig a háttérrendszer. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés – a TestVNet1 VPN Gateway létrehozása BGP-paraméterekkel
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. az IP-és alhálózati konfigurációk létrehozása
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Megadhatja a szükséges alhálózatot és IP-konfigurációkat is.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. hozza létre a VPN-átjárót az AS-számmal
Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP egy Route-Based VPN-átjárót, valamint a (z)-ASN paramétert is megköveteli, hogy beállítsa a TestVNet1 ASN (AS Number) értékét. Ha nem állítja be az ASN paramétert, a rendszer az ASN 65515-et rendeli hozzá. Az átjáró létrehozása akár 30 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. az Azure BGP-társ IP-címének beszerzése
Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN Gatewayon. Ez a címe szükséges ahhoz, hogy az Azure VPN Gateway BGP-társként konfigurálja a helyszíni VPN-eszközökhöz.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Az utolsó parancs megjeleníti a kapcsolódó BGP-konfigurációkat az Azure VPN Gateway; például:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Az átjáró létrehozása után ezt az átjárót használhatja a létesítmények közötti kapcsolat vagy VNet-VNet kapcsolat létrehozására a BGP használatával. Az alábbi szakaszokban ismertetjük a gyakorlat befejezésének lépéseit.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>2. rész – létesítmények közötti kapcsolat létrehozása BGP-vel

Létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót, amely a helyszíni VPN-eszközt jelöli, valamint egy kapcsolatot a VPN-átjáró helyi hálózati átjáróval való összekapcsolásához. Habár vannak olyan cikkek, amelyek végigvezetik ezeket a lépéseket, ez a cikk a BGP konfigurációs paramétereinek megadásához szükséges további tulajdonságokat tartalmazza.

![BGP a létesítmények közötti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

A folytatás előtt győződjön meg arról, hogy végrehajtotta a gyakorlat [1. részét](#enablebgp) .

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – a helyi hálózati átjáró létrehozása és konfigurálása

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Ez a gyakorlat továbbra is a diagramon látható konfiguráció összeállítását mutatja be. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Néhány tudnivaló a helyi hálózati átjáró paraméterekkel kapcsolatban:

* A helyi hálózati átjáró a VPN-átjáróval megegyező vagy eltérő helyen és erőforráscsoporthoz is lehet. Ebben a példában a különböző telephelyeken található különböző erőforráscsoportok láthatók.
* A helyi hálózati átjáróhoz deklarálni kívánt előtag a VPN-eszközön található BGP-társ IP-címének gazdagép-címe. Ebben az esetben ez a "10.52.255.254/32" a/32 előtag.
* Emlékeztetőként különböző BGP-ASN kell használnia a helyszíni hálózatok és az Azure-VNet között. Ha ezek megegyeznek, akkor módosítania kell a VNet ASN-t, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédokkal.

Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. hozza létre a helyi hálózati átjárót a site5 számára

A helyi hálózati átjáró létrehozása előtt ne felejtse el létrehozni az erőforráscsoportot, ha az nincs létrehozva. Figyelje meg a helyi hálózati átjáró két további paraméterét: ASN és BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a VNet-átjáró és a helyi hálózati átjáró összekötése

#### <a name="1-get-the-two-gateways"></a>1. a két átjáró beolvasása

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. hozza létre a TestVNet1 site5-kapcsolatban

Ebben a lépésben létrehozza a kapcsolódást a TestVNet1 és a site5 között. A BGP ehhez a kapcsolódáshoz való engedélyezéséhez meg kell adnia a "-EnableBGP $True" lehetőséget. Ahogy korábban már említettük, lehetséges, hogy a BGP és a nem BGP kapcsolatok is ugyanahhoz az Azure-VPN Gatewayhoz tartoznak. Ha a BGP engedélyezve van a kapcsolatok tulajdonságban, az Azure nem engedélyezi a BGP-t ehhez a hálózathoz, bár a BGP-paraméterek már konfigurálva vannak mindkét átjárón.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Az alábbi példa azokat a paramétereket sorolja fel, amelyeket a helyszíni VPN-eszköz BGP-konfiguráció szakasza tartalmaz a következő gyakorlathoz:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány percen belül létrejön, és a BGP-társas munkamenet elindul az IPsec-kapcsolat létrehozása után.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>3. rész – VNet és VNet közötti kapcsolat létrehozása a BGP-vel

Ez a szakasz a BGP-vel való VNet-VNet, az alábbi ábrán látható módon:

![A V net és a V közötti hálózati kapcsolatokat bemutató diagram.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások az előző lépésekkel folytatódnak. Be kell fejeznie a TestVNet1 létrehozásához és konfigurálásához, valamint a BGP-vel való VPN Gatewayához szükséges [részt](#enablebgp) . 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – a TestVNet2 és a VPN-átjáró létrehozása

Fontos annak biztosítása, hogy az új virtuális hálózat (TestVNet2) IP-címének mérete ne legyen átfedésben a VNet-tartományokkal.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. A különböző előfizetések közötti VNet-VNet kapcsolatokat is beállíthat. További információ: [VNet-to-VNet-kapcsolatok konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md). Győződjön meg arról, hogy hozzáadja a "-EnableBgp $True" kifejezést a BGP-t engedélyező kapcsolatok létrehozásakor.

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. hozzon létre TestVNet2 az új erőforráscsoporthoz

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. a TestVNet2 VPN Gateway létrehozása BGP-paraméterekkel

Igényeljen egy nyilvános IP-címet a VNet létrehozandó átjáróhoz, és adja meg a szükséges alhálózatot és IP-konfigurációkat.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Hozza létre a VPN-átjárót a AS számmal. Az alapértelmezett ASN-t felül kell bírálnia az Azure VPN Gateway-átjárón. A csatlakoztatott virtuális hálózatok ASN eltérőnek kell lennie a BGP és az átvitel útválasztásának engedélyezéséhez.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés – a TestVNet1 és a TestVNet2-átjárók összekötése

Ebben a példában mindkét átjáró ugyanahhoz az előfizetéshez tartozik. Ezt a lépést ugyanabban a PowerShell-munkamenetben végezheti el.

#### <a name="1-get-both-gateways"></a>1. mindkét átjáró lekérése

Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. mindkét kapcsolat létrehozása

Ebben a lépésben létrehozza a TestVNet1 és a TestVNet2 közötti kapcsolatokat, valamint a TestVNet2 és a TestVNet1 közötti kapcsolatokat.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Ügyeljen arra, hogy mindkét kapcsolat esetében engedélyezze a BGP-t.
> 
> 

A fenti lépések elvégzése után a rendszer néhány percen belül meghozza a kapcsolatokat. A BGP-társas munkamenet a VNet-VNet kapcsolat befejezése után jön létre.

Ha befejezte a gyakorlat mindhárom részét, a következő hálózati topológiát hozta:

![BGP VNet – VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).