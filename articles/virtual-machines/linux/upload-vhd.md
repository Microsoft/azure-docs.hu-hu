---
title: Egyéni Linux rendszerű virtuális gép feltöltése vagy másolása az Azure CLI használatával
description: Testreszabott virtuális gép feltöltése vagy másolása az Resource Manager-alapú üzembe helyezési modell és az Azure CLI használatával
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759598"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Linux rendszerű virtuális gép létrehozása egyéni lemezről az Azure CLI használatával

<!-- rename to create-vm-specialized -->

Ez a cikk bemutatja, hogyan tölthet fel testreszabott virtuális merevlemezt (VHD-t), és hogyan másolhatja egy meglévő virtuális merevlemezt az Azure-ba. Az újonnan létrehozott virtuális merevlemezt a rendszer új Linux rendszerű virtuális gépek (VM-ek) létrehozására használja. Telepíthet és konfigurálhat egy Linux-disztribúciót a követelményekhez, majd a VHD használatával létrehozhat egy új Azure-beli virtuális gépet.

Ha több virtuális gépet is létre kell hoznia a testreszabott lemezről, először hozzon létre egy rendszerképet a virtuális gépről vagy a virtuális merevlemezről. További információ: Egyéni rendszerkép létrehozása Azure-beli virtuális [gépről a CLI használatával.](tutorial-custom-images.md)

Egyéni lemez létrehozásához két lehetőség áll rendelkezésre:
* VHD feltöltése
* Meglévő Azure-beli virtuális gép másolása


## <a name="requirements"></a>Követelmények
A következő lépések befejezéséhez a következőkre lesz szüksége:

