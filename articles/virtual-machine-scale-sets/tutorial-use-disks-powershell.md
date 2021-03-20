---
title: Oktatóanyag – lemezek létrehozása és használata méretezési csoportokhoz Azure PowerShell
description: Megismerheti, hogyan hozhat létre és használhat felügyelt lemezeket a virtuálisgép-méretezési csoportokhoz az Azure PowerShell használatával, beleértve a lemezek hozzáadását, előkészítését, listázását és leválasztását.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 9e995e88b80bf14f9c7784f465bcd3d89d0bed65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92367958"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-azure-powershell"></a>Oktatóanyag: Lemezek létrehozása és használata virtuálisgép-méretezési csoportokhoz Azure PowerShell-lel

A virtuálisgép-méretezési csoportok lemezeket használnak a virtuálisgép-példányok operációs rendszereinek, alkalmazásainak és adatainak tárolására. Méretezési csoportok létrehozásakor és kezelésekor fontos szempont, hogy a számítási feladatok jelentette várható terhelésnek megfelelő lemezméretet és konfigurációt válasszon ki. Ez az oktatóprogram bemutatja, hogyan hozhat létre és kezelhet virtuálisgép-lemezeket. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek
Egy méretezési csoport létrehozásakor vagy skálázásakor a rendszer két lemezt automatikusan az egyes virtuálisgép-példányokhoz csatol. 

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 2 TB méretűek is lehetnek, és a virtuálisgép-példány operációs rendszere található rajtuk. Az operációsrendszer-lemez alapértelmezés szerint a */dev/sda* címkét kapja. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. A konfiguráció miatt az operációsrendszer-lemez **nem** üzemeltethet alkalmazásokat és nem tárolhat adatokat. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó. 

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuálisgép-példány. Ezek nagy teljesítményű lemezek, és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuálisgép-példányt egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a virtuálisgép-példány mérete határozza meg. Az ideiglenes lemezek a */dev/sdb* címkét kapják, a csatlakoztatási pontjuk pedig */mnt*.

### <a name="temporary-disk-sizes"></a>Ideiglenes lemezek méretei
| Típus | Gyakori méretek | Ideiglenes lemez max. mérete (GiB) |
|----|----|----|
| [Általános célú](../virtual-machines/sizes-general.md) | A, B és D sorozat | 1600 |
| [Számításoptimalizált](../virtual-machines/sizes-compute.md) | F sorozat | 576 |
| [Memóriaoptimalizált](../virtual-machines/sizes-memory.md) | D, E, G és M sorozat | 6144 |
| [Tároptimalizált](../virtual-machines/sizes-storage.md) | L sorozat | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N sorozat | 1440 |
| [Nagy teljesítmény](../virtual-machines/sizes-hpc.md) | A és H sorozat | 2000 |


## <a name="azure-data-disks"></a>Azure-adatlemezek
További adatlemezek adhatók hozzá, amelyekre alkalmazásokat telepíthet és amelyeken adatokat tárolhat. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. Az egyes adatlemezek kapacitása maximum 4 TB lehet. A virtuálisgép-példány mérete határozza meg, hány adatlemez csatolható. A virtuális gépek minden vCPU-jához két adatlemez csatolható.

### <a name="max-data-disks-per-vm"></a>Adatlemezek max. száma virtuális gépenként
| Típus | Gyakori méretek | Adatlemezek max. száma virtuális gépenként |
|----|----|----|
| [Általános célú](../virtual-machines/sizes-general.md) | A, B és D sorozat | 64 |
| [Számításoptimalizált](../virtual-machines/sizes-compute.md) | F sorozat | 64 |
| [Memóriaoptimalizált](../virtual-machines/sizes-memory.md) | D, E, G és M sorozat | 64 |
| [Tároptimalizált](../virtual-machines/sizes-storage.md) | L sorozat | 64 |
| [GPU](../virtual-machines/sizes-gpu.md) | N sorozat | 64 |
| [Nagy teljesítmény](../virtual-machines/sizes-hpc.md) | A és H sorozat | 64 |


## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai
Az Azure két lemeztípust kínál.

### <a name="standard-disk"></a>Standard lemez
A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

### <a name="premium-disk"></a>Prémium lemez
A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Ezek a lemezek termelési számítási feladatokat futtató virtuális gépekhez ajánlatosak. A Premium Storage támogatja a DS, a DSv2, a GS és az FS sorozatú virtuális gépeket. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete kisebb 128 GB-nál, a lemez típusa P10. Ha a lemez mérete 129 GB és 512 GB közé esik, a típus P20. Az 512 GB feletti méretek esetében a típus P30.

