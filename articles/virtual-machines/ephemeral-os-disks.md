---
title: Rövid élettartamú operációsrendszer-lemezek
description: További információ az Azure-beli virtuális gépek elmúló operációsrendszer-lemezéről.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 814824fb6708abaf549bb3de19b4aced4774a244
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485758"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Ideiglenes operációsrendszer-lemezek Azure-beli virtuális gépekhez

Az ideiglenes operációsrendszer-lemezek a helyi virtuális gép (VM) tárolóján jönnek létre, és nem lesznek mentve a távoli Azure-tárolóba. Az időszakos operációsrendszer-lemezek jól működnek az állapot nélküli munkaterhelések esetében, ahol az alkalmazások az egyes virtuálisgép-hibáktól függenek, de a virtuális gép üzembe helyezési ideje vagy az egyes virtuálisgép-példányok újrarendszerképe is befolyásolja. Az elmúló operációsrendszer-lemezzel alacsonyabb olvasási/írási késést kap az operációsrendszer-lemez és a virtuális gép lemezképének gyors alaphelyzetbe állítása. 
 
Az ideiglenes lemezek legfontosabb funkciói a következők: 
- Ideális állapot nélküli alkalmazásokhoz.
- A piactéren és az egyéni rendszerképeken is használhatók.
- Lehetővé teszi a virtuális gépek gyors alaphelyzetbe állítását vagy áttelepítését, illetve a méretezési csoport példányainak eredeti rendszerindítási állapotát.  
- Kisebb késés, hasonlóan egy ideiglenes lemezhez. 
- Az ideiglenes operációsrendszer-lemezek ingyenesek, az operációsrendszer-lemez tárolási díja nem jár.
- Ezek az összes Azure-régióban elérhetők. 
- A [megosztott rendszerkép](./shared-image-galleries.md)-katalógus támogatja az ideiglenes operációsrendszer-lemezt. 
 

 
Az állandó és az elmúló operációsrendszer-lemezek közötti fő különbségek:

