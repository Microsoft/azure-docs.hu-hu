---
title: Oktatóanyag – SAP HANA adatbázis biztonsági mentése az Azure-ban az Azure CLI használatával
description: Ez az oktatóanyag bemutatja, hogyan lehet biztonsági SAP HANA Azure-beli virtuális gépen futó adatbázisokról egy Azure Backup Recovery Services-tárolóba az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: bebfe852aaac965fc7d07371be889fe515e3da3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768488"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: Biztonsági SAP HANA azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel való létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen bemutatja, hogyan lehet biztonsági másolatot készíteni egy SAP HANA adatbázisról, és hogyan lehet aktiválni az igény szerinti biztonsági mentéseket – mindezt az Azure CLI használatával. Ezeket a lépéseket a következővel is [Azure Portal.](./backup-azure-sap-hana-database.md)

Ez a dokumentum feltételezi, hogy már rendelkezik SAP HANA azure-beli virtuális gépre telepített adatbázissal. (Virtuális gépet [az Azure CLI használatával is létrehozhat.](../virtual-machines/linux/quick-create-cli.md) Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * Regisztráljon SAP HANA, és derítse fel rajta az adatbázis(ok)t
> * Biztonsági mentés engedélyezése egy SAP HANA adatbázison
> * Igény szerinti biztonsági mentés aktiválása

Tekintse meg [a jelenleg támogatott](./sap-hana-backup-support-matrix.md#scenario-support) forgatókönyveket a SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tárolók olyan logikai tárolók, amelyek az egyes védett erőforrások , például Az Azure-beli virtuális gépeken futó Azure-beli virtuális gépek vagy számítási feladatok – például SQL- vagy HANA-adatbázisok – biztonsági másolati adatait tárazják. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tárolót az [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) paranccsal hozhat létre. Adja meg a védeni kívánt virtuális gépével megegyező erőforráscsoportot és helyet. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépet az Azure CLI [használatával.](../virtual-machines/linux/quick-create-cli.md)

Ebben az oktatóanyagban a következőket fogjuk használni:

* egy *saphanaResourceGroup nevű erőforráscsoport*
* egy *saphanaVM nevű virtuális gép*
* erőforrások a *westus2 helyen.*

Létrehozunk egy *saphanaVault nevű tárolót.*

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

A Recovery Services-tároló alapértelmezés szerint georedundáns tárolásra van beállítva. Geo-Redundant tárolás biztosítja, hogy a biztonsági mentési adatok replikálva vannak egy másodlagos Azure-régióba, amely több száz kilométerre van az elsődleges régiótól. Ha módosítani kell a tárhely-redundancia beállítását, használja [az az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az_backup_vault_backup_properties_set) parancsmagot.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Ha meg kell tudni, hogy sikeresen létrejött-e a tároló, használja [az az backup vault list](/cli/azure/backup/vault#az_backup_vault_list) parancsmagot. A következő választ fogja látni:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>A SAP HANA regisztrálása és védelme

Ahhoz, SAP HANA [Azure-szolgáltatások](https://aka.ms/scriptforpermsonhana) felderítsen egy SAP HANA-példányt (a virtuális gépre telepített SAP HANA), egy előregisztrációs szkriptet kell futtatni a SAP HANA gépen. A szkript futtatása [](./tutorial-backup-sap-hana-db.md#prerequisites) előtt győződjön meg arról, hogy minden előfeltétel teljesül. Ha többet szeretne megtudni arról, hogy mire való a szkript, tekintse meg a Mit csinál az előzetes regisztrációs [szkript?](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) című szakaszt.

A szkript futtatása után a SAP HANA-példány regisztrálható a korábban létrehozott Recovery Services-tárolóban. A példány regisztráláshoz használja az [az backup container register](/cli/azure/backup/container#az_backup_container_register) parancsmagot. *A VMResourceId* a virtuális gép erőforrás-azonosítója, amely a virtuális gép telepítéséhez SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Ha a virtuális gép nem ugyanabban az erőforráscsoportban van, mint a tároló, akkor a *saphanaResourceGroup* arra az erőforráscsoportra hivatkozik, amelyben a tárolót létrehozták.

A SAP HANA-példány regisztrálása automatikusan felderíti az összes aktuális adatbázist. A jövőben hozzáadható új adatbázisok felderítését azonban a Regisztrált adatbázispéldányhoz hozzáadott új adatbázisok felderítése [SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) szakaszban talál.

Az az backup [container list](/cli/azure/backup/container#az_backup_container_list) SAP HANA ellenőrizheti, hogy az SAP HANA-példány sikeresen regisztrálva van-e a tárolóban. A következő választ fogja látni:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A fenti kimenetben a "name" oszlop a tároló nevére hivatkozik. A következő szakaszokban ezt a tárolónevet fogjuk használni a biztonsági mentések engedélyezéséhez és aktiválásukhoz. Ami ebben az esetben a *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Biztonsági mentés engedélyezése SAP HANA adatbázison

Az [az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) parancsmag felsorolja az előző lépésben regisztrált SAP HANA-példányon felderített összes adatbázist.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Ebben a listában meg kell keresnie azt az adatbázist, amelyről biztonsági mentéset szeretne, a következőképpen néz ki:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Ahogy a fenti kimenetből látható, a rendszer biztonsági SAP HANA HXE. Ebben az oktatóanyagban a *hxehost* kiszolgálón található *saphanadatabase;hxe;hxe* adatbázis biztonsági mentését konfiguráljuk.

Az adatbázisok biztonsági mentésének védelméhez és konfigurálásához az [az backup protection enable-for-azurewl](/cli/azure/backup/protection#az_backup_protection_enable_for_azurewl) parancsmagot használjuk. Adja meg a használni kívánt szabályzat nevét. Ha a CLI használatával hoz létre szabályzatot, használja [az az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) parancsmagot. Ebben az oktatóanyagban a *sapahanaPolicy szabályzatot fogjuk* használni.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Az [az backup job list](/cli/azure/backup/job#az_backup_job_list) parancsmag használatával ellenőrizheti, hogy a fenti biztonsági mentési konfiguráció befejeződött-e. A kimenet a következőképpen jelenik meg:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Az [az backup job list](/cli/azure/backup/job#az_backup_job_list) parancsmag felsorolja az összes olyan (ütemezett vagy igény szerinti) biztonsági mentési feladatot, amelyek a védett adatbázison futnak vagy futnak, valamint az egyéb műveleteket, például a biztonsági mentés regisztrálását, konfigurálását és a biztonsági mentési adatok törlését.

>[!NOTE]
>Azure Backup a rendszer nem módosítja automatikusan a nyári időszámítást az Azure-beli virtuális gépeken SAP HANA adatbázis biztonsági mentésekor.
>
>Módosítsa a szabályzatot manuálisan, ha szükséges.

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés aktiválása

Bár a fenti szakasz az ütemezett biztonsági mentés konfigurálásról szól, ez a szakasz az igény szerinti biztonsági mentés aktiválását tárgyalja. Ehhez az [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now) parancsmagot használjuk.

>[!NOTE]
> Az igény szerinti biztonsági mentések adatmegőrzési szabályzatát az adatbázisra vonatkozó mögöttes adatmegőrzési szabályzat határozza meg.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A kimenet a következőképpen jelenik meg:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A válaszban adjuk meg a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmag használatával.

>[!NOTE]
>A naplók biztonsági mentései automatikusan aktiválódnak, és belsőleg SAP HANA kezelik.

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretne ismerkedni egy SAP HANA-adatbázis Azure-beli virtuális gépen a CLI használatával való visszaállításával, folytassa [a](tutorial-sap-hana-restore-cli.md) következő oktatóanyagokkal: Azure-beli virtuális SAP HANA visszaállítása a CLI használatával

* Az Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentésének Azure Portal [](./backup-azure-sap-hana-database.md) az Azure-beli virtuális gépeken SAP HANA biztonsági mentését.
