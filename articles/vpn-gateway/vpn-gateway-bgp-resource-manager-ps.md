---
title: 'A BGP konfigurálása Azure VPN Gateway átjárókról: Erőforrás-kezelő: PowerShell |} A Microsoft Docs'
description: Ez a cikk végigvezeti a BGP konfigurálása Azure VPN Gateway Azure Resource Manager és a PowerShell használatával.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: c65ea038fc39702affae93cb68b8cf644393c62e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66150213"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>BGP konfigurálása Azure VPN Gateway PowerShell-lel
Ez a cikk végigvezeti a BGP engedélyezéséhez a létesítmények közötti Site-to-Site (S2S) VPN-kapcsolat és VNet – VNet kapcsolat a Resource Manager üzemi modell és a PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>A BGP ismertetése
A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. A BGP lehetővé teszi az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.

Lásd: [BGP áttekintése az Azure VPN-átjárókkal](vpn-gateway-bgp-overview.md) további vitafórum a előnyeiről BGP és technikai követelményeket és szempontokat kell figyelembe vennie a BGP használatával.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Az Azure VPN Gateway átjárókról BGP használatának első lépései

Ez a cikk végigvezeti a lépéseken, a következő feladatokat végezheti el:

* [1. rész – az Azure VPN gateway a BGP engedélyezése](#enablebgp)
* 2. rész – a BGP létesítmények közötti kapcsolat létrehozása
* [3. rész – a BGP VNet – VNet kapcsolat létrehozása](#v2vbgp)

Az egyes részek az utasításokat a hálózati kapcsolatot a BGP engedélyezéséhez építőeleme képezi. Ha elvégezte az összes három részből áll, létre a topológia az alábbi ábrán látható módon:

![BGP-topológiákkal kapcsolatban](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Kombinálhatja a részek együtt, és hozhat létre egy összetettebb, Többugrásos, átviteli hálózat, amely megfelel az igényeinek.

## <a name ="enablebgp"></a>1. rész – az Azure VPN Gateway a BGP konfigurálása
A konfigurációs lépések az Azure VPN gateway a BGP paramétereket állíthatja be az alábbi ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek
* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse az Azure Resource Manager PowerShell-parancsmagokat. További információ a PowerShell-parancsmagok telepítéséről: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>1. lépés – létrehozása és konfigurálása a VNet1
#### <a name="1-declare-your-variables"></a>1. A változók deklarálása
Ehhez a gyakorlathoz módszertan változók deklarálásával. Az alábbi példában használt deklarálja a változókat az értékeket a gyakorlatban. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Csatlakozás az előfizetéshez, és hozzon létre egy új erőforráscsoportot
A Resource Manager parancsmagjainak használatához győződjön meg arról, hogy váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása
Az alábbi minta létrehoz egy TestVNet1 és három alhálózatot, egy nevű átjáró-alhálózat, egy nevű előtérbeli és a egy nevű háttérbeli nevű virtuális hálózatot. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés – a VPN-átjáró létrehozása a testvnet1-hez a BGP-paraméterekkel
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Hozza létre az IP-cím és alhálózat-konfigurációit
Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. A szükséges alhálózatot és IP-konfigurációk is meghatározhatja.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Az AS-számot a VPN-átjáró létrehozása
Hozza létre a TestVNet1 virtuális hálózati átjáróját. BGP szükséges Útvonalalapú VPN-átjáró, valamint emellett paraméter - ASN-t, az ASN (AS-szám) beállítása a testvnet1-hez. Ha nincs beállítva az ASN-t paraméterrel, 65515 ASN szám van hozzárendelve. Az átjáró létrehozása akár 30 percet vagy hosszabb időt is igénybe vehet.

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-Társgép IP-cím beszerzése
Az átjáró létrehozása után meg kell szereznie az Azure VPN Gateway a BGP-Társgép IP-címe. Ez a cím az Azure VPN Gateway a BGP-társ számára a helyszíni VPN-eszközök konfigurálása van szükség.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Az utolsó parancs a megfelelő BGP-konfigurációk az Azure VPN gatewayen; jeleníti meg. Példa:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Az átjáró létrehozása után a létesítmények közötti vagy BGP-vel VNet – VNet-kapcsolatot létesíteni használhatja ezt az átjárót. A következő részek lépésről lépésre bemutatjuk a gyakorlatban végrehajtásához.

## <a name ="crossprembbgp"></a>2. rész – a BGP létesítmények közötti kapcsolat létrehozása

Létesítmények közötti kapcsolatot létesíteni, szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró és a egy kapcsolatot a VPN-átjáró összekapcsolása a helyi hálózati átjáró létrehozásához. Nincsenek cikkek, amelyek végig ezeket a lépéseket, amíg ez a cikk a BGP-konfigurációs paraméterek megadásához szükséges további tulajdonságokat tartalmazza.

![BGP-t létesítmények közötti esetében](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Mielőtt folytatná, győződjön meg arról, hogy befejezte [1. rész](#enablebgp) ebben a gyakorlatban.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>1. lépés – létrehozása és a helyi hálózati átjáró konfigurálása

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

Ebben a gyakorlatban továbbra is össze az ábrán látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Néhány dolgot a helyi hálózati átjáró paraméterek kapcsolatban vegye figyelembe:

* A helyi hálózati átjárót, a VPN-átjáró lehet az azonos vagy eltérő helyen és erőforráscsoportban. Ez a példa bemutatja azokat a különböző helyek eltérő erőforráscsoportokban.
* Az előtag, kell deklarálnia a helyi hálózati átjáró, a BGP-Társgép IP-cím a VPN-eszköz a gazdagép-címét. Ebben az esetben egy tulajdonságot/32 előtag "10.52.255.254/32".
* Ne feledje a helyszíni hálózat és az Azure VNet közötti különböző BGP ASN kell használnia. Ha azonos, módosítsa a virtuális hálózat ASN-t, ha a helyszíni VPN-eszköz már használja az ASN-t más BGP-szomszédok társviszonyt szeretne.

Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. A helyi hálózati átjáró létrehozása Site5

Győződjön meg arról, az erőforráscsoport létrehozása, ha nem jön létre, a helyi hálózati átjáró létrehozása előtt. Figyelje meg, hogy a helyi hálózati átjáró két további paraméterek: Az ASN és BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés – a virtuális hálózati átjáró és a helyi hálózati átjáró csatlakoztatása

#### <a name="1-get-the-two-gateways"></a>1. A két átjáró beolvasása

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. A TestVNet1 – Site5 kapcsolat létrehozása

Ebben a lépésben, a TestVNet1 felől a létre Site5. Meg kell adnia "-enablebgp paramétert $True" BGP engedélyezése ehhez a kapcsolathoz. Ahogy arra már korábban, akkor lehetséges, hogy ugyanazon Azure VPN Gateway a BGP- és -BGP kapcsolatokat. Kivéve, ha BGP engedélyezve van a kapcsolat tulajdonságban, az Azure nem engedélyezi BGP ezt a kapcsolatot annak ellenére, hogy a BGP-paraméterek már konfigurálva vannak az átjárók.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Az alábbi példa felsorolja a paramétereket ad meg, a BGP-konfigurációs szakasz ebben a gyakorlatban a helyszíni VPN-eszköz:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat létrejötte után néhány percet, és a BGP társviszony-létesítési munkamenet indítása után az IPsec-kapcsolat létrejött.

## <a name ="v2vbgp"></a>3. rész – a BGP VNet – VNet kapcsolat létrehozása

Ez a szakasz ad hozzá egy VNet – VNet kapcsolat BGP-vel, az alábbi ábrán látható módon:

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasításokat a leírt lépések folytatása. Meg kell adnia a [i. rész](#enablebgp) létrehozása és konfigurálása a TestVNet1 és a VPN Gateway a BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>1. lépés – TestVNet2 és a VPN-átjáró létrehozása

Fontos győződjön meg arról, hogy az IP-címtér az új TestVNet2, virtuális hálózat nem átfedésben a VNet-címtartományok.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetések között. További információkért lásd: [VNet – VNet kapcsolat konfigurálása](vpn-gateway-vnet-vnet-rm-ps.md). Mindenképpen adja hozzá a "-enablebgp paramétert $True" BGP engedélyezése a kapcsolatok létrehozásakor.

#### <a name="1-declare-your-variables"></a>1. A változók deklarálása

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 az új erőforráscsoport létrehozása

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. A VPN-átjáró létrehozása TestVNet2 BGP paraméterekkel

Kérjen egy nyilvános IP-cím kiosztását az átjáró a virtuális hálózat létrehoz, és adja meg a szükséges alhálózatot és IP-konfigurációk.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Hozzon létre a VPN-átjáró az AS-számot. Az Azure VPN Gateway átjárókról felül kell bírálnia az alapértelmezett ASN-t. Az ASN-eket a csatlakoztatott virtuális hálózat számára ahhoz, hogy a BGP és a tranzit útválasztást különbözőnek kell lennie.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés – a TestVNet1 és TestVNet2 átjárók csatlakoztatása

Ebben a példában mindkét átjáró ugyanabban az előfizetésben vannak. Ebben a lépésben a PowerShell-munkamenetben hajthatja végre.

#### <a name="1-get-both-gateways"></a>1. Mindkét átjárótípus beolvasása

Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Kapcsolatok létrehozása

Ebben a lépésben a TestVNet1 kapcsolat TestVNet2 és, és a kapcsolat felől létre TestVNet2 a testvnet1 felé.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Győződjön meg arról, hogy mindkét kapcsolat – BGP engedélyezve.
> 
> 

Ezek a lépések elvégzése után létrejön a kapcsolat néhány perc múlva. A BGP társviszony-létesítési munkamenet működik, ha a VNet – VNet kapcsolat befejeződött.

Ha befejezte az összes három részből ebben a gyakorlatban, létrehozta a következő hálózati topológia:

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>További lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
