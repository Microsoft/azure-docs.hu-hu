---
title: Oktatóanyag – Windows rendszerű virtuális gépek létrehozása és kezelése Azure PowerShell
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Windows rendszerű virtuális gépeket az Azure-ban az Azure PowerShell használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: ae298562b5d3306cf6c20b4c2fb7b378ba99c986
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561619"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Oktatóanyag: Windows rendszerű virtuális gépek létrehozása és felügyelete Azure PowerShell-lel

Az Azure-beli virtuális gépek egy teljes mértékben konfigurálható és rugalmas számítási környezetet nyújtanak. Az oktatóanyag az Azure-beli virtuális gépek (VM) üzembe helyezésének alapvető feladatait ismerteti, például a virtuális gépek méretének és rendszerképeinek kiválasztását és a virtuális gépek üzembe helyezését. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) paranccsal.

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportot még a virtuális gép létrejötte előtt létre kell hozni. A következő példában létrehozunk egy *myResourceGroupVM* nevű erőforráscsoportot a *EastUS* régióban:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

Az erőforráscsoport meghatározására a virtuális gép létrehozásakor vagy módosításakor kerül sor, ahogy ez az oktatóanyagból is kiderül.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A virtuális gépek létrehozásakor több lehetőség is elérhető, például az operációs rendszer képe, a hálózati konfiguráció és a rendszergazdai hitelesítő adatok. Ez a példa létrehoz egy *myVM* nevű virtuális gépet, amely a Windows Server 2016 Datacenter alapértelmezett verzióját futtatja.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```

Hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Kapcsolódás egy virtuális géphez

Miután az üzembe helyezés befejeződött, hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához. Jegyezze fel ezt az IP-címet, hogy egy későbbi lépésben csatlakozhasson majd hozzá a böngészőjében a webes kapcsolat tesztelése érdekében.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Használja az alábbi parancsot a helyi gépen, ha távoli asztali kapcsolatot szeretne létrehozni a virtuális géphez. Cserélje le az IP-címet a virtuális gép *publicIPAddress* címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```powershell
mstsc /v:<publicIpAddress>
```

A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a virtuális géphez létrehozott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

## <a name="understand-marketplace-images"></a>A Marketplace-beli rendszerképek megismerése

Az Azure Marketplace-en számos rendszerkép található, amelyekkel új virtuális gépeket lehet létrehozni. Az előző lépések során a virtuális gépet a Windows Server 2016 Datacenter rendszerképének használatával hoztuk létre. Ebben a lépésben a PowerShell-modul segítségével további Windows-rendszerképeket keresünk a piactéren, amelyek az új virtuális gépek alapjául szolgálhatnak. Ez a folyamat a rendszerkép [azonosításához](cli-ps-findimage.md#terminology) szükséges közzétevő, ajánlat, termékváltozat és esetleg verziószám megkereséséből áll.

A [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) parancs használatával visszaállíthatja a rendszerkép-közzétevők listáját:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

A [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) segítségével visszaállíthatja a képajánlatok listáját. Ezzel a paranccsal a lekért lista az adott közzétevőre lesz szűrve, amelynek neve `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

Az eredmény az alábbi példában láthatóhoz hasonló lesz: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

A [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) parancs ezután a közzétevő és az ajánlat neve alapján szűri a képnevek listáját.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

