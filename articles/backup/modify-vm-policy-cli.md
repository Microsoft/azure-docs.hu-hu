---
title: A meglévő virtuális gép biztonsági mentési szabályzatának frissítése a parancssori felület használatával
description: Megtudhatja, hogyan frissítheti a meglévő virtuális gépek biztonsági mentési szabályzatát az Azure CLI használatával.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728578"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>A meglévő virtuális gép biztonsági mentési szabályzatának frissítése a parancssori felület használatával

Az Azure CLI használatával frissítheti a meglévő virtuális gépek biztonsági mentési szabályzatát. Ez a cikk bemutatja, hogyan exportálhatja a meglévő szabályzatot egy JSON-fájlba, módosíthatja a fájlt, majd az Azure CLI használatával frissítheti a szabályzatot a módosított házirenddel.

## <a name="modify-an-existing-policy"></a>Meglévő házirend módosítása

Meglévő virtuális gép biztonsági mentési szabályzatának módosításához kövesse az alábbi lépéseket:

1. Hajtsa végre az az [biztonsági mentési házirend megjelenítése](/cli/azure/backup/policy#az_backup_policy_show) parancsot a frissíteni kívánt szabályzat részleteinek lekéréséhez.

    Példa:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    A fenti példa egy *testing123* nevű virtuálisgép-házirend részleteit jeleníti meg.

    Kimenet:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Mentse a fenti kimenetet egy. JSON-fájlba. Tegyük fel például, hogy *Policy.jsként* menti.
1. Frissítse a JSON-fájlt a követelmények alapján, és mentse a módosításokat.

    Példa: Ha a heti megőrzést 60 napra szeretné frissíteni, frissítse a JSON-fájl következő szakaszát úgy, hogy módosítja a darabszámot a 60 értékre.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Mentse a módosításokat.
1. Hajtsa végre az az [Backup Policy set](/cli/azure/backup/policy#az_backup_policy_set) parancsot, és adja át a frissített JSON-fájl teljes elérési útját a **--Policy** paraméter értékeként.

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>A JSON-szabályzatot a [Get-default-for-VM](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) parancs végrehajtásával is lekérheti.

## <a name="next-steps"></a>Következő lépések

- [Azure-beli virtuális gépek biztonsági másolatainak kezelése a Azure Backup szolgáltatással](backup-azure-manage-vms.md)