|                             | Állandó operációsrendszer-lemez                          | Rövid élettartamú operációsrendszer-lemez                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **OPERÁCIÓSRENDSZER-lemezre vonatkozó méretkorlát**      | 2 TiB                                                                                        | A virtuális gép méretének vagy 2TiB a gyorsítótár mérete, attól függően, hogy melyik a kisebb. A **GIB gyorsítótár-méretének** megtekintéséhez lásd [: DS](sizes-general.md), [es](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md)és [GS](sizes-previous-gen.md#gs-series)              |
| **Támogatott VM-méretek**          | Mind                                                                                          | A Premium Storage-t támogató virtuálisgép-méretek, például DSv1, DSv2, DSv3, Esv3, FS, FsV2, GS, M                                               |
| **Lemez típusának támogatása**           | Felügyelt és nem felügyelt operációsrendszer-lemez                                                                | Csak felügyelt operációsrendszer-lemez                                                               |
| **Régiós támogatás**              | Minden régió                                                                                  | Minden régió                              |
| **Adatmegőrzés**            | Az operációsrendszer-lemezre írt operációsrendszer-lemezeket az Azure Storage tárolja                                  | Az operációsrendszer-lemezre írt adatkészleteket a rendszer a helyi virtuálisgép-tárolóba tárolja, és nem őrzi meg az Azure Storage szolgáltatásban. |
| **Lefoglalt állapot leállítása**      | A virtuális gépek és a méretezési csoport példányai leállíthatók, és a Leállított állapotból indíthatók újra. | A virtuális gépek és a méretezési csoport példányai nem állíthatók le – nem foglalható le.                                  |
| **Speciális operációsrendszer-lemezek támogatása** | Igen                                                                                          | Nem                                                                                 |
| **OPERÁCIÓSRENDSZER-lemez átméretezése**              | A virtuális gép létrehozásakor és a virtuális gép leállítása után támogatott                                | Csak a virtuális gépek létrehozásakor támogatott                                                  |
| **Átméretezés új virtuálisgép-méretre**   | Az operációsrendszer-lemezre vonatkozó adatmegőrzés                                                                    | Az operációsrendszer-lemezen lévő adatlemez törölve lett, az operációs rendszer újból kiépítve       
| **Lapozófájl elhelyezése**   | Windows esetén a lapozófájl tárolása az erőforrás-lemezen történik                                              | Windows esetén a lapozófájlt az operációsrendszer-lemezen tárolja a rendszer.   |

## <a name="size-requirements"></a>Méretre vonatkozó követelmények

A virtuális gépek és a példányok rendszerképeinek üzembe helyezése a virtuális gép gyorsítótárának méretétől függetlenül végezhető el. A piactér standard szintű Windows Server-rendszerképei például a 127 GiB-ra vonatkoznak, ami azt jelenti, hogy a virtuális gép méretének a 127 GiB-nál nagyobb gyorsítótárral kell rendelkeznie. Ebben az esetben a [Standard_DS2_v2](dv2-dsv2-series.md) 86 GIB gyorsítótár-mérettel rendelkezik, ami nem elég nagy. A Standard_DS3_v2 gyorsítótárának mérete 172 GiB, ami elég nagy. Ebben az esetben a Standard_DS3_v2 a DSv2-sorozat legkisebb mérete, amelyet ezzel a képpel lehet használni. Az alapszintű Linux-rendszerképek a piactéren és a Windows Server-lemezképekben, amelyeknek a célja az, hogy `[smallsize]` körülbelül 30 GIB, és a rendelkezésre álló virtuálisgép-méretek többségét használhatják.

Az ideiglenes lemezek esetében a virtuális gép mérete is támogatja a Premium Storage-ot. A méretek általában (de nem mindig) rendelkeznek `s` a névvel, például a DSv2 és a EsV3. További információ: Azure-beli virtuálisgép- [méretek](sizes.md) a Premium Storage-t támogató méretek részleteiről.

## <a name="preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks"></a>Előzetes verzió – az elmúló operációsrendszer-lemezek mostantól a temp Disks szolgáltatásban tárolhatók
A virtuális gépek gyorsítótárán kívül a virtuális gépekre vonatkozó ideiglenes operációsrendszer-lemezek mostantól a VM Temp/Resource lemezén is tárolhatók. Így most már használhat olyan ideiglenes operációsrendszer-lemezeket a virtuális géppel, amelyeknek nincs gyorsítótára, vagy nincs elegendő gyorsítótára, de az ideiglenes operációsrendszer-lemez (például a Dav3, a Dav4, a Eav4 és a Eav3) tárolására a temp/Resource lemez tartozik. Ha egy virtuális gépnek elegendő gyorsítótára van, és az ideiglenes területtel rendelkezik, mostantól megadhatja, hová szeretné tárolni az ideiglenes operációsrendszer-lemezt egy új, [DiffDiskPlacement](/rest/api/compute/virtualmachines/list#diffdiskplacement)nevű tulajdonság használatával. Ezzel a funkcióval a Windows rendszerű virtuális gépek üzembe helyezésekor a lapozófájlt az operációsrendszer-lemezen kell elhelyezni. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Első lépésként [kérjen hozzáférést](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6cQw0fZJzdIsnbfbI13601URTBCRUZPMkQwWFlCOTRIMFBSNkM1NVpQQS4u).

## <a name="powershell"></a>PowerShell

Ha ideiglenes lemezt szeretne használni egy PowerShell virtuálisgép-telepítéshez, használja a [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) a virtuális gép konfigurációjában. Állítsa a és a értékre a következőt: `-DiffDiskSetting` `Local` `-Caching` `ReadOnly` .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

A méretezési csoport üzembe helyezéséhez használja a konfigurációban a [set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) parancsmagot. Állítsa a és a értékre a következőt: `-DiffDiskSetting` `Local` `-Caching` `ReadOnly` .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>parancssori felület

Ha ideiglenes lemezt szeretne használni egy CLI virtuálisgép-telepítéshez, állítsa be a `--ephemeral-os-disk` paramétert az [az VM Create](/cli/azure/vm#az-vm-create) to `true` és a paraméter értékeként `--os-disk-caching` `ReadOnly` .

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

A méretezési csoportok esetében ugyanazt a paramétert használja az az `--ephemeral-os-disk true` [-vmss-Create](/cli/azure/vmss#az-vmss-create) paranccsal, és állítsa a paramétert a következőre: `--os-disk-caching` `ReadOnly` .

## <a name="portal"></a>Portál

A Azure Portal a **lemezek** lap **speciális** szakaszának megnyitásával kiválaszthatja, hogy a virtuális gépek üzembe helyezése során az ideiglenes lemezeket használja-e. Az **ideiglenes operációsrendszer-lemez használata** beállításnál válassza az **Igen** lehetőséget.

![Képernyőfelvétel: az elmúló operációsrendszer-lemez használatának kiválasztására szolgáló választógombot ábrázoló gomb](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Ha egy ideiglenes lemez használatának lehetősége szürkén jelenik meg, lehetséges, hogy olyan virtuálisgép-méretet választott ki, amely nem rendelkezik az operációsrendszer-lemezképnél nagyobb gyorsítótár-mérettel, vagy amely nem támogatja a Premium Storage-ot. Lépjen vissza az **alapvető beállítások** lapra, és válasszon másik virtuális gép méretét.

A portálon létrehozhat olyan méretezési csoportokat is, amelyek elmúló operációsrendszer-lemezzel rendelkeznek. Csak győződjön meg arról, hogy a virtuális gép méretét nagy méretű gyorsítótár-mérettel, majd az **elmúló operációsrendszer-lemez használata** területen válassza az **Igen** lehetőséget.

![Képernyőfelvétel: a választógomb, amely a méretezési csoport elmúló operációsrendszer-lemezének kiválasztására használható](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Méretezési csoport sablonjának üzembe helyezése  
Az elmúló operációsrendszer-lemezt használó méretezési csoport létrehozásának folyamata a tulajdonság hozzáadása a `diffDiskSettings` `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` sablonban lévő erőforrástípus. Emellett a gyorsítótárazási házirendet az `ReadOnly` ideiglenes operációsrendszer-lemezre kell beállítani. 


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
Sablon használatával üzembe helyezhet egy ideiglenes operációsrendszer-lemezzel rendelkező virtuális gépet. Az elmúló operációsrendszer-lemezeket használó virtuális gépek létrehozásának folyamata az, hogy hozzáadja a `diffDiskSettings` tulajdonságot a sablonban található Microsoft. számítási/virtualMachines erőforrástípus számára. Emellett a gyorsítótárazási házirendet az `ReadOnly` ideiglenes operációsrendszer-lemezre kell beállítani. 

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


## <a name="reimage-a-vm-using-rest"></a>Virtuális gép rendszerképének alaphelyzetbe állítása a REST használatával
A virtuálisgép-példányokat az alábbi módon, az Azure Portalon, a virtuális gép áttekintés paneljén található REST API segítségével alaphelyzetbe helyezheti egy ideiglenes operációsrendszer-lemezzel. A méretezési csoportok esetében az Alaphelyzetbe állítás már elérhető a PowerShell, a CLI és a portál használatával.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: mekkora a helyi operációsrendszer-lemezek mérete?**

A: támogatjuk a platformot és az egyéni rendszerképeket, akár a virtuális gép gyorsítótárának méretétől, ahol az operációs rendszer lemezének összes olvasási/írási helye a virtuális géppel megegyező csomóponton lesz. 

**K: az ideiglenes operációsrendszer-lemez átméretezése is lehetséges?**

A: nem, az ideiglenes operációsrendszer-lemez kiépítés után az operációsrendszer-lemez nem méretezhető át. 

**K: Csatolhatok Managed Disks egy ideiglenes virtuális géphez?**

Válasz: igen, a felügyelt adatlemezt egy ideiglenes operációsrendszer-lemezt használó virtuális géphez csatolhatja. 

**K: az összes virtuálisgép-méret támogatott az ideiglenes operációsrendszer-lemezek esetében?**

A: nem, a legtöbb Premium Storage VM-méret támogatott (DS, ES, FS, GS, M stb.). Ha tudni szeretné, hogy egy adott virtuálisgép-méret támogatja-e az ideiglenes operációsrendszer-lemezeket, a következőket teheti:

A PowerShell-parancsmag meghívása `Get-AzComputeResourceSku`
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
 
**K: az ideiglenes operációsrendszer-lemez alkalmazható a meglévő virtuális gépekre és méretezési csoportokra?**

A: nem, az ideiglenes operációsrendszer-lemez csak a virtuális gép és a méretezési csoport létrehozásakor használható. 

**K: a méretezési csoportokban elmúló és normál operációsrendszer-lemezek is használhatók?**

A: nem, nem rendelkezhet az egyazon méretezési csoportba tartozó ideiglenes és állandó operációsrendszer-példányok keverékéből. 

**K: az ideiglenes operációsrendszer-lemez a PowerShell vagy a parancssori felület használatával hozható létre?**

Válasz: igen, a REST, a Templates, a PowerShell és a parancssori felület használatával hozhat létre virtuális gépeket az ideiglenes operációsrendszer-lemezzel.

**K: milyen funkciók nem támogatottak az elmúló operációsrendszer-lemezzel?**

A: az ideiglenes lemezek nem támogatják A következőket:
- VM-lemezképek rögzítése
- Lemez-pillanatképek 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- OPERÁCIÓSRENDSZER-lemez cseréje 

> [!NOTE]
> 
> Az ideiglenes lemez nem lesz elérhető a portálon keresztül. A rendszer "erőforrás nem található" vagy "404" hibaüzenetet kap a várt ideiglenes lemez elérésekor.
> 
 
## <a name="next-steps"></a>Következő lépések
Az [Azure CLI](/cli/azure/vm#az-vm-create)használatával létrehozhat egy ideiglenes operációsrendszer-lemezzel ellátott virtuális gépet.
