---
title: 'Klasszikus virtuális hálózatok összekötése Azure Resource Manager virtuális hálózatok: PowerShell'
description: Hozzon létre egy VPN-kapcsolatot a klasszikus virtuális hálózatok és a Resource Manager-virtuális hálózatok között a VPN Gateway és a PowerShell használatával.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376488"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Különböző üzemi modellekből származó virtuális hálózatok összekapcsolása a PowerShell-lel

Ez a cikk segítséget nyújt a klasszikus virtuális hálózatok Resource Manager-virtuális hálózatok való összekapcsolásához, hogy a különálló üzemi modellekben található erőforrások kommunikáljanak egymással. A cikkben ismertetett lépések a PowerShellt használják, de ezt a konfigurációt a Azure Portal használatával is létrehozhatja, ehhez a listából válassza ki a cikket.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

A klasszikus VNet Resource Manager-VNet való csatlakoztatása hasonló ahhoz, hogy egy VNet egy helyszíni helyhez kapcsolódjon. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban található virtuális hálózatok között. Olyan virtuális hálózatok is csatlakoztathatók, amelyek már rendelkeznek kapcsolattal a helyszíni hálózatokkal, feltéve, hogy az átjárók dinamikus vagy Route-alapúak. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik virtuális hálózati átjáróval, és nem szeretne létrehozni egyet, érdemes lehet inkább a virtuális hálózatok csatlakoztatni a VNet-társ használatával. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Előkészületek

A következő lépések végigvezetik az egyes VNet dinamikus vagy Route-alapú átjárójának konfigurálásához szükséges beállításokkal, és létrehoznak egy VPN-kapcsolatot az átjárók között. Ez a konfiguráció nem támogatja a statikus vagy a házirend-alapú átjárókat.

### <a name="prerequisites"></a><a name="pre"></a>Előfeltételek

* Mindkét virtuális hálózatok már létre lett hozva. Ha Resource Manager virtuális hálózatot kell létrehoznia, tekintse meg [az erőforráscsoport és a virtuális hálózat létrehozása](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)című témakört. Klasszikus virtuális hálózat létrehozásához tekintse meg [a klasszikus VNet létrehozása](/previous-versions/azure/virtual-network/create-virtual-network-classic)című témakört.
* A virtuális hálózatok címtartományok nem fedik egymást egymással, vagy átfedésben vannak más kapcsolatok bármely tartományával, amelyekhez az átjárók csatlakozhatnak.
* Telepítette a legújabb PowerShell-parancsmagokat. További információért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/) . Győződjön meg arról, hogy a Service Management (SM) és a Resource Manager (RM) parancsmagokat is telepíti. 

### <a name="example-settings"></a><a name="exampleref"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus VNet-beállítások**

VNet neve = ClassicVNet <br>
Location = USA nyugati régiója <br>
Virtual Network címterület = 10.0.0.0/24 <br>
Alhálózat-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Helyi hálózat neve = RMVNetLocal <br>
GatewayType = DynamicRouting

**Resource Manager-VNet beállításai**

VNet neve = RMVNet <br>
Erőforráscsoport = RG1 <br>
Virtual Network IP-cím szóközök = 192.168.0.0/16 <br>
Alhálózat-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Hely = USA keleti régiója <br>
Átjáró nyilvános IP-neve = gwpip <br>
Helyi hálózati átjáró = ClassicVNetLocal <br>
Virtual Network átjáró neve = RMGateway <br>
Átjáró IP-címzési konfigurációja = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>1. szakasz – a klasszikus VNet konfigurálása
### <a name="1-download-your-network-configuration-file"></a>1. Töltse le a hálózati konfigurációs fájlt
1. Jelentkezzen be az Azure-fiókjába a PowerShell-konzolon emelt szintű jogosultságokkal. A következő parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatainak megadását. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára. Ebben a szakaszban a klasszikus Service Management (SM) Azure PowerShell parancsmagokat használjuk.

   ```azurepowershell
   Add-AzureAccount
   ```

   Szerezze be az Azure-előfizetését.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportálja az Azure hálózati konfigurációs fájlját a következő parancs futtatásával. Ha szükséges, módosíthatja a fájl helyét egy másik helyre való exportáláshoz.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Nyissa meg a letöltött. xml fájlt a szerkesztéshez. A hálózati konfigurációs fájlra példaként tekintse meg a [hálózati konfigurációs sémát](/previous-versions/azure/reference/jj157100(v=azure.100)).

