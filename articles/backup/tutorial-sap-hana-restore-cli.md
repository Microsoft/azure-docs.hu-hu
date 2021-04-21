---
title: Oktatóanyag – SAP HANA adatbázis visszaállítása az Azure-ban a CLI használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatók SAP HANA Azure-beli virtuális gépen futó adatbázisokat egy Azure Backup Recovery Services-tárolóból az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a249ab63aa72c1d39ab1626e72ff3b2037f3f723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768450"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA visszaállítása Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel való létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen bemutatja, hogyan állítható vissza egy biztonsági SAP HANA azure-beli virtuális gépen az Azure CLI használatával. Ezeket a lépéseket a következővel is [Azure Portal.](./sap-hana-db-restore.md)

Parancssori [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) parancssori felületi parancsok futtatásához használja a következőt: .

Az oktatóanyag végére a következőre lesz képes:

> [!div class="checklist"]
>
> * Biztonsági másolattal biztonsági másolattal felhozott adatbázisok visszaállítási pontjainak megtekintése
> * Adatbázis helyreállítása

Ez az oktatóanyag feltételezi, hogy SAP HANA azure-beli virtuális gépen futó adatbázisa biztonsági mentése a Azure Backup. Ha az Azure-beli [SAP HANA-adatbázis](tutorial-sap-hana-backup-cli.md) biztonsági mentése a cli használatával használta a SAP HANA-adatbázis biztonsági mentéset, akkor a következő erőforrásokat használja:

* egy *saphanaResourceGroup nevű erőforráscsoport*
* egy *saphanaVault* nevű tároló
* *VMAppContainer nevű védett tároló; Compute;saphanaResourceGroup;saphanaVM*
* biztonsági mentéses *adatbázis/saphanadatabase nevű elem;hxe;hxe*
* erőforrások a *westus2 régióban*

## <a name="view-restore-points-for-a-backed-up-database"></a>Biztonsági másolattal biztonsági másolattal felhozott adatbázisok visszaállítási pontjainak megtekintése

Egy adatbázis összes helyreállítási pontja listájának megtekintéséhez használja az [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) parancsmagot a következőképpen:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A helyreállítási pontok listája a következőképpen néz ki:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Amint látható, a fenti lista három helyreállítási pontot tartalmaz: egyet a teljes, különbségi és naplók biztonsági mentéséhez.

>[!NOTE]
>Az [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) parancsmag használatával megtekintheti az összes töretlen napló biztonsági mentési láncának kezdő és végpontját.

## <a name="prerequisites-to-restore-a-database"></a>Adatbázis visszaállításának előfeltételei

Az adatbázis visszaállítása előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Az adatbázist csak egy ugyanabban a régióban SAP HANA példányra lehet visszaállítani
* A célpéldányt ugyanazokkal a tárolóval kell regisztrálni, mint a forrást
* Azure Backup nem tud azonosítani két különböző SAP HANA ugyanazon a virtuális gépen. Ezért nem lehet adatokat visszaállítani egy példányról egy másikra ugyanazon a virtuális gépen.

## <a name="restore-a-database"></a>Adatbázis helyreállítása

Azure Backup azure SAP HANA virtuális gépeken futó adatbázisokat a következőképpen lehet visszaállítani:

* Visszaállítás egy adott dátumra vagy időpontra (másodpercre) naplómentésekkel. Azure Backup automatikusan meghatározza a megfelelő teljes, különbségi biztonsági mentéseket és a naplóalapú biztonsági mentések láncát, amelyek a visszaállításhoz szükségesek a kiválasztott idő alapján.
* Visszaállítás egy adott teljes vagy különbségi biztonsági mentésre egy adott helyreállítási pontra való visszaállításhoz.

Egy adatbázis visszaállításához használja az [az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) parancsmagot, amelyhez egy helyreállítási konfigurációs objektumra van szükség az egyik bemenetként. Ez az objektum az [az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) parancsmaggal generálható. A helyreállítási konfigurációs objektum tartalmazza a visszaállítás végrehajtásához szükséges összes részletet. Az egyik a visszaállítási mód – **OriginalWorkloadRestore** vagy **AlternateWorkloadRestore.**

