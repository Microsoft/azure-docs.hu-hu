---
title: Windows rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel – Azure PowerShell
description: Hozzon létre egy Windows rendszerű virtuális gépet (VM) a gyorsított hálózatkezeléssel, hogy nagy mértékben javítsa a hálózati teljesítményét.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919969"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Gyorsított hálózatkezeléssel rendelkező, Windows rendszerű virtuális gép létrehozása az Azure PowerShell használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy gyorsított hálózatkezeléssel rendelkező Windows rendszerű virtuális gépet (VM).

> [!NOTE]
> Ha a gyorsított hálózatkezelést Linux rendszerű virtuális géppel szeretné használni, tekintse meg [a Linux rendszerű virtuális gép gyorsított hálózatkezeléssel történő létrehozását](create-vm-accelerated-networking-cli.md)ismertető témakört.

A Gyorsított hálózatkezelés engedélyezi az egygyökerű I/O-virtualizálást (SR-IOV) a virtuális gépekre, nagy mértékben javítva a hálózati teljesítményt. A nagy teljesítményű elérési út megkerüli az adatelérési úton lévő gazdagépet, ami csökkenti a legnagyobb igényű hálózati számítási feladatok késését, jitterét és processzorhasználatát a támogatott virtuálisgép-típusokon. Az alábbi ábra azt szemlélteti, hogyan kommunikálnak két virtuális gép a gyorsított hálózatkezeléssel és anélkül:

![Azure-beli virtuális gépek közötti kommunikáció gyorsított hálózatkezeléssel és anélkül](./media/create-vm-accelerated-networking/accelerated-networking.png)

A gyorsított hálózatkezelés nélkül a virtuális GÉPEN belüli és kívüli összes hálózati forgalomnak át kell haladnia a gazdagépen és a virtuális kapcsolón. A virtuális kapcsoló biztosítja az összes házirend-kényszerítést, például a hálózati biztonsági csoportokat, a hozzáférés-vezérlési listát, az elkülönítést és az egyéb, a hálózati forgalomhoz tartozó virtualizált szolgáltatásokat.

