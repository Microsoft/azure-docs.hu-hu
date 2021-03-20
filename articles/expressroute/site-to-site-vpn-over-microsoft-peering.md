---
title: 'Azure-ExpressRoute: S2S VPN konfigurálása Microsoft-partneri kapcsolaton keresztül'
description: Az IPsec/IKE-kapcsolat konfigurálása az Azure-hoz egy ExpressRoute-alapú Microsoft-partneri áramkör használatával helyek közötti VPN-átjáróval.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393139"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Helyek közötti VPN konfigurálása ExpressRoute Microsoft-partneri kapcsolaton keresztül

Ez a cikk segítséget nyújt a biztonságos titkosított kapcsolatok konfigurálásához a helyszíni hálózat és az Azure Virtual Network (virtuális hálózatok) között egy ExpressRoute privát kapcsolaton keresztül. A Microsoft társközi szolgáltatásával létrehozhat egy helyek közötti IPsec/IKE VPN-alagutat a kiválasztott helyszíni hálózatok és az Azure-virtuális hálózatok között. A biztonságos alagút ExpressRoute-en keresztüli konfigurálása lehetővé teszi az adatcserét a bizalmas kezelés, a visszajátszás elleni védelem, a hitelesség és az integritás érdekében.

>[!NOTE]
>A helyek közötti VPN Microsoft-kapcsolaton keresztüli beállításakor a VPN-átjáró és a VPN kimenő forgalom díját számítjuk fel. További információ: [VPN Gateway díjszabása](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektúra


  ![a kapcsolatok áttekintése](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


A magas rendelkezésre állás és a redundancia érdekében több alagutat is beállíthat egy ExpressRoute-áramkör két MSEE-PE pár felett, és engedélyezheti az alagutak közötti terheléselosztást.

  ![magas rendelkezésre állási lehetőségek](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

A Microsoft-partneri VPN-alagutak a VPN Gateway használatával vagy az Azure Marketplace-en keresztül elérhető megfelelő hálózati virtuális berendezéssel (NVA) állíthatók le. Az útvonalakat statikusan vagy dinamikusan is átválthatja a titkosított alagutakon anélkül, hogy az átirányítja az alapul szolgáló Microsoft-társat. A jelen cikkben szereplő példákban a BGP (a Microsoft-társ létrehozásához használt BGP-munkamenettől eltérő) használatával dinamikusan cseréli az előtagokat a titkosított alagutakon.

>[!IMPORTANT]
>A helyszíni oldalon általában a Microsoft-társak megszakadnak a DMZ-ben, és az alaphálózati zónában megszűnik a privát levelezés. A két zóna el lesz különítve a tűzfalak használatával. Ha kizárólag a biztonságos bújtatás ExpressRoute való engedélyezését konfigurálja a Microsoft-társak számára, ne feledje, hogy csak azon nyilvános IP-címekre kell szűrnie, amelyeket a Microsoft-társon keresztül hirdettek meg.
>
>

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

1. Konfigurálja a Microsoft-társat a ExpressRoute-áramkörhöz.
2. A kiválasztott Azure regionális nyilvános előtagok reklámozása a helyszíni hálózatra Microsoft-partneri kapcsolaton keresztül.
3. VPN-átjáró konfigurálása és IPsec-alagutak létrehozása
4. Konfigurálja a helyszíni VPN-eszközt.
5. Hozza létre a helyek közötti IPsec/IKE-kapcsolatokat.
6. Választható Konfigurálja a tűzfalakat és a szűrést a helyszíni VPN-eszközön.
7. Tesztelje és ellenőrizze az IPsec-kommunikációt a ExpressRoute áramkörön.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. a Microsoft-társak konfigurálása

A helyek közötti VPN-kapcsolat ExpressRoute-en keresztüli konfigurálásához a ExpressRoute Microsoft-társait kell használnia.

* Új ExpressRoute-kör konfigurálásához Kezdje a [ExpressRoute előfeltételeinek](expressroute-prerequisites.md) leírásával, majd [hozzon létre és módosítson egy ExpressRoute-áramkört](expressroute-howto-circuit-arm.md).

* Ha már van ExpressRoute-áramköre, de nincs Microsoft-társa konfigurálva, konfigurálja a Microsoft-társat a [ExpressRoute-áramkör létrehozási és módosítási társításának](expressroute-howto-routing-arm.md#msft) használatával.

Miután konfigurálta az áramkört és a Microsoft-társat, könnyedén megtekintheti a Azure Portal **Áttekintés** lapján.

![kapcsolatcsoport](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. útválasztási szűrők konfigurálása

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezési lista az összes BGP közösségi értékről. 

![útvonal szűrője](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Ebben a példában a központi telepítés csak az *USA 2. nyugati* régiójában található. Az útvonal-szűrési szabály hozzáadása lehetővé teszi, hogy csak az Azure West US 2 regionális előtagjainak hirdetményét engedélyezze, amelynek a BGP közösségi értéke *12076:51026*. A **szabály kezelése** lehetőség kiválasztásával adhatja meg a regionális előtagokat, amelyeket engedélyezni szeretne.

Az útvonal-szűrőn belül ki kell választania azokat a ExpressRoute-áramköröket, amelyekhez az útválasztási szűrő vonatkozik. A ExpressRoute-áramköröket az **áramkör hozzáadása** lehetőség kiválasztásával választhatja ki. Az előző ábrán az útválasztási szűrő a példa ExpressRoute áramkörhöz van társítva.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 az útvonal-szűrő konfigurálása

Útválasztási szűrő konfigurálása A lépéseket lásd: [útválasztási szűrők konfigurálása a Microsoft-társak számára](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2 BGP-útvonalak ellenőrzése

Miután sikeresen létrehozott egy Microsoft-társat a ExpressRoute áramkörön, és hozzárendelt egy útvonal-szűrőt az áramkörhöz, ellenőrizheti, hogy a Msee érkező BGP-útvonalak a Msee-vel egyenrangú PE-eszközökön találhatók-e. Az ellenőrzési parancs a PE-eszközök operációs rendszerének függvényében változhat.

#### <a name="cisco-examples"></a>Cisco-példák

Ez a példa egy Cisco IOS-XE parancsot használ. A példában a rendszer egy virtuális útválasztási és továbbítási (VRF) példányt használ a társítási forgalom elkülönítésére.

```
show ip bgp vpnv4 vrf 10 summary
```

A következő részleges kimenet azt mutatja, hogy 68 előtag érkezett a szomszédból \* . 243.229.34 az ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

A szomszédból fogadott előtagok listájának megtekintéséhez használja a következő példát:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Annak ellenőrzéséhez, hogy az előtagok megfelelő készletét kapja-e, megtekintheti a többit. A következő Azure PowerShell parancs kimenete felsorolja az egyes szolgáltatásokhoz és az egyes Azure-régiókhoz tartozó Microsoft-társon keresztül meghirdetett előtagokat:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. a VPN-átjáró és az IPsec-alagutak konfigurálása

Ebben a szakaszban az IPsec VPN-alagutak jönnek létre az Azure VPN Gateway és a helyszíni VPN-eszköz között. A példák a Cisco Cloud Service router (CSR1000) VPN-eszközöket használják.

Az alábbi ábrán a helyszíni VPN-eszköz 1 és az Azure VPN Gateway instance pár között létrejött IPsec VPN-alagutak láthatók. A helyszíni VPN-eszköz 2 és az Azure VPN Gateway instance pár között létrejött két IPsec VPN-alagút nem szerepel a diagramon, és a konfigurációs adatok nem szerepelnek a felsorolásban. Ugyanakkor a további VPN-alagutak is javítják a magas rendelkezésre állást.

  ![VPN-alagutak](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Az IPsec-alagút pár alatt létrejön egy eBGP-munkamenet a magánhálózati útvonalak cseréjéhez. Az alábbi ábrán az IPsec-alagút pár alatt létesített eBGP-munkamenet látható:

  ![eBGP-munkamenetek bújtatási párral](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Az alábbi ábrán a példa hálózatának absztrakt áttekintése látható:

  ![Példa hálózatra](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Példák a Azure Resource Manager sablonra

A példákban a VPN-átjáró és az IPsec-alagút megszakításai egy Azure Resource Manager sablonnal konfigurálhatók. Ha a Resource Manager-sablonokkal kapcsolatos újdonságokkal vagy a Resource Manager-sablonok alapjaival kapcsolatos ismeretekkel rendelkezik, tekintse meg [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](../azure-resource-manager/templates/template-syntax.md)ismertető cikket. Az ebben a szakaszban található sablon egy zöldmezős Azure-környezetet (VNet) hoz létre. Ha azonban van meglévő VNet, hivatkozhat rá a sablonban. Ha nem ismeri a VPN Gateway IPsec/IKE helyek közötti konfigurációkat, tekintse meg [a helyek közötti kapcsolat létrehozása](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)című témakört.

>[!NOTE]
>Ennek a konfigurációnak a létrehozásához nincs szükség Azure Resource Manager-sablonok használatára. Ezt a konfigurációt a Azure Portal vagy a PowerShell használatával is létrehozhatja.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1 a változók deklarálása

Ebben a példában a változó deklarációi a példában szereplő hálózatnak felelnek meg. Változók deklarálása esetén módosítsa ezt a szakaszt a környezetének megfelelően.

* A **localAddressPrefix** változó a helyszíni IP-címek egy tömbje, amely megszünteti az IPSec-alagutakat.
* A **gatewaysku paraméterben** határozza meg a VPN átviteli sebességét. A Gatewaysku paraméterben és a vpnType kapcsolatos további információkért lásd: [VPN Gateway konfigurációs beállítások](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). A díjszabással kapcsolatban lásd: [VPN Gateway díjszabása](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Állítsa a **VpnType** **útvonalalapú** értékre.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 virtuális hálózat létrehozása (VNet)

Ha egy meglévő VNet társít a VPN-alagutakhoz, akkor kihagyhatja ezt a lépést.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 nyilvános IP-címek kiosztása VPN Gateway-példányokhoz
 
Rendeljen hozzá egy nyilvános IP-címet a VPN-átjáró minden példányához.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 a helyszíni VPN-alagút lezárásának (helyi hálózati átjáró) meghatározása

A helyszíni VPN-eszközöket a **helyi hálózati átjárónak** nevezzük. A következő JSON-kódrészlet a távoli BGP-társak adatait is megadja:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 a VPN-átjáró létrehozása

A sablon ezen szakasza konfigurálja a VPN-átjárót az aktív-aktív konfigurációhoz szükséges beállításokkal. Tartsa szem előtt az alábbi követelményeket:

* Hozza létre a VPN-átjárót egy **"útvonalalapú"** VpnType. Ez a beállítás kötelező, ha engedélyezni szeretné a BGP-útválasztást a VPN-átjáró és a helyszíni VPN között.
* Ahhoz, hogy VPN-alagutakat hozzon létre a VPN-átjáró két példánya és egy adott helyszíni eszköz aktív-aktív módban, a **"aktív"** paraméter **true** értékre van állítva a Resource Manager-sablonban. Ha többet szeretne megtudni a rendelkezésre álló VPN-átjárókkal kapcsolatban, tekintse meg a [rendelkezésre álló VPN Gateway-kapcsolat](../vpn-gateway/vpn-gateway-highlyavailable.md)című témakört.
* A VPN-alagutak közötti eBGP-munkamenetek konfigurálásához két különböző ASN kell megadnia mindkét oldalon. A privát ASN-számok megadására érdemes. További információ: [a BGP és az Azure VPN Gateway áttekintése](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 az IPsec-alagutak létrehozása

A szkript végső művelete IPsec-alagutakat hoz létre az Azure VPN Gateway és a helyszíni VPN-eszköz között.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. a helyszíni VPN-eszköz konfigurálása

Az Azure VPN Gateway számos különböző gyártótól származó VPN-eszközzel kompatibilis. A VPN-átjáróval való együttműködésre ellenőrzött konfigurációs információk és eszközök: [Tudnivalók a VPN-eszközökről](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

A VPN-eszköz konfigurálásakor a következő elemek szükségesek:

* Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor adott meg. A példák egy Alapszintű megosztott kulcsot használnak. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A VPN-átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének Azure Portal használatával történő megkereséséhez válassza a Virtual network gateways (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére.

Általában a eBGP-partnerek közvetlenül kapcsolódnak (gyakran WAN-kapcsolaton keresztül). Ha azonban IPsec VPN-alagutakon keresztül konfigurálja az eBGP-t a Microsoft ExpressRoute keresztül, több útválasztási tartomány van a eBGP-partnerek között. A **ebgp-multihop** parancs használatával hozza létre a két nem közvetlenül csatlakoztatott társ közötti ebgp szomszédos kapcsolatot. Az ebgp-multihop parancsot követő egész szám határozza meg a BGP-csomagok ÉLETTARTAMának értékét. A parancs **maximális elérési útjai eibgp 2** lehetővé teszi a forgalom terheléselosztását a két BGP-útvonal között.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000 – példa

Az alábbi példa a Cisco CSR1000 konfigurációját mutatja be egy Hyper-V virtuális gépen a helyszíni VPN-eszközként:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. konfigurálja a VPN-eszközök szűrését és a tűzfalakat (nem kötelező)

Konfigurálja a tűzfalat és a szűrést az igényeinek megfelelően.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. az IPsec-alagút tesztelése és érvényesítése

Az IPsec-alagutak állapota ellenőrizhető az Azure VPN-átjárón a PowerShell-parancsok használatával:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Példa a kimenetre:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Ha az Azure VPN Gateway-példányokon lévő alagutak állapotát egymástól függetlenül szeretné megnézni, használja a következő példát:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Példa a kimenetre:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Az alagút állapotát a helyszíni VPN-eszközön is megtekintheti.

Cisco CSR1000-példa:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Példa a kimenetre:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

A virtuális alagút felületén (VTI) lévő Line protokoll nem módosul "up" értékre, amíg az IKE 2. fázisa nem fejeződött be. A következő parancs ellenőrzi a biztonsági társítást:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>A helyszíni hálózaton belüli és az Azure-VNet közötti végpontok közötti kapcsolat ellenőrzése

Ha az IPsec-alagutak működnek, és a statikus útvonalak megfelelően vannak beállítva, a távoli BGP-társ IP-címének pingelését el kell tudnia érni:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>A BGP-munkamenetek ellenőrzése az IPsec protokollon keresztül

Az Azure VPN gatewayben ellenőrizze a BGP-társ állapotát:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Példa a kimenetre:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

A VPN-sűrítőtől a helyszíni eBGP keresztül fogadott hálózati előtagok listájának ellenőrzéséhez a "forrás" attribútum alapján szűrheti a következőt:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

A példában a kimenetben az ASN 65010 a helyi VPN-ben lévő BGP autonóm rendszer száma.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

A hirdetett útvonalak listájának megtekintéséhez:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Példa a kimenetre:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Példa a helyszíni Cisco CSR1000:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

A helyszíni Cisco CSR1000 az Azure VPN Gateway-be hirdetett hálózatok listája a következő paranccsal listázható:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Következő lépések

* [Network Performance Monitor for ExpressRoute konfigurálása](how-to-npm.md)

* [Helyek közötti kapcsolat hozzáadása egy meglévő VPN Gateway-kapcsolattal rendelkező VNet](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
