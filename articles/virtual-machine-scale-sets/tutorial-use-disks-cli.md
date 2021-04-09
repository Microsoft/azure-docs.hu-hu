---
title: Oktatóanyag – lemezek létrehozása és használata méretezési csoportokhoz az Azure CLI-vel
description: Megismerheti, hogyan hozhat létre és használhat felügyelt lemezeket a virtuálisgép-méretezési csoportokhoz az Azure CLI használatával, beleértve a lemezek hozzáadását, előkészítését, listázását és leválasztását.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433493"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Oktatóanyag: Lemezek létrehozása és használata virtuálisgép-méretezési csoportokhoz Azure CLI-vel
A virtuálisgép-méretezési csoportok lemezeket használnak a virtuálisgép-példányok operációs rendszereinek, alkalmazásainak és adatainak tárolására. Méretezési csoportok létrehozásakor és kezelésekor fontos szempont, hogy a számítási feladatok jelentette várható terhelésnek megfelelő lemezméretet és konfigurációt válasszon ki. Ez az oktatóprogram bemutatja, hogyan hozhat létre és kezelhet virtuálisgép-lemezeket. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.29 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

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
További adatlemezek adhatók hozzá, amelyekre alkalmazásokat telepíthet és amelyeken adatokat tárolhat. Az adatlemezeket akkor érdemes használni, ha tartós és rugalmas adattárolásra van szükség. Az egyes adatlemezek kapacitása maximum 4 TB lehet. A virtuálisgép-példány mérete határozza meg, hány adatlemez csatolható. Mindegyik virtuálisgép-vCPU két adatlemez csatlakoztatható virtuális gépenként legfeljebb 64 lemezzel.

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

Míg a fenti táblázatban a lemezenkénti maximális IOPS-érték látható, nagyobb teljesítmény is elérhető több adatlemez összevonásával. Például egy Standard_GS5 virtuális gép esetében maximálisan 80 000 IOPS érhető el. A virtuális gépenkénti maximális IOPS-értékkel kapcsolatos részletes információkért lásd a [Linux rendszerű virtuális gépek méreteit](../virtual-machines/sizes.md) ismertető cikket.


## <a name="create-and-attach-disks"></a>Lemezek létrehozása és csatolása
A méretezési csoport létrehozásakor vagy egy meglévő méretezési csoporthoz is létrehozhat és csatlakoztathat lemezeket.

Az API verziójának megfelelően beállíthatja az `2019-07-01` operációsrendszer-lemez méretét a [StorageProfile. OsDisk. diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) tulajdonsággal rendelkező virtuálisgép-méretezési csoportokban. A kiépítés után előfordulhat, hogy a teljes terület használatához ki kell bontania vagy újra particionálnia kell a lemezt. További információ [a lemez kibővítéséről](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Lemezek csatlakoztatása méretezési csoport létrehozásakor
Első lépésként hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre az *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) parancs használatával. A következő példa létrehoz egy *myScaleSet* nevű méretezési készletet, és SSH-kulcsokat hoz létre, ha azok nem léteznek. Két lemez jön létre a `--data-disk-sizes-gb` paraméterrel. Az első lemez mérete *64* GB, a második lemez pedig *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

A méretezési csoport erőforrásainak és virtuálisgép-példányainak létrehozása és konfigurálása néhány percet vesz igénybe.

### <a name="attach-a-disk-to-existing-scale-set"></a>Lemez csatolása meglévő méretezési csoporthoz
Meglévő méretezési csoporthoz is hozzáadhat lemezeket. Adjon hozzá egy újabb lemezt az előző lépésben létrehozott méretezési csoporthoz az [az vmss disk attach](/cli/azure/vmss/disk) paranccsal. Az alábbi példa egy újabb *128* GB-os lemezt csatlakoztat:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Az adatlemezek előkészítése
A méretezési csoport virtuálisgép-példányaihoz létrehozott és hozzácsatolt lemezek nyers lemezek. Mielőtt adatokhoz vagy alkalmazásokhoz használhatná őket, a lemezeket elő kell készíteni. A lemezek előkészítéséhez létrehoz egy partíciót, egy fájlrendszert és beolvassa azokat.

