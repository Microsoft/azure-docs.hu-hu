---
title: Oktatóanyag – Azure-lemezek kezelése az Azure PowerShell-lel
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-lemezeket virtuális gépekhez az Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a61d7425a7a907230008ab1d4f15a836150e1518
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549056"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Oktatóanyag – Azure-lemezek kezelése az Azure PowerShell-lel

Az Azure-beli virtuális gépek lemezeket használnak a virtuális gépek operációs rendszereinek, alkalmazásainak és adatainak tárolására. Virtuális gép létrehozásakor fontos szempont, hogy a számítási feladatokkal járó várható terhelésnek megfelelő lemezméretet és konfigurációt válasszon ki. Ez az oktatóanyag a virtuálisgép-lemezek üzembe helyezését és kezelését mutatja be. Az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek

Egy Azure-beli virtuális gép létrehozásakor a rendszer két lemezt automatikusan a virtuális géphez csatol. 

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 4 terabájt méretűek is lehetnek, és a virtuális gépek operációs rendszerei találhatók rajtuk. Ha egy új virtuális gépet (VM) hoz létre egy [Azure Marketplace](https://azure.microsoft.com/marketplace/) -rendszerképből, a JELLEMZŐEN 127 GB (de néhány RENDSZERKÉP kisebb operációsrendszer-lemezzel rendelkezik). Az operációs rendszert futtató lemezhez alapértelmezés szerint a *C:* meghajtóbetűjel van hozzárendelve. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. Az operációsrendszer-lemez **nem** üzemeltethet alkalmazásokat, és nem tárolhat adatokat. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó.

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuális gép. Az ideiglenes lemezek nagy teljesítményűek és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuális gépet egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a [virtuális gép mérete](../sizes.md) határozza meg. Az ideiglenes lemezekhez alapértelmezés szerint a *D:* meghajtóbetűjel van hozzárendelve.

## <a name="azure-data-disks"></a>Azure-adatlemezek

További adatlemezeket adhat hozzá, amelyekre alkalmazásokat telepíthet és amelyeken adatokat tárolhat. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. A virtuális gép mérete határozza meg, hány adatlemez csatolható a virtuális géphez.

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

Az Azure két lemeztípust kínál.

**Standard lemezek** – a merevlemez-meghajtókra épülő lemezek költséghatékony tárolási megoldást, ugyanakkor jó teljesítményt nyújtanak. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

**Prémium szintű lemezek** – SSD-alapú, nagy teljesítményű, kis késleltetésű lemez. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. A virtuális gépek [mérete a méret nevével](../vm-naming-conventions.md), általában **támogatja a Premium Storage** . A DS-sorozat, a DSv2-sorozat, a GS-sorozat és az FS sorozatú virtuális gépek például támogatják a Premium Storage szolgáltatást. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete meghaladja a 64 GB-ot, de kevesebb, mint 128 GB, a lemez típusa P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Ha Premium Storage-lemezt épít ki, a standard szintű tárterülettől eltérően, akkor garantált a lemez kapacitása, IOPS és átviteli sebessége. Ha például létrehoz egy P50 lemezt, az Azure 4 095 GB tárolókapacitást, 7 500 IOPS és 250 MB/s adatátviteli sebességet biztosít a lemez számára. Az alkalmazás a kapacitás és a teljesítmény egészét vagy egy részét használhatja. A prémium SSD lemezek úgy lettek kialakítva, hogy alacsony egyszámjegyű ezredmásodperces késéseket és célként megadott IOPS és átviteli sebességet biztosítson az idő 99,9%-ában.

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például a Standard_GS5 virtuális géphez 64 adatlemez csatolható. Ha ezen lemezek mindegyike P30-ra van méretezve, maximum 80 000-es IOPS-érték érhető el. A virtuális gépenkénti maximális IOPS-értékről szóló részletes információkért lásd a [virtuális gépek típusait és méreteit](../sizes.md) ismertető cikket.

## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, hozzon létre egy virtuális gépet az alábbi parancsokkal.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal:


Hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Amikor a rendszer kéri, adja meg a virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Hozza létre a kezdeti konfigurációt a [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig). A következő példa egy 128 GB méretű lemezt konfigurál.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Hozza létre az adatlemezt a [New-AzDisk](/powershell/module/az.compute/new-azdisk) paranccsal.

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Szerezze be azt a virtuális gépet, amelyhez hozzá szeretné adni az adatlemezt a [Get-AzVM](/powershell/module/az.compute/get-azvm) paranccsal.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adja hozzá az adatlemezt a virtuális gép konfigurációjához az [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) paranccsal.

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Frissítse a virtuális gépet az [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) paranccsal.

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Adatlemezek előkészítése

Miután hozzácsatolt egy lemezt a virtuális géphez, az operációs rendszert konfigurálni kell a lemez használatához. Az alábbi példa bemutatja, hogyan konfigurálhatja manuálisan a virtuális géphez elsőként hozzáadott lemezt. Ez a folyamat is automatizálható az [egyéni szkriptbővítmény](./tutorial-automate-vm-deployment.md) használatával.

### <a name="manual-configuration"></a>Manuális konfigurálás

Hozzon léte egy RDP-kapcsolatot a virtuális géppel. Nyissa meg a PowerShellt, és futtassa ezt a szkriptet.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Az adatlemez ellenőrzése

Annak ellenőrzéséhez, hogy az adatlemez csatlakozik-e, tekintse meg a csatlakoztatott `DataDisks``StorageProfile` profilját.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Az alábbi példában látható kimenethez hasonló eredményt kell kapnia:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuálisgép-lemezekkel kapcsolatos témakörökkel ismerkedett meg, például:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

Folytassa a következő oktatóanyaggal, amely a virtuális gép konfigurálásának automatizálását ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
