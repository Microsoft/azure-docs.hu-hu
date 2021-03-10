---
title: Felügyelt rendszerkép létrehozása az Azure-ban
description: Egy általánosított virtuális gép vagy virtuális merevlemez felügyelt rendszerképének létrehozása az Azure-ban. A rendszerképek használatával több, felügyelt lemezeket használó virtuális gép hozható létre.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 9128c44b7f446ab849d2afac055005a1b5fb3fcb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562231"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Felügyelt rendszerkép létrehozása általánosított Azure-beli virtuális gépből

Az olyan általánosított virtuális gépekből (VM-ekből), amelyek felügyelt lemezként vagy nem felügyelt lemezként vannak tárolva egy tárfiókban, létrehozható egy felügyelt rendszerkép-erőforrás. A rendszerkép ezután több virtuális gép létrehozásához is használható. További információ a felügyelt lemezképek számlázásáról: [Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). 

Egy felügyelt rendszerkép akár 20 egyidejű telepítést is támogat. Ha több mint 20 virtuális gépet kíván létrehozni párhuzamosan, ugyanabból a felügyelt rendszerképből, az egyes virtuális merevlemezek tárolási teljesítményének korlátai miatt kiépítési időtúllépéseket eredményezhet. Több mint 20 virtuális gép egyidejű létrehozásához használjon egy 1 replikával konfigurált [megosztott Képgyűjteményi](../shared-image-galleries.md) képet minden 20 egyidejű virtuálisgép-telepítéshez.

## <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep eltávolítja az összes személyes fiókot és biztonsági információt, majd előkészíti a gépet képként való használatra. További információ a Sysprep eszközről: a [Sysprep áttekintése](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Győződjön meg arról, hogy a Sysprep támogatja a számítógépen futó kiszolgálói szerepköröket. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) és a nem [támogatott forgatókönyvekhez](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Miután futtatta a sysprept egy virtuális gépen, a virtuális gép *általánosított* minősül, és nem indítható újra. A virtuális gép általánosítása nem vonható vissza. Ha meg kell őriznie az eredeti virtuális gép működését, hozzon létre egy [másolatot a virtuális](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) gépről, és általánosítsa a másolatát. 
>
>A Sysprep használatához a meghajtókat teljes mértékben vissza kell fejteni. Ha engedélyezte a titkosítást a virtuális gépen, tiltsa le a titkosítást a Sysprep futtatása előtt.
>
> Ha a virtuális merevlemez (VHD) első alkalommal történő feltöltése előtt tervezi a Sysprep futtatását, győződjön meg arról, hogy [előkészítette a virtuális gépet](prepare-for-upload-vhd-image.md).  
> 
> 

A Windows rendszerű virtuális gép általánosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
   
2. Nyisson meg egy parancssori ablakot rendszergazdaként. 

3. Törölje a Panther könyvtárat (C:\Windows\Panther). Ezután módosítsa a könyvtárat a%WINDIR%\system32\sysprep értékre, majd futtassa a parancsot `sysprep.exe` .
   
4. A **rendszerelőkészítő eszköz** párbeszédpanelen jelölje be a **rendszerszintű felhasználói élmény (OOBE) megadása** jelölőnégyzetet, és jelölje be az **általánosítás** jelölőnégyzetet.
   
5. A **leállítási beállításoknál** válassza a **Leállítás** lehetőséget.
   
6. Válassza az **OK** lehetőséget.
   
    ![A Sysprep elindítása](./media/upload-generalized-managed/sysprepgeneral.png)

6. A Sysprep befejezésekor a rendszer leállítja a virtuális gépet. Ne indítsa újra a virtuális gépet.

> [!TIP]
> Nem **kötelező** A [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) használatával optimalizálja a rendszerképet, és csökkentse a virtuális gép első indításának idejét.
>
> A rendszerkép optimalizálásához csatlakoztassa a virtuális merevlemezt úgy, hogy duplán rákattint rá a Windows Intézőben, majd futtassa a DISM eszközt a `/optimize-image` paraméterrel.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Ahol D: a csatlakoztatott virtuális merevlemez elérési útja.
>
> A futtatásnak `DISM /optimize-image` a virtuális merevlemez utolsó módosításának kell lennie. Ha az üzembe helyezés előtt módosítja a virtuális merevlemezt, újra kell futtatnia `DISM /optimize-image` .

## <a name="create-a-managed-image-in-the-portal"></a>Felügyelt rendszerkép létrehozása a portálon 