>[!NOTE]
> **OriginalWorkloadRestore** – Az adatokat az eredeti forrással SAP HANA példányra állítsa vissza. Ez a beállítás felülírja az eredeti adatbázist. <br>
> **AlternateWorkloadRestore** – Állítsa vissza az adatbázist egy másik helyre, és tartsa meg az eredeti forrásadatbázist.

## <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

Egy adatbázis másik helyre való visszaállításához használja az **AlternateWorkloadRestore** visszaállítási módot. Ezután ki kell választania a visszaállítási pontot, amely lehet egy korábbi időponthoz vagy az előző visszaállítási pontok bármelyike.

Ebben az oktatóanyagban egy korábbi visszaállítási pontra fog visszaállítani. [Tekintse meg az adatbázis visszaállítási](#view-restore-points-for-a-backed-up-database) pontjainak listáját, és válassza ki azt a pontot, amelybe vissza szeretné állítani. Ez az oktatóanyag a *7660777527047692711* nevű visszaállítási pontot fogja használni.

A fenti visszaállítási pont nevének és a visszaállítási módnak a használatával hozza létre a helyreállítási konfigurációs objektumot [az az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) parancsmaggal. Nézzük meg, mit jelentenek a parancsmag többi paramétere:

* **--target-item-name** Ezt a nevet fogja használni a visszaállított adatbázis. Ebben az esetben a következő nevet *használtjuk: restored_database.*
* **--target-server-name** Ez egy olyan SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy Recovery Services-tárolóban, és ugyanabban a régióban található, mint a visszaállítani szükséges adatbázis. Ebben az oktatóanyagban az adatbázist a védett SAP HANA *hxehost* nevű kiszolgálóra fogjuk visszaállítani.
* **--target-server-type** Az adatbázis-SAP HANA a **HANAInstance-t** kell használni.

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

A fenti lekérdezésre adott válasz egy helyreállítási konfigurációs objektum lesz, amely a következőre hasonlít:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Most az adatbázis visszaállításához futtassa [az az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) parancsmagot. A parancs használata érdekében megadjuk a fenti JSON-kimenetet, amely egy nevű fájlba lesz *recoveryconfig.jsa következőn:*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet a következő lesz:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válaszban adjuk meg a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmag használatával.

## <a name="restore-and-overwrite"></a>Visszaállítás és felülírás

Az eredeti helyre való visszaállításhoz az **OrignialWorkloadRestore** visszaállítási módot fogjuk használni. Ezután ki kell választania a visszaállítási pontot, amely lehet egy korábbi időponthoz vagy az előző visszaállítási pontok bármelyike.

Ebben az oktatóanyagban az előző időponthoz "28-11-2019-09:53:00" időpontot választjuk a visszaállításhoz. Ezt a visszaállítási pontot a következő formátumokban használhatja: dd-mm-yyy, dd-mm-yyy-hh:mm:mm. Érvényes időpontra való visszaállításhoz használja az [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) parancsmagot, amely felsorolja a töretlen naplólánc biztonsági mentésének időközét.

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

Most az adatbázis visszaállításához futtassa [az az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) parancsmagot. A parancs használatával a fenti JSON-kimenetet adjuk meg, amely egy nevű fájlba van *recoveryconfig.jsa fájlban.*

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A kimenet a következő lesz:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A válasz megadja a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmag használatával.

## <a name="restore-as-files"></a>Visszaállítás fájlként

Ha adatbázis helyett fájlként szeretnénk visszaállítani a biztonsági mentési adatokat, visszaállítási módként a **RestoreAsFiles-t** fogjuk használni. Ezután válassza ki a visszaállítási pontot, amely lehet egy korábbi időponthoz vagy az előző visszaállítási pontok bármelyike. A fájlok megadott elérési útra való mentése után ezeket a fájlokat bármely olyan virtuális gépre SAP HANA, ahol vissza szeretné állítani őket adatbázisként. Mivel ezeket a fájlokat bármely gépre áthelyezheti, most már visszaállíthatja az adatokat az előfizetések és régiók között.

Ebben az oktatóanyagban az előző időponthoz hasonló visszaállítási lehetőséget választjuk, valamint azt a helyet, ahol a biztonsági mentési fájlokat ugyanazon a kiszolgálón SAP HANA `28-11-2019-09:53:00` `/home/saphana/restoreasfiles` ki. Ezt a visszaállítási pontot a következő formátumok valamelyikével használhatja: **dd-mm-yyy vagy** **dd-mm-yyyy-hh:mm:ss**. Érvényes időpontra való visszaállításhoz használja az [az backup recoverypoint show-log-chain](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) parancsmagot, amely felsorolja a töretlen naplólánc biztonsági mentésének időközét.

A fenti visszaállítási pont nevével és a visszaállítási móddal hozza létre a helyreállítási konfigurációs objektumot [az az backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) parancsmaggal. Nézzük meg, mit jelentenek a parancsmag többi paramétere:

* **--target-container-name** Ez egy olyan SAP HANA-kiszolgáló neve, amely sikeresen regisztrálva van egy Recovery Services-tárolóban, és ugyanabban a régióban található, mint a visszaállítani szükséges adatbázis. Ebben az oktatóanyagban fájlokként fogjuk visszaállítani az adatbázist ugyanannak a SAP HANA-kiszolgálónak, amely a *hxehost* nevet kapta.
* **--rp-name** Időponthoz időben való visszaállítás esetén a visszaállítási pont neve **DefaultRangeRecoveryPoint lesz**

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

Az adatbázis fájlokként való visszaállításához futtassa [az az restore restore-azurewl](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) parancsmagot. A parancs használata érdekében megadjuk a fenti JSON-kimenetet, amelyet a rendszer egy nevű fájlba *ment,recoveryconfig.jsa következőn:*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

A kimenet a következő lesz:

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

A válaszban adjuk meg a feladat nevét. Ez a feladatnév a feladat állapotának nyomon követésére használható [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmag használatával.

A céltárolóba a következő fájlok vannak kihozva:

* Adatbázis biztonsági mentési fájljai
* Katalógusfájlok
* JSON-metaadatfájlok (minden érintett biztonsági mentési fájlhoz)

A hálózati megosztási útvonal vagy a csatlakoztatott Azure-fájlmegosztás elérési útja, ha célelérési útvonalként van megadva, általában könnyebb hozzáférést biztosít ezekhez a fájlokhoz az ugyanazon a hálózaton található más gépek, vagy ha ugyanazon Azure-fájlmegosztás csatlakoztatva van hozzájuk.

>[!NOTE]
>Az adatbázis biztonsági mentési fájljainak visszaállításához a célként regisztrált virtuális géphez csatlakoztatott Azure-fájlmegosztáson ellenőrizze, hogy a gyökérfiók rendelkezik-e olvasási/írási engedélyekkel az Azure-fájlmegosztáson.

A kiválasztott visszaállítási pont **típusától** függően ( Időponthoz időben vagy Teljes & **különbségi**) egy vagy több létrehozott mappát fog látni a célútvonalon. Az egyik nevű mappa tartalmazza a teljes és különbségi biztonsági mentéseket, a másik nevű mappa pedig a naplók `Data_<date and time of restore>` `Log` biztonsági másolatát.

Helyezze át ezeket a visszaállított SAP HANA a SAP HANA, ahol vissza szeretné állítani őket adatbázisként. Ezután kövesse az alábbi lépéseket az adatbázis visszaállításához:

1. Állítsa be a biztonsági mentési fájlokat tároló mappára/könyvtárra vonatkozó engedélyeket a következő paranccsal:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Futtassa a következő parancskészletet a következőként: `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Hozza létre a katalógusfájlt a visszaállításhoz. Bontsa ki a **BackupId** adatokat a JSON-metaadatfájlból a teljes biztonsági mentéshez, amelyet a visszaállítási művelet során később fog használni. Győződjön meg arról, hogy a teljes és a naplók biztonsági mentése különböző mappákban van, és törölje a katalógusfájlokat és a JSON-metaadatfájlokat ezekben a mappákban.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    A fenti parancsban:

    * `<DataFileDir>` – a teljes biztonsági másolatokat tartalmazó mappa
    * `<LogFilesDir>` – a naplók biztonsági mentését tartalmazó mappa
    * `<PathToPlaceCatalogFile>` – a mappát, ahová a katalógusfájlt létre kell helyezni

1. Állítsa vissza az újonnan létrehozott katalógusfájlt a HANA Studióban, vagy futtassa a HDBSQL visszaállítási lekérdezést ezzel az újonnan létrehozott katalógussal. A HDBSQL-lekérdezések listája alább látható:

    * Visszaállítás időpontra:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL-parancsot egy új adatbázis létrehozásához, majd állítsa le az `<DatabaseName>` adatbázist visszaállításra. Ha azonban csak egy meglévő adatbázist állítja vissza, a HDBSQL-paranccsal állítsa le az adatbázist.

        Ezután futtassa a következő parancsot az adatbázis visszaállításához:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – A visszaállítani kívánt új vagy meglévő adatbázis neve
        * `<Timestamp>` - Az időponthoz való visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>`– Annak az adatbázisnak a neve,  amelynek a biztonsági másolatát visszaállításra használják, valamint a gazdagép/kiszolgáló SAP HANA, amelyen az adatbázis található. A beállítás azt adja meg, hogy az adatok biztonsági mentése (a visszaállításhoz használt) egy olyan adatbázisból van, amely a célként megadott biztonsági azonosítótól vagy `USING SOURCE <DatabaseName@HostName>` névtől eltérő SAP HANA géphez. Ezért nem kell megadni az ugyanazon a HANA-kiszolgálón végzett visszaállításhoz, ahonnan a biztonsági mentés készült.
        * `<PathToGeneratedCatalogInStep3>`– A **3.** lépésben létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági másolatokat tartalmazó mappa
        * `<LogFilesDir>` – a naplók biztonsági mentését tartalmazó mappa
        * `<BackupIdFromJsonFile>`– **a** **3.** lépésben kinyert BackupId

    * Visszaállítás egy adott teljes vagy különbségi biztonsági mentésre:

        Ha új visszaállított adatbázist hoz létre, futtassa a HDBSQL-parancsot egy új adatbázis létrehozásához, majd állítsa le az `<DatabaseName>` adatbázist a visszaállításhoz. Ha azonban csak egy meglévő adatbázist állítja vissza, futtassa a HDBSQL-parancsot az adatbázis leállításához:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – a visszaállítani kívánt új adatbázis vagy meglévő adatbázis neve
        * `<Timestamp>` – az időponthoz való visszaállítás pontos időbélyege
        * `<DatabaseName@HostName>`– annak az adatbázisnak a neve,  amelynek a biztonsági mentését visszaállításra használják, valamint a gazdagép/SAP HANA kiszolgáló neve, amelyen az adatbázis található. A beállítás azt adja meg, hogy az adatok biztonsági mentése (a visszaállításhoz használt) egy, a célgép cél biztonsági azonosítóját `USING SOURCE <DatabaseName@HostName>`  vagy nevét SAP HANA adatbázisból. Ezért nem kell megadni az ugyanazon a HANA-kiszolgálón végzett visszaállításhoz, ahonnan a biztonsági másolat készült.
        * `<PathToGeneratedCatalogInStep3>`– a **3.** lépésben létrehozott katalógusfájl elérési útja
        * `<DataFileDir>` – a teljes biztonsági másolatokat tartalmazó mappa
        * `<LogFilesDir>` – a naplók biztonsági mentését tartalmazó mappa
        * `<BackupIdFromJsonFile>`– **a** **3.** lépésben kinyert BackupId

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretne ismerkedni az Azure SAP HANA használatával biztonsági mentéses adatbázisokkal, folytassa a következő oktatóanyagmal: SAP HANA-adatbázis kezelése [Azure-beli](tutorial-sap-hana-backup-cli.md) virtuális gépen a CLI használatával

* Az Azure-beli virtuális gépeken futó SAP HANA azure-beli virtuális gépeken az Azure Portal használatával való visszaállítással kapcsolatban tekintse meg a következőt: SAP HANA-adatbázisok visszaállítása Azure-beli [virtuális gépeken](./sap-hana-db-restore.md)
