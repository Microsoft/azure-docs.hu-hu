---
title: Rövid útmutató – Virtuális gépek biztonsági visszaállítása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Recovery Services-tárolót, hogyan engedélyezheti a védelmet egy virtuális gépen, és hogyan hozhatja létre a kezdeti helyreállítási pontot az Azure CLI használatával.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 521f9332590eca5cdf94fadc4b59ab4d482eb658
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768558"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-azure-cli"></a>Virtuális gép biztonsági létrehozása az Azure-ban az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítási tárolókban tárolható helyreállítási pontokat hoz létre. Ez a cikk az Azure virtuális gépek (VM-ek) az Azure CLI használatával való biztonsági mentését mutatja be részletesen. Az [Azure PowerShell](quick-backup-vm-powershell.md) vagy az [Azure Portal](quick-backup-vm-portal.md) használatával is elvégezheti ezeket a lépéseket.

Ez a rövid útmutató a meglévő Azure-beli virtuális gépek biztonsági mentését mutatja be. Ha létre kell hoznia egy virtuális gépet, [létrehozhatja azt az Azure CLI használatával](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy logikai tároló, amely az egyes védett erőforrások, például az Azure-beli virtuális gépek biztonsági másolatainak adatait tárolja. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tárolót az [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) paranccsal hozhat létre. Adja meg a védeni kívánt virtuális gépével megegyező erőforráscsoportot és helyet. Ha a [virtuális gépek rövid útmutatóját](../virtual-machines/linux/quick-create-cli.md) használta, a következőket hozta létre:

- egy *myResourceGroup* nevű erőforráscsoportot,
- egy *myVM* nevű virtuális gépet,
- erőforrásokat az *eastus* régióban.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

A Recovery Services-tároló alapértelmezés szerint georedundáns tárolásra van beállítva. Geo-Redundant tárolás biztosítja, hogy a biztonsági mentési adatok replikálva vannak egy másodlagos Azure-régióba, amely több száz kilométerre van az elsődleges régiótól. Ha módosítani kell a tárhely-redundancia beállítását, használja [az az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az_backup_vault_backup_properties_set) parancsmagot.

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gépeken

Hozzon létre egy biztonsági szabályzatot, amely meghatározza, hogy a rendszer mikor futtassa a biztonsági mentési feladatokat, és meddig tárolja a helyreállítási pontokat. Az alapértelmezett védelmi házirend naponta egyszer futtat biztonsági mentési feladatot, és 30 napig őrzi meg a helyreállítási pontokat. Ezekkel az alapértelmezett értékekkel gyorsan biztosíthatja virtuális gépe védelmét. Egy virtuális gép biztonsági mentésének engedélyezéséhez használja [az az backup protection enable-for-vm parancsokat.](/cli/azure/backup/protection#az_backup_protection_enable_for_vm) Adja meg az erőforráscsoportot és a védeni kívánt virtuális gépet, majd a használni kívánt házirendet:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Ha a virtuális gép nem ugyanabban az erőforráscsoportban van, mint a tároló, akkor a myResourceGroup arra az erőforráscsoportra hivatkozik, amelyben a tároló létre lett hozva. A virtuális gép neve helyett adja meg a virtuális gép azonosítóját az alábbiak szerint.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> Ha a CLI használatával egyszerre több virtuális gép biztonsági mentését is engedélyezi, győződjön meg arról, hogy egyetlen szabályzathoz nem tartozik több mint 100 virtuális gép. Ez az [ajánlott eljárás.](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy) A PowerShell-ügyfél jelenleg nem tiltja le explicit módon, ha több mint 100 virtuális gép van, de az ellenőrzést a jövőben tervezünk hozzáadni.

## <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

Ha szeretné a biztonsági mentést most elindítani, ahelyett, hogy megvárná, amíg az alapértelmezett házirend az ütemezett időben futtatja a feladatot, használja az [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now) parancsot. Ez az első biztonsági mentési feladat létrehoz egy teljes helyreállítási pontot. Az ezt a kezdeti biztonsági mentést követő további biztonsági mentési feladatok növekményes helyreállítási pontokat hoznak létre. A növekményes helyreállítási pontok hatékonyan használják a tárhelyet és az időt, mivel csak az utolsó biztonsági mentés óta végzett módosításokat viszik át.

A virtuális gépek biztonsági mentéséhez a következő paraméterek használhatók:

- A(z) `--container-name` a virtuális gép neve
- A(z) `--item-name` a virtuális gép neve
- A(z) `--retain-until` értékét UTC időformátumban (**nn-hh-éééé**) arra az utolsó elérhető dátumra kell állítani, ameddig szeretné, hogy a helyreállítási pont elérhető legyen.

A következő példa elvégzi a *myVM* virtuális gép biztonsági mentését, és a helyreállítási pont lejártát 2017. október 18-ra állítja be:

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A biztonsági mentési feladatok állapotának monitorozásához használja az [az backup job list](/cli/azure/backup/job#az_backup_job_list) parancsot:

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A kimenet a következő példához hasonló, amelyen látható, hogy a biztonsági mentési feladat *InProgress* (Folyamatban) állapotban van:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Amikor a biztonsági mentési feladat *Állapota* alatt *Befejezve* látható, a virtuális gép a Recovery Services védelme alatt áll, és megtörtént egy teljes helyreállítási pont tárolása.

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Ha többé már nincs szükség rá, a virtuális gép védelmét bármikor leállíthatja, eltávolíthatja a helyreállítási pontokat és a Recovery Services-tárolót, majd törölheti az erőforráscsoportot és a társított virtuális gép erőforrásait. Ha egy meglévő virtuális gépet használt, eltekinthet a végső [az group delete](/cli/azure/group#az_group_delete) parancs végrehajtásától, és megtarthatja az erőforráscsoportot és a virtuális gépet.

Ha szeretne megpróbálkozni a biztonsági mentéssel foglalkozó oktatóanyaggal, amely a virtuális gép adatainak visszaállítását ismerteti, ugorjon a [Következő lépések](#next-steps) részre.

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot. Ha bővebb információra van szüksége az Azure Backup és a Recovery Services szolgáltatásokkal kapcsolatban, lépjen tovább a következő oktatóanyagokra.

> [!div class="nextstepaction"]
> [Biztonsági mentés készítése több Azure-beli virtuális gépről](./tutorial-backup-vm-at-scale.md)
