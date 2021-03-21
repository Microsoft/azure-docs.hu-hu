---
title: 'VNet csatlakoztatása egy másik VNet Azure VPN Gateway VNet – VNet kapcsolat használatával: PowerShell'
description: Egymáshoz csatlakoztathatja a virtuális hálózatokat a virtuális hálózatok közötti kapcsolat és a PowerShell használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655357"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Virtuális hálózatok közötti VPN Gateway-kapcsolat konfigurálása a PowerShell használatával

Ez a cikk bemutatja, hogyan lehet virtuális hálózatokat csatlakoztatni virtuális hálózatok közötti kapcsolat használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben. Amikor különböző előfizetésekről csatlakoztat virtuális hálózatokat, az előfizetéseket nem kell társítani ugyanazzal az Active Directory-bérlővel.

A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak és a PowerShellt használják. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Különböző üzemi modellek összekapcsolása – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Különböző üzemi modellek összekapcsolása – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>Tudnivalók a virtuális hálózatok csatlakoztatásáról

A virtuális hálózatok többféleképpen is összekapcsolhatók. Az alábbi szakaszok a virtuális hálózatok összekapcsolásának különböző módjait ismertetik.

### <a name="vnet-to-vnet"></a>Virtuális hálózatok közötti kapcsolat

Virtuális hálózatok közötti kapcsolat konfigurálásával könnyedén kapcsolat össze virtuális hálózatokat. Két virtuális hálózat virtuális hálózatok közötti kapcsolattal történő összekapcsolása (VNet2VNet) nagyon hasonlít egy helyek közötti IPsec-kapcsolat helyszíni helyhez való csatlakoztatásához.  Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához, és mindkettő ugyanúgy működik a kommunikáció során. A kapcsolattípusok közötti különbség a helyi hálózati átjáró konfigurálásának módjában rejlik. Virtuális hálózatok közötti kapcsolat létrehozásakor a helyi hálózati átjáró címtere nem látható. Ennek létrehozása és feltöltése automatikusan történik. Amikor azonban frissíti az egyik virtuális hálózat címterét, a másik automatikusan tudni fogja a frissített címtér útvonalát. A virtuális hálózatok közötti kapcsolat létrehozása általában gyorsabb és egyszerűbb, mintha egy helyek közötti kapcsolatot hozna létre a virtuális hálózatok között.

### <a name="site-to-site-ipsec"></a>Helyek közötti kapcsolat (IPsec)

Amikor bonyolult hálózati konfigurációkkal dolgozik, a virtuális hálózatok közötti kapcsolatok konfigurációs lépései helyett érdemesebb lehet a [helyek közötti kapcsolat](vpn-gateway-create-site-to-site-rm-powershell.md) lépéseit használni virtuális hálózatai összekapcsolására. A helyek közötti kapcsolatokra vonatkozó lépésekkel manuálisan hozhatja létre és konfigurálhatja a helyi hálózati átjárókat. Az egyes virtuális hálózatok helyi hálózati átjárója helyi helyként kezeli a többi virtuális hálózatot. Így további címtereket határozhat meg a helyi hálózati átjáróhoz a forgalom irányítása érdekében. Ha egy virtuális hálózat címtere megváltozik, frissítenie kell a megfelelő helyi hálózati átjárót a változás tükrözése érdekében. Az átjáró nem frissül automatikusan.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

