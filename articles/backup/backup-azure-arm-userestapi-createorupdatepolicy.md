---
title: Biztonsági mentési szabályzatok létrehozása REST API használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet biztonsági mentési házirendeket a REST API használatával.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89179606"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Azure Recovery Services biztonsági mentési szabályzatok létrehozása REST API használatával

Az Azure Recovery Services-tárolóra vonatkozó biztonsági mentési szabályzat létrehozásához szükséges lépéseket a [szabályzat REST API dokumentum](/rest/api/backup/protectionpolicies/createorupdate)ismerteti. Ezt a dokumentumot hivatkozásként használjuk az Azure virtuális gépek biztonsági mentésére szolgáló szabályzat létrehozásához.

## <a name="create-or-update-a-policy"></a>Szabályzat létrehozása vagy frissítése

Azure Backup szabályzat létrehozásához vagy frissítéséhez használja a következő *put* műveletet

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

A `{policyName}` és az `{vaultName}` URI-ban van megadva. A kérés törzsében további információk is megtalálhatók.

## <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

Ha például az Azure virtuális gép biztonsági mentésére vonatkozó szabályzatot szeretne létrehozni, az alábbiakban a kérelem törzsének összetevői láthatók.

|Name  |Kötelező  |Típus  |Description  |
|---------|---------|---------|---------|
|properties     |   Igaz      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource tulajdonságai        |
|tags     |         | Objektum        |  Erőforráscímkék       |

A kérelem törzsében található definíciók teljes listájáért tekintse meg a [biztonsági mentési szabályzat REST API dokumentumát](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Példa kérelem törzsére

A következő kérelem törzse az Azure-beli virtuális gépek biztonsági másolatainak biztonsági mentési szabályzatát határozza meg.

A szabályzat szerint:

- Készítsen heti biztonsági mentést hétfőn, szerdán, csütörtökön, 10:00-kor, a csendes-óceáni téli idő szerint.
- A biztonsági másolatok megtartása minden hétfőn, szerdán, csütörtökön, egy hétre.
- A biztonsági másolatok megtartása a hónap első szerdán és harmadik csütörtökön, két hónapig (felülbírálja az előző megőrzési feltételeket, ha van ilyen).
- A negyedik hétfőn és novemberben tartott biztonsági másolatok megtartása négy évig (amely felülbírálja a korábbi megőrzési feltételeket, ha vannak ilyenek).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Az ütemterv és a megőrzési idő formátuma csak a DateTime formátumot támogatja. Nem támogatják az időformátumot.

## <a name="responses"></a>Válaszok

A biztonsági mentési szabályzat létrehozása/frissítése [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva) egy másik művelet létrehozásakor, majd 200 (OK), amikor a művelet befejeződik.

|Név  |Típus  |Description  |
|---------|---------|---------|
|200 OK     |    [Védelem PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 elfogadva     |         |     Elfogadva    |

### <a name="example-responses"></a>Válaszok – példa

Miután elküldte a *put* kérelmet a szabályzat létrehozásához vagy frissítéséhez, a kezdeti válasz 202 (elfogadva), egy Location fejléctel vagy egy Azure-aszinkron-fejléccel.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután nyomon követheti az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával egy egyszerű *Get* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

A művelet befejezése után a 200 (OK) értéket adja vissza a válasz törzsében lévő szabályzat tartalmával.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Ha egy házirend már használatban van egy elem védelmére, a házirendben szereplő összes frissítés az összes kapcsolódó elem [védelmét is módosítja](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) .

## <a name="next-steps"></a>Következő lépések

[Védelem engedélyezése a nem védett Azure](backup-azure-arm-userestapi-backupazurevms.md)-beli virtuális gépek számára.

A Azure Backup REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)