### <a name="2-verify-the-gateway-subnet"></a>2. Ellenőrizze az átjáró alhálózatát
A **VirtualNetworkSites** elemben adjon hozzá egy átjáró-alhálózatot a VNet, ha még nem lett létrehozva. Ha a hálózati konfigurációs fájllal dolgozik, az átjáró-alhálózatnak "GatewaySubnet" névvel kell RENDELKEZNIE, vagy az Azure nem ismeri fel és nem használhatja átjáró-alhálózatként.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Példa**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. a helyi hálózati hely hozzáadása
A hozzáadni kívánt helyi hálózati hely azt az RM-VNet jelöli, amelyhez csatlakozni szeretne. Ha még nem létezik, adjon hozzá egy **LocalNetworkSites** elemet a fájlhoz. A konfiguráció ezen pontján a VPNGatewayAddress bármely érvényes nyilvános IP-cím lehet, mivel még nem hoztuk létre az átjárót a Resource Manager-VNet. Az átjáró létrehozása után a helyőrző IP-címet a megfelelő nyilvános IP-címmel cseréljük le, amelyet az RM-átjáróhoz rendeltek.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. a VNet hozzárendelése a helyi hálózati helyhez
Ebben a szakaszban megadjuk azt a helyi hálózati helyet, amelyhez a VNet csatlakozni kívánja. Ebben az esetben a korábban hivatkozott Resource Manager-VNet. Győződjön meg arról, hogy a nevek egyeznek. Ez a lépés nem hoz létre átjárót. Megadja azt a helyi hálózatot, amelyhez az átjáró csatlakozni fog.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. mentse a fájlt, és töltse fel
Mentse a fájlt, majd importálja az Azure-ba a következő parancs futtatásával. Győződjön meg arról, hogy a környezetéhez szükség van a fájl elérési útjának módosítására.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Egy hasonló eredmény jelenik meg, amely azt mutatja, hogy az importálás sikeres volt.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. az átjáró létrehozása

A példa futtatása előtt tekintse meg az Azure által megtekinteni kívánt pontos nevekhez letöltött hálózati konfigurációs fájlt. A hálózati konfigurációs fájl tartalmazza a klasszikus virtuális hálózatok értékeit. Előfordul, hogy a klasszikus virtuális hálózatok neve megváltozik a hálózati konfigurációs fájlban, amikor a Azure Portal klasszikus VNet-beállításokat hoz létre a telepítési modellek közötti különbségek miatt. Ha például a Azure Portal használatával létrehozta a klasszikus VNet nevű klasszikus VNet, és létrehozta azt egy "ClassicRG" nevű erőforráscsoporthoz, a hálózati konfigurációs fájlban található név a "csoport ClassicRG klasszikus VNet" értékre lesz konvertálva. Szóközöket tartalmazó VNet nevének megadásakor idézőjeleket kell használni az érték körül.


A következő példa használatával hozzon létre egy dinamikus útválasztási átjárót:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Az átjáró állapotát a **Get-AzureVNetGateway** parancsmag használatával tekintheti meg.

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>2. szakasz – az RM VNet-átjáró konfigurálása



Az előfeltételek feltételezik, hogy már létrehozott egy RM-VNet. Ebben a lépésben létrehoz egy VPN-átjárót az RM-VNet. Ezeket a lépéseket csak akkor kezdje el, ha a klasszikus VNet-átjáróhoz tartozó nyilvános IP-címet nem kéri le. 

1. Jelentkezzen be az Azure-fiókjába a PowerShell-konzolon. A következő parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adatainak megadását. A bejelentkezést követően a rendszer letölti a fiók beállításait, hogy elérhetők legyenek Azure PowerShell számára. Igény szerint használhatja a "kipróbálás" funkciót is a böngészőben való Azure Cloud Shell indításához.

   Ha Azure Cloud Shell használ, ugorja át a következő parancsmagot:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Annak ellenőrzéséhez, hogy a megfelelő előfizetést használja-e, futtassa a következő parancsmagot:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Ha egynél több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Hozzon létre egy helyi hálózati átjárót. Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. Ebben az esetben a helyi hálózati átjáró a klasszikus VNet hivatkozik. Adja meg azt a nevet, amellyel az Azure hivatkozhat rá, és adja meg a Címterület előtagját is. Az Azure a megadott IP-címelőtagot a helyszíni helyre küldendő adatforgalom azonosítására használja. Ha később, az átjáró létrehozása előtt módosítania kell az adatokat, módosíthatja az értékeket, és újra futtathatja a mintát.
   
   **-A név** a hozzárendelni kívánt név a helyi hálózati átjáróra.<br>
   A **-AddressPrefix** a klasszikus VNet tartozó címtartomány.<br>
   A **-GatewayIpAddress** a klasszikus VNet átjárójának nyilvános IP-címe. Ügyeljen arra, hogy a megfelelő IP-cím megjelenítéséhez módosítsa az "n. n. n. n" szöveget a következő mintaszöveg alapján.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Igényeljen egy nyilvános IP-címet, amelyet a Resource Manager-VNet virtuális hálózati átjárójának kell lefoglalni. A használni kívánt IP-címet nem adhatja meg. Az IP-címet a rendszer dinamikusan osztja ki a virtuális hálózati átjáró számára. Ez azonban nem jelenti azt, hogy az IP-cím változik. A virtuális hálózati átjáró IP-címe csak akkor változik, ha az átjárót törlik és újra létrehozzák. Az átjáró átméretezése, alaphelyzetbe állítása vagy egyéb belső karbantartása/frissítése nem változik.

   Ebben a lépésben egy későbbi lépésben használt változót is beállíthat.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Győződjön meg arról, hogy a virtuális hálózat átjáró-alhálózattal rendelkezik. Ha nem létezik átjáró-alhálózat, vegyen fel egyet. Győződjön meg arról, hogy az átjáró-alhálózat neve *GatewaySubnet*.
