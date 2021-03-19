---
title: Azure-fájlmegosztás biztonsági mentésének kezelése a REST API-val
description: Megtudhatja, hogyan kezelheti és figyelheti az Azure Backup által készített biztonsági mentést az Azure-fájlmegosztás REST API használatával.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88892847"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Azure-fájlmegosztás biztonsági mentésének kezelése REST API

Ez a cikk bemutatja, hogyan végezheti el a [Azure Backup](./backup-overview.md)által készített biztonsági mentést végző Azure-fájlmegosztás felügyeletére és figyelésére vonatkozó feladatokat.

## <a name="monitor-jobs"></a>Feladatok figyelése

A Azure Backup szolgáltatás a háttérben futó feladatokat indítja el. Ide tartoznak például a biztonsági mentés elindítása, a visszaállítási műveletek és a biztonsági mentés letiltása. Ezek a feladatok az azonosítóik használatával követhetők nyomon.

### <a name="fetch-job-information-from-operations"></a>Feladatok adatainak beolvasása a műveletből

Egy művelet, például a biztonsági mentés elindítása mindig jobID ad vissza a válaszban.

Például egy [trigger biztonsági mentési REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) műveletének utolsó válasza a következő:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Az Azure-fájlmegosztás biztonsági mentési feladatainak azonosítása a **jobId** mező alapján történik, és az [itt](/rest/api/backup/jobdetails/) leírtak szerint nyomon követhető a Get kérelem használatával.

### <a name="tracking-the-job"></a>A feladatok nyomon követése

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

A (z) {jobName} a fent említett "jobId". A válasz mindig "200 OK", a feladatok állapotát jelző **állapot** mezővel. Ha "befejezett" vagy "CompletedWithWarnings", a **extendedInfo** szakasz a feladattal kapcsolatos további részleteket mutatja.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Reagálás

Név  | Típus  |  Description
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Példa válaszra

Miután elküldte a *Get* URI-t, a rendszer egy 200-es választ ad vissza.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Házirend módosítása

A fájlmegosztás által védett házirend módosításához használhatja ugyanazt a formátumot, mint a védelem engedélyezése. Csak adja meg az új házirend-azonosítót a kérelem házirendjében, és küldje el a kérelmet.

Például: Ha módosítani szeretné a *testshare* védelmi házirendjét a *schedule1* -ről a *schedule2*-re, adja meg a *schedule2* azonosítóját a kérelem törzsében.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Védelem leállítása, de meglévő adat megőrzése

Eltávolíthatja a védelmet egy védett fájlmegosztás esetében, de megtarthatja a már biztonsági mentés alatt álló adat védelmét is. Ehhez távolítsa el a szabályzatot a[biztonsági mentés engedélyezéséhez](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) és a kérelem elküldéséhez használt kérelem törzsében. Ha eltávolítja a szabályzattal való társítást, a biztonsági mentések már nem aktiválódnak, és nem jön létre új helyreállítási pont.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Mintaválasz

Egy fájlmegosztás védelmének leállítása aszinkron művelet. A művelet egy másik műveletet hoz létre, amelyet nyomon kell követni. Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 200, amikor a művelet befejeződik.

Válasz fejléce, ha a művelet sikeresen elfogadva:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Ezután kövesse az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával a GET paranccsal:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Választörzs

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Védelem leállítása és az adattörlés

A védett fájlmegosztás védelmének eltávolításához és a biztonsági másolati adatok törléséhez hajtsa végre az [itt](/rest/api/backup/protecteditems/delete)részletezett törlési műveletet.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A ({containerName} és {protectedItemName}) paraméterek [itt](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)vannak beállítva.

A következő példa egy műveletet indít el a *azurefilesvault*-mel védett *testshare* -fájlmegosztás védelmének leállítására.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Válaszok

A védelem törlése aszinkron művelet. A művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.
Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 204 (nincs tartalom), amikor a művelet befejeződik.

## <a name="next-steps"></a>Következő lépések

* Útmutató az [Azure-fájlmegosztás biztonsági mentésének konfigurálása során felmerülő problémák elhárításához](troubleshoot-azure-files.md).
