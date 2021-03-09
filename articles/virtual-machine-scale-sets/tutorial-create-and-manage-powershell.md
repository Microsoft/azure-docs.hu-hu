---
title: 'Oktatóanyag: Azure virtuálisgép-méretezési csoport létrehozása & kezelése – Azure PowerShell'
description: Arra vonatkozó tudnivalók, hogyan használhatja a PowerShellt virtuálisgép-méretezési csoportok létrehozásához, valamint néhány olyan általános kezelési feladat elvégzéséhez, amilyen a példányok elindítása és leállítása, vagy a méretezési csoport kapacitásának módosítása.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 102ab619a3f341658383dc4997f9fbfc2cd12a43
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519180"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Oktatóanyag: Virtuálisgép-méretezési csoport létrehozása és kezelése az Azure PowerShell használatával

A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Virtuálisgép-példányok adott méreteinek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuálisgép-méretezési csoport létrejötte előtt létre kell hozni. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hoznak létre a *EastUS* régióban. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
Az erőforráscsoport nevének meghatározására a méretezési csoport létrehozásakor vagy módosításakor kerül sor a jelen oktatóanyag keretein belül.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuálisgép-példányok rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozzon létre egy virtuálisgép-méretezési készletet a [New-AzVmss](/powershell/module/az.compute/new-azvmss). A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó olyan szabályokat tartalmaz, amelyek elosztják a 80-as TCP-porton beérkező forgalmat, valamint lehetővé teszi a távoli asztali forgalmat a 3389-es TCP-porton és a PowerShell távoli eljáráshívást az 5985-ös TCP-porton:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

A méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe.

> [!IMPORTANT]
> Ha nem tud csatlakozni a méretezési csoporthoz, előfordulhat, hogy létre kell hoznia egy hálózati biztonsági csoportot a *[-SecurityGroupName "mySecurityGroup"](/powershell/module/az.compute/new-azvmss)* paraméter hozzáadásával.


## <a name="view-the-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok megtekintése
A méretezési csoportokban lévő virtuálisgép-példányok listájának megtekintéséhez használja a [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) a következő módon:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében két virtuálisgép-példány látható a méretezési csoportban:

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Egy adott virtuálisgép-példányra vonatkozó további információk megtekintéséhez adja hozzá a `-InstanceId` [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)paramétert. A következő példa információkat tekint meg az *1*-es számú virtuálisgép-példányról:

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Kapcsolatadatok listázása
A rendszer egy nyilvános IP-címet rendel hozzá a terheléselosztóhoz, amely a forgalmat az egyéni virtuálisgép-példányokhoz irányítja. Alapértelmezés szerint a rendszer a hálózati címfordítási (NAT) szabályokat adja hozzá az Azure-terheléselosztóhoz, amely továbbítja a távoli kapcsolati forgalmat az egyes virtuális gépekre egy adott porton keresztül. A méretezési csoportban lévő virtuálisgép-példányok csatlakoztatásához létesítsen távoli kapcsolatot a hozzárendelt nyilvános IP-címhez és portszámhoz.

