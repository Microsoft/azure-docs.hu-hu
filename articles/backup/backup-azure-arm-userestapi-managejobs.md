---
title: Biztonsági mentési feladatok kezelése REST API használatával
description: Ebből a cikkből megtudhatja, hogyan nyomon követheti és kezelheti Azure Backup biztonsági mentési és visszaállítási feladatait REST API használatával.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b234533e-ac51-4482-9452-d97444f98b38
ms.openlocfilehash: ced0e0020fe955734bf6cc767480fbadd6eaffc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88890280"
---
# <a name="track-backup-and-restore-jobs-using-rest-api"></a>Biztonsági mentési és visszaállítási feladatok nyomon követése REST API használatával

Azure Backup szolgáltatás olyan feladatokat indít el, amelyek a háttérben futnak különböző forgatókönyvekben, például a biztonsági mentés elindítása, a visszaállítási műveletek, a biztonsági mentés letiltása. Ezek a feladatok az azonosítóik használatával követhetők nyomon.

## <a name="fetch-job-information-from-operations"></a>Feladatok adatainak beolvasása a műveletből

Egy művelet, például a biztonsági mentés elindítása mindig jobID ad vissza. Például: a [trigger biztonsági mentési REST API műveletének](backup-azure-arm-userestapi-backupazurevms.md#example-responses-for-on-demand-backup) végső válasza a következő:

```http
{
  "id": "cd153561-20d3-467a-b911-cc1de47d4763",
  "name": "cd153561-20d3-467a-b911-cc1de47d4763",
  "status": "Succeeded",
  "startTime": "2018-09-12T02:16:56.7399752Z",
  "endTime": "2018-09-12T02:16:56.7399752Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "41f3e94b-ae6b-4a20-b422-65abfcaf03e5"
  }
}
```

Az Azure virtuális gép biztonsági mentési feladatainak azonosítása a "jobId" mező alapján történik, és az [itt](/rest/api/backup/jobdetails/) leírtak szerint nyomon követhető egy egyszerű *Get* kérelem használatával.

## <a name="tracking-the-job"></a>A feladatok nyomon követése

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

A a `{jobName}` fent említett "jobId". A válasz mindig 200 OK az "állapot" mezővel, amely a feladatok aktuális állapotát jelzi. Ha "befejezett" vagy "CompletedWithWarnings", a "extendedInfo" szakasz a feladattal kapcsolatos további részleteket mutatja be.

### <a name="response"></a>Reagálás

|Név  |Típus  |Description  |
|---------|---------|---------|
|200 OK     | [JobResource](/rest/api/backup/jobdetails/get#jobresource)        | OK        |

#### <a name="example-response"></a>Példaválasz

Miután elküldte a *Get* URI-t, a rendszer egy 200 (ok) választ ad vissza.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-client-request-id: ba4dff71-1655-4c1d-a71f-c9869371b18b; ba4dff71-1655-4c1d-a71f-c9869371b18b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14989
x-ms-correlation-request-id: e9702101-9da2-4681-bdf3-a54e17329a56
x-ms-routing-request-id: SOUTHINDIA:20180521T102317Z:e9702101-9da2-4681-bdf3-a54e17329a56
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:23:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-RecoveryServices-ResourceGroup-centralindia/providers/microsoft.recoveryservices/vaults/abdemovault/backupJobs/7ddead57-bcb9-4269-ac31-6a1b57588700",
  "name": "7ddead57-bcb9-4269-ac31-6a1b57588700",
  "type": "Microsoft.RecoveryServices/vaults/backupJobs",
  "properties": {
    "jobType": "AzureIaaSVMJob",
    "duration": "00:20:23.0896697",
    "actionsInfo": [
      1
    ],
    "virtualMachineVersion": "Compute",
    "extendedInfo": {
      "tasksList": [
        {
          "taskId": "Take Snapshot",
          "duration": "00:00:00",
          "status": "Completed"
        },
        {
          "taskId": "Transfer data to vault",
          "duration": "00:00:00",
          "status": "Completed"
        }
      ],
      "propertyBag": {
        "VM Name": "uttestvmub1",
        "Backup Size": "2332 MB"
      }
    },
    "entityFriendlyName": "uttestvmub1",
    "backupManagementType": "AzureIaasVM",
    "operation": "Backup",
    "status": "Completed",
    "startTime": "2018-05-21T08:35:40.9488967Z",
    "endTime": "2018-05-21T08:56:04.0385664Z",
    "activityId": "7df8e874-1d66-4f81-8e91-da2fe054811d"
  }
}
}

```
