---
title: A cloud-init használatával a lapozófájl konfigurálása a Linux rendszerű virtuális gép |} A Microsoft Docs
description: A lapozófájl konfigurálása a Linux rendszerű virtuális gép létrehozása az Azure CLI-vel során a cloud-init használatával
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 626fd4739daf2506854c42f16ac986a361ebab38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60729817"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>A lapozófájl konfigurálása a Linux rendszerű virtuális gépen a cloud-init használatával
Ez a cikk bemutatja, hogyan használható [a cloud-init](https://cloudinit.readthedocs.io) különböző Linux-disztribúciókon a lapozófájl konfigurálása. A lapozófájl hagyományosan konfigurálása által a Linuxos ügynök (WALA) melyik disztribúció szükséges egyik alapján.  Ez a dokumentum igény szerinti üzembe helyezés ideje a cloud-init használata során a lapozófájl létrehozásához fog vázoltuk.  A cloud-init működése natív módon az Azure és a támogatott Linux-disztribúciók kapcsolatos további információkért lásd: [cloud-init áttekintése](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Lapozófájl Ubuntu-alapú lemezképek létrehozása
Ubuntu-katalógusbeli rendszerképeket az Azure-ban alapértelmezés szerint ne hozzon létre lapozó. Virtuális gép üzembe helyezési idő cloud-init használata során a lapozófájl-kapacitás fájl konfiguráció engedélyezése – tekintse át a [AzureSwapPartitions dokumentum](https://wiki.ubuntu.com/AzureSwapPartitions) Ubuntu wiki.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Red Hat és a CentOS-alapú lemezképek lapozófájl létrehozása

Hozzon létre egy fájlt az aktuális felületen *cloud_init_swapfile.txt* , és illessze be a következő konfigurációt. Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud_init_swapfile.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Győződjön meg arról, hogy a teljes cloud-init-fájlt, másolja, különösen az első sort.  

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

Ez a rendszerkép üzembe helyezése előtt hozzon létre egy erőforráscsoportot kell a [az csoport létrehozása](/cli/azure/group) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm) , és adja meg a cloud-init fájl `--custom-data cloud_init_swapfile.txt` módon:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Lapozófájl létrehozásának ellenőrzéséhez
SSH nyilvános IP-címet a virtuális gép az előző parancs kimenetében látható. Adja meg a saját **publicIpAddress** módon:

```bash
ssh <publicIpAddress>
```

Miután SSH'ed a virtuális géppel, ellenőrizze, ha a lapozófájl létrehozása

```bash
swapon -s
```

Ez a parancs kimenete kell kinéznie:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Ha rendelkezik meglévő Azure-rendszerképet, amely rendelkezik egy lapozófájl konfigurált, és meg szeretné változtatni a lapozófájl-kapacitás fájl konfigurációs új képek, távolítsa el a meglévő lapozófájl. Tekintse át "Testreszabás képek üzembe helyezni a cloud-init" dokumentum további részletekért.

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások további a cloud-init példákért tekintse meg a következőket:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
