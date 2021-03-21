---
title: IPsec/IKE-szabályzat a S2S VPN & VNet – VNet kapcsolatok
titleSuffix: Azure VPN Gateway
description: Konfigurálja az IPsec/IKE-szabályzatot az Azure VPN Gateway Azure Resource Manager és a PowerShell használatával történő S2S-vagy VNet-VNet-kapcsolatokhoz.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94649262"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>IPsec/IKE-szabályzat S2S VPN- vagy VNet–VNet-kapcsolatokhoz történő konfigurálása

Ez a cikk végigvezeti az IPsec/IKE-házirend konfigurálásának lépésein a helyek közötti VPN-vagy VNet-VNet kapcsolatokhoz a Resource Manager-alapú üzemi modell és a PowerShell használatával.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>Tudnivalók az Azure VPN-átjárók IPsec-és IKE-szabályzati paramétereinek használatáról
Az IPsec és az IKE protokoll standard verziója számos titkosítási algoritmust támogat különböző kombinációkban. A [titkosítási követelményekkel és az Azure VPN-átjárókkal kapcsolatos információkért](vpn-gateway-about-compliance-crypto.md) tekintse meg, hogy ez hogyan segítheti a létesítmények közötti és VNet kapcsolatok biztosítását a megfelelőségi vagy biztonsági követelmények kielégítése érdekében.

Ez a cikk útmutatást nyújt az IPsec/IKE-szabályzatok létrehozásához és konfigurálásához, valamint az új vagy meglévő kapcsolatok alkalmazásához:

