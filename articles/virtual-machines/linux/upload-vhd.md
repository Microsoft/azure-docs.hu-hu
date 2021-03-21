---
title: Egyéni linuxos virtuális gép feltöltése vagy másolása az Azure CLI-vel
description: Testreszabott virtuális gép feltöltése vagy másolása a Resource Manager-alapú üzemi modell és az Azure CLI használatával
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
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564611"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel

<!-- rename to create-vm-specialized -->

Ebből a cikkből megtudhatja, hogyan tölthet fel egy testreszabott virtuális merevlemezt (VHD), és hogyan másolhat egy meglévő VHD-t az Azure-ban. Az újonnan létrehozott virtuális merevlemezt a rendszer új linuxos virtuális gépek (VM-EK) létrehozásához használja. Telepíthet és konfigurálhat Linux-disztribúciót az igényeinek megfelelően, majd a virtuális merevlemez használatával létrehozhat egy új Azure-beli virtuális gépet.

Ha több virtuális gépet szeretne létrehozni a testreszabott lemezről, először hozzon létre egy rendszerképet a virtuális gépről vagy a VHD-ről. További információ: [Egyéni rendszerkép létrehozása Azure-beli virtuális gépen a parancssori felület használatával](tutorial-custom-images.md).

Egyéni lemez létrehozásához két lehetőség közül választhat:
* VHD feltöltése
* Meglévő Azure-beli virtuális gép másolása


## <a name="requirements"></a>Követelmények
A következő lépések elvégzéséhez a következőkre lesz szüksége:

