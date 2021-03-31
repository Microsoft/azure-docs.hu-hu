---
title: Oktatóanyag – SAP HANA adatbázis-visszaállítás az Azure-ban a parancssori felület használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja vissza az Azure-beli virtuális gépen futó SAP HANA-adatbázisokat egy Azure Backup Recovery Services-tárolóból az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334803"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok visszaállítása Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen ismerteti, hogyan lehet visszaállítani egy biztonsági másolattal rendelkező SAP HANA adatbázist egy Azure-beli virtuális gépen – az Azure CLI használatával. Ezeket a lépéseket a [Azure Portal](./sap-hana-db-restore.md)használatával is végrehajthatja.

A CLI-parancsok futtatásához használja a [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) .

Az oktatóanyag végén a következőket teheti:

> [!div class="checklist"]
>
> * Biztonsági másolattal rendelkező adatbázis visszaállítási pontjainak megtekintése
> * Adatbázis helyreállítása

Ez az oktatóanyag feltételezi, hogy rendelkezik egy olyan Azure-beli virtuális gépen futó SAP HANA-adatbázissal, amelyről biztonsági másolat készül Azure Backup használatával. Ha az [Azure-ban SAP HANA-adatbázis biztonsági mentését](tutorial-sap-hana-backup-cli.md) HASZNÁLTA a parancssori felület használatával a SAP HANA-adatbázis biztonsági mentéséhez, akkor a következő erőforrásokat fogja használni:

* egy *saphanaResourceGroup* nevű erőforráscsoport
* egy *saphanaVault* nevű tároló
* VMAppContainer nevű védett tároló *; Számítás; saphanaResourceGroup; saphanaVM*
* a (z) *saphanadatabase; hxe; hxe* nevű biztonsági másolati adatbázis/tétel
* a *westus2* régió erőforrásai

## <a name="view-restore-points-for-a-backed-up-database"></a>Biztonsági másolattal rendelkező adatbázis visszaállítási pontjainak megtekintése

Az adatbázishoz tartozó összes helyreállítási pont listájának megtekintéséhez használja az az [Backup recoverypoint List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) parancsmagot az alábbiak szerint:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A helyreállítási pontok listája a következőképpen fog megjelenni:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Amint láthatja, a fenti lista három helyreállítási pontot tartalmaz: az egyiket a teljes, a különbözeti és a napló biztonsági mentéshez.

>[!NOTE]
>Megtekintheti az összes nem törött napló biztonsági mentési láncának kezdő-és végpontját az az [Backup recoverypoint show-log-Chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) parancsmag használatával is.

## <a name="prerequisites-to-restore-a-database"></a>Adatbázis visszaállításának előfeltételei

Az adatbázis visszaállítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az adatbázist csak olyan SAP HANA-példányra állíthatja vissza, amely ugyanabban a régióban található
* A cél példányt regisztrálni kell ugyanazzal a tárolóval, mint a forrás
* Azure Backup nem képes két különböző SAP HANA példány azonosítására ugyanazon a virtuális gépen. Ezért nem lehetséges az adatok egyik példányról egy másikra való visszaállítása ugyanazon a virtuális gépen.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

A Azure Backup az Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat a következőképpen állíthatja helyre:

* Visszaállítás adott dátumra vagy időpontra (a másodikra) a naplók biztonsági másolatainak használatával. A Azure Backup automatikusan meghatározza a megfelelő teljes, különbözeti biztonsági másolatokat, valamint a visszaállításhoz szükséges napló biztonsági mentésének láncát a kiválasztott idő alapján.
* Egy adott helyreállítási pontra való visszaállításhoz állítsa vissza egy adott teljes vagy különbözeti biztonsági mentést.

Egy adatbázis visszaállításához használja az az Restore [Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) parancsmagot, amelyhez egy helyreállítási konfigurációs objektum szükséges a bemenetek egyikéhez. Ez az objektum az az [Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) parancsmag használatával hozható létre. A helyreállítási konfigurációs objektum a visszaállítás végrehajtásához szükséges összes adatot tartalmazza. Egyikük a visszaállítási mód – **OriginalWorkloadRestore** vagy **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – az eredeti forrással megegyező SAP HANA példányra állítja vissza az adathalmazt. Ez a beállítás felülírja az eredeti adatbázist. <br>
> **AlternateWorkloadRestore** – az adatbázist egy másik helyre állítja vissza, és megtartja az eredeti forrás-adatbázist.