- Az Azure-ban való használatra előkészített Linux rendszerű virtuális gép. A [cikk A](#prepare-the-vm) virtuális gép előkészítése című szakasza bemutatja, hogyan találhatja meg az Azure Linux-ügynök (waagent) telepítésével kapcsolatos disztribúcióspecifikus információkat, amelyekre szükség van a virtuális gépekhez SSH-val való csatlakozáshoz.
- Egy [meglévő, Azure által](endorsed-distros.md) támogatott Linux-disztribúció [VHD-fájlja](create-upload-generic.md)(vagy lásd a nem támogatott disztribúciókra vonatkozó információkat) egy virtuális lemezre VHD formátumban. Több eszköz is létezik virtuális gép és virtuális merevlemez létrehozásához:
  - Telepítse és konfigurálja a [QEMU-t](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) vagy a [KVM-et,](https://www.linux-kvm.org/page/RunningKVM)ügyelve arra, hogy a VHD-t használja képformátumként. Szükség esetén konvertálhat egy képet [a sel.](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) `qemu-img convert`
  - A Hyper-V-t [](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) a Windows 10 [Windows Server 2012/2012 R2 rendszeren is használhatja.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Virtuális gép létrehozásakor adja meg a VHD formátumot. Szükség esetén vhDX-lemezeket konvertálhat [VHD-ként a qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell-parancsmag használatával. Az Azure nem támogatja a dinamikus VHD-fájlok feltöltését, ezért a feltöltés előtt ezeket a lemezeket statikus VHD-kké kell konvertálni. A dinamikus lemezek konvertálásához olyan eszközöket használhat, mint az [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) az Azure-ba való feltöltésük során.
> 
> 


- Győződjön meg arról, hogy az [Azure CLI](/cli/azure/install-az-cli2) legújabb verziójával van telepítve, és hogy be van jelentkezve egy Azure-fiókba az az [login használatával.](/cli/azure/reference-index#az_login)

A következő példákban cserélje le a példaparaméter-neveket a saját értékeire, például a `myResourceGroup` , a és a `mystorageaccount` `mydisks` értékére.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az Azure különböző Linux-disztribúciókat támogat [(lásd: Támogatott disztribúciók).](endorsed-distros.md) A következő cikkek ismertetik, hogyan készítheti elő az Azure-ban támogatott különböző Linux-disztribúciókat:

* [CentOS-alapú disztribúciók](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES és openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Egyéb tudnivalók: Nem támogatott disztribúciók](create-upload-generic.md)

A [Linux-rendszerképek Azure-hoz](create-upload-generic.md#general-linux-installation-notes) való előkészítésével kapcsolatos általános tippekért tekintse meg a Linux telepítési megjegyzéseit is.

> [!NOTE]
> Az [Azure platform SLA-ja](https://azure.microsoft.com/support/legal/sla/virtual-machines/) csak akkor vonatkozik a Linuxot futtató virtuális gépekre, ha az egyik támogatott disztribúciót a linuxos disztribúciókon a "Támogatott verziók" alatt megadott konfigurációs Azure-Endorsed [használják.](endorsed-distros.md)
> 
> 

## <a name="option-1-upload-a-vhd"></a>1. lehetőség: VHD feltöltése

Mostantól közvetlenül egy felügyelt lemezre töltheti fel a VHD-t. Útmutatásért [lásd: VHD feltöltése az Azure-ba az Azure CLI használatával.](disks-upload-vhd-to-managed-disk-cli.md)

## <a name="option-2-copy-an-existing-vm"></a>2. lehetőség: Meglévő virtuális gép másolása

Létrehozhat egy testreszabott virtuális gépet az Azure-ban, majd átmásolhatja az operációsrendszer-lemezt, és csatlakoztathatja egy új virtuális géphez egy újabb másolat létrehozásához. Ez megfelel a teszteléshez, de ha egy meglévő Azure-beli virtuális gépet szeretne modellként használni több új virtuális géphez, hozzon létre inkább egy *rendszerképet.* További információ a rendszerkép meglévő Azure-beli virtuális gépből való létrehozásáról: Egyéni rendszerkép létrehozása Azure-beli virtuális gépről [a CLI használatával.](tutorial-custom-images.md)

Ha egy meglévő virtuális gépet egy másik régióba szeretne másolni, az azcopy használatával egy másik régióban lévő lemez másolatát [is létre lehet hozva.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Ellenkező esetben pillanatképet kell készítenie a virtuális gépről, majd létre kell hoznia egy új operációsrendszer-vhD-t a pillanatképből.

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Ez a példa egy *myVM* nevű virtuális gép pillanatképét hozza létre a *myResourceGroup* erőforráscsoportban, és létrehoz egy *osDiskSnapshot nevű pillanatképet.*

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>A felügyelt lemez létrehozása

Hozzon létre egy új felügyelt lemezt a pillanatképből.

Szerezze be a pillanatkép azonosítóját. Ebben a példában a pillanatkép neve *osDiskSnapshot,* és a *myResourceGroup erőforráscsoportban* található.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Hozza létre a felügyelt lemezt. Ebben a példában létrehozunk egy *myManagedDisk* nevű felügyelt lemezt a pillanatképből, ahol a lemez standard tárolóban van, és 128 GB-os méretben van méretezett.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet [az az vm create gombra,](/cli/azure/vm#az_vm_create) és csatlakoztassa (--attach-os-disk) a felügyelt lemezt operációsrendszer-lemezként. Az alábbi példa létrehoz egy *myNewVM* nevű virtuális gépet a feltöltött VHD-ről létrehozott felügyelt lemez használatával:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

A forrás virtuális gépről származó hitelesítő adatokkal be kell tudnia SSH-kapcsolatot elérni a virtuális géphez. 

## <a name="next-steps"></a>Következő lépések
Miután előkészítette és feltöltötte az egyéni virtuális lemezt, többet is olvashat a virtuális gépek és [Resource Manager használatával kapcsolatban.](../../azure-resource-manager/management/overview.md) Az is előfordulhat, hogy [adatlemezt szeretne hozzáadni](add-disk.md) az új virtuális gépekhez. Ha olyan alkalmazások futnak a virtuális gépeken, amelyekhez hozzá kell férni, mindenképpen nyissa meg a portokat és [a végpontokat.](nsg-quickstart.md)