- Az Azure-ban használatra előkészített Linux rendszerű virtuális gép. Ez a cikk a [virtuális gép előkészítése](#prepare-the-vm) című szakasza ismerteti, hogyan lehet megkeresni a disztribúcióval kapcsolatos információkat az Azure Linux-ügynök (waagent) telepítésével kapcsolatban, amely szükséges ahhoz, hogy SSH-val csatlakozzon egy virtuális géphez.
- Egy meglévő [Azure által támogatott Linux-disztribúcióból](endorsed-distros.md) származó vhd-fájl (vagy a [nem támogatott disztribúciókkal kapcsolatos információk](create-upload-generic.md)) egy virtuális lemezre a VHD formátumban. Több eszköz létezik a virtuális gép és a virtuális merevlemez létrehozásához:
  - Telepítse és konfigurálja a [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) -t vagy a [KVM](https://www.linux-kvm.org/page/RunningKVM)-t, ügyelve arra, hogy a VHD-t használja képformátumként. Ha szükséges, [konvertálhat egy képet](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) a használatával `qemu-img convert` .
  - A Hyper-V-t [Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) vagy [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))rendszeren is használhatja.

> [!NOTE]
> Az újabb VHDX formátum nem támogatott az Azure-ban. Amikor létrehoz egy virtuális gépet, a VHD formátumot kell megadnia formátumként. Ha szükséges, átalakíthatja a VHDX lemezeket virtuális merevlemezre a [QEMU-IMG konvertálása](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) vagy a [Convert-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell-parancsmag használatával. Az Azure nem támogatja a dinamikus virtuális merevlemezek feltöltését, ezért a feltöltés előtt át kell alakítania ezeket a lemezeket a statikus virtuális merevlemezekre. Az Azure-ba való feltöltésének folyamata során olyan eszközöket használhat, mint például az [Azure VHD-segédprogramok](https://github.com/Microsoft/azure-vhd-utils-for-go) , amelyek a dinamikus lemezek átalakítására szolgálnak.
> 
> 


- Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és bejelentkezett egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)paranccsal.

Az alábbi példákban cserélje le a példában szereplő paramétereket a saját értékeire (például: `myResourceGroup` ,, `mystorageaccount` és `mydisks` ).

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>A virtuális gép előkészítése

Az Azure különböző Linux-disztribúciókat támogat (lásd a [támogatott disztribúciókat](endorsed-distros.md)). A következő cikkek az Azure-ban támogatott különböző Linux-disztribúciók előkészítését ismertetik:

* [CentOS-alapú disztribúciók](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES és openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Egyebek: nem támogatott disztribúciók](create-upload-generic.md)

Tekintse meg a [Linux telepítési megjegyzéseit](create-upload-generic.md#general-linux-installation-notes) is, amelyek az Azure-hoz készült Linux-rendszerképek előkészítésével kapcsolatos általános tippeket mutatnak.

> [!NOTE]
> Az [Azure platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) -ja a Linux rendszerű virtuális gépekre vonatkozik, ha a támogatott disztribúciók egyikét használja a [linux rendszeren a Azure-Endorsed-disztribúciókban](endorsed-distros.md)megadott konfigurációs adatokkal.
> 
> 

## <a name="option-1-upload-a-vhd"></a>1. lehetőség: virtuális merevlemez feltöltése

Most már közvetlenül is feltöltheti a virtuális merevlemezt egy felügyelt lemezre. Útmutatásért lásd: [virtuális merevlemez feltöltése az Azure-ba az Azure CLI használatával](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>2. lehetőség: meglévő virtuális gép másolása

Létrehozhat egy testreszabott virtuális gépet az Azure-ban, majd másolhatja az operációsrendszer-lemezt, és csatolhatja azt egy új virtuális géphez egy másik másolat létrehozásához. Ez csak teszteléshez használható, de ha egy meglévő Azure-beli virtuális gépet kíván használni több új virtuális gép modellje, hozzon létre egy *rendszerképet* . A rendszerkép meglévő Azure-beli virtuális gépről való létrehozásával kapcsolatos további információkért lásd: [Egyéni rendszerkép létrehozása Azure-beli virtuális gépről a parancssori felület használatával](tutorial-custom-images.md).

Ha egy meglévő virtuális gépet egy másik régióba szeretne másolni, érdemes lehet a azcopy használatával másolatot készíteni egy [lemezről egy másik régióban](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Ellenkező esetben készítsen pillanatképet a virtuális gépről, majd hozzon létre egy új, az operációs rendszerhez készült virtuális merevlemezt a pillanatképből.

### <a name="create-a-snapshot"></a>Pillanatkép létrehozása

Ez a példa egy *myVM* nevű virtuális gép pillanatképét hozza létre az erőforráscsoport *myResourceGroup* , és létrehoz egy *osDiskSnapshot* nevű pillanatképet.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>A felügyelt lemez létrehozása

Hozzon létre egy új felügyelt lemezt a pillanatképből.

A pillanatkép AZONOSÍTÓjának beolvasása. Ebben a példában a pillanatkép neve *osDiskSnapshot* , és a *myResourceGroup* erőforráscsoport tagja.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Hozza létre a felügyelt lemezt. Ebben a példában létrehozunk egy *myManagedDisk* nevű felügyelt lemezt a pillanatképből, amelyben a lemez szabványos tárolóban van, és mérete 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal, és csatolja a felügyelt lemezt operációsrendszer-lemezként. Az alábbi példa egy *myNewVM* nevű virtuális gépet hoz létre a feltöltött VHD-ből létrehozott felügyelt lemez használatával:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

A forrás virtuális gép hitelesítő adataival SSH-val csatlakozhat a virtuális géphez. 

## <a name="next-steps"></a>Következő lépések
Miután felkészítette és feltöltötte az egyéni virtuális lemezt, további információt olvashat a [Resource Manager és a sablonok használatáról](../../azure-resource-manager/management/overview.md). Előfordulhat, hogy [adatlemezt](add-disk.md) is szeretne hozzáadni az új virtuális gépekhez. Ha olyan alkalmazások futnak a virtuális gépeken, amelyekhez hozzá kell férnie, ne felejtse el [megnyitni a portokat és a végpontokat](nsg-quickstart.md).
