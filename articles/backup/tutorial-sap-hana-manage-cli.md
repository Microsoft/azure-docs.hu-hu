---
title: 'Oktatóanyag: Biztonsági adatbázis biztonsági SAP HANA kezelése a CLI használatával'
description: Ez az oktatóanyag bemutatja, hogyan felügyelhet biztonsági SAP HANA Azure-beli virtuális gépen futó adatbázisokat az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7090701e3642fd9703737060e0876c8bbfc27994
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765178"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: Adatbázis SAP HANA kezelése Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel való létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen bemutatja, hogyan kezelheti a biztonsági SAP HANA azure-beli virtuális gépen – mindezt az Azure CLI használatával. Ezeket a lépéseket a következővel is [Azure Portal.](./sap-hana-db-manage.md)

Parancssori [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) parancssori felületi parancsok futtatásához használja a következőt: .

Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Biztonsági mentési és visszaállítási feladatok figyelése
> * Új, egy új példányhoz hozzáadott SAP HANA védelme
> * A szabályzat módosítása
> * A védelem kikapcsolása
> * Védelem folytatása

Ha az Azure-beli [SAP HANA-adatbázis](tutorial-sap-hana-backup-cli.md) biztonsági mentése a cli használatával használta a SAP HANA-adatbázis biztonsági mentéset, akkor a következő erőforrásokat használja:

* egy *saphanaResourceGroup nevű erőforráscsoport*
* egy *saphanaVault* nevű tároló
* *VMAppContainer nevű védett tároló; Compute;saphanaResourceGroup;saphanaVM*
* biztonsági mentéses *adatbázis/saphanadatabase nevű elem;hxe;hxe*
* erőforrások a *westus2 régióban*

Az Azure CLI segítségével könnyedén felügyelhet egy azure SAP HANA-alapú virtuális gépen futó adatbázist, amelyről biztonsági Azure Backup. Ez az oktatóanyag az egyes felügyeleti műveleteket részletezi.

## <a name="monitor-backup-and-restore-jobs"></a>Biztonsági mentési és visszaállítási feladatok figyelése

