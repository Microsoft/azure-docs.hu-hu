---
title: Oktatóanyag – Azure-lemezek kezelése az Azure CLI használatával
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre és felügyelhet Azure-lemezeket virtuális gépekhez az Azure CLI használatával
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 56e804bc0d479f09ef2900c42361fbd24eed1d98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765952"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Oktatóanyag – Azure-lemezek kezelése az Azure CLI használatával

Az Azure-beli virtuális gépek (VM-ek) lemezeket használnak az operációs rendszerek, alkalmazások és adatok tárolására. Virtuális gép létrehozásakor fontos, hogy a várt számítási feladatnak megfelelő lemezméretet és konfigurációt válasszon. Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe és kezelhet virtuálisgép-lemezeket. Az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése
> * Lemez-pillanatképek


## <a name="default-azure-disks"></a>Alapértelmezett Azure-lemezek

Egy Azure-beli virtuális gép létrehozásakor a rendszer két lemezt automatikusan a virtuális géphez csatol.

**Operációsrendszer-lemez** – Az operációsrendszer-lemezek akár 2 TB méretűek is lehetnek, és a virtuális gépek operációs rendszerei találhatók rajtuk. Az operációsrendszer-lemez alapértelmezés szerint a */dev/sda* címkét kapja. Az operációsrendszer-lemez gyorsítótárazási konfigurációja az operációs rendszer teljesítményéhez van optimizálva. A konfiguráció miatt az operációsrendszer-lemez **nem** használható alkalmazásokhoz és adatokhoz. Alkalmazásokhoz és adatokhoz használjon adatlemezeket, amelyekről később lesz szó az oktatóanyagban.

**Ideiglenes lemez** – Az ideiglenes lemezek olyan tartós állapotú meghajtót (SSD-t) használnak, amely ugyanazon az Azure-gazdagépen található, mint a virtuális gép. Az ideiglenes lemezek nagy teljesítményűek és olyan műveletekhez használhatók, mint például az ideiglenes adatfeldolgozás. Ha azonban a virtuális gépet egy új gazdagépre költöztetik, az ideiglenes lemezen tárolt adatokat a rendszer eltávolítja. Az ideiglenes lemez méretét a virtuális gép mérete határozza meg. Az ideiglenes lemezek a */dev/sdb* címkét kapják, a csatlakoztatási pontjuk pedig */mnt*.

## <a name="azure-data-disks"></a>Azure-adatlemezek

Alkalmazások telepítéséhez és adatok tárolásához további adatlemezek adhatók hozzá. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. A virtuális gép mérete határozza meg, hány adatlemez csatolható a virtuális géphez.

## <a name="vm-disk-types"></a>Virtuálisgép-lemezek típusai

Az Azure két lemeztípust kínál.

**Standard lemezek** – a merevlemez-meghajtókra épülő lemezek költséghatékony tárolási megoldást, ugyanakkor jó teljesítményt nyújtanak. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.

**Prémium lemezek** – SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Az S **betűméretű** virtuálisgép-méretek általában támogatják a Premium Storage. [](../vm-naming-conventions.md) A DS- és DSv2-, GS- és FS-sorozatú virtuális gépek például támogatják a prémium szintű tárolást. Lemezméret kiválasztásakor az értéket felfelé kerekíti a rendszer a következő típusra. Ha például a lemez mérete nagyobb 64 GB-nál, de 128 GB-nál kisebb, akkor a lemez típusa P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