A méretezési csoportokban lévő virtuálisgép-példányokhoz való csatlakozáshoz szükséges NAT-portok listázásához először töltse le a terheléselosztó objektumot a [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Ezután tekintse meg a bejövő NAT-szabályokat a [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Az alábbi példakimeneten a példány neve, a terheléselosztó nyilvános IP-címe és a portszám látható, amelyeket a NAT-szabályok a forgalom továbbításához használnak:

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

A szabály *neve* a virtuálisgép-példány nevével van igazítva, ahogy az egy korábbi [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) parancsban látható. Ha például a *0-s* virtuálisgép-példányhoz szeretne csatlakozni, használja a *myScaleSet3389.0* elemet, és kapcsolódjon az *50001*-es porthoz. Az *1-es* virtuálisgép-példányhoz való csatlakozáshoz használja a *myScaleSet3389.1* elemben lévő értéket, és kapcsolódjon az *50002*-es porthoz. A PowerShell távoli eljáráshívás használatához csatlakozzon az *5985*-ös *TCP*-port megfelelő virtuálisgép-példány szabályához.

A terheléselosztó nyilvános IP-címének megtekintése a [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Példa a kimenetre:

```powershell
IpAddress
---------
52.168.121.216
```

Távoli kapcsolaton keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg saját nyilvános IP-címét, valamint a kívánt VM-példány portszámát, amint az az előző parancsokban látható. Ha a rendszer kéri, adja meg a méretezési csoport létrehozásakor használt hitelesítő adatokat (alapértelmezés szerint a minta parancsaiban, az *azureuser* és a *P \@ ssw0rd!*). Az Azure Cloud Shell használata esetén ezt a lépést egy helyi PowerShell-parancssorból vagy egy távoli asztali ügyfélről hajtsa végre. A következő példa az *1* nevű virtuálisgép-példányhoz csatlakozik:

```powershell
mstsc /v 52.168.121.216:50001
```

A virtuálisgép-példányba történő bejelentkezést követően szükség szerint manuális konfigurációs módosításokat hajthat végre. Egyelőre zárja be a távoli kapcsolatot.


## <a name="understand-vm-instance-images"></a>A virtuálisgép-példányok rendszerképeinek ismertetése
Az Azure Marketplace-en számos rendszerkép található, amelyekkel új virtuálisgép-példányokat lehet létrehozni. Az elérhető közzétevők listájának megtekintéséhez használja a [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancsot.

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Egy adott közzétevőhöz tartozó rendszerképek listájának megtekintéséhez használja a [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku). A rendszerképek listája a `-PublisherName` (közzétevő) vagy az `-Offer` (ajánlat) alapján is szűrhető. Az alábbi példában a listát az összes olyan rendszerképre szűrjük, amelyek közzétevője a *MicrosoftWindowsServer*, és amelyek ajánlata megfelel a *WindowsServer*-nek:

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

A például szolgáló következő kimenet az összes elérhető Windows Server-rendszerképet megjeleníti:

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Az oktatóanyag elején a méretezési csoport létrehozásakor a *Windows Server 2016 DataCenter* alapértelmezett virtuálisgép-rendszerkép lett megadva a virtuálisgép-példányokhoz. A [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)kimenetén alapuló másik virtuálisgép-rendszerkép is megadható. Az alábbi példa egy `-ImageName` paraméterrel rendelkező méretezési csoportot hoz létre a *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest* virtuálisgép-rendszerképének megadásához. Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> Javasoljuk, hogy a *legújabb* rendszerkép-verziót használja. A "legfrissebb" érték megadásával a lemezkép legújabb verziója használható a telepítés ideje alatt. Vegye figyelembe, hogy még ha a legújabbat is használja, a virtuálisgép-lemezkép nem frissül automatikusan az üzembe helyezési idő után, még akkor is, ha az új verzió elérhetővé válik.

## <a name="understand-vm-instance-sizes"></a>A virtuálisgép-példányok méreteinek ismertetése
A virtuálisgép-példány mérete, más néven *SKU*, a virtuálisgép-példány által elérhető számítási erőforrások, azaz a processzor, a grafikus processzor és a memória mennyiségét határozza meg. A méretezési csoportban lévő virtuálisgép-példányokat a várható számítási igényeknek megfelelően kell méretezni.

### <a name="vm-instance-sizes"></a>A virtuálisgép-példányok mérete
Az alábbi táblázat a virtuális gépek gyakori méreteit használati esetek alapján kategorizálja.

| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításoptimalizált](../virtual-machines/sizes-compute.md)   | Fs, F             | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](../virtual-machines/sizes-storage.md)      | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](../virtual-machines/sizes-hpc.md) | H, A8-11          | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Elérhető virtuálisgép-példányméretek keresése
Egy adott régióban elérhető virtuálisgép-példányok méretének megtekintéséhez használja a [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) parancsot. 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

A kimenet a következő sűrített példához hasonló, amelyben az egyes virtuálisgép-méretekhez hozzárendelt erőforrások láthatók:

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Az oktatóanyag elején a méretezési csoport létrehozásakor a *Standard_DS1_v2* alapértelmezett virtuálisgép-termékváltozat (SKU) lett megadva a virtuálisgép-példányokhoz. A [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize)kimenete alapján eltérő virtuálisgép-példányok is megadhatók. Az alábbi példa a `-VmSize` paraméterrel a méretezési csoportot a *Standard_F1* virtuálisgép-példányméret megadásával hozza létre. Mivel a méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe, nem kell üzembe helyeznie az alábbi méretezési csoportot:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
A méretezési csoport létrehozása során két virtuálisgép-példányt igényelt. A méretezési csoportban lévő virtuálisgép-példányok számának növeléséhez vagy csökkentéséhez manuálisan módosíthatja a kapacitást. A méretezési csoport létrehozza vagy eltávolítja a szükséges számú virtuálisgép-példányt, majd konfigurálja a terheléselosztót a forgalom elosztásához.

Először hozzon létre egy méretezési csoport objektumot a [Get-AzVmss](/powershell/module/az.compute/get-azvmss)paranccsal, majd adja meg a új értékét `sku.capacity` . A kapacitás módosításának alkalmazásához használja az [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Az alábbi példa a méretezési csoportban lévő virtuálisgép-példányok számát *3*-ra állítja:

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. A méretezési csoportba tartozó példányok számának megtekintéséhez használja a [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Az alábbi példa kimenetében látható, hogy a méretezési csoport kapacitása mostantól *3*:

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Általános kezelési feladatok
Mostantól létrehozhat egy méretezési csoportot, listát készíthet a kapcsolatadatokról és virtuálisgép-példányokhoz csatlakozhat. Megismerte, hogyan használhatók a különböző operációsrendszer-lemezképek a virtuálisgép-példányokhoz, hogyan választhatók ki a különféle virtuálisgép-méretek és hogyan méretezhető manuálisan a példányok száma. A napi felügyelet részeként szükség lehet a méretezési csoportban lévő virtuálisgép-példányok leállítására, elindítására vagy újraindítására.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok leállítása és felszabadítása
A méretezési csoportokban lévő egy vagy több virtuális gép leállításához használja a [stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. A következő példa leállítja az *1-es* példányt:

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

A leállított virtuális gépek alapértelmezés szerint felszabadított állapotban vannak, és nem kell értük díjat fizetni. Ha azt szeretné, hogy a virtuális gép a leállítás után is üzembe helyezett állapotban maradjon, adja hozzá a `-StayProvisioned` paramétert az előző parancshoz. Az üzembe helyezett állapotú, leállított virtuális gépek esetében normál számítási díjak merülnek fel.

### <a name="start-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok indítása
Egy vagy több virtuális gép egy méretezési csoportba való elindításához használja a [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. A következő példa elindítja az *1-es* példányt:

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>A méretezési csoportokban lévő virtuálisgép-példányok újraindítása
Egy vagy több virtuális gép újraindításához a méretezési csoportokban használja a [refanyar-AzVmss](/powershell/module/az.compute/restart-azvmss). Az `-InstanceId` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. A következő példa újraindítja az *1-es* példányt:

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az erőforráscsoportok törlésével az összes bennük foglalt erőforrás, azaz a virtuálisgép-példányok, a virtuális hálózat és a lemezek is törölve lesznek. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag az Azure PowerShell néhány alapszintű méretezésicsoport-létrehozási és -kezelési műveletét mutatta be:

> [!div class="checklist"]
> * Virtuálisgép-méretezési csoport létrehozása és kapcsolódás a csoporthoz
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Méretezési csoport manuális méretezése
> * Méretezési csoportra vonatkozó gyakori felügyeleti feladatok végrehajtása

Folytassa a következő oktatóanyaggal, amely a méretezésicsoport-lemezeket ismerteti.

> [!div class="nextstepaction"]
> [Adatlemezek használata méretezési csoportokkal](tutorial-use-disks-powershell.md)
