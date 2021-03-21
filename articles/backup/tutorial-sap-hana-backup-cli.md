---
title: Oktatóanyag – SAP HANA adatbázis biztonsági mentése az Azure-ban az Azure CLI használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépeken futó SAP HANA adatbázisokról egy Azure Backup Recovery Services-tárolóra az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba06ef876f30dc51e04fe7491d491621f5d8e21b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710600"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen ismerteti a SAP HANA adatbázis biztonsági mentését és az igény szerinti biztonsági mentések elindítását – mindezt az Azure CLI használatával. Ezeket a lépéseket a [Azure Portal](./backup-azure-sap-hana-database.md)használatával is végrehajthatja.

Ez a dokumentum azt feltételezi, hogy már rendelkezik egy Azure-beli virtuális gépen telepített SAP HANA-adatbázissal. ( [Virtuális gépet is létrehozhat az Azure CLI használatával](../virtual-machines/linux/quick-create-cli.md)). Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Recovery Services-tároló létrehozása
> * Regisztrálja SAP HANA példányát, és keresse meg az adatbázis (oka) t
> * Biztonsági mentés engedélyezése SAP HANA adatbázison
> * Igény szerinti biztonsági mentés indítása

Tekintse át a SAP HANA [jelenleg támogatott forgatókönyveket](./sap-hana-backup-support-matrix.md#scenario-support) .

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.30 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services tároló egy logikai tároló, amely az egyes védett erőforrások, például Azure-beli virtuális gépek vagy Azure-beli virtuális gépeken futó munkaterhelések (például SQL vagy HANA-adatbázisok) biztonsági mentési adatait tárolja. Amikor egy védett erőforrás biztonsági mentésének feladata fut, a rendszer egy helyreállítási pontot hoz létre a Recovery Services-tárolóban. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra.

Recovery Services-tárolót az [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) paranccsal hozhat létre. Adja meg a védeni kívánt virtuális gépével megegyező erőforráscsoportot és helyet. Megtudhatja, hogyan hozhat létre virtuális gépet az Azure CLI- [vel ezzel a](../virtual-machines/linux/quick-create-cli.md)virtuálisgép-útmutatóval.

Ebben az oktatóanyagban a következőket fogjuk használni:

* egy *saphanaResourceGroup* nevű erőforráscsoport
* egy *saphanaVM* nevű virtuális gép
* erőforrások a *westus2* helyen.

Létrehozunk egy *saphanaVault* nevű tárat.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

A Recovery Services-tároló alapértelmezés szerint georedundáns tárolásra van beállítva. Geo-Redundant Storage biztosítja a biztonsági mentési adatok replikálását egy olyan másodlagos Azure-régióba, amely több száz kilométerre van az elsődleges régiótól. Ha módosítani kell a tárolási redundancia beállítást, használja az az [Backup Vault Backup-Properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) parancsmagot.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Ha szeretné megtudni, hogy a tár létrehozása sikeres volt-e, használja az az [Backup Vault List](/cli/azure/backup/vault#az-backup-vault-list) parancsmagot. A következő választ fogja látni:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Az SAP HANA-példány regisztrálása és megóvása

Ahhoz, hogy az Azure-szolgáltatások felderítsék az SAP HANA-példányt (a SAP HANA telepített virtuális gépet), az SAP HANA gépen egy [regisztrációs parancsfájlt](https://aka.ms/scriptforpermsonhana) kell futtatni. A parancsfájl futtatása előtt győződjön meg arról, hogy minden [előfeltétel](./tutorial-backup-sap-hana-db.md#prerequisites) teljesül. Ha többet szeretne megtudni a szkripttel kapcsolatban, tekintse meg a [Mi az előzetes regisztrációs parancsfájl](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) a következő témakört:.

A szkript futtatása után a SAP HANA-példány regisztrálható a korábban létrehozott Recovery Services-tárolóban. A példány regisztrálásához használja az az [Backup Container Register](/cli/azure/backup/container#az-backup-container-register) parancsmagot. A *VMResourceId* a SAP HANA telepítéséhez létrehozott virtuális gép erőforrás-azonosítója.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Ha a virtuális gép nem ugyanabban az erőforráscsoporthoz van, mint a tároló, akkor a *saphanaResourceGroup* arra az erőforráscsoporthoz utal, amelyben a tárolót létrehozták.

A SAP HANA példány regisztrálása automatikusan felfedi az összes aktuális adatbázisát. Azonban a későbbiekben felvehető új adatbázisok felderítéséhez tekintse meg a regisztrált SAP HANA példányhoz [hozzáadott új adatbázisok felfedése](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) című szakaszt.

Az az [Backup Container List](/cli/azure/backup/container#az-backup-container-list) parancsmag használatával ellenőrizze, hogy a SAP HANA-példány sikeresen regisztrálva van-e a tárolóban. A következő választ fogja látni:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> A fenti kimenet "Name" oszlopa a tároló nevére utal. Ezt a tároló nevét fogja használni a következő részekben a biztonsági mentések engedélyezéséhez és azok aktiválásához. Ebben az esetben a *VMAppContainer; Számítás; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Biztonsági mentés engedélyezése SAP HANA adatbázison

Az az [Backup Protected-Item List](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) parancsmag felsorolja az előző lépésben regisztrált SAP HANA példányon felderített összes adatbázist.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

A listában keresse meg azt az adatbázist, amelyről biztonsági másolatot szeretne készíteni, amely a következőképpen fog kinézni:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Ahogy a fenti kimenetben látható, a SAP HANA rendszer SID-HXE. Ebben az oktatóanyagban a *saphanadatabase; hxe; hxe* -adatbázis biztonsági mentését konfigurálja, amely a *hxehost* -kiszolgálón található.

Egy adatbázis biztonsági mentésének védelméhez és konfigurálásához használja az az [Backup Protection Enable-for-azurewl](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) parancsmagot. Adja meg a használni kívánt szabályzat nevét. Ha a parancssori felület használatával szeretne szabályzatot létrehozni, használja az az [Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) parancsmagot. Ebben az oktatóanyagban a *sapahanaPolicy* szabályzatot fogjuk használni.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Megtekintheti, hogy a fenti biztonsági mentési konfiguráció készen áll-e az az [Backup Job List](/cli/azure/backup/job#az-backup-job-list) parancsmag használatával. A kimenet a következőképpen fog megjelenni:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Az az [Backup Job List](/cli/azure/backup/job#az-backup-job-list) parancsmag felsorolja az összes olyan biztonsági mentési feladatot (ütemezett vagy igény szerint), amely fut vagy jelenleg fut a védett adatbázisban, valamint az egyéb műveleteket, például a regisztrálást, a biztonsági mentés konfigurálását és a biztonsági mentési adatok törlését.

>[!NOTE]
>A Azure Backup nem módosítja automatikusan a nyári időmegtakarítást az Azure-beli virtuális gépen futó SAP HANA-adatbázis biztonsági mentésekor.
>
>Szükség szerint módosítsa manuálisan a szabályzatot.

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés indítása

A fenti szakasz részletesen ismerteti az ütemezett biztonsági mentést, ez a szakasz az igény szerinti biztonsági mentés aktiválását mutatja be. Ehhez használja az az [Backup Protection Backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) parancsmagot.

>[!NOTE]
> Az igény szerinti biztonsági mentés megőrzési házirendjét az adatbázis alapjául szolgáló adatmegőrzési házirend határozza meg.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

A kimenet a következőképpen fog megjelenni:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmag használatával.

>[!NOTE]
>A naplók biztonsági mentését a rendszer automatikusan aktiválja és felügyeli SAP HANA belsőleg.

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan állíthatja vissza SAP HANA adatbázist az Azure virtuális gépen a parancssori felület használatával, folytassa az Oktatóanyaggal – [SAP HANA adatbázis visszaállítása az Azure virtuális gépen a CLI használatával](tutorial-sap-hana-restore-cli.md)

* Az Azure-beli virtuális gépen futó SAP HANA adatbázis biztonsági mentéséhez Azure Portal használatával: [SAP HANA-adatbázisok biztonsági mentése Azure virtuális gépeken](./backup-azure-sap-hana-database.md)