> [!NOTE]
> További információ a virtuális kapcsolókról: [Hyper-V virtuális kapcsoló](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

A gyorsított hálózatkezeléssel a hálózati forgalom a virtuális gép hálózati adapterén (NIC) érkezik, majd továbbítva lesz a virtuális géphez. A virtuális kapcsoló által érintett összes hálózati házirend már ki van kiszervezve és alkalmazva a hardveren. Mivel a házirend a hardverben van alkalmazva, a hálózati adapter közvetlenül a virtuális géphez továbbítja a hálózati forgalmat. A hálózati adapter megkerüli a gazdagépet és a virtuális kapcsolót, miközben a gazdagépen alkalmazott összes házirendet karbantartja.

A gyorsított hálózatkezelés előnyei csak azokra a virtuális gépekre érvényesek, amelyeken engedélyezve van a szolgáltatás. A legjobb eredmény érdekében engedélyezze ezt a szolgáltatást legalább két, ugyanahhoz az Azure-beli virtuális hálózathoz csatlakozó virtuális gépen. A virtuális hálózatok közötti kommunikáció vagy a helyszíni csatlakozás esetén ez a funkció minimális hatással van a teljes késésre.

## <a name="benefits"></a>Előnyök

- **Kisebb késés/nagyobb csomagok másodpercenként (PPS)**: a virtuális kapcsolónak az adatelérési útról való eltávolítása eltávolítja a gazdagépen a házirendek feldolgozásához töltött időt. Emellett növeli a virtuális gépen belül feldolgozható csomagok számát is.

- **Csökkentett Jitter**: a virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől függ. Emellett a feldolgozást végző CPU munkaterheléstől is függ. A szabályzat kényszerítésének a hardverre való kiszervezése eltávolítja a változékonyságot a csomagok közvetlenül a virtuális géphez való továbbításával. A kiszervezés a gazdagépek közötti kommunikációt, az összes szoftver megszakítását és az összes környezeti kapcsolót is eltávolítja.

- **Csökkentett CPU-kihasználtság**: a gazdagépen lévő virtuális kapcsoló megkerülése kevesebb CPU-kihasználtságot eredményez a hálózati forgalom feldolgozásakor.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi disztribúciók közvetlenül az Azure-katalógusból támogatottak:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

### <a name="supported-vm-instances"></a>Támogatott VM-példányok

A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja 2 vagy több vCPU. A feleznie támogató példányokon a gyorsított hálózatkezelést a 4 vagy több vCPU rendelkező virtuálisgép-példányok támogatják. 

A gyorsított hálózatkezelés támogatását az egyes [virtuálisgép-méretek](../virtual-machines/sizes.md) dokumentációjában találja. 

### <a name="custom-images"></a>Egyéni képek

Ha egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelést, győződjön meg arról, hogy rendelkezik a szükséges illesztőprogramokkal, amelyek a Mellanox ConnectX-3 és a ConnectX-4 LX hálózati adapterek használatával működnek az Azure-ban.

### <a name="regions"></a>Régiók

A gyorsított hálózatkezelés minden globális Azure-régióban és Azure Government-felhőben elérhető.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Gyorsított hálózatkezelés engedélyezése egy futó virtuális gépen

A gyorsított hálózatkezelést nem támogató, támogatott virtuálisgép-méret csak akkor engedélyezhető, ha a funkció le van állítva és fel van foglalva.

### <a name="deployment-through-azure-resource-manager"></a>Üzembe helyezés Azure Resource Manageron keresztül

A virtuális gépek (klasszikus) nem helyezhetők üzembe gyorsított hálózatkezeléssel.

## <a name="vm-creation-using-the-portal"></a>VIRTUÁLIS gépek létrehozása a portál használatával

Bár ez a cikk a gyorsított hálózatkezelést használó virtuális gépek Azure PowerShell használatával történő létrehozásának lépéseit ismerteti, a Azure Portal is használhatja a gyorsított hálózatkezelést lehetővé tevő [virtuális gép létrehozásához](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Amikor létrehoz egy virtuális gépet a portálon, a **virtuális gép létrehozása** lapon válassza a **hálózatkezelés** lapot. Ezen a lapon megadható a **gyorsított hálózatkezelés**. Ha a [támogatott operációs rendszert](#supported-operating-systems) és virtuálisgép- [méretet](#supported-vm-instances)választotta, akkor **Ez a beállítás** automatikusan be értékre van állítva. Ellenkező esetben a beállítás **ki** értékre van állítva, és az Azure megjeleníti az okát, hogy miért nem engedélyezhető.

> [!NOTE]
> Csak a támogatott operációs rendszerek engedélyezhetők a portálon keresztül. Ha egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelést, akkor a parancssori felület vagy a PowerShell használatával hozza létre a virtuális gépet. 

A virtuális gép létrehozása után ellenőrizheti, hogy engedélyezve van-e a gyorsított hálózat. Kövesse az alábbi utasításokat:

1. A virtuális gépek kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. A virtuális gép listából válassza ki az új virtuális gépet.

3. A virtuális gép menüsorában válassza a **hálózatkezelés** lehetőséget.

A hálózati adapteren, a **gyorsított hálózati** címke mellett a portál vagy a **letiltott** , vagy a gyorsított hálózatkezelési állapothoz **engedélyezve** érték jelenik meg.

## <a name="vm-creation-using-powershell"></a>VIRTUÁLIS gépek létrehozása a PowerShell-lel

A folytatás előtt telepítse a [Azure PowerShell](/powershell/azure/install-az-ps) vagy újabb verziójú 1.0.0-t. A jelenleg telepített verziójának megkereséséhez futtassa a parancsot `Get-Module -ListAvailable Az` . Ha telepíteni vagy frissíteni szeretne, telepítse az az modul legújabb verzióját a [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az). Egy PowerShell-munkamenetben jelentkezzen be egy Azure-fiókba a Connection [-AzAccount](/powershell/module/az.accounts/connect-azaccount)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa a paraméterek neveire: *myResourceGroup*, *myNic* és *myVM*.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). A következő parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *CentralUS* helyen:

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). A következő parancs létrehoz egy *mySubnet* nevű alhálózatot:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Hozzon létre egy [új AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)rendelkező virtuális hálózatot a *mySubnet* alhálózattal.

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

1. Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Hozzon létre egy hálózati biztonsági csoportot [új-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) , és rendelje hozzá az *Allow-RDP-minden* biztonsági szabályt. Az *Allow-RDP-all* szabályon kívül a hálózati biztonsági csoport több alapértelmezett szabályt is tartalmaz. Egy alapértelmezett szabály letiltja az összes bejövő hozzáférést az internetről. A létrehozást követően az *Engedélyezés-RDP-all* szabály hozzá van rendelve a hálózati biztonsági csoporthoz, hogy távolról is csatlakozhasson a virtuális géphez.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Társítsa a hálózati biztonsági csoportot a *mySubnet* alhálózathoz a [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A hálózati biztonsági csoport szabálya az alhálózaton üzembe helyezett összes erőforrásra érvényes.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Hálózati adapter létrehozása gyorsított hálózatkezeléssel

1. Hozzon létre egy nyilvános IP [-címet a New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). A nyilvános IP-cím szükségtelen, ha nem tervezi a virtuális gép internetről való elérését. Azonban a cikk lépéseinek végrehajtásához szükséges.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Hozzon létre egy olyan hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) , amelyen engedélyezve van a gyorsított hálózat, és rendelje hozzá a nyilvános IP-címet a hálózati adapterhez. A következő példa egy *myNic* nevű hálózati adaptert hoz létre a *MyVnet* virtuális hálózat *mySubnet* alhálózatában, és hozzárendeli a *myPublicIp* nyilvános IP-címét:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Virtuális gép létrehozása és a hálózati adapter csatlakoztatása

1. Állítsa be a virtuális gép hitelesítő adatait a `$cred` [Get-hitelesítő adatokkal](/powershell/module/microsoft.powershell.security/get-credential)a változóra, amely felszólítja a bejelentkezésre:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Adja meg a virtuális gépet a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A következő parancs egy *myVM* nevű virtuális gépet definiál egy virtuálisgép-mérettel, amely támogatja a gyorsított hálózatkezelést (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    A virtuális gépek méretének és jellemzőinek listáját lásd: [Windows rendszerű virtuális gépek mérete](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Hozza létre a virtuális gép többi konfigurációját a [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) és a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). A következő parancs egy Windows Server 2016 rendszerű virtuális gépet hoz létre:

    ```azurepowershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
    ```

4. Csatlakoztassa a korábban az [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)által létrehozott hálózati adaptert:

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Hozzon létre egy új virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Ellenőrizze, hogy az Ethernet-vezérlő telepítve van-e a Windows rendszerű virtuális gépen

Miután létrehozta a virtuális gépet az Azure-ban, kapcsolódjon a virtuális géphez, és ellenőrizze, hogy az Ethernet-vezérlő telepítve van-e a Windows rendszerben.

1. A virtuális gépek kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. A virtuális gép listából válassza ki az új virtuális gépet.

3. A virtuális gép áttekintése lapon, ha a virtuális gép **állapota** **létrehozáskor** szerepel, várjon, amíg az Azure befejezi a virtuális gép létrehozását. A virtuális gép létrehozása után az **állapot** a **futtatásra** módosul.

4. A virtuális gép áttekintő eszköztárán válassza az   >  **RDP**-  >  **Letöltés RDP-fájljának** összekapcsolását.

5. Nyissa meg az. rdp-fájlt, majd jelentkezzen be a virtuális gépre a [virtuális gép létrehozása és a hálózati adapter csatlakoztatása](#create-a-vm-and-attach-the-network-interface) című szakaszban megadott hitelesítő adatokkal. Ha még soha nem kapcsolódott az Azure-beli Windows rendszerű virtuális géphez, tekintse meg a [Csatlakozás virtuális géphez](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)című témakört.

6. Miután megjelenik a virtuális gép távoli asztali munkamenete, kattintson a jobb gombbal a Windows Start gombra, és válassza a **Eszközkezelő** lehetőséget.

7. A **Eszközkezelő** ablakban bontsa ki a **hálózati adapterek** csomópontot.

8. Győződjön meg arról, hogy a **Mellanox ConnectX-3 virtuális funkció Ethernet-adaptere** megjelenik, ahogy az az alábbi képen is látható:

    ![Mellanox ConnectX-3 virtuális funkció Ethernet-adapter, új hálózati adapter a gyorsított hálózatkezeléshez, Eszközkezelő](./media/create-vm-accelerated-networking/device-manager.png)

A gyorsított hálózatkezelés mostantól engedélyezve van a virtuális gépen.

> [!NOTE]
> Ha a Mellanox-adapter nem indul el, nyisson meg egy rendszergazdai parancssort a távoli asztali munkamenetben, és adja meg a következő parancsot:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Gyorsított hálózatkezelés engedélyezése meglévő virtuális gépeken

Ha gyorsított hálózatkezelés nélküli virtuális gépet hozott létre, ezt a funkciót egy meglévő virtuális gépen engedélyezheti. A virtuális gépnek a következő előfeltételeknek megfelelően támogatnia kell a gyorsított hálózatkezelést:

* A virtuális gépnek a gyorsított hálózatkezeléshez támogatott méretnek kell lennie.
* A virtuális gépnek támogatott Azure Gallery-rendszerképnek (és Linux kernel-verziónak) kell lennie.
* A gyorsított hálózatkezelés bármely hálózati adapteren való engedélyezése előtt le kell állítani vagy fel kell szüntetni minden virtuális gépet egy rendelkezésre állási csoportba vagy egy virtuálisgép-méretezési csoportba.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Egy rendelkezésre állási csoportba tartozó egyes virtuális gépek és virtuális gépek

1. Állítsa le vagy szabadítsa fel a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > Ha önálló virtuális gépet hoz létre, rendelkezésre állási csoport nélkül, a gyorsított hálózatkezelés engedélyezéséhez csak le kell állítania vagy fel kell szabadítania az egyes virtuális gépeket. Ha a virtuális gép rendelkezésre állási csoporttal lett létrehozva, le kell állítania vagy fel kell szabadítania a rendelkezésre állási csoportba tartozó összes virtuális gépet, mielőtt engedélyezné a gyorsított hálózatkezelést bármelyik hálózati adapteren, hogy a virtuális gépek a gyorsított hálózatkezelést támogató fürtön legyenek. A leállítási vagy felszabadítási követelmény szükségtelen, ha letiltja a gyorsított hálózatkezelést, mert a gyorsított hálózatkezelést támogató fürtök is megfelelően működnek a gyorsított hálózatkezelést nem használó hálózati adapterekkel.

2. Gyorsított hálózatkezelés engedélyezése a virtuális gép hálózati adapterén:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Indítsa újra a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép, és ellenőrizze, hogy engedélyezve van-e a gyorsított hálózat:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport

A virtuálisgép-méretezési csoport némileg eltérő, de ugyanazt a munkafolyamatot követi.

1. Virtuális gépek leállítása:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Frissítse a gyorsított hálózatkezelési tulajdonságot a hálózati adapter alatt:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Állítsa be az alkalmazott frissítéseket automatikusra, hogy a módosítások azonnal elérhetők legyenek:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > A méretezési csoport olyan virtuálisgép-frissítéseket tartalmaz, amelyek három különböző beállítással alkalmazzák a frissítéseket: automatikus, működés közbeni és manuális. Ebben az útmutatóban a szabályzat automatikus értékre van beállítva, így a méretezési csoport az újraindítást követően azonnal felveszi a módosításokat.

4. A méretezési csoport újraindítása:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Az újraindítás után várjon, amíg a frissítések befejeződik. A frissítések befejezését követően a virtuális funkció (VF) megjelenik a virtuális GÉPEN. Győződjön meg arról, hogy támogatott operációs rendszert és virtuálisgép-méretet használ.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Meglévő virtuális gépek átméretezése gyorsított hálózatkezeléssel

Ha egy virtuális gépen engedélyezve van a gyorsított hálózatkezelés, akkor csak a gyorsított hálózatkezelést támogató virtuális gépekre lehet átméretezni.  

A gyorsított hálózatkezelést használó virtuális gépek nem méretezhetők át olyan virtuálisgép-példányra, amely nem támogatja a gyorsított hálózatkezelést az átméretezési művelettel. Ehelyett a következő virtuális gépek egyikének átméretezéséhez:

1. Állítsa le vagy szabadítsa fel a virtuális gépet. Rendelkezésre állási csoport vagy méretezési csoport esetében állítsa le vagy szabadítson fel minden virtuális gépet a rendelkezésre állási csoport vagy a méretezési csoport számára.

2. Tiltsa le a gyorsított hálózatkezelést a virtuális gép hálózati adapterén. Rendelkezésre állási csoport vagy méretezési csoport esetén tiltsa le a gyorsított hálózatkezelést a rendelkezésre állási csoport vagy a méretezési csoport összes virtuális gépe hálózati adapterén.

3. Miután letiltotta a gyorsított hálózatkezelést, helyezze át a virtuális gépet, a rendelkezésre állási készletet vagy a méretezési beállítást egy olyan új méretre, amely nem támogatja a gyorsított hálózatkezelést, majd indítsa újra őket.