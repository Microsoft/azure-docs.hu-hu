---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban egy virtuális hálózatot hoz létre a Azure Portal használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások kommunikációját egymással és az internettel.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199033"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Gyors útmutató: Virtuális hálózat létrehozása a PowerShell használatával

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-EK), a magánjellegű kommunikációt és az internetet. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozhat a virtuális gépekhez az internetről, és privát kommunikációt végez a virtuális hálózaton keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Néhány lépésből megtudhatja, hogy az erőforráscsoport és a virtuális hálózat konfigurálva van-e.

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Ez a példa létrehoz egy **CreateVNetQS-RG** nevű erőforráscsoportot a **Eastus** helyen:

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Ez a példa létrehoz egy **myVNet** nevű alapértelmezett virtuális hálózatot a **EastUS** helyen:

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Az Azure üzembe helyezi az erőforrásokat egy virtuális hálózaton belüli alhálózaton, ezért létre kell hoznia egy alhálózatot. Hozzon létre egy **alapértelmezett** nevű alhálózati konfigurációt az [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Az alhálózat hozzárendelése a virtuális hálózathoz

Az alhálózati konfigurációt megírhatja a virtuális hálózatra a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)használatával. Ez a parancs az alhálózatot hozza létre:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozza létre az első virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). A következő parancs futtatásakor a rendszer kéri a hitelesítő adatokat. Adja meg a virtuális gép felhasználónevét és jelszavát:

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

A `-AsJob` beállítás a háttérben hozza létre a virtuális gépet. Folytassa a következő lépéssel.

Amikor az Azure a háttérben elkezdi létrehozni a virtuális gépet, a következőhöz hasonló lesz:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Hozza létre a második virtuális gépet a következő paranccsal:

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Létre kell hoznia egy másik felhasználót és jelszót. Az Azure-ban a virtuális gép létrehozása eltarthat néhány percig.

> [!IMPORTANT]
> Ne folytassa a következő lépéssel, amíg az Azure elkészült.  Ha a kimenetet a PowerShellbe tér vissza, a rendszer készen áll.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Ez a példa a **myVm1** virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Nyisson meg egy parancssort a helyi számítógépen. Futtassa a következő parancsot: `mstsc`. Cserélje le az `<publicIpAddress>` elemet az utolsó lépésből visszaadott nyilvános IP-címhez:

> [!NOTE]
> Ha ezeket a parancsokat egy PowerShell-parancssorból futtatta a helyi számítógépen, és az az PowerShell-modul 1,0-es vagy újabb verzióját használja, akkor folytathatja a felületet.

```cmd
mstsc /v:<publicIpAddress>
```
1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

1. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    > [!NOTE]
    > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha így tesz, válassza az **Igen** vagy a **Folytatás** lehetőséget.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A **myVm1** távoli asztal nyissa meg a PowerShellt.

1. Írja be a következő szöveget: `ping myVm2`.

    A következőhöz hasonló lesz:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A pingelés sikertelen, mert a Internet Control Message Protocol (ICMP) protokollt használja. Alapértelmezés szerint az ICMP nem engedélyezett a Windows tűzfalon keresztül.

1. Ha engedélyezni szeretné a **myVm2** számára a **myVm1** pingelését egy későbbi lépésben, írja be a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ezzel a paranccsal a Windows tűzfalon keresztül küldhet ICMP-t.

1. Zárja be a **myVm1** virtuális géppel létesített távoli asztali kapcsolatot.

1. Ismételje meg a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet)című témakör lépéseit. Ezúttal kapcsolódjon a **myVm2**.

1. A **myVm2** virtuális gépen írja be a `ping myvm1` parancsot egy parancssorba.

    A következőhöz hasonló lesz:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a **myVm1** virtuális gép esetében, válaszokat fog kapni a **myVm1** virtuális géptől.

1. Zárja be a **myVm2** virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózattal és a virtuális gépekkel, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban: 

* Létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. 
* Egy virtuális géphez kapcsolódott az internetről, és a két virtuális gép között magántulajdonban kommunikál.

A virtuális gépek közötti magánhálózati kommunikáció nem korlátozott a virtuális hálózatokban. 

A következő cikkből megtudhatja, hogyan konfigurálhat különböző típusú virtuálisgép-hálózati kommunikációt:
> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)