## <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

Egy adatbázis másik helyre történő visszaállításához használja a **AlternateWorkloadRestore** visszaállítási módot. Ezután ki kell választania a visszaállítási pontot, amely lehet korábbi időponthoz vagy az előző visszaállítási pontokhoz.

Ebben az oktatóanyagban visszaállítja egy korábbi visszaállítási pontra. [Tekintse meg az adatbázishoz tartozó visszaállítási pontok listáját](#view-restore-points-for-a-backed-up-database) , és válassza ki azt a pontot, amelyet vissza szeretne állítani. Ez az oktatóanyag a *7660777527047692711* nevű visszaállítási pontot fogja használni.

A fenti visszaállítási pont nevét és a visszaállítási módot használva hozzon létre egy helyreállítási konfigurációs objektumot az az [Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) parancsmag használatával. Nézzük meg, hogy a parancsmag összes többi paramétere mit jelent:

* **--cél-elemnév-név** Ez az a név, amelyet a visszaállított adatbázis fog használni. Ebben az esetben a *restored_database* nevet használtuk.
* **--cél-kiszolgáló neve** Ez egy olyan SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy Recovery Services-tárolóban, és ugyanabban a régióban található, mint a visszaállítani kívánt adatbázis. Ebben az oktatóanyagban visszaállítjuk az adatbázist ugyanarra a SAP HANA-kiszolgálóra, amelyet a védett *hxehost* nevezett meg.
* **--célkiszolgáló-típus** SAP HANA adatbázisok visszaállításához a **HANAInstance** -t kell használni.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

A fenti lekérdezésre adott válasz egy olyan helyreállítási konfigurációs objektum lesz, amely a következőképpen néz ki:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Az adatbázis visszaállításához futtassa az az Restore [Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) parancsmagot. A parancs használatához adja meg a fenti JSON-kimenetet, amelyet egy *recoveryconfig.js* nevű fájlba ment.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet így fog kinézni:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az [az Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmag használatával.

## <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

Az eredeti helyre történő visszaállításhoz a **OrignialWorkloadRestore** -t a visszaállítási módként fogjuk használni. Ezután ki kell választania a visszaállítási pontot, amely lehet korábbi időponthoz vagy az előző visszaállítási pontokhoz.

Ebben az oktatóanyagban az előző "28-11-2019-09:53:00" időpontot választjuk vissza a következőre:. Ezt a visszaállítási pontot a következő formátumokban adhatja meg: nn-hh-éééé, nn-hh-éééé-hh: PP: mm. Ha egy érvényes időpontot szeretne visszaállítani a szolgáltatásra, használja az az [Backup recoverypoint show-log-Chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) parancsmagot, amely felsorolja a nem törött naplózási lánc biztonsági másolatainak intervallumait.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A fenti lekérdezésre adott válasz egy helyreállítási konfigurációs objektum lesz, amely a következőképpen néz ki:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Az adatbázis visszaállításához futtassa az az Restore [Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) parancsmagot. A parancs használatához adja meg a fenti JSON-kimenetet, amelyet egy *recoveryconfig.js* nevű fájlba ment.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet így fog kinézni:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmag használatával.

## <a name="restore-as-files"></a>Visszaállítás fájlként

Ha egy adatbázis helyett fájlként kívánja visszaállítani a biztonsági mentési adatmennyiséget, a **RestoreAsFiles** -t a visszaállítási módként fogjuk használni. Ezután válassza ki a visszaállítási pontot, amely lehet korábbi időponthoz vagy az előző visszaállítási pontokhoz. Ha a fájlok egy megadott elérési útra lettek kiadva, ezeket a fájlokat bármely olyan SAP HANA-gépre elvégezheti, ahol adatbázisként szeretné visszaállítani őket. Mivel ezeket a fájlokat bármely gépre áthelyezheti, mostantól visszaállíthatja az összes előfizetést és régiót.

Ebben az oktatóanyagban kiválasztjuk az előző időpontot, `28-11-2019-09:53:00` amelyről vissza kell állítani a (z) és a biztonságimásolat-fájlok kiírásának helyét `/home/saphana/restoreasfiles` ugyanazon a SAP HANA kiszolgálón. Ezt a visszaállítási pontot a következő formátumok valamelyikében adhatja meg: **nn-hh-éééé** vagy **nn-hh-éééé-hh: PP: mm**. Ha egy érvényes időpontot szeretne visszaállítani a szolgáltatásra, használja az az [Backup recoverypoint show-log-Chain](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) parancsmagot, amely felsorolja a nem törött naplózási lánc biztonsági másolatainak intervallumait.

A fenti visszaállítási pont neve és a visszaállítási mód használatával hozzuk létre a helyreállítási konfigurációs objektumot az az [Backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) parancsmag használatával. Nézzük meg, hogy a parancsmag összes többi paramétere mit jelent:

* **– cél-tároló – név** Ez egy olyan SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy Recovery Services-tárolóban, és ugyanabban a régióban található, mint a visszaállítani kívánt adatbázis. Ebben az oktatóanyagban az adatbázist fájlként fogjuk visszaállítani a védett, *hxehost* nevű SAP HANA-kiszolgálóra.
* **--RP-név** Az időponthoz tartozó visszaállításhoz a visszaállítási pont neve **DefaultRangeRecoveryPoint** lesz.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

A fenti lekérdezésre adott válasz egy helyreállítási konfigurációs objektum lesz, amely a következőképpen néz ki:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Most, hogy a-adatbázist fájlként szeretné visszaállítani, futtassa az az Restore [Restore-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) parancsmagot. A parancs használatához adja meg a fenti JSON-kimenetet, amely a *recoveryconfig.js* nevű fájlba lesz mentve.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

A kimenet így fog kinézni:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

A válasz megadja a feladatnév nevét. Ez a feladatnév használható a feladatok állapotának nyomon követésére az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmag használatával.

A célként megadott tárolóra kimásolt fájlok a következők:

* Adatbázis biztonságimásolat-fájljai
* Katalógus fájljai
* JSON-metaadatok fájljai (minden érintett biztonságimásolat-fájlhoz)

A célként megadott elérési úttal megegyező hálózati megosztási elérési út vagy egy csatlakoztatott Azure-fájlmegosztás elérési útja lehetővé teszi, hogy a fájlok könnyebben hozzáférhessenek az azonos hálózatban lévő más gépekhez, illetve az azokhoz csatlakoztatott Azure-fájlmegosztáshoz.

>[!NOTE]
>Ha vissza szeretné állítani az adatbázis biztonsági másolatának fájljait egy olyan Azure-fájlmegosztás számára, amely a cél regisztrált virtuális gépen van csatlakoztatva, győződjön meg arról, hogy a főfiók rendelkezik írási/olvasási engedéllyel az Azure-fájlmegosztás számára.

A kiválasztott visszaállítási pont (**időpont** vagy **teljes & különbözet**) alapján egy vagy több, a célként megadott elérési úton létrehozott mappa jelenik meg. Az egyik nevű mappa `Data_<date and time of restore>` tartalmazza a teljes és a különbözeti biztonsági mentést, és a nevű másik mappa `Log` tartalmazza a naplók biztonsági másolatait.

Helyezze át ezeket a visszaállított fájlokat arra a SAP HANA kiszolgálóra, amelyen adatbázisként szeretné visszaállítani őket. Ezután kövesse az alábbi lépéseket az adatbázis visszaállításához:

1. Állítsa be az engedélyeket a mappához/könyvtárhoz, ahol a biztonsági mentési fájlokat a következő paranccsal tárolja a rendszer:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Futtassa a következő parancsokat `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. A katalógus fájljának előállítása a visszaállításhoz. Bontsa ki a **biztonsági másolat azonosítója** a JSON metaadat-fájljából a teljes biztonsági mentéshez, amelyet később a visszaállítási művelet során fog használni. Győződjön meg arról, hogy a teljes és a naplózott biztonsági mentés különböző mappákban található, és törölje a katalógus fájljait és a JSON-metaadatok fájljait ezekben a mappákban.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    A fenti parancsban:

    * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
    * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
    * `<PathToPlaceCatalogFile>` – az a mappa, ahová a katalógust létre kell hozni

1. Állítsa vissza az újonnan létrehozott katalógusfájlt a HANA studión keresztül, vagy futtassa a HDBSQL-visszaállítási lekérdezést ezzel az újonnan létrehozott katalógussal. Az HDBSQL-lekérdezések az alábbi listában láthatók:

    * Visszaállítás egy adott időpontra:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL parancsot egy új adatbázis létrehozásához, `<DatabaseName>` majd állítsa le az adatbázist a visszaállításhoz. Ha azonban csak egy meglévő adatbázist állít helyre, futtassa a HDBSQL parancsot az adatbázis leállításához.

        Ezután futtassa a következő parancsot az adatbázis visszaállításához:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – A visszaállítani kívánt új adatbázis vagy meglévő adatbázis neve
        * `<Timestamp>` – Az időponthoz tartozó visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>` – Annak az adatbázisnak a neve, amelynek biztonsági másolatát a rendszer a visszaállításhoz használja, valamint a **gazdagép** -vagy SAP HANA-kiszolgáló nevét, amelyen az adatbázis található. A `USING SOURCE <DatabaseName@HostName>` beállítással megadhatja, hogy az adatbiztonsági másolat (visszaállításra használt) egy másik SID vagy nevű adatbázis, mint a cél SAP HANA gép. Így nem kell megadnia a visszatároláshoz ugyanazt a HANA-kiszolgálót, amelyről a biztonsági mentés készült.
        * `<PathToGeneratedCatalogInStep3>`– A **3. lépésben** létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
        * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
        * `<BackupIdFromJsonFile>`– a **3. lépésben** kinyert **biztonsági másolat azonosítója**

    * Visszaállítás egy adott teljes vagy különbözeti biztonsági másolatra:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL parancsot egy új adatbázis létrehozásához, `<DatabaseName>` majd állítsa le az adatbázist a visszaállításhoz. Ha azonban csak egy meglévő adatbázist állít helyre, futtassa a HDBSQL parancsot az adatbázis leállításához:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – a visszaállítani kívánt új adatbázis vagy meglévő adatbázis neve
        * `<Timestamp>` – az időponthoz tartozó visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>` – annak az adatbázisnak a neve, amelynek biztonsági másolatát a rendszer a visszaállításhoz használja, valamint a **gazdagép** -vagy SAP HANA-kiszolgáló nevét, amelyen az adatbázis található. A `USING SOURCE <DatabaseName@HostName>`  beállítással megadhatja, hogy az adatbiztonsági másolat (visszaállításra használt) egy másik SID vagy nevű adatbázis, mint a cél SAP HANA gép. Ezért nem kell megadnia a visszaállításokat azon a HANA-kiszolgálón, amelyről a biztonsági mentés készült.
        * `<PathToGeneratedCatalogInStep3>`– a **3. lépésben** létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági mentést tartalmazó mappa
        * `<LogFilesDir>` – a napló biztonsági másolatait tartalmazó mappa
        * `<BackupIdFromJsonFile>`– a **3. lépésben** kinyert **biztonsági másolat azonosítója**

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan kezelheti SAP HANA az Azure CLI használatával biztonsági mentést tartalmazó adatbázisokat, folytassa a [SAP HANA-adatbázis kezelése az Azure virtuális gépen a parancssori felület használatával](tutorial-sap-hana-backup-cli.md) című oktatóanyagot.

* Az Azure-beli virtuális gépen futó SAP HANA-adatbázisok a Azure Portal használatával történő visszaállításáról a [SAP HANA adatbázisok visszaállítása Azure](./sap-hana-db-restore.md) -beli virtuális gépeken című témakörben talál további információt.
