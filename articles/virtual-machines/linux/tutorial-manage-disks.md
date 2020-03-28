---
title: Oktatóanyag – Azure-lemezek kezelése az Azure CLI használatával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-lemezeket virtuális gépekhez az Azure CLI használatával
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: 78b47075ba0c717ffd8e813f6cf1ebb86031a7e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060215"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Oktatóanyag – Azure-lemezek kezelése az Azure CLI használatával

Az Azure-beli virtuális gépek (VM-ek) lemezeket használnak az operációs rendszerek, alkalmazások és adatok tárolására. Virtuális gép létrehozásakor fontos, hogy válasszon a lemez mérete és konfigurációja a várt számítási feladatoknak megfelelő. Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe és kezelhet virtuálisgép-lemezeket. Az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése
> * Lemezek átméretezése
> * Lemez-pillanatképek


## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek

Egy Azure-beli virtuális gép létrehozásakor a rendszer két lemezt automatikusan a virtuális géphez csatol.

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 2 TB méretűek is lehetnek, és a virtuális gépek operációs rendszerei találhatók rajtuk. Az operációsrendszer-lemez alapértelmezés szerint a */dev/sda* címkét kapja. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. A konfiguráció miatt az operációsrendszer-lemez **nem** használható alkalmazásokhoz és adatokhoz. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó az oktatóanyagban.

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuális gép. Az ideiglenes lemezek nagy teljesítményűek és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuális gépet egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a virtuális gép mérete határozza meg. Az ideiglenes lemezek a */dev/sdb* címkét kapják, a csatlakoztatási pontjuk pedig */mnt*.

## <a name="azure-data-disks"></a>Azure-adatlemezek

Alkalmazások telepítéséhez és adatok tárolásához további adatlemezek adhatók hozzá. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. A virtuális gép mérete határozza meg, hány adatlemez csatolható a virtuális géphez.

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

Az Azure kétféle lemezt biztosít, a standard és a prémium szintű.

### <a name="standard-disk"></a>Standard lemez

A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

### <a name="premium-disk"></a>Prémium lemez

A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. A Premium Storage támogatja a DS, a DSv2, a GS és az FS sorozatú virtuális gépeket. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete kisebb 128 GB-nál, a lemez típusa P10. Ha a lemez mérete 129 GB és 512 GB közé esik, a típus P20. Az 512 GB feletti méretek esetében a típus P30.

### <a name="premium-disk-performance"></a>Prémium szintű lemezek teljesítménye
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például egy Standard_GS5 virtuális gép esetében maximálisan 80 000 IOPS érhető el. A virtuális gépenkénti maximális IOPS-értékkel kapcsolatos részletes információkért lásd a [Linux rendszerű virtuális gépek méreteit](sizes.md) ismertető cikket.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj, amely segítségével futtathatja a cikkben leírt lépéseket. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Próbálja ki** a kódblokk jobb felső sarkából lehetőséget. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása

Adatlemezek létrehozhatók és csatolhatók a virtuális gépek létrehozáskor vagy a meglévő virtuális gépekhez is.

### <a name="attach-disk-at-vm-creation"></a>Lemez csatolása virtuális gép létrehozásakor

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gépet, hozzáadja az *azureuser* nevű felhasználói fiókot, valamint SSH-kulcsokat hoz létre, ha azok még nem léteznének. A `--datadisk-sizes-gb` argumentum használatával adhatja meg, hogy egy további lemezt kell létrehozni és a virtuális géphez csatolni. Több lemez létrehozásához és csatolásához a lemezméreteket egy szóközzel tagolt listában adja meg. A következő példában egy virtuális gépet hozunk létre két, egyenként 128 GB méretű adatlemezzel. Mivel a lemezek mérete 128 GB, mindkettő P10 típusú lemezként van konfigurálva, amely lemezenként maximálisan 500 IOPS-t biztosít.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Lemez csatolása meglévő virtuális géphez

Új lemez egy meglévő virtuális gépen való létrehozásához és csatolásához használja az [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) parancsot. A következő példában egy 128 gigabájt méretű, prémium szintű lemezt hozunk létre, majd csatoljuk a legutóbbi lépésben létrehozott virtuális géphez.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Adatlemezek előkészítése