A befejezett vagy aktuálisan futó feladatok (biztonsági mentés vagy visszaállítás) figyeléhez használja [az az backup job list](/cli/azure/backup/job#az_backup_job_list) parancsmagot. A CLI azt is lehetővé teszi, hogy [felfüggesztsen egy jelenleg futó feladatot,](/cli/azure/backup/job#az_backup_job_stop) vagy megvárja, amíg egy feladat [befejeződik.](/cli/azure/backup/job#az_backup_job_wait)

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A kimenet a következőhöz hasonlóan fog kinézni:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Szabályzat módosítása

A biztonsági mentés konfigurációját SAP HANA az az [backup policy set](/cli/azure/backup/policy#az_backup_policy_set) parancsmag használatával módosíthatja. A parancsmag névparamétere arra a biztonsági mentési elemre vonatkozik, amelynek a szabályzatát módosítani szeretnénk. Ebben az oktatóanyagban az SAP HANA adatbázis *saphanadatabase;hxe;hxe* szabályzatát egy új *newsaphanaPolicy* szabályzatra cseréljük. Új szabályzatok az [az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) parancsmag használatával hozhatók létre.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

A kimenetnek az alábbihoz hasonlónak kell lennie:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Növekményes biztonsági mentési szabályzat létrehozása

Növekményes biztonsági mentési szabályzat létrehozásához hajtsa végre [az az backup policy create](/cli/azure/backup/policy#az_backup_policy_create) parancsot a következő paraméterekkel:

* **--backup-management-type** – Azure Workload
* **--workload-type** – SAPHana
* **--name** – A szabályzat neve
* **--policy** – JSON-fájl az ütemezés és a megőrzés megfelelő részleteivel
* **--resource-group** – a tároló erőforráscsoportja
* **--vault-name** – a tároló neve

Példa:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Minta JSON (sappolicy.jsbe):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

A szabályzat sikeres létrehozása után a parancs kimenete megjeleníti a parancs végrehajtásakor paraméterként átadott szabályzat JSON-t.

A házirend következő szakaszát módosíthatja a növekményes biztonsági mentések kívánt biztonsági mentési gyakoriságának és megőrzési gyakoriságának megadásához.

Például:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Példa:

Ha a növekményes biztonsági mentéseket csak szombatra szeretné megőrizni, és 60 napig szeretné megőrizni őket, a következő módosításokat kell követnie a szabályzatban:

* A **retentionDuration count frissítése** 60 napra
* Csak szombati értéket adjon meg **ScheduleRunDays értékként**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Új, egy új példányhoz hozzáadott SAP HANA védelme

[A helyreállítási SAP HANA recovery](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) services-tárolóval való regisztrálása automatikusan felderíti a példány összes adatbázisát.

Ha azonban később új adatbázisokat adnak a SAP HANA-példányhoz, használja az [az backup protectable-item initialize](/cli/azure/backup/protectable-item#az_backup_protectable_item_initialize) parancsmagot. Ez a parancsmag felderíti a hozzáadott új adatbázisokat.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Ezután használja [az az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) parancsmagot a saját példányán felderített összes SAP HANA listához. Ez a lista azonban kizárja azokat az adatbázisokat, amelyeken már konfigurálva van a biztonsági mentés. Miután a rendszer felderíti a biztonsági mentésre szükséges adatbázist, tekintse meg a következőt: Biztonsági mentés engedélyezése [a SAP HANA adatbázison.](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

A listában megjelenik az új adatbázis, amelyről biztonsági adatbázist szeretne biztonsági mentéset, a következőképpen néz ki:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Adatbázis védelmének SAP HANA le

Többféleképpen is leállíthatja SAP HANA adatbázis védelmét:

* Állítsa le az összes jövőbeli biztonsági mentési feladat, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatokat, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy elhagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

* Az összes helyreállítási pont örökre érintetlen marad, és minden adatmegőrzési folyamat leáll, és a védelem leáll, és megőrzi az adatokat.
* A védett példányért és a felhasznált tárterületért díjat számítunk fel.
* Ha a biztonsági mentések leállítása nélkül töröl egy adatforrást, az új biztonsági mentések sikertelenek lesznek.

Nézzük meg részletesebben a védelem leállításának különböző módjait.

### <a name="stop-protection-with-retain-data"></a>Védelem leállítása az adatok megőrzésével

Ha le szeretné állítani a védelmet az adatok megőrzésével, használja [az az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) parancsmagot.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

A kimenetnek az alábbihoz hasonlónak kell lennie:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

### <a name="stop-protection-without-retain-data"></a>Védelem leállítása az adatok megőrzése nélkül

Ha az adatok megőrzése nélkül szeretné leállítani a védelmet, használja [az az backup protection disable](/cli/azure/backup/protection#az_backup_protection_disable) parancsmagot.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

A kimenetnek az alábbihoz hasonlónak kell lennie:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="resume-protection"></a>Védelem folytatása

Ha leállítja a SAP HANA adatbázis védelmét az adatok megőrzésével, később folytathatja a védelmet. Ha nem őrzi meg a biztonságimentett adatokat, nem tudja folytatni a védelmet.

A védelem folytatásához használja [az az backup protection resume](/cli/azure/backup/protection#az_backup_protection_resume) parancsmagot.

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

A kimenetnek az alábbihoz hasonlónak kell lennie:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

A művelet állapotának ellenőrzéséhez használja [az az backup job show](/cli/azure/backup/job#az_backup_job_show) parancsmagot.

## <a name="next-steps"></a>Következő lépések

* Az Azure-beli virtuális gépen SAP HANA adatbázis biztonsági mentésének Azure Portal az Azure-beli virtuális gépeken SAP HANA [Backup-adatbázisokról](./backup-azure-sap-hana-database.md)

* Az Azure-beli virtuális gépen futó biztonsági SAP HANA-adatbázisok Azure Portal kezeléséhez tekintse meg az Azure-beli virtuális gépeken SAP HANA biztonsági [mentéseit](./sap-hana-db-manage.md)