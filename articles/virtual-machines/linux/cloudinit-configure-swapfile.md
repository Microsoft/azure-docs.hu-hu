---
title: A Cloud-init használata a swap-partíciók Linux rendszerű virtuális gépen való konfigurálásához
description: A Cloud-init használata a swap-partíció konfigurálásához Linux rendszerű virtuális gépen az Azure CLI-vel való létrehozás során
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559375"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>A Cloud-init használata a swap-partíciók Linux rendszerű virtuális gépen való konfigurálásához
Ez a cikk bemutatja, hogyan konfigurálhatja a swap partíciót különböző Linux-disztribúciókban a [Cloud-init](https://cloudinit.readthedocs.io) használatával. A swap-partíciót hagyományosan a Linux-ügynök (WALA) állította be, amely alapján a disztribúciók közül egy szükséges.  Ez a dokumentum felvázolja a swap-partíció igény szerinti felépítési folyamatát a üzembe helyezés ideje alatt a Cloud-init használatával.  További információ arról, hogyan működik a Cloud-init natív módon az Azure-ban és a támogatott Linux-disztribúciókban: a [Cloud-init áttekintése](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Swap-partíció létrehozása Ubuntu-alapú rendszerképekhez
Az Azure-ban alapértelmezés szerint az Ubuntu Gallery-lemezképek nem hoznak létre swap-partíciókat. Ha engedélyezni szeretné a felcserélt partíciók konfigurációját a virtuális gép üzembe helyezésekor a Cloud-init használatával – tekintse meg a [AzureSwapPartitions dokumentumot](https://wiki.ubuntu.com/AzureSwapPartitions) az Ubuntu wikin.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Swap-partíció létrehozása a Red Hat és a CentOS-alapú rendszerképekhez

Hozzon létre egy fájlt a *cloud_init_swappart.txt* nevű aktuális rendszerhéjban, és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_swappart.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Győződjön meg arról, hogy a teljes Cloud-init fájl megfelelően van másolva, különösen az első sorban.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

A rendszerkép telepítése előtt létre kell hoznia egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm) paranccsal, és határozza meg a Cloud-init fájlt a `--custom-data cloud_init_swappart.txt` következő módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>A swap-partíció létrehozásának ellenőrzése
SSH-t az előző parancs kimenetében látható virtuális gép nyilvános IP-címére. Adja meg saját **publicIpAddress** a következőképpen:

```bash
ssh <publicIpAddress>
```

Miután SSH'ed a virtuális gépre, ellenőrizze, hogy létrejött-e a swap-partíció.

```bash
swapon -s
```

A parancs kimenetének a következőhöz hasonlóan kell kinéznie:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Ha van olyan meglévő Azure-lemezképe, amelynek van konfigurált swap-partíciója, és módosítani szeretné az új lemezképek swap-partíciójának konfigurációját, távolítsa el a meglévő swap partíciót. További részletekért tekintse meg a "rendszerképek testreszabása a Cloud-init" dokumentumban.

## <a name="next-steps"></a>Következő lépések
További felhő-inicializálási példákat a konfiguráció változásairól a következő témakörben talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
