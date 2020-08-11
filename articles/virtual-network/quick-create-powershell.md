---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban egy virtuális hálózatot hoz létre a Azure Portal használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások kommunikációját egymással és az internettel.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: f469256841bf81f5e35ebf9c4a0fe322b513153d
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056975"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Gyors útmutató: Virtuális hálózat létrehozása a PowerShell használatával

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-EK), a magánjellegű kommunikációt és az internetet. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozhat a virtuális gépekhez az internetről, és privát kommunikációt végez a virtuális hálózaton keresztül.

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha ehelyett a PowerShell helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz Azure PowerShell-modul 1.0.0-es vagy újabb verzióját kell használnia. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) a telepítéshez és a frissítéshez.

Végül, ha helyileg futtatja a PowerShellt, a parancsot is futtatnia kell `Connect-AzAccount` . Ez a parancs létrehozza az Azure-hoz való hozzáférést.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Néhány lépésből megtudhatja, hogy az erőforráscsoport és a virtuális hálózat konfigurálva van-e.

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Ez a példa létrehoz egy *myVirtualNetwork* nevű alapértelmezett virtuális hálózatot a *EastUS* helyen:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Az Azure üzembe helyezi az erőforrásokat egy virtuális hálózaton belüli alhálózaton, ezért létre kell hoznia egy alhálózatot. Hozzon létre egy *alapértelmezett* nevű alhálózati konfigurációt az [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
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
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
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
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Létre kell hoznia egy másik felhasználót és jelszót. Az Azure-ban a virtuális gép létrehozása eltarthat néhány percig.

> [!IMPORTANT]
> Ne folytassa a következő lépéssel, amíg az Azure elkészült.  Ha a kimenetet a PowerShellbe tér vissza, a rendszer készen áll.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) használatával visszaállíthatja egy virtuális gép nyilvános IP-címét. Ez a példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
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
    > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha így tesz, válassza az **Igen** vagy a **Folytatás**lehetőséget.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A *myVm1*távoli asztal nyissa meg a PowerShellt.

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

1. Ha engedélyezni szeretné a *myVm2* számára a *myVm1* pingelését egy későbbi lépésben, írja be a következő parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ezzel a paranccsal a Windows tűzfalon keresztül küldhet ICMP-t.

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Ismételje meg a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet)című témakör lépéseit. Ezúttal kapcsolódjon a *myVm2*.

1. A *myVm2* virtuális gépen írja be a `ping myvm1` parancsot egy parancssorba.

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

    Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a *myVm1* virtuális gép esetében, válaszokat fog kapni a *myVm1* virtuális géptől.

1. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózattal és a virtuális gépekkel, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Egy virtuális géphez kapcsolódott az internetről, és a két virtuális gép között magántulajdonban kommunikál.
Az Azure lehetővé teszi, hogy korlátlan privát kommunikációt biztosítson a virtuális gépek között. Alapértelmezés szerint az Azure csak a Windows rendszerű virtuális gépekről bejövő távoli asztali kapcsolatokat teszi lehetővé az internetről. A következő cikkből megtudhatja, hogyan konfigurálhat különböző típusú virtuálisgép-hálózati kommunikációt:
> [!div class="nextstepaction"]
> [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md)