A folyamat a méretezési csoport több virtuálisgép-példányán való automatizálásához használja az Azure egyéni szkriptek futtatására szolgáló bővítményét. A bővítmény képes a szkriptek helyi végrehajtására az egyes virtuálisgép-példányokon, például előkészíti a csatlakoztatott adatlemezeket. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/extensions/custom-script-linux.md).

Az alábbi példa végrehajtja a GitHub mintatárból származó szkriptet az egyes virtuálisgép-példányokon az [az vmss extension set](/cli/azure/vmss/extension) paranccsal, és előkészíti az összes csatlakoztatott nyers adatlemezt:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Annak megerősítéséhez, hogy a lemezek megfelelően elő lettek készítve, SSH-kapcsolaton keresztül csatlakozzon az egyik virtuálisgép-példányhoz. Listázza ki a méretezési csoport kapcsolatadatait az [az vmss list-instance-connection-info](/cli/azure/vmss) paranccsal:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Az alábbi példában látható módon használja a saját nyilvános IP-címét és portszámát az első virtuálisgép-példányhoz való csatlakozáshoz:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Az alábbiak szerint vizsgálja meg a virtuálisgép-példány partícióit:

```bash
sudo fdisk -l
```

Az alábbi példa kimenetében látható, hogy három lemez van csatlakoztatva a virtuálisgép-példányhoz: */dev/sdc*, */dev/sdd* és */dev/sde*. Mindegyik lemez egyetlen partícióval rendelkezik, amely felhasználja az összes elérhető tárterületet:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Az alábbiak szerint vizsgálja meg a virtuálisgép-példány fájlrendszereit és csatlakozási pontjait:

```bash
sudo df -h
```

Az alábbi példa kimenetében látható, hogy a három lemez fájlrendszerei megfelelően csatlakoztatva vannak a */datadisks* elérési útra:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

A rendszer a skálázásban lévő egyes virtuálisgép-példányokon lévő lemezeket ugyanilyen módon, automatikusan készíti elő. A méretezési csoport vertikális felskálázása esetén a rendszer hozzácsatolja a szükséges adatlemezeket az új virtuálisgép-példányokhoz. Az egyéni szkriptbővítmény is fut, hogy automatikusan előkészítse a lemezeket.

Zárja be a virtuálisgép-példány felé indított SSH-kapcsolatot:

```bash
exit
```


## <a name="list-attached-disks"></a>Csatlakoztatott lemezek listázása
A méretezési csoporthoz csatlakoztatott lemezekkel kapcsolatos információk megtekintéséhez használja az [az vmss show](/cli/azure/vmss) parancsot, és állítsa be a *virtualMachineProfile.storageProfile.dataDisks* lekérdezést:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

A lemez méretével, a tárolási szinttel és a logikai egység számával (LUN) kapcsolatos információk jelennek meg. Az alábbi példa kimenetében három, a méretezési csoporthoz csatlakoztatott adatlemez látható:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Lemez leválasztása
Ha már nincs szüksége egy adott lemezre, válassza azt le a méretezési csoportról. A rendszer eltávolítja a lemezt a méretezési csoport minden virtuálisgép-példányáról. A lemez a méretezési csoportról történő leválasztásához használja az [az vmss disk detach](/cli/azure/vmss/disk) parancsot, és adja meg a lemez logikai egységének számát. A logikai egységek számai az előző szakaszban használt [az vmss show](/cli/azure/vmss) parancs kimenetében láthatók. Az alábbi példa leválasztja a *2* LUN-számú lemezt a méretezési csoportról:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a lemezek eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást az [az group delete](/cli/azure/group) paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat lemezeket a méretezési csoportokkal együtt az Azure CLI használatával:

> [!div class="checklist"]
> * Operációsrendszer-lemezek és ideiglenes lemezek
> * Adatlemezek
> * Standard és Prémium lemezek
> * Lemezteljesítmény
> * Adatlemezek csatolása és előkészítése

A következő oktatóanyag azt mutatja be, hogyan használhat egyedi rendszerképeket a méretezési csoport virtuálisgép-példányaihoz.

> [!div class="nextstepaction"]
> [Egyedi rendszerkép használata a méretezési csoport virtuálisgép-példányaihoz](tutorial-use-custom-image-cli.md)