A standard szintű tárolóktól eltérően prémium szintű tárolólemez kiépítésekor garantált a lemez kapacitása, IOPS-értéke és átviteli sebessége. Ha például P50-lemezt hoz létre, az Azure 4095 GB tárkapacitást, 7500 IOPS-t és 250 MB/s átviteli sebességet biztosít a lemez számára. Az alkalmazás a kapacitás és a teljesítmény egészét vagy egy részét használhatja. prémium SSD lemezek úgy vannak kialakítva, hogy az idő 99,9%-ában alacsony egyszámjegyű ezredmásodperc késést és cél IOPS-értékeket és átviteli sebességet biztosítanak.

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például a Standard_GS5 virtuális géphez 64 adatlemez csatolható. Ha ezen lemezek mindegyike P30-ra van méretezve, maximum 80 000-es IOPS-érték érhető el. A virtuális gépenkénti maximális IOPS-értékről szóló részletes információkért lásd a [virtuális gépek típusait és méreteit](../sizes.md) ismertető cikket.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Azure Cloud Shell egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell a kódblokk jobb felső sarkában található Try **it** (Próbálja ki) gombra. A böngészőablakot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/powershell](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása

Adatlemezek létrehozhatók és csatolhatók a virtuális gépek létrehozáskor vagy a meglévő virtuális gépekhez is.

### <a name="attach-disk-at-vm-creation"></a>Lemez csatolása virtuális gép létrehozásakor

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. A következő példa létrehoz egy *myVM* nevű virtuális gépet, hozzáadja az *azureuser* nevű felhasználói fiókot, valamint SSH-kulcsokat hoz létre, ha azok még nem léteznének. A `--datadisk-sizes-gb` argumentum használatával adhatja meg, hogy egy további lemezt kell létrehozni és a virtuális géphez csatolni. Több lemez létrehozásához és csatolásához a lemezméreteket egy szóközzel tagolt listában adja meg. A következő példában egy virtuális gépet hozunk létre két, egyenként 128 GB méretű adatlemezzel. Mivel a lemezek mérete 128 GB, mindkettő P10 típusú lemezként van konfigurálva, amely lemezenként maximálisan 500 IOPS-t biztosít.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Lemez csatolása meglévő virtuális géphez

Új lemez egy meglévő virtuális gépen való létrehozásához és csatolásához használja az [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach) parancsot. A következő példában egy 128 gigabájt méretű, prémium szintű lemezt hozunk létre, majd csatoljuk a legutóbbi lépésben létrehozott virtuális géphez.

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

Particionálja a lemezt az `parted` használatával.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Írjon egy fájlrendszert a partícióra az `mkfs` paranccsal. A `partprobe` használatával tudatja az operációs rendszerrel a változást.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Csatolja az új lemezt, hogy elérhető legyen az operációs rendszerben.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

A lemez most már elérhető a csatlakozási ponton keresztül, amely a parancs futtatásával `/datadrive` `df -h` ellenőrizhető.

```bash
df -h | grep -i "sd"
```

A kimenetben a csatlakoztatott új meghajtó `/datadrive` látható.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Annak érdekében, hogy a meghajtó a rendszer újraindítása után ismét csatolva legyen, fel kell venni azt az */etc/fstab* fájlba. Ehhez kérje le a lemez UUID-jét a `blkid` segédprogrammal.

```bash
sudo -i blkid
```

A kimenet megjeleníti a meghajtó UUID-jét, amely esetünkben a `/dev/sdc1`.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Ha nem megfelelően szerkeszti az **/etc/fstab** fájlt, az rendszer nem lesz rendszer. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Javasolt az /etc/fstab fájl biztonsági mentése is a szerkesztés előtt.

Nyissa meg `/etc/fstab` a fájlt egy szövegszerkesztőben a következőképpen:

```bash
sudo nano /etc/fstab
```

Adjon hozzá egy, az alábbihoz hasonló sort az */etc/fstab* fájlhoz, és cserélje le az UUID értéket a saját értékére.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Ha végzett a fájl szerkesztésével, a használatával írja meg a fájlt, `Ctrl+O` és lépjen ki a `Ctrl+X` szerkesztőből.

Most, hogy a lemez konfigurálva lett, zárja be az SSH-munkamenetet.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Lemez pillanatképének készítése

A lemezpillanatképek létrehozása során az Azure egy csak olvasható, adott időponthoz kötött másolatot hoz létre a lemezről. Az Azure-beli virtuális gépekről készült pillanatképek akkor lehetnek hasznosak, ha a virtuális gépek állapotának gyors mentésére van szükség a konfiguráció módosítása előtt. Probléma vagy hiba esetén a virtuális gép visszaállítható egy pillanatkép segítségével. Ha a virtuális gép egynél több lemezzel rendelkezik, az egyes lemezekről egymástól független pillanatképek készülnek. Alkalmazáskonzisztens biztonsági másolatok készítéséhez érdemes lehet leállítania a virtuális gépet a lemezpillanatképek készítése előtt. Másik megoldásként használhatja az [Azure Backup szolgáltatást](../../backup/index.yml) is, amelynek segítségével automatikus biztonsági mentést végezhet, miközben a virtuális gép fut.

### <a name="create-snapshot"></a>Pillanatkép készítése

A pillanatkép létrehozása előtt szüksége lesz a lemez azonosítójára vagy nevére. A [lemezazonosítót az az vm show](/cli/azure/vm#az_vm_show) használatával használhatja. A példában a lemezazonosítót egy változó tárolja, így az egy későbbi lépésben majd felhasználható.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Most, hogy megvan az azonosító, [az az snapshot create](/cli/azure/snapshot#az_snapshot_create) használatával hozzon létre egy pillanatképet a lemezről.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Lemez létrehozása pillanatképből

Ez a pillanatkép ezután átalakítható lemezre [az az disk create](/cli/azure/disk#az_disk_create)használatával, amely a virtuális gép újbóli létrehozásához használható.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Virtuális gép visszaállítása pillanatképből

A virtuális gép helyreállításának szemléltetése érdekében törölje a meglévő virtuális gépet [az az vm delete parancs használatával.](/cli/azure/vm#az_vm_delete)

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

Keresse meg az adatlemez nevét az [az disk list paranccsal.](/cli/azure/disk#az_disk_list) Ebben a példában a lemez nevét egy nevű változóban adhatja meg, amelyet a `datadisk` következő lépésben fog használni.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

A lemezt az [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach) paranccsal csatolhatja.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a virtuálisgép-lemezekkel kapcsolatos témakörökkel ismerkedett meg, például:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése
> * Lemez-pillanatképek

Folytassa a következő oktatóanyaggal, amely a virtuális gép konfigurálásának automatizálását ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-konfiguráció létrehozása](./tutorial-automate-vm-deployment.md)