Miután hozzácsatolt egy lemezt a virtuális géphez, az operációs rendszert konfigurálni kell a lemez használatához. A következő példa bemutatja, hogyan konfigurálhatja manuálisan a lemezt. Ez a folyamat is automatizálható a cloud-init használatával, amelyet egy [későbbi oktatóanyag](./tutorial-automate-vm-deployment.md) mutat be.


Hozzon léte egy SSH-kapcsolatot a virtuális géppel. Cserélje le a példában szereplő IP-címet a virtuális gép nyilvános IP-címére.

```console
ssh 10.101.10.10
```

Particionálja a lemezt az `fdisk` használatával.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Írjon egy fájlrendszert a partícióra az `mkfs` paranccsal.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Csatolja az új lemezt, hogy elérhető legyen az operációs rendszerben.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

A lemez a *datadrive* (adatmeghajtó) csatlakozási ponton keresztül érhető el, ami a `df -h` parancs futtatásával ellenőrizhető.

```bash
df -h
```

A kimenet a */datadrive* pontra csatlakoztatott új meghajtót mutatja.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Annak érdekében, hogy a meghajtó a rendszer újraindítása után ismét csatolva legyen, fel kell venni azt az */etc/fstab* fájlba. Ehhez kérje le a lemez UUID-jét a `blkid` segédprogrammal.

```bash
sudo -i blkid
```

A kimenet megjeleníti a meghajtó UUID-jét, amely esetünkben a `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adjon hozzá egy, a következőhöz hasonló sort az */etc/fstab* fájlba.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Most, hogy a lemez konfigurálva lett, zárja be az SSH-munkamenetet.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Lemezpillanatkép készítése

A lemezpillanatképek létrehozása során az Azure egy csak olvasható, adott időponthoz kötött másolatot hoz létre a lemezről. Az Azure-beli virtuális gépekről készült pillanatképek akkor lehetnek hasznosak, ha a virtuális gépek állapotának gyors mentésére van szükség a konfiguráció módosítása előtt. Probléma vagy hiba esetén a virtuális gép pillanatkép használatával visszaállítható. Ha a virtuális gép egynél több lemezzel rendelkezik, az egyes lemezekről egymástól független pillanatképek készülnek. Alkalmazáskonzisztens biztonsági másolatok készítéséhez érdemes lehet leállítania a virtuális gépet a lemezpillanatképek készítése előtt. Másik megoldásként használhatja az [Azure Backup szolgáltatást](/azure/backup/) is, amelynek segítségével automatikus biztonsági mentést végezhet, miközben a virtuális gép fut.

### <a name="create-snapshot"></a>Pillanatkép készítése

A virtuálisgép-lemez pillanatképének elkészítése előtt szükség van a lemez azonosítójára vagy nevére. Használja az [az vm show](/cli/azure/vm#az-vm-show) parancsot a lemezazonosító visszaadásához. A példában a lemezazonosítót egy változó tárolja, így az egy későbbi lépésben majd felhasználható.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Most, hogy rendelkezik a virtuálisgép-lemez azonosítójával, a következő paranccsal készítheti el a lemez pillanatképét.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Lemez létrehozása pillanatképből

A pillanatkép ezután lemezzé alakítható, amelynek segítségével újra létrehozhatja a virtuális gépet.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Virtuális gép visszaállítása pillanatképből

A virtuálisgép-helyreállítás bemutatása érdekében törölje a meglévő virtuális gépet.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Hozzon létre egy új virtuális gépet pillanatképlemezből.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Adatlemez újracsatolása

Az összes adatlemezt újra kell csatolni a virtuális gépre.

Először keresse ki a lemez nevét az [az disk list](/cli/azure/disk#az-disk-list) paranccsal. A példában a lemez nevét a *datadisk* nevű változóba helyezzük, amelyet a következő lépésben használunk majd.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

A lemezt az [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) paranccsal csatolhatja.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a virtuálisgép-lemezekkel kapcsolatos témakörökkel ismerkedett meg, például:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezek teljesítménye
> * Adatlemezek csatolása és előkészítése
> * Lemezek átméretezése
> * Lemez-pillanatképek

Folytassa a következő oktatóanyaggal, amely a virtuális gép konfigurálásának automatizálását ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
