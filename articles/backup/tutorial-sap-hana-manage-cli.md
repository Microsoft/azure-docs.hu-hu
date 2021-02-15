---
title: 'Oktatóanyag: biztonsági mentéssel elkészített SAP HANA adatbázis kezelése a parancssori felület használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan kezelheti az Azure-beli virtuális gépeken futó biztonsági másolatok SAP HANA adatbázisait az Azure CLI használatával.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 665dfc64e750f448fc4c1a2d7e18f0cb6552f223
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371770"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Oktatóanyag: SAP HANA-adatbázisok kezelése Azure-beli virtuális gépen az Azure CLI használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére szolgál. Ez a dokumentáció részletesen ismerteti, hogyan kezelhető az Azure-beli virtuális gépen futó biztonsági másolat SAP HANA adatbázisa – mindezt az Azure CLI használatával. Ezeket [a lépéseket a Azure Portal](./sap-hana-db-manage.md)használatával is végrehajthatja.

A CLI-parancsok futtatásához használja a [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) .

Az oktatóanyag végére a következőkre lesz képes:

> [!div class="checklist"]
>
> * Biztonsági mentési és visszaállítási feladatok figyelése
> * SAP HANA-példányhoz hozzáadott új adatbázisok védelmének biztosítása
> * A házirend módosítása
> * A védelem kikapcsolása
> * Védelem folytatása

Ha az [Azure-ban SAP HANA-adatbázis biztonsági mentését](tutorial-sap-hana-backup-cli.md) HASZNÁLTA a parancssori felület használatával a SAP HANA-adatbázis biztonsági mentéséhez, akkor a következő erőforrásokat fogja használni:

* egy *saphanaResourceGroup* nevű erőforráscsoport
* egy *saphanaVault* nevű tároló
* VMAppContainer nevű védett tároló *; Számítás; saphanaResourceGroup; saphanaVM*
* a (z) *saphanadatabase; hxe; hxe* nevű biztonsági másolati adatbázis/tétel
* a *westus2* régió erőforrásai

Az Azure CLI-vel könnyedén kezelheti az olyan Azure-beli virtuális gépeken futó SAP HANA-adatbázisokat, amelyekről biztonsági másolat készül Azure Backup használatával. Ez az oktatóanyag részletesen ismerteti az egyes felügyeleti műveleteket.

## <a name="monitor-backup-and-restore-jobs"></a>Biztonsági mentési és visszaállítási feladatok figyelése