1. A virtuális gép rendszerképének kezeléséhez lépjen a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.

2. Válassza ki a virtuális gépet a listából.

3. A **virtuális gép virtuálisgép** -lapjának felső menüjében válassza a **rögzítés** lehetőséget.

   Megjelenik a **rendszerkép létrehozása** lap.

4. A **név** mezőben fogadja el az előre megadott nevet, vagy adjon meg egy nevet, amelyet a képhez használni szeretne.

5. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, adjon meg egy nevet, vagy válasszon ki egy erőforráscsoportot, amelyet a legördülő listából szeretne használni.

6. Ha a rendszerkép létrehozása után törölni szeretné a forrás virtuális gépet, jelölje be a **virtuális gép automatikus törlése a rendszerkép létrehozása után** jelölőnégyzetet.

7. Ha azt szeretné, hogy a rendszerképek bármelyik [rendelkezésre állási zónában](../../availability-zones/az-overview.md)használhatók legyenek, válassza **a** be lehetőséget a **zóna rugalmassága** lehetőségnél.

8. A kép létrehozásához válassza a **Létrehozás** lehetőséget.

A rendszerkép létrehozása után **képerőforrásként** megkeresheti az erőforráscsoport erőforrásainak listáját.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Virtuális gép rendszerképének létrehozása a PowerShell használatával

 

A rendszerkép közvetlenül a virtuális gépről való létrehozása biztosítja, hogy a lemezkép tartalmazza a virtuális géphez társított összes lemezt, beleértve az operációsrendszer-lemezt és az adatlemezeket. Ebből a példából megtudhatja, hogyan hozhat létre felügyelt rendszerképeket egy felügyelt lemezeket használó virtuális gépről.

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a Azure PowerShell modul legújabb verziójával. A verzió megkereséséhez futtassa a parancsot a `Get-Module -ListAvailable Az` PowerShellben. Ha frissítenie kell, tekintse [meg a Azure PowerShell telepítése Windows rendszerre a PowerShellGet](/powershell/azure/install-az-ps)használatával című témakört. Ha helyileg futtatja a PowerShellt, futtassa a parancsot `Connect-AzAccount` Az Azure-beli kapcsolatok létrehozásához.


> [!NOTE]
> Ha a rendszerképet a zóna redundáns tárolójában szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , és tartalmazza a `-ZoneResilient` paramétert a rendszerkép-konfigurációban ( `New-AzImageConfig` parancs).

Virtuálisgép-rendszerkép létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre néhány változót.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Győződjön meg arról, hogy a virtuális gép fel van foglalva.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Állítsa a virtuális gép állapotát **általánosítva** értékre. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Töltse be a virtuális gépet. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Rendszerkép létrehozása felügyelt lemezről a PowerShell használatával

Ha csak az operációsrendszer-lemez lemezképét szeretné létrehozni, akkor a felügyelt lemez AZONOSÍTÓját állítsa be operációsrendszer-lemezként:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Szerezze be a virtuális gépet.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. A felügyelt lemez AZONOSÍTÓjának beolvasása.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Rendszerkép létrehozása pillanatképből a PowerShell használatával

A következő lépések végrehajtásával hozhat létre felügyelt rendszerképet egy általánosított virtuális gépről készült pillanatképből:

    
1. Hozzon létre néhány változót. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. A pillanatkép beszerzése.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Hozza létre a rendszerkép-konfigurációt.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Hozza létre a rendszerképet.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Rendszerkép létrehozása a Storage-fiókot használó virtuális gépről

Ha olyan virtuális gépről szeretne felügyelt rendszerképet létrehozni, amely nem felügyelt lemezeket használ, a Storage-fiókban a következő formátumban kell megadnia az operációs rendszer virtuális merevlemezének URI-ját: https://*mystorageaccount*. blob.Core.Windows.net/*vhdcontainer* / *vhdfilename. vhd*. Ebben a példában a VHD a *mystorageaccount*, egy *vhdcontainer* nevű TÁROLÓban, a VHD-fájl pedig *vhdfilename. vhd*.


1.  Hozzon létre néhány változót.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. A virtuális gép leállítása/felszabadítása.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. A virtuális gépet általánosítva kell megjelölnie.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Hozza létre a rendszerképet az általánosított operációs rendszer VHD-fájljának használatával.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Következő lépések
- [Hozzon létre egy virtuális gépet egy felügyelt rendszerképből](create-vm-generalized-managed.md). 