### <a name="premium-disk-performance"></a>Prémium szintű lemezek teljesítménye
|Prémium szintű tárolólemezek típusai | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Lemezméret (felfelé kerekítés) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Lemezenkénti maximális IOPS-érték | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Adattovábbítás lemezenként | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például egy Standard_GS5 virtuális gép esetében maximálisan 80 000 IOPS érhető el. A virtuális gépenkénti maximális IOPS-értékkel kapcsolatos részletes információkért lásd a [Windows rendszerű virtuális gépek méreteit](../virtual-machines/sizes.md) ismertető cikket.


## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása
A méretezési csoport létrehozásakor vagy egy meglévő méretezési csoporthoz is létrehozhat és csatlakoztathat lemezeket.

Az API verziójának megfelelően beállíthatja az `2019-07-01` operációsrendszer-lemez méretét a [StorageProfile. OsDisk. diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) tulajdonsággal rendelkező virtuálisgép-méretezési csoportokban. A kiépítés után előfordulhat, hogy a teljes terület használatához ki kell bontania vagy újra particionálnia kell a lemezt. További információ [a lemez kibővítéséről](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Lemezek csatlakoztatása méretezési csoport létrehozásakor
Hozzon létre egy virtuálisgép-méretezési készletet a [New-AzVmss](/powershell/module/az.compute/new-azvmss). Amikor a rendszer kéri, adjon meg egy felhasználónevet és egy jelszót a virtuálisgép-példányokhoz. A forgalom az egyes virtuális gépek közötti elosztása érdekében a parancs egy terheléselosztót is létrehoz. A terheléselosztó szabályai elosztják a 80-as TCP-porton érkező forgalmat, valamint lehetővé teszik a távoli asztali forgalmat a 3389-es, valamint a PowerShell távoli eljáráshívásokat az 5985-ös TCP-porton.

Két lemez jön létre a `-DataDiskSizeGb` paraméterrel. Az első lemez *64* GB, míg a második lemez *128* GB méretű. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -DataDiskSizeInGb 64,128
```

A méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="attach-a-disk-to-existing-scale-set"></a>Lemez csatolása meglévő méretezési csoporthoz
Meglévő méretezési csoporthoz is hozzáadhat lemezeket. Használja az előző lépésben létrehozott méretezési készletet egy másik lemez hozzáadásához az [Add-AzVmssDataDisk](/powershell/module/az.compute/add-azvmssdatadisk)használatával. Az alábbi példa egy újabb *128* GB-os lemezt csatlakoztat egy meglévő méretezési csoporthoz:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Attach a 128 GB data disk to LUN 2
Add-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -CreateOption Empty `
  -Lun 2 `
  -DiskSizeGB 128

# Update the scale set to apply the change
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="prepare-the-data-disks"></a>Az adatlemezek előkészítése
A méretezési csoport virtuálisgép-példányaihoz létrehozott és hozzácsatolt lemezek nyers lemezek. Mielőtt adatokhoz vagy alkalmazásokhoz használhatná őket, a lemezeket elő kell készíteni. A lemezek előkészítéséhez létrehoz egy partíciót, egy fájlrendszert és beolvassa azokat.

A folyamat a méretezési csoport több virtuálisgép-példányán való automatizálásához használja az Azure egyéni szkriptek futtatására szolgáló bővítményét. A bővítmény képes a szkriptek helyi végrehajtására az egyes virtuálisgép-példányokon, például előkészíti a csatlakoztatott adatlemezeket. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/extensions/custom-script-windows.md).


Az alábbi példa egy GitHub-minta-tárházból származó szkriptet hajt végre minden olyan virtuálisgép-példányon, amely az összes nyers csatolt adatlemezt előkészítő [AzVmssExtension](/powershell/module/az.compute/Add-AzVmssExtension) rendelkezik:


```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Define the script for your Custom Script Extension to run
$publicSettings = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
}

# Use Custom Script Extension to prepare the attached data disks
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Annak megerősítéséhez, hogy a lemezek megfelelően elő lettek készítve, RDP-kapcsolaton keresztül csatlakozzon az egyik virtuálisgép-példányhoz. 

Először szerezze be a terheléselosztó objektumot a [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer). Ezután tekintse meg a bejövő NAT-szabályokat a [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig). A NAT-szabályok listázzák az RDP által figyelt mindegyik virtuálisgép-példány *FrontendPort* értékét. Végül szerezze be a terheléselosztó nyilvános IP-címét a [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress):


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort

# View the public IP address of the load balancer
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIPAddress | Select IpAddress
```

A virtuális géphez való csatlakozáshoz adja meg a kívánt VM-példány saját nyilvános IP-címét és portszámát, amint az az előző parancsokban látható. Amikor a rendszer kéri, adja meg a méretezési csoport létrehozásakor használt hitelesítő adatokat. Az Azure Cloud Shell használata esetén ezt a lépést egy helyi PowerShell-parancssorból vagy egy távoli asztali ügyfélről hajtsa végre. A következő példa az *1* nevű virtuálisgép-példányhoz csatlakozik:

```powershell
mstsc /v 52.168.121.216:50001
```

Nyisson meg egy helyi PowerShell-munkamenetet a virtuálisgép-példányon, és tekintse meg a csatlakoztatott lemezeket a [Get-Disk](/powershell/module/storage/get-disk) paranccsal:

```powershell
Get-Disk
```

Az alábbi példakimenet azt mutatja, hogy a három adatlemez csatlakozik a virtuálisgép-példányhoz.

```powershell
Number  Friendly Name      HealthStatus  OperationalStatus  Total Size  Partition Style
------  -------------      ------------  -----------------  ----------  ---------------
0       Virtual HD         Healthy       Online                 127 GB  MBR
1       Virtual HD         Healthy       Online                  14 GB  MBR
2       Msft Virtual Disk  Healthy       Online                  64 GB  MBR
3       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
4       Msft Virtual Disk  Healthy       Online                 128 GB  MBR
```

Az alábbiak szerint vizsgálja meg a virtuálisgép-példány fájlrendszereit és csatlakozási pontjait:

```powershell
Get-Partition
```

Az alábbi példakimenet azt mutatja, hogy a három adatlemez rendelkezik hozzárendelt meghajtó-betűjelekkel:

```powershell
   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000001

PartitionNumber  DriveLetter  Offset   Size  Type
---------------  -----------  ------   ----  ----
1                F            1048576  64 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000002

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                G            1048576  128 GB  IFS

   DiskPath: \\?\scsi#disk&ven_msft&prod_virtual_disk#000003

PartitionNumber  DriveLetter  Offset   Size   Type
---------------  -----------  ------   ----   ----
1                H            1048576  128 GB  IFS
```

A rendszer a skálázásban lévő egyes virtuálisgép-példányokon lévő lemezeket ugyanilyen módon, automatikusan készíti elő. A méretezési csoport vertikális felskálázása esetén a rendszer hozzácsatolja a szükséges adatlemezeket az új virtuálisgép-példányokhoz. Az egyéni szkriptbővítmény is fut, hogy automatikusan előkészítse a lemezeket.

Zárja le a VM-példánnyal kiépített távoli asztali kapcsolat munkamenetet.


## <a name="list-attached-disks"></a>Csatlakoztatott lemezek listázása
A méretezési csoportokhoz csatolt lemezekkel kapcsolatos információk megtekintéséhez használja a [Get-AzVmss](/powershell/module/az.compute/get-azvmss) a következő módon:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
```

A *VirtualMachineProfile.StorageProfile* tulajdonság alatt látható a *DataDisks* (Adatlemezek) listája. A lemez méretével, a tárolási szinttel és a logikai egység számával (LUN) kapcsolatos információk jelennek meg. Az alábbi példa kimenetében három, a méretezési csoporthoz csatlakoztatott adatlemez látható:

```powershell
DataDisks[0]                            :
  Lun                                   : 0
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 64
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[1]                            :
  Lun                                   : 1
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
DataDisks[2]                            :
  Lun                                   : 2
  Caching                               : None
  CreateOption                          : Empty
  DiskSizeGB                            : 128
  ManagedDisk                           :
    StorageAccountType                  : PremiumLRS
```


## <a name="detach-a-disk"></a>Lemez leválasztása
Ha már nincs szüksége egy adott lemezre, válassza azt le a méretezési csoportról. A rendszer eltávolítja a lemezt a méretezési csoport minden virtuálisgép-példányáról. Ha le szeretne választani egy lemezt egy méretezési csoportból, használja a [Remove-AzVmssDataDisk](/powershell/module/az.compute/remove-azvmssdatadisk) , és adja meg a lemez LUN-számát. A logikai egységek az előző szakaszban található [Get-AzVmss](/powershell/module/az.compute/get-azvmss)  kimenetében jelennek meg. Az alábbi példa leválasztja a *3* LUN-számú lemezt a méretezési csoportról:

```azurepowershell-interactive
# Get scale set object
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Detach a disk from the scale set
Remove-AzVmssDataDisk `
  -VirtualMachineScaleSet $vmss `
  -Lun 2

# Update the scale set and detach the disk from the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a lemezek eltávolításához törölje az erőforráscsoportot és az összes erőforrását a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával. A `-Force` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat. A `-AsJob` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat lemezeket a méretezési csoportokkal együtt az Azure PowerShell használatával:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

A következő oktatóanyag azt mutatja be, hogyan használhat egyedi rendszerképeket a méretezési csoport virtuálisgép-példányaihoz.

> [!div class="nextstepaction"]
> [Egyedi rendszerkép használata a méretezési csoport virtuálisgép-példányaihoz](tutorial-use-custom-image-powershell.md)