A befejezett vagy jelenleg futó feladatok (biztonsági mentés vagy visszaállítás) figyeléséhez használja az az [Backup Job List](/cli/azure/backup/job#az-backup-job-list) parancsmagot. A CLI lehetővé teszi [a jelenleg futó feladatok felfüggesztését](/cli/azure/backup/job#az-backup-job-stop) , vagy [várjon, amíg a feladatok befejeződik](/cli/azure/backup/job#az-backup-job-wait).

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

## <a name="change-policy"></a>Házirend módosítása

A SAP HANA biztonsági mentési konfiguráció alapjául szolgáló házirend módosításához használja az az [Backup Policy set](/cli/azure/backup/policy#az-backup-policy-set) parancsmagot. A parancsmag Name paramétere arra a biztonsági mentési elemre hivatkozik, amelynek a szabályzatát módosítani szeretné. Ebben az oktatóanyagban a SAP HANA Database *saphanadatabase; hxe; hxe* szabályzatát cseréljük új házirend- *newsaphanaPolicy*. Új házirendek hozhatók létre az az [Backup Policy Create](/cli/azure/backup/policy#az-backup-policy-create) parancsmag használatával.

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

Növekményes biztonsági mentési szabályzat létrehozásához hajtsa végre az az [biztonsági mentési szabályzat létrehozása](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_create) parancsot a következő paraméterekkel:

* **--Backup-Management-Type** – Azure munkaterhelés
* **--munkaterhelés-Type** -SAPHana
* **--Name** – a szabályzat neve
* **--Policy** -JSON-fájl az ütemterv és a megőrzés megfelelő részleteivel
* **– Erőforrás-csoport** – a tár erőforráscsoport
* **--Vault-Name** – a tár neve

Példa:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

JSON-minta (sappolicy.js):

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
A szabályzat sikeres létrehozása után a parancs kimenete a parancs végrehajtásakor paraméterként megadott JSON-t jeleníti meg.

A szabályzat következő szakaszának módosításával megadhatja a biztonsági mentés kívánt gyakoriságát és a növekményes biztonsági mentések megőrzését.

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

Ha a növekményes biztonsági mentést csak szombaton szeretné használni, és 60 napig kívánja megőrizni őket, hajtsa végre a következő módosításokat a szabályzatban:

* **RetentionDuration** -szám frissítése 60 napra
* Csak a szombat **ScheduleRunDays** kell megadnia

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

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>SAP HANA-példányhoz hozzáadott új adatbázisok védelmének biztosítása

Egy Recovery Services-tárolóval [rendelkező SAP HANA-példány regisztrálása](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) automatikusan felfedi az összes adatbázist ebben a példányban.

Azonban abban az esetben, ha később új adatbázisokat ad hozzá a SAP HANA-példányhoz, használja az az [Backup Protect-Item inicializálási](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) parancsmagot. Ez a parancsmag felfedi a hozzáadott új adatbázisokat.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Ezután használja az az [Backup Protected-Item List](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) parancsmagot az SAP HANA-példányon felderített adatbázisok listázásához. Ez a lista azonban kizárja azokat az adatbázisokat, amelyeken már konfigurálva van a biztonsági másolat. Az adatbázis biztonsági mentésének észlelése után tekintse meg az SAP HANA-  [adatbázis biztonsági másolatának engedélyezése](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database)című témakört.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Ebben a listában megjelenik az új adatbázis, amelyről biztonsági másolatot szeretne készíteni, majd a következőképpen fog megjelenni:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA-adatbázis védelmének leállítása

Több módon is leállíthatja a SAP HANA adatbázisok védelmét:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot.
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és hagyja érintetlenül a helyreállítási pontokat.

Ha úgy dönt, hogy kihagyja a helyreállítási pontokat, tartsa szem előtt az alábbi adatokat:

* Az összes helyreállítási pont érintetlen marad, és az összes törlés leáll a védelem leállításakor az adatmegőrzéssel.
* A védett példányért és a felhasznált tárterületért díjat számítunk fel.
* Ha töröl egy adatforrást a biztonsági mentések leállítása nélkül, az új biztonsági mentések sikertelenek lesznek.

Nézzük meg, hogyan lehet részletesebben leállítani a védelmet.

### <a name="stop-protection-with-retain-data"></a>Védelem leállítása az adatok megőrzésével

Az adatmegőrzési védelem leállításához használja az az [Backup Protection disable](/cli/azure/backup/protection#az-backup-protection-disable) parancsmagot.

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

A művelet állapotának megtekintéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

### <a name="stop-protection-without-retain-data"></a>Védelem leállítása az adat megőrzése nélkül

A védelem leállításához az adat megőrzése nélkül használja az az [Backup Protection disable](/cli/azure/backup/protection#az-backup-protection-disable) parancsmagot.

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

A művelet állapotának megtekintéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="resume-protection"></a>Védelem folytatása

Ha leállítja a SAP HANA-adatbázis védelmét az adatmegőrzési lehetőséggel, később folytathatja a védelmet. Ha nem őrzi meg a biztonsági másolatban szereplő adatait, nem fogja tudni folytatni a védelmet.

A védelem folytatásához használja az az [Backup Protection Resume](/cli/azure/backup/protection#az-backup-protection-resume) parancsmagot.

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

A művelet állapotának megtekintéséhez használja az az [Backup Job show](/cli/azure/backup/job#az-backup-job-show) parancsmagot.

## <a name="next-steps"></a>Következő lépések

* Az Azure-beli virtuális gépen futó SAP HANA adatbázisok biztonsági mentéséről a Azure Portal használatával a [biztonsági mentés SAP HANA-adatbázisok Azure virtuális gépeken](./backup-azure-sap-hana-database.md) című témakörben olvashat.

* Az Azure-beli virtuális gépen futó biztonsági másolatok SAP HANA-adatbázisnak a Azure Portal használatával történő kezeléséről az Azure-beli [virtuális gépen található biztonsági másolatok kezelése SAP HANA adatbázisok](./sap-hana-db-manage.md) című témakörben olvashat.