Érdemes megfontolni a virtuális hálózatok virtuális hálózatok közötti társviszony útján történő összekötését. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót, és más korlátozásokkal rendelkezik. Emellett a [virtuális hálózatok közötti társviszony díjszabásának](https://azure.microsoft.com/pricing/details/virtual-network) kiszámítása máshogy történik, mint a [virtuális hálózatok közötti VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway) esetén. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Mikor érdemes virtuális hálózatok közötti kapcsolatot létrehozni?

A virtuális hálózatokat a következő okokból érdemes virtuális hálózatok közötti kapcsolattal összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**

  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**

  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

A virtuális hálózatok közötti kommunikáció kombinálható többhelyes konfigurációkkal. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal kombinálják.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Melyik virtuális hálózatok közötti konfigurációs lépést alkalmazzam?

Ebben a cikkben kétféle lépéssorozatot láthat. Az egyik lépéssorozat az [azonos előfizetésben található virtuális hálózatokra](#samesub), a másik az [eltérő előfizetésekben található virtuális hálózatokra](#difsub) vonatkozik.
A kettő között az egyik legfőbb különbség az, hogy az eltérő előfizetésekben található virtuális hálózatok kapcsolatainak konfigurálásához külön PowerShell-munkameneteket kell használnia. 

Ebben a gyakorlatban igény szerint kombinálhatja a konfigurációkat, vagy csak kiválaszthat egyet, amelyet használni kíván. Az összes konfiguráció a virtuális hálózatok közötti kapcsolattípust használja. A hálózati adatforgalom a közvetlenül egymáshoz csatlakoztatott virtuális hálózatok között zajlik. Ebben a gyakorlatban a TestVNet4 forgalma nem a TestVNet5 felé irányul.

* [Azonos előfizetésben található virtuális hálózatok](#samesub): a konfiguráció lépései a TestVNet1 és a TestVNet4-t használják.

  ![Olyan diagram, amely az ugyanazon előfizetésben található V hálók V net – V hálózati lépéseit mutatja.](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [Különböző előfizetésekben található virtuális hálózatok](#difsub): a konfiguráció lépései a TestVNet1 és a TestVNet5-t használják.

  ![v2v ábra](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Azonos előfizetésben található virtuális hálózatok összekapcsolása

### <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Mivel az átjáró létrehozása akár 45 percet is igénybe vehet, Azure Cloud Shell a gyakorlat során időnként időtúllépést okoz. Cloud Shell újraindításához kattintson a terminál bal felső részén található gombra. A terminál újraindításakor mindenképpen minden változót újra deklaráljon.

* Ha a Azure PowerShell modul legújabb verzióját szeretné helyileg telepíteni, tekintse meg a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/)című témakört.

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>1. lépés – Az IP-címtartományok megtervezése

A következő lépések során két virtuális hálózatot fog létrehozni, a hozzájuk tartozó átjáróalhálózatokkal és konfigurációkkal együtt. Ezután létrehoz egy VPN-kapcsolatot a két virtuális hálózat között. Fontos, hogy megtervezze a hálózati konfiguráció IP-címtartományát. Ügyeljen arra, hogy a virtuális hálózata tartományai és a helyi hálózata tartományai ne legyenek átfedésben egymással. Ezekben a példákban nem szerepel DNS-kiszolgáló. Ha névfeloldással szeretné ellátni a virtuális hálózatokat, tekintse meg a [Névfeloldás](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) című cikket.

A példákban a következő értékeket használjuk:

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Erőforráscsoport: TestRG1
* Hely: East US
* TestVNet1: 10.11.0.0/16 és 10.12.0.0/16
* Előtér: 10.11.0.0/24
* Háttér: 10.12.0.0/24
* Átjáróalhálózat: 10.12.255.0/27
* Átjáró neve: VNet1GW
* Nyilvános IP-cím: VNet1GWIP
* VPN típusa: RouteBased
* Kapcsolat (1–4): VNet1toVNet4
* Kapcsolat (1–5): VNet1toVNet5 (virtuális hálózatokhoz eltérő előfizetésekben)
* Kapcsolat típusa: VNet2VNet

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* TestVNet2: 10.41.0.0/16 és 10.42.0.0/16
* Előtér: 10.41.0.0/24
* Háttér: 10.42.0.0/24
* Átjáróalhálózat: 10.42.255.0/27
* Erőforráscsoport: TestRG4
* Hely: West US
* Átjáró neve: VNet4GW
* Nyilvános IP-cím: VNet4GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet4toVNet1
* Kapcsolat típusa: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>2. lépés – A TestVNet1 létrehozása és konfigurálása

1. Ellenőrizze az előfizetési beállításokat.

   Kapcsolódjon a fiókjához, ha a PowerShellt helyileg futtatja a számítógépen. Ha Azure Cloud Shell használ, a rendszer automatikusan csatlakozik.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Keresse meg a fiókot az előfizetésekben.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Ha egynél több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Deklarálja a változókat. Ez a példa a gyakorlathoz használt értékekkel deklarálja a változókat. A legtöbb esetben ezeket az értékeket a saját értékeire kell lecserélnie. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Hozza létre a TestVNet1 alhálózat-konfigurációit. Ez a példa létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul. Ezért nem az alábbi változón keresztül van hozzárendelve.

   A következő példa a korábban beállított változókat használja. A példában az átjáróalhálózat /27-es alhálózatot használ. Ugyan létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, a /28-as vagy /27-es lehetőség választásával ajánlott nagyobb alhálózatot létrehozni, amely több címet tartalmaz. Ez elegendő címet biztosít ahhoz, hogy az esetleges további konfigurációkat is elbírják.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Hozza létre a TestVNet1-et.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Vegye figyelembe, hogy az AllocationMethod értéke Dynamic. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Hozza létre az átjáró konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A példa használatával hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Hozza létre a TestVNet1 átjáróját. Ebben a lépésben a TestVNet1 virtuális hálózati átjáróját fogja létrehozni. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

A parancsok befejezése után az átjáró létrehozása akár 45 percet is igénybe vehet. Ha Azure Cloud Shell használ, a Cloud Shell terminál bal felső részén kattintson az Cloud Shell-munkamenet újraindításához, majd a TestVNet4 konfigurálása lehetőségre. Nem kell megvárnia, amíg a TestVNet1-átjáró be nem fejeződik.

### <a name="step-3---create-and-configure-testvnet4"></a>3. lépés – A TestVNet4 létrehozása és konfigurálása

A TestVNet1 konfigurálása után a hozza létre a TestVNet4 virtuális hálózatot. Kövesse az alábbi lépéseket, az értékeket a saját értékeire cserélve.

1. A változók összekötése és deklarálása. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Hozzon létre egy erőforráscsoportot.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Hozza létre a TestVNet4 alhálózat-konfigurációit.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Hozza létre a TestVNet4-et.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Kérjen egy nyilvános IP-címet.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. A TestVNet4 átjárójának létrehozása Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>4. lépés – A kapcsolatok létrehozása

Várjon, amíg mindkét átjáró be nem fejeződik. Indítsa újra a Azure Cloud Shell-munkamenetet, és másolja és illessze be a változókat a 2. lépés és a 3. lépés elejétől a-konzolba az értékek újbóli deklarálása érdekében.

1. Hozza létre a virtuális hálózati átjárókat.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Hozza létre a TestVNet1–TestVNet4 kapcsolatot. Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Hozza létre a TestVNet4–TestVNet1 kapcsolatot. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek. A kapcsolat néhány perc alatt létrejön.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Ellenőrizze a kapcsolatot. Tekintse meg [A kapcsolat ellenőrzése](#verify) című szakaszt.

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Különböző előfizetésben található virtuális hálózatok összekapcsolása

Ebben a forgatókönyvben csatlakoztatja a TestVNet1 és a TestVNet5 virtuális hálózatot. A TestVNet1 és a TestVNet5 különböző előfizetésekben található. Az előfizetéseket nem kell társítani ugyanazzal az Active Directory bérlővel.

A jelen és korábbi lépések közötti különbség abban áll, hogy a konfigurációs lépések egy részét külön PowerShell-munkamenetben kell elvégezni a második előfizetés környezetében. Ez különösen akkor van így, ha a két előfizetés különböző szervezetekhez tartozik.

A gyakorlat előfizetési környezetének módosítása miatt előfordulhat, hogy könnyebben használhatja a PowerShellt helyileg a számítógépen, és nem használja a Azure Cloud Shell, ha a 8. lépésre kerül.

### <a name="step-5---create-and-configure-testvnet1"></a>5. lépés – A TestVNet1 létrehozása és konfigurálása

Az előző szakaszban található [1. lépés](#Step1) és a [2. lépés](#Step2) elvégzésével hozza létre és konfigurálja a TestVNet1-et, valamint a TestVNet1 VPN-átjáróját. Ehhez a konfigurációhoz nem kell létrehoznia az előző szakaszban a TestVNet4-et, azonban ha már megtette, az sem akadályozza az alábbi lépések végrehajtását. Miután elvégezte az 1. lépést és a 2. lépést, folytassa az 6. lépéssel a TestVNet5 létrehozásához.

### <a name="step-6---verify-the-ip-address-ranges"></a>6. lépés – Az IP-címtartományok ellenőrzése

Fontos ügyelni arra, hogy az új virtuális hálózat (TestVNet5) IP-címtere ne legyen átfedésben a többi virtuális hálózati vagy hálózati átjárói tartománnyal. Ebben a példában a virtuális hálózatok különböző szervezetekhez is tartozhatnak. A gyakorlatban a TestVNet5 hálózathoz a következő értékeket használhatja:

**Értékek a TestVNet5-höz:**

* Virtuális hálózat neve: TestVNet5
* Erőforráscsoport: TestRG5
* Hely: Japan East
* TestVNet5: 10.51.0.0/16 és 10.52.0.0/16
* Előtér: 10.51.0.0/24
* Háttér: 10.52.0.0/24
* Átjáróalhálózat: 10.52.255.0.0/27
* Átjáró neve: VNet5GW
* Nyilvános IP-cím: VNet5GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet5toVNet1
* Kapcsolat típusa: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>7. lépés – A TestVNet5 létrehozása és konfigurálása

Ezt a lépést az új előfizetés környezetében kell elvégezni. Ezt a részt azon másik szervezet rendszergazdájának kell elvégeznie, amely az előfizetés tulajdonosa.

1. Deklarálja a változókat. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Csatlakozzon az 5. előfizetéshez. Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Keresse meg a fiókot az előfizetésekben.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Új erőforráscsoport létrehozása.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Hozza létre a TestVNet5 alhálózat-konfigurációit.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Hozza létre a TestVNet5-öt.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Kérjen egy nyilvános IP-címet.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Hozza létre az átjáró konfigurációját.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Hozza létre a TestVNet5 átjáróját.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>8. lépés – A kapcsolatok létrehozása

Ebben a példában, mivel az átjárók különböző előfizetésekben találhatóak, a lépést felosztottuk két PowerShell-munkamenetre, amelyek jelölése [1. előfizetés] és [5. előfizetés].

1. **[1. előfizetés]** Szerezze be az 1. előfizetés virtuális hálózati átjáróját. Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez az alábbi példa futtatása előtt:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Másolja a következő elemek kimenetét, és küldje el a másolatokat az 5. előfizetés rendszergazdájának e-mailben vagy egyéb módon.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[5. előfizetés]** Szerezze be az 5. előfizetés virtuális hálózati átjáróját. A következő példa futtatása előtt jelentkezzen be, és kapcsolódjon az 5. előfizetéshez:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Másolja a következő elemek kimenetét, és küldje el a másolatokat az 1. előfizetés rendszergazdájának e-mailben vagy egyéb módon.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[1. előfizetés]** Hozza létre a TestVNet1 a TestVNet5-kapcsolatok létrehozásához. Ebben a lépésben a TestVNet1 felől a TestVNet5 felé irányuló kapcsolatot hozza létre. A különbség itt az, hogy a $vnet5gw nem szerezhető be közvetlenül, mert másik előfizetésben található. Ehhez létre kell hoznia egy új PowerShell-objektumot a fenti lépésekben az 1. előfizetésből átküldött értékekkel. Használja az alábbi példát. Cserélje le a nevet, az azonosítót és a megosztott kulcsot a saját értékeire. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

   Csatlakozzon az 1. előfizetéshez az alábbi példa futtatása előtt:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[5. előfizetés]** Hozza létre a TestVNet5 a TestVNet1-kapcsolatok létrehozásához. Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet5 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Itt ugyanúgy egy PowerShell-objektumot kell létrehozni az 1. előfizetésből szerzett értékek alapján. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek ebben a lépésben.

   Csatlakozzon az 5. előfizetéshez az alábbi példa futtatása előtt:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>A kapcsolat ellenőrzése

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [Virtual Machines-dokumentációt](../index.yml).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).