5. Kérje le az átjáróhoz használt alhálózatot az alábbi parancs futtatásával. Ebben a lépésben a következő lépésben használandó változót is beállítjuk.
   
   **-A name** a Resource Manager-VNet neve.<br>
   **– A ResourceGroupName** az az erőforráscsoport, amelyhez a VNet társítva van. Az átjáró alhálózatának már léteznie kell ehhez a VNet, és a megfelelő működéshez a *GatewaySubnet* nevűnek kell lennie.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Hozza létre az átjáró IP-címzési konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő minta használatával hozza létre az átjáró konfigurációját.

   Ebben a lépésben a **-Denetid** és a **-PublicIpAddressId** paramétereket át kell adni az alhálózat és az IP-cím objektumok azonosító tulajdonságának. Nem használhat egyszerű karakterláncot. Ezeket a változókat a rendszer a nyilvános IP-cím kérése és az alhálózat beolvasásának lépései szerint állítja be.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Hozza létre a Resource Manager-beli virtuális hálózati átjárót a következő parancs futtatásával. A `-VpnType` *útvonalalapú* kell lennie. Az átjáró létrehozása akár 45 percet is igénybe vehet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. A VPN-átjáró létrehozása után másolja a nyilvános IP-címet. Ezt akkor használhatja, amikor konfigurálja a helyi hálózati beállításokat a klasszikus VNet. A nyilvános IP-cím lekéréséhez a következő parancsmagot használhatja. A nyilvános IP-cím a Return as *IP*-címe mezőben jelenik meg.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>3. szakasz – a klasszikus VNet helyi hely beállításainak módosítása

Ebben a szakaszban a klasszikus VNet működik. Lecseréli azt a helyőrző IP-címet, amelyet a Resource Manager VNet-átjáróhoz való csatlakozáshoz használt helyi hely beállításainak megadásakor használt. Mivel a klasszikus VNet dolgozik, a PowerShellt helyileg kell telepíteni a számítógépre, nem a Azure Cloud Shell TryIt.

1. Exportálja a hálózati konfigurációs fájlt.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Szövegszerkesztő használatával módosítsa a VPNGatewayAddress értékét. Cserélje le a helyőrző IP-címet a Resource Manager-átjáró nyilvános IP-címére, majd mentse a módosításokat.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importálja a módosított hálózati konfigurációs fájlt az Azure-ba.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>4. szakasz – kapcsolat létrehozása az átjárók között
Az átjárók közötti kapcsolat létrehozásához a PowerShell szükséges. Előfordulhat, hogy fel kell vennie az Azure-fiókját a PowerShell-parancsmagok klasszikus verziójának használatára. Ehhez használja az **Add-AzureAccount**.

1. A PowerShell-konzolon állítsa be a megosztott kulcsot. A parancsmagok futtatása előtt tekintse meg az Azure által megtekinteni kívánt pontos nevekhez letöltött hálózati konfigurációs fájlt. Szóközöket tartalmazó VNet nevének megadásakor a rendszer az érték körüli aposztrófokat használja.<br><br>A következő példában a **-VNetName** a klasszikus VNet neve, a **-LocalNetworkSiteName** pedig a helyi hálózati helyhez megadott név. A **-SharedKey** egy létrehozott és megadott érték. A példában a "abc123"-t használtuk, de egy összetettebbt is létrehozhat és használhat. A lényeg az, hogy az itt megadott értéknek meg kell egyeznie a következő lépésben a kapcsolódás létrehozásakor megadott értékkel. A visszatérésnek a következő **állapotot kell tartalmaznia: sikeres**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. A VPN-kapcsolat létrehozásához futtassa a következő parancsokat:
   
   Állítsa be a változókat.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Hozza létre a kapcsolatot. Figyelje meg, hogy a **-ConnectionType** IPSec, nem Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>5. szakasz – a kapcsolatok ellenőrzése

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A klasszikus VNet létesített kapcsolatok ellenőrzése a Resource Manager-VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>A kapcsolódás ellenőrzése a Resource Manager-VNet a klasszikus VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