* [1. rész – az IPsec/IKE-házirend létrehozásához és beállításához szükséges munkafolyamat](#workflow)
* [2. rész – támogatott titkosítási algoritmusok és fő erősségek](#params)
* [3. rész – új S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel](#crossprem)
* [4. rész – új VNet-VNet kapcsolat létrehozása IPsec/IKE-házirenddel](#vnet2vnet)
* [5. rész – a kapcsolat IPsec/IKE-szabályzatának kezelése (létrehozás, Hozzáadás, eltávolítás)](#managepolicy)

> [!IMPORTANT]
> 1. Vegye figyelembe, hogy az IPsec/IKE-szabályzat csak a következő átjárók esetében működik:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (Route-based)
>    * ***Standard** _ és _ *_HighPerformance_** (Route-based)
> 2. Egy adott kapcsolathoz csak ***egy*** házirendet adhat meg.
> 3. Meg kell adnia az összes algoritmust és paramétert mind az IKE (Main Mode), mind az IPsec (gyors mód) esetében. A részleges házirend-megadás nem engedélyezett.
> 4. A VPN-eszközök gyártójának specifikációit megkeresve ellenőrizze, hogy a helyi VPN-eszközökön támogatott-e a házirend. A S2S-vagy VNet-VNet kapcsolatok nem tudják létrehozni, ha a házirendek nem kompatibilisek.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>1. rész – az IPsec/IKE-házirend létrehozásához és beállításához szükséges munkafolyamat
Ez a szakasz az IPsec/IKE-házirend létrehozásához és frissítéséhez szükséges munkafolyamatot ismerteti egy S2S VPN-vagy VNet-VNet kapcsolaton:
1. Virtuális hálózat és VPN-átjáró létrehozása
2. Helyi hálózati átjáró létrehozása a létesítmények közötti kapcsolatok eléréséhez, vagy egy másik virtuális hálózat és átjáró VNet-VNet való kapcsolódáshoz
3. IPsec/IKE-szabályzat létrehozása a kiválasztott algoritmusokkal és paraméterekkel
4. Hozzon létre egy (IPsec-vagy VNet2VNet-) kapcsolatokat az IPsec/IKE-házirenddel
5. IPsec/IKE-házirend hozzáadása/frissítése vagy eltávolítása meglévő kapcsolatok esetén

Az ebben a cikkben szereplő utasítások segítséget nyújt az IPsec/IKE-szabályzatok beállításához és konfigurálásához a következő ábrán látható módon:

![IPSec-IKE-Policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>2. rész – támogatott titkosítási algoritmusok & a kulcs erősségeit

A következő táblázat felsorolja az ügyfelek által konfigurálható támogatott titkosítási algoritmusokat és főbb erősségeket:

| **IPsec/IKEv2**  | **Beállítások**    |
| ---  | --- 
| IKEv2-titkosítás | AES256, AES192, AES128, DES3, DES  
| IKEv2-integritás  | SHA384, MD5, SHA1, SHA256  |
| DH-csoport         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, nincs |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nincs    |
| IPsec-integritás  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-csoport        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nincs 
| Gyorsmódú biztonsági társítás élettartama   | (Nem **kötelező**: Ha nincs megadva, az alapértelmezett értékek szerepelnek)<br>Másodperc (egész szám; **min. 300**/alapértelmezett érték: 27000 másodperc)<br>KB (egész szám; **min. 1024**/alapértelmezett érték: 102400000 KB)   |
| Forgalomválasztó | UsePolicyBasedTrafficSelectors * * ($True/$False; Nem **kötelező**, alapértelmezett $false, ha nincs megadva)    |
|  |  |

> [!IMPORTANT]
> 1. **A helyszíni VPN-eszköz konfigurációjának meg kell egyezniük velük, vagy tartalmazniuk kell az alábbi, az Azure IPsec/IKE-házirendben megadott algoritmusokat és paramétereket:**
>    * IKE titkosítási algoritmus (fő mód/1. fázis)
>    * IKE integritási algoritmus (fő mód/1. fázis)
>    * DH-csoport (fő mód/1. fázis)
>    * IPsec titkosítási algoritmus (gyors mód/2. fázis)
>    * IPsec-integritási algoritmus (gyors mód/2. fázis)
>    * PFS-csoport (gyors mód/2. fázis)
>    * Forgalmi választó (ha UsePolicyBasedTrafficSelectors van használatban)
>    * AZ SA-k élettartamai helyi szinten definiáltak, így azoknak nem kell megegyezniük.
>
> 2. **Ha a GCMAES-t IPsec titkosítási algoritmusként használja, ki kell választania ugyanazt a GCMAES algoritmust és a kulcs hosszát az IPsec-integritáshoz; például a GCMAES128 használata a következőhöz:**
> 3. A fenti táblázatban:
>    * A IKEv2 felel meg a Main Mode vagy az 1. fázisnak.
>    * Az IPsec megfelel a gyors vagy a 2. fázisnak
>    * A DH-csoport a főmódban vagy az 1. fázisban használt Diffie-Hellmen csoportot határozza meg.
>    * A PFS-csoport a Diffie-Hellmen a gyors módban vagy a 2. fázisban használt csoportot adta meg
> 4. Az IKEv2 fő módú biztonsági hozzárendelés élettartama 28 800 másodpercen van rögzítve az Azure VPN-átjárókon
> 5. A "UsePolicyBasedTrafficSelectors" beállítása a kapcsolaton keresztüli $True az Azure VPN-átjárót úgy konfigurálja, hogy a helyi házirend-alapú VPN-tűzfalhoz kapcsolódjon. Ha engedélyezi a PolicyBasedTrafficSelectors-t, gondoskodnia kell arról, hogy a VPN-eszköz megfelelő forgalmi választókkal rendelkezik, amelyek a helyszíni hálózat (helyi hálózati átjáró) előtagjainak az Azure Virtual Network előtagjaihoz tartozó összes kombinációját megadják, nem pedig bármely-a-any érték helyett. Például ha a helyszíni hálózati előtagok a 10.1.0.0/16 és a 10.2.0.0/16, a virtuális hálózati előtagok pedig 192.168.0.0/16 és 172.16.0.0/16, az alábbi forgalomválasztókat kell megadnia:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

A házirend-alapú forgalmi választókkal kapcsolatos további információkért lásd: [több helyszíni házirend-alapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md).

A következő táblázat felsorolja az egyéni házirend által támogatott megfelelő Diffie-Hellman csoportokat:

| **Diffie-Hellman Group**  | **DH-csoport**              | **PFS-csoport** | **A kulcs hossza** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 bites MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 bites MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 bites MODP  |
| 19                        | ECP256                   | ECP256       | 256 bites ECP    |
| 20                        | ECP384                   | ECP384       | 384 bites ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 bites MODP  |

További részletekért lásd: [RFC3526](https://tools.ietf.org/html/rfc3526) és [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>3. rész – új S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

Ez a szakasz végigvezeti a S2S VPN-kapcsolat IPsec/IKE-házirenddel való létrehozásának lépésein. A következő lépésekkel hozza létre a kapcsolatokat a diagramon látható módon:

![S2S – szabályzat](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

A S2S VPN-kapcsolat létrehozásával kapcsolatos részletes útmutatásért tekintse meg [a S2S VPN-kapcsolat létrehozása](vpn-gateway-create-site-to-site-rm-powershell.md) című témakört.

### <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

* Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Telepítse a Azure Resource Manager PowerShell-parancsmagokat. A PowerShell-parancsmagok telepítésével kapcsolatos további információkért tekintse meg a [Azure PowerShell áttekintése](/powershell/azure/) című témakört.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>1. lépés – a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Ebben a gyakorlatban először deklaráljuk a változókat. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. kapcsolódjon az előfizetéshez, és hozzon létre egy új erőforráscsoportot

A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md).

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. a virtuális hálózat, a VPN-átjáró és a helyi hálózati átjáró létrehozása

A következő minta létrehozza a virtuális hálózatot, a TestVNet1 és a három alhálózatot, valamint a VPN-átjárót. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>2. lépés – S2S VPN-kapcsolat létrehozása IPsec/IKE-házirenddel

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-szabályzat létrehozása

Az alábbi parancsfájl egy IPsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS nincs, SA élettartama 14400 másodperc & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Ha IPsec-GCMAES használ, ugyanazt a GCMAES algoritmust és a kulcs hosszát kell használnia az IPsec-titkosítás és az integritás esetében is. A fentiekben például a megfelelő paraméterek a "-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256" lesznek a GCMAES256 használatakor.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. a S2S VPN-kapcsolat létrehozása az IPsec/IKE-házirenddel

Hozzon létre egy S2S VPN-kapcsolatát, és alkalmazza a korábban létrehozott IPsec/IKE-házirendet.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Opcionálisan hozzáadhat "-UsePolicyBasedTrafficSelectors $True" parancsot a kapcsolat létrehozása parancsmaghoz, amely lehetővé teszi az Azure VPN Gateway számára, hogy a fentiekben leírtak szerint kapcsolódjon a házirend alapú VPN-eszközökhöz a helyszínen.

> [!IMPORTANT]
> Ha egy kapcsolathoz IPsec/IKE-házirend van megadva, az Azure VPN Gateway csak az IPsec/IKE-javaslatot küldi el vagy fogadja el az adott kapcsolaton megadott titkosítási algoritmusokkal és fő erősségekkel. Győződjön meg arról, hogy a kapcsolat a helyszíni VPN-eszközt használja, vagy fogadja el a pontos szabályzat-kombinációt, ellenkező esetben a S2S VPN-alagút nem jön létre.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>4. rész – új VNet-VNet kapcsolat létrehozása IPsec/IKE-házirenddel

Az IPsec/IKE-szabályzattal VNet és VNet közötti kapcsolat létrehozásának lépései hasonlóak a S2S VPN-kapcsolatokhoz. A következő parancsfájl létrehozza a kapcsolatokat a diagramon látható módon:

![V2V – szabályzat](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

A VNet-VNet kapcsolatok létrehozásának részletes lépéseiért lásd: [VNet-to-VNet-kapcsolatok létrehozása](vpn-gateway-vnet-vnet-rm-ps.md) . A TestVNet1 és a VPN Gateway létrehozásához és konfigurálásához el kell végeznie a [3. részt](#crossprem) .

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>1. lépés – a második virtuális hálózat és a VPN-átjáró létrehozása

#### <a name="1-declare-your-variables"></a>1. a változók deklarálása

Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. hozza létre a második virtuális hálózatot és a VPN-átjárót az új erőforráscsoporthoz

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>2. lépés – VNet-toVNet-kapcsolatok létrehozása az IPsec/IKE-házirenddel

A S2S VPN-kapcsolathoz hasonlóan hozzon létre egy IPsec/IKE-házirendet, majd alkalmazza a házirendet az új kapcsolatra.

#### <a name="1-create-an-ipsecike-policy"></a>1. IPsec/IKE-szabályzat létrehozása

Az alábbi mintaszkript egy eltérő IPsec/IKE-szabályzatot hoz létre a következő algoritmusokkal és paraméterekkel:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA-élettartam 14400 másodperc & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. VNet és VNet közötti kapcsolatok létrehozása az IPsec/IKE-házirenddel

Hozzon létre egy VNet-VNet-kapcsolódást, és alkalmazza a létrehozott IPsec/IKE-házirendet. Ebben a példában mindkét átjáró ugyanahhoz az előfizetéshez tartozik. Így mindkét kapcsolat ugyanazzal az IPsec/IKE-házirenddel hozható létre és konfigurálható ugyanabban a PowerShell-munkamenetben.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Ha egy kapcsolathoz IPsec/IKE-házirend van megadva, az Azure VPN Gateway csak az IPsec/IKE-javaslatot küldi el vagy fogadja el az adott kapcsolaton megadott titkosítási algoritmusokkal és fő erősségekkel. Győződjön meg arról, hogy mindkét kapcsolat IPsec-házirendjei azonosak, ellenkező esetben a VNet – VNet kapcsolat nem jön létre.

A fenti lépések elvégzése után a rendszer néhány percen belül kialakítja a kapcsolatokat, és a következő hálózati topológiával fog rendelkezni, amint az elején látható:

![IPSec-IKE-Policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>5. rész – az IPsec/IKE-házirend frissítése egy kapcsolathoz

Az utolsó szakaszban megtudhatja, hogyan kezelheti az IPsec/IKE-házirendet egy meglévő S2S vagy VNet-VNet-kapcsolatok esetében. Az alábbi gyakorlat végigvezeti a következő műveleteken egy adott kapcsolatban:

1. Kapcsolat IPsec/IKE-házirendjének megjelenítése
2. IPsec/IKE-házirend hozzáadása vagy frissítése egy kapcsolatban
3. IPsec/IKE-házirend eltávolítása egy kapcsolatban

Ugyanezek a lépések mind a S2S, mind a VNet-VNet kapcsolatra érvényesek.

> [!IMPORTANT]
> Az IPsec/IKE-házirend csak a *standard* és a *HighPerformance* Route-alapú VPN-átjárók esetében támogatott. Nem működik az alapszintű átjáró SKU-ban vagy a házirend-alapú VPN-átjárón.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. a kapcsolat IPsec/IKE-szabályzatának megjelenítése

Az alábbi példa bemutatja, hogyan kérheti le az IPsec/IKE-házirendet egy adott kapcsolatban. A szkriptek továbbra is a fenti gyakorlatokból származnak.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó parancs felsorolja a kapcsolatban konfigurált aktuális IPsec/IKE-házirendet, ha van ilyen. A következő példa a kapcsolatok kimenetét jeleníti meg:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Ha nincs konfigurálva IPsec/IKE-házirend, a parancs (PS> $connection 6. A IpsecPolicies) üres értéket ad vissza. Nem jelenti azt, hogy az IPsec/IKE nincs konfigurálva a kapcsolatban, de nincs Egyéni IPsec/IKE-házirend. A tényleges kapcsolat a helyszíni VPN-eszköz és az Azure VPN Gateway között egyeztetett alapértelmezett házirendet használja.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. IPsec/IKE-házirend hozzáadása vagy frissítése a kapcsolatok számára

Az új szabályzat hozzáadásának vagy egy meglévő házirend frissítésének lépései azonosak: hozzon létre egy új szabályzatot, majd alkalmazza az új házirendet a kapcsolódásra.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Ha a helyi házirend-alapú VPN-eszközhöz való csatlakozáskor engedélyezni szeretné a "UsePolicyBasedTrafficSelectors" beállítást, adja hozzá a "-UsePolicyBaseTrafficSelectors" paramétert a parancsmaghoz, vagy állítsa be $Falsere a beállítás letiltásához:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

A kapcsolódás újbóli beszerzésével ellenőrizhető, hogy a házirend frissítve van-e.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Az utolsó sorból származó kimenetnek az alábbi példában látható módon kell megjelennie:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. IPsec/IKE-szabályzat eltávolítása egy kapcsolatban

Miután eltávolította az egyéni házirendet egy kapcsolatból, az Azure VPN Gateway visszavált az [IPSec/IKE-javaslatok alapértelmezett listájára](vpn-gateway-about-vpn-devices.md) , és újból egyezteti a helyszíni VPN-eszközzel.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Ugyanazzal a parancsfájllal ellenőrizhető, hogy a házirend el lett-e távolítva a kapcsolatban.

## <a name="next-steps"></a>Következő lépések

További részletekért lásd: [több helyszíni házirend-alapú VPN-eszköz csatlakoztatása](vpn-gateway-connect-multiple-policybased-rm-ps.md) a házirend-alapú forgalmi választókkal kapcsolatban.

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).