---
title: Oktatóanyag – Windows rendszerű virtuális gépek terheléselosztása az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre terheléselosztót az Azure PowerShell-lel egy magas rendelkezésre állású és biztonságos alkalmazáshoz három Windows rendszerű virtuális gépen keresztül
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/03/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d6c872ecfbad6cb8bb01ad5a7c3df8c47aadaebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549107"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Oktatóanyag: Windows rendszerű Azure-beli virtuális gépek terheléselosztása az Azure PowerShell használatával magas rendelkezésre állású alkalmazások létrehozásához
A terheléselosztás magasabb szintű rendelkezésre állást biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban megismerkedhet az Azure Load Balancer különböző összetevőivel, amelyek elosztják a forgalmat, és gondoskodnak a magas rendelkezésre állásról. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure-terheléselosztó létrehozása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-oldal létrehozása egyéni szkriptbővítménnyel
> * Virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása a terheléselosztóhoz és eltávolításuk a terheléselosztóból

## <a name="azure-load-balancer-overview"></a>Az Azure-terheléselosztók áttekintése
Az Azure-terheléselosztók 4. rétegbeli (TCP, UDP) terheléselosztók, amelyek magas rendelkezésre állást biztosítanak azáltal, hogy a bejövő forgalmat elosztják az ép virtuális gépek között. A terheléselosztó állapotmintája ez egyes virtuális gépek adott portjait monitorozza, és csak a működő virtuális gépekre terjeszt forgalmat.

Meg kell adnia egy előtérbeli IP-konfigurációt, amely egy vagy több nyilvános IP-címet tartalmaz. Ez az előtérbeli IP-konfiguráció lehetővé teszi, hogy a terheléselosztó és az alkalmazások elérhetők legyenek az interneten keresztül. 

A virtuális gépek a virtuális hálózati adapterkártyájuk (NIC) segítségével csatlakoznak a terheléselosztóhoz. Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét.

A forgalom szabályozásához terheléselosztási szabályokat kell megadnia a virtuális gépekhez rendelt adott portokhoz és protokollokhoz.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó egyes összetevőinek létrehozását és konfigurálását. A terheléselosztó létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával. A következő példában létrehozunk egy *myResourceGroupLoadBalancer* nevű erőforráscsoportot a *EastUS* helyen:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP [-címet a New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Az alábbi példában létrejön egy *myPublicIP* nevű nyilvános IP-cím a *myResourceGroupLoadBalancer* erőforráscsoportban:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása
Hozzon létre egy új előtér [-IP-címkészletet a New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Az alábbi példában létrejön egy *myFrontEndPool* nevű előtérbeli IP-címkészlet, amely a *myPublicIP* címhez kötődik: 

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Hozzon létre egy háttér-címkészletet a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). A virtuális gépek ehhez a háttérkészlethez lesznek csatolva a hátralévő lépések során. A következő példa létrehoz egy *myBackEndPool* nevű háttér-címkészletet:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "myBackEndPool"
```

Most hozza létre a terheléselosztó-t a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Az alábbi példában egy *myLoadBalancer* nevű terheléselosztó jön létre az előző lépésekben létrehozott elő- és háttérbeli IP-címkészletek használatával:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni. 

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

A TCP Health-mintavétel létrehozásához használja az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *TCP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell-interactive
Add-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Az állapot-mintavétel alkalmazásához frissítse a terheléselosztó-t a [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztó-szabályt az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell-interactive
$probe = Get-AzLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

A terheléselosztó frissítése a [set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer):

```azurepowershell-interactive
Set-AzLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelné az elosztót, hozza létre a támogató virtuális hálózati erőforrásokat. További információt a virtuális hálózatokról az [Azure-beli virtuális hálózatok kezelésével](tutorial-virtual-network.md) foglalkozó oktatóanyagban talál.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

A virtuális hálózati adaptereket a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)hozza létre. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási készletet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

A [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. Az alábbi példa három virtuális gépet hoz létre, illetve a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Job` parancsmaggal tekintheti meg. A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.


### <a name="install-iis-with-custom-script-extension"></a>IIS telepítése egyéni szkriptbővítménnyel
A [Windows rendszerű virtuális gép testreszabásával](tutorial-automate-vm-deployment.md) foglalkozó korábbi oktatóanyagból megtudhatta, hogyan automatizálható egy virtuális gép testreszabása a Windows rendszerhez készült egyéni szkriptbővítménnyel. Ugyanezzel a módszerrel telepítheti és konfigurálhatja az IIS-t a virtuális gépeken.

A [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) használatával telepítse az egyéni szkriptek bővítményét. A bővítmény a `powershell Add-WindowsFeature Web-Server` parancs futtatásával telepíti az IIS-webkiszolgálót, majd a *Default.htm* lapot frissítve megjeleníti a virtuális gép eszköznevét:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![IIS-webhely futtatása](./media/tutorial-load-balancer/running-iis-website.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató három virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.


## <a name="add-and-remove-vms"></a>Virtuális gépek hozzáadása és eltávolítása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ez a szakasz bemutatja, hogyan távolíthat el egy virtuális gépet a terheléselosztóból, vagy hogyan adhat hozzá virtuális gépeket.

### <a name="remove-a-vm-from-the-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból
Kérje le a hálózati adaptert a [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface), majd állítsa a virtuális hálózati adapter *LoadBalancerBackendAddressPools* tulajdonságát *$nullra*. Végül frissítse a virtuális NIC-t:

```azurepowershell-interactive
$nic = Get-AzNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzNetworkInterface -NetworkInterface $nic
```

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató megmaradt kettő virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. Most már elvégezheti a virtuális gépen a szükséges karbantartást, például telepítheti az operációs rendszer frissítéseit, vagy újraindíthatja a virtuális gépet.

### <a name="add-a-vm-to-the-load-balancer"></a>Virtuális gép hozzáadása a terheléselosztóhoz
Miután elvégezte a virtuális gép karbantartását, vagy ha ki kell bővíteni a kapacitást, állítsa be a virtuális hálózati adapter *LoadBalancerBackendAddressPools* tulajdonságát a [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) *BackendAddressPool* :

A terheléselosztó lekérése:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy terheléselosztót, majd virtuális gépeket csatolt hozzá. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure-terheléselosztó létrehozása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-oldal létrehozása egyéni szkriptbővítménnyel
> * Virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása a terheléselosztóhoz és eltávolításuk a terheléselosztóból

A következő oktatóanyag a virtuális gépek hálózatkezelését mutatja be.

> [!div class="nextstepaction"]
> [Virtuális gépek és virtuális hálózatok kezelése](./tutorial-virtual-network.md)
