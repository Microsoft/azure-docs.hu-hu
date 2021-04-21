---
title: Rövid élettartamú operációsrendszer-lemezek
description: További információ az Azure-beli virtuális gépekhez való operációsrendszer-lemezekről.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 24b1be2ca55b057c887c8782ce7eea1150f143da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762622"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Az Azure-beli virtuális gépek rendszerlemezei aphemeral operációsrendszer-lemezeken

A rendszer a kis operációsrendszer-lemezeket a helyi virtuálisgép-tárolón (VM) hozta létre, és nem menti a távoli Azure Storage-ba. Aphemerális operációsrendszer-lemezek jól működnek az állapot nélküli számítási feladatokhoz, ahol az alkalmazások tűrik az egyes virtuális gépek meghibásodását, de a virtuális gépek üzembehelyezésének ideje vagy az egyes virtuálisgép-példányok rendszerképének visszaállítása nagyobb hatással van rá. A rövid idejű operációsrendszer-lemezekkel alacsonyabb olvasási/írási késés érhető el az operációsrendszer-lemezen, és gyorsabb a virtuális gépek rendszerének rendszer-visszaírása. 
 
Aphemerális lemezek fő jellemzői a következőek: 
- Ideális állapot nélküli alkalmazásokhoz.
- A Marketplace-en és egyéni rendszerképekkel is használhatók.
- A virtuális gépek gyors alaphelyzetbe állítása vagy rendszerindításának alaphelyzetbe állítása és a méretezésikészlet-példányok eredeti rendszerindítási állapotba való méretezése.  
- Alacsonyabb késés, az ideiglenes lemezhez hasonlóan. 
- Aphemerális operációsrendszer-lemezek ingyenesek, az operációsrendszer-lemezek tárolási költsége nem jár.
- Minden Azure-régióban elérhetők. 
- A rendszer a rendszer operációsrendszer-lemezét támogatja [a Shared Image Gallery.](./shared-image-galleries.md) 
 

 
Fő különbségek az állandó és a hosszútű operációsrendszer-lemezek között:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **Operációsrendszer-lemez méretkorlátja**      | 2 TiB                                                                                        | Gyorsítótár mérete a virtuális gép méretének vagy 2TiB-nek (amelyik kisebb). A **Gyorsítótár mérete GiB-ban:** [DS,](sizes-general.md) [ES,](sizes-memory.md) [M,](sizes-memory.md) [FS](sizes-compute.md)és [GS](sizes-previous-gen.md#gs-series)              |
| **Támogatott virtuálisgép-méretek**          | Mind                                                                                          | A Prémium szintű tárolást támogató virtuálisgép-méretek, például DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| **Lemeztípus támogatása**           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Csak felügyelt operációsrendszer-lemez                                                               |
| **Régiós támogatás**              | Minden régió                                                                                  | Minden régió                              |
| **Adatmegőrzés**            | Az operációsrendszer-lemezre írt operációsrendszer-lemezadatokat az Azure Storage tárolja                                  | Az operációsrendszer-lemezre írt adatok a helyi virtuálisgép-tárolóban tárolódnak, és nem maradnak meg az Azure Storage-ban. |
| **Felszabadított állapot leállítása**      | A virtuális gépek és a méretezésicsoport-példányok leállíthatóak és újraindíthatóak a felszabadított állapotból | A virtuális gépek és a méretezésicsoport-példányok nem állíthatóak fel                                  |
| **Speciális operációsrendszer-lemezek támogatása** | Igen                                                                                          | Nem                                                                                 |
| **Operációsrendszer-lemez átméretezése**              | A virtuális gép létrehozása során és a virtuális gép felszabadítása után támogatott                                | Csak a virtuális gépek létrehozása során támogatott                                                  |
| **Átméretezés új virtuálisgép-méretre**   | Az operációsrendszer-lemez adatai megmaradnak                                                                    | Az operációsrendszer-lemezen lévő adatok törlődnek, az operációs rendszer újra ki van építve       
| **Lapfájl elhelyezése**   | Windows esetén a lapfájl az erőforráslemezen van tárolva                                              | Windows esetén a lapfájl az operációsrendszer-lemezen van tárolva   |

## <a name="size-requirements"></a>Méretkövetelmények

A virtuálisgép- és példány-rendszerképeket a virtuálisgép-gyorsítótár méretének megfelelően helyezheti üzembe. A Marketplace-ről származó Standard Windows Server-rendszerképek például körülbelül 127 GiB méretűek, ami azt jelenti, hogy olyan virtuálisgép-méretre van szükség, amelynek gyorsítótára nagyobb, mint 127 GiB. Ebben az esetben a [Standard_DS2_v2](dv2-dsv2-series.md) 86 GiB gyorsítótármérettel rendelkezik, ami nem elég nagy. A Standard_DS3_v2 172 GiB gyorsítótármérettel rendelkezik, ami elég nagy. Ebben az esetben a Standard_DS3_v2 A DSv2 sorozat legkisebb mérete, amely használható ezzel a képpel. A Marketplace alapszintű Linux-rendszerképei és a Windows Server rendszerképei, amelyek neve általában körülbelül 30 GiB, és a legtöbb elérhető virtuálisgép-méretet `[smallsize]` használják.

Aphemerális lemezek esetében a virtuális gép méretének is támogatva kell a Prémium szintű tárolást. A méretek neve általában (de nem mindig) `s` egy, például DSv2 és EsV3. További információért tekintse meg az Azure-beli [virtuális gépek](sizes.md) méretei oldalon annak részleteit, hogy mely méretek támogatják a Prémium szintű tárolást.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Előzetes verzió – A ideiglenes operációsrendszer-lemezek mostantól tárolhatók ideiglenes lemezeken
A ideiglenes operációsrendszer-lemezek mostantól a virtuális gép ideiglenes/erőforrás-lemezén is tárolhatók a virtuális gép gyorsítótára mellett. Így most már használhat ideiglenes operációsrendszer-lemezeket olyan virtuális gépekkel, amelyek nem rendelkezik gyorsítótárral, vagy nem rendelkezik megfelelő gyorsítótárral, de rendelkezik ideiglenes/erőforráslemezzel a ideiglenes operációsrendszer-lemez, például a Dav3, a Dav4, az Eav4 és az Eav3 tárolására. Ha egy virtuális gép elegendő gyorsítótárral és ideiglenes területtel rendelkezik, egy [új, DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement)nevű tulajdonság használatával megadhatja, hogy hol szeretné tárolni a ideiglenes operációsrendszer-lemezt. Ezzel a funkcióval a Windows rendszerű virtuális gépek kiépítésekor a lapozófájlt úgy konfiguráljuk, hogy az az operációsrendszer-lemezen legyen. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Első lépésekként kérjen [hozzáférést.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u)

## <a name="powershell"></a>PowerShell

Ha rövid távú lemezt kell használnia egy PowerShell virtuális gép üzembe helyezéséhez, használja a [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) parancsot a virtuális gép konfigurációjában. Állítsa a `-DiffDiskSetting` és `Local` a `-Caching` beállítását a `ReadOnly` következőre: .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Méretezési készlet üzembe helyezéséhez használja a [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmagot a konfigurációban. Állítsa a `-DiffDiskSetting` és `Local` a `-Caching` beállítását a `ReadOnly` következőre: .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

Ha a cli-s virtuális gép üzembe helyezéséhez egy gyors üzembe helyezési lemezt használ, állítsa a paramétert az az vm create paraméterre, a paramétert pedig a `--ephemeral-os-disk` [](/cli/azure/vm#az_vm_create) `true` `--os-disk-caching` következőre: `ReadOnly` .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Méretezési csoport esetében ugyanazt a paramétert használja `--ephemeral-os-disk true` [az az-vmss-create](/cli/azure/vmss#az_vmss_create) paraméterhez, és állítsa a `--os-disk-caching` paramétert a következőre: `ReadOnly` .

## <a name="portal"></a>Portál

A Azure Portal virtuális gépek üzembe helyezésekor a Lemezek lap Speciális szakaszának megnyitásával  választhatja ki, hogy a virtuális gépek telepítésekor használjon-e  rendszertelen lemezeket. A **Usephemeral OS disk (Operációsrendszer-lemez használata) beállításhoz válassza** az **Igen lehetőséget.**

![A gyors operációsrendszer-lemez használatának kiválasztásához használható választógombot bemutató képernyőkép](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha a rendszerjel nélküli lemezek használata ki van szürkén, előfordulhat, hogy olyan virtuálisgép-méretet választott, amely nem rendelkezik az operációsrendszer-rendszerképnél nagyobb gyorsítótármérettel, vagy nem támogatja a Prémium szintű tárolót. Vissza Az alapok **lapra,** és válasszon egy másik virtuálisgép-méretet.

A portál használatával is létrehozhat olyan méretezési csoportokat, amelyekhez a rendszer operációsrendszer-lemezeket használ. Csak győződjön meg arról, hogy megfelelő méretű virtuálisgép-méretet választ ki, majd a A gyors operációsrendszer-lemez **használata** területen válassza az **Igen lehetőséget.**

![Képernyőkép a gyors operációsrendszer-lemez méretezési készlethez való használatát választó választógombról](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Méretezési készlet sablonjának üzembe helyezése  
A rendszerkézbesi operációsrendszer-lemezt használó méretezési csoport létrehozásához hozzá kell adni a tulajdonságot a sablonban lévő `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` erőforrástípushoz. Emellett a gyorsítótárazó házirendet a következőre kell beállítani a rendszerkézbesű `ReadOnly` operációsrendszer-lemezhez: . 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>Virtuálisgép-sablon üzembe helyezése 
A virtuális gépeket egy sablon használatával helyezheti üzembe egy operációsrendszer-lemezzel. A rendszer operációsrendszer-lemezeket használó virtuális gépek létrehozási folyamata az, hogy hozzáadja a tulajdonságot a `diffDiskSettings` Microsoft.Compute/virtualMachines erőforrástípushoz a sablonban. Emellett a gyorsítótárazó házirendet a következőre kell beállítani a rendszerkézbesű `ReadOnly` operációsrendszer-lemezhez: . 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Virtuális gép rendszermemóba helyezése REST használatával
A rövidtű operációsrendszer-lemezzel használt virtuálisgép-példányok rendszerképét az REST API használatával, az Azure Portalon keresztül, a virtuális gép Áttekintés panelén tudja újra rendszerképként használni. Méretezési készletekhez a képátképezés már elérhető a PowerShellen, a parancssori felületen és a portálon keresztül.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Mekkora a helyi operációsrendszer-lemezek mérete?**

V: Platform- és egyéni rendszerképeket támogatunk a virtuálisgép-gyorsítótár méretének megfelelően, ahol az operációsrendszer-lemezre való összes olvasási/írási adat ugyanazon a csomóponton lesz helyi, mint a virtuális gép. 

**K: Átméretezhetők aphemerális operációsrendszer-lemezek?**

NEM: Nem, az operációsrendszer-lemez kiépítése után az operációsrendszer-lemez nem méretezhető át. 

**K: Csatolok egy Managed Disks egyphemeral virtuális géphez?**

V: Igen, csatolhat felügyelt adatlemezt egy olyan virtuális géphez, amely egy operációsrendszer-lemezt használ. 

**K: Minden virtuálisgép-méret támogatott lesz a hamarosan elérhető operációsrendszer-lemezeken?**

V: Nem, Premium Storage virtuálisgép-méretek (DS, ES, FS, GS, M stb.) támogatottak. A következővel tudhatja meg, hogy egy adott virtuálisgép-méret támogatja-e a rendszer operációsrendszer-lemezeket:

A `Get-AzComputeResourceSku` PowerShell-parancsmag hívása
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**K: Alkalmazható aphemerális operációsrendszer-lemez a meglévő virtuális gépekre és méretezési készletekre?**

V: Nem, a rendszer operációsrendszer-lemeze csak a virtuális gép és a méretezési készlet létrehozása során használható. 

**K: Képes vegyesen elegyedni a hosszú életű és a normál operációsrendszer-lemezek egy méretezési készletben?**

NEM: Nem, nem lehet rövid és állandó operációsrendszer-lemezpéldányokat vegyesen ugyanabban a méretezési készletben. 

**K: Létre lehet-e hozatni a rövid operációsrendszer-lemezt a PowerShell vagy a parancssori felület használatával?**

V: Igen, rövid életű operációsrendszer-lemezzel is létrehozhat virtuális gépeket a REST, a Sablonok, a PowerShell és a parancssori felület használatával.

**K: Milyen funkciók nem támogatottak aphemerális operációsrendszer-lemezeken?**

A: Aphemeral-lemezek nem támogatják a következőt:
- Virtuálisgép-rendszerképek rögzítése
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Operációsrendszer-lemez felcserél 

> [!NOTE]
> 
> Aphemeral-lemezek nem érhetők el a portálon keresztül. A "Resource not Found" (Erőforrás nem található) vagy "404" típusú hibaüzenet jelenik meg a várhatóan hamarosan elérhető lemez elérésekor.
> 
 
## <a name="next-steps"></a>Következő lépések
A virtuális gépeket az [Azure CLI](/cli/azure/vm#az_vm_create)használatával hozhatja létre egy operációsrendszer-lemezzel.