Az eredmény az alábbi példában láthatóhoz hasonló lesz: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Ezek az adatok egy virtuális gép adott rendszerképpel történő üzembe helyezéséhez használhatók. Ez a példa egy virtuális gépet helyez üzembe a tárolói rendszerképpel rendelkező Windows Server 2016 legfrissebb verziójának használatával.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Get-Job` parancsmaggal tekintheti meg.

## <a name="understand-vm-sizes"></a>Virtuális gépek méreteinek ismertetése

A virtuális gép mérete határozza meg a virtuális gép számára elérhető számítási erőforrások, például a processzor, a GPU és a memória mennyiségét. A virtuális gépeket a számítási feladatok számára megfelelő virtuálisgép-mérettel kell létrehozni. Ha a számítási feladatok mennyisége nő, a meglévő virtuális gépet át is lehet méretezni.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Az alábbi táblázat a méreteket használati esetek alapján kategorizálja.  

| Típus                     | Gyakori méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Általános célú](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Kiegyensúlyozott processzor-memória arány. Ideális választás fejlesztéshez/teszteléshez, valamint kis- és közepes méretű alkalmazásokhoz és adatkezelési megoldásokhoz.  |
| [Számításoptimalizált](../sizes-compute.md)   | Fsv2          | Magas processzor-memória arány a processzor javára. Megfelelő választás a közepes forgalmú alkalmazásokhoz, hálózati berendezésekhez és kötegelt folyamatokhoz.        |
| [Memóriaoptimalizált](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Magas memória-mag arány a memória javára. Ideális választás relációs adatbázisokhoz, közepes és nagy gyorsítótárakhoz, memóriában végzett elemzésekhez.                 |
| [Tároptimalizált](../sizes-storage.md)      | Lsv2, ls              | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Specializált virtuális gépek nagy terhelést jelentő grafikus rendereléshez és videószerkesztéshez.       |
| [Nagy teljesítmény](../sizes-hpc.md) | H        | Leghatékonyabb processzorral rendelkező virtuális gépeink, választható nagy átviteli sebességű hálózati adapterekkel (RDMA). |

### <a name="find-available-vm-sizes"></a>Elérhető virtuálisgép-méretek keresése

Egy adott régióban elérhető virtuálisgép-méretek listájának megtekintéséhez használja a [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) parancsot.

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek az üzembe helyezésüket követően a rendelkezésre álló erőforrás-kiosztás növelése vagy csökkentése érdekében átméretezhetők.

A virtuális gép átméretezése előtt ellenőrizze, hogy a kívánt méret elérhető-e az aktuális virtuálisgép-fürtön. A [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) parancs a méretek listáját adja vissza.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Ha a méret elérhető, a virtuális gép átméretezhető a bekapcsolt állapotból, azonban a művelet során újraindul.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Ha a kívánt méret nem érhető el az aktuális fürtön, akkor a virtuális gépet fel kell osztani az átméretezési művelet megkezdése előtt. A virtuális gép felszabadítása törli az ideiglenes lemezen található adatokat, és a nyilvános IP-cím is módosul, ha nem statikus IP-címet használ.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Virtuális gépek energiaállapotai

Számos energiaállapot van, amelyek közül az Azure-beli virtuális gépek felvesznek egyet. 


| Energiaállapot | Leírás
|----|----|
| Indítás | A virtuális gép indítása folyamatban van. |
| Futó | A virtuális gép fut. |
| Leállítás | A virtuális gép leállítása folyamatban van. |
| Leállítva | A virtuális gép leállt. A leállított virtuális gépekért továbbra is díjat kell fizetni.  |
| Felszabadítás | A virtuális gép fel van foglalva. |
| Felszabadítva | Azt jelzi, hogy a virtuális gép el lett távolítva a hypervisorból, de továbbra is elérhető a vezérlési síkon. A `Deallocated` állapotban lévő virtuális gépekért nem kell díjat fizetni. |
| - | A virtuális gép energiagazdálkodási állapota ismeretlen. |


Egy adott virtuális gép állapotának beszerzéséhez használja a [Get-AzVM](/powershell/module/az.compute/get-azvm) parancsot. Ügyeljen arra, hogy érvényes nevet adjon meg a virtuális gép és az erőforráscsoport számára.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

A kimenet az alábbi példában láthatóhoz hasonló lesz:

```powershell
Status
------
PowerState/running
```

Az előfizetésben lévő összes virtuális gép energiaellátási állapotának lekéréséhez használja [Virtual Machines az összes olyan API-](/rest/api/compute/virtualmachines/listall) t, amelynek **statusOnly** a paraméter értéke *true (igaz*).

## <a name="management-tasks"></a>Felügyeleti feladatok

A virtuális gépek életciklusa során előfordulhat, hogy olyan felügyeleti feladatokat szeretne futtatni, mint például a virtuális gépek elindítása, leállítása vagy törlése. Emellett előfordulhat, hogy szkripteket is szeretne létrehozni az ismétlődő vagy összetett feladatok automatizálására. Az Azure PowerShell használatával számos gyakori felügyeleti feladat futtatható a parancssorból vagy szkriptek segítségével.

### <a name="stop-a-vm"></a>Virtuális gép leállítása

Állítsa le és szabadítson fel egy virtuális gépet a [stop-AzVM](/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Ha regisztrált állapotban szeretné tartani a virtuális gépet, használja a -StayProvisioned paramétert.

### <a name="start-a-vm"></a>Virtuális gép elindítása

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Az erőforráscsoport minden elemét törli, amikor törli az erőforráscsoportot.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuális gépek létrehozásának és kezelésének alapvető műveleteivel ismerkedett meg, például:

> [!div class="checklist"]
> * Virtuális gép létrehozása és csatlakozás a virtuális géphez
> * Virtuálisgép-rendszerképek kiválasztása és használata
> * Adott virtuálisgép-méretek áttekintése és használata
> * Virtuális gép átméretezése
> * Virtuális gép állapotának áttekintése és értelmezése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-lemezeket ismerteti.  

> [!div class="nextstepaction"]
> [VM-lemezek létrehozása és kezelése](./tutorial-manage-data-disk.md)