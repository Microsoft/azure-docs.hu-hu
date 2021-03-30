---
title: Azure-fájlmegosztás visszaállítása REST API
description: Megtudhatja, hogyan használhatja a REST APIt az Azure-fájlmegosztás vagy a megadott fájlok visszaállítására Azure Backup által létrehozott visszaállítási pontról
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 60c73caa5db684e38b94b4d5786f2fd24aa65d08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761797"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Azure-fájlmegosztás visszaállítása REST API használatával

Ez a cikk azt ismerteti, hogyan állíthatja vissza a teljes fájlmegosztást vagy adott fájlokat egy [Azure Backup](./backup-overview.md) által létrehozott visszaállítási pontról a REST API használatával.

A cikk végén megtudhatja, hogyan hajthatja végre a következő műveleteket a REST API használatával:

* Egy biztonsági másolattal rendelkező Azure-fájlmegosztás visszaállítási pontjainak megtekintése.
* Teljes Azure-fájlmegosztás visszaállítása.
* Egyéni fájlok vagy mappák visszaállítása.

## <a name="prerequisites"></a>Előfeltételek

Feltételezzük, hogy már rendelkezik a visszaállítani kívánt biztonsági másolattal. Ha nem, tekintse meg a [biztonsági mentés Azure-fájlmegosztás REST API használatával](backup-azure-file-share-rest-api.md) című témakört, és Ismerje meg, hogyan hozhat létre egyet.

Ebben a cikkben a következő erőforrásokat fogjuk használni:

* **RecoveryServicesVault**: *azurefilesvault*
* **Erőforráscsoport**: *azurefiles*
* **Storage-fiók**: *afsaccount*
* **Fájlmegosztás**: *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>ContainerName és ProtectedItemName beolvasása

A visszaállítással kapcsolatos API-hívások többsége esetében a {containerName} és {protectedItemName} URI paraméterek értékeit kell átadnia. Használja az ID attribútumot a [backupprotectableitems beolvasása](/rest/api/backup/protecteditems/get) művelet válasz törzsében a paraméterek értékeinek lekéréséhez. A példában a védelemmel ellátni kívánt fájlmegosztás azonosítója a következő:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Így az értékek a következőképpen fordíthatók le:

* {ContainerName} – *storagecontainer; Storage; azurefiles; afsaccount*
* {protectedItemName} – *azurefileshare; azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Helyreállítási pontok beolvasása az Azure-fájlmegosztás biztonsági mentéséhez

A mentett fájlmegosztás vagy fájlok visszaállításához először válasszon ki egy helyreállítási pontot a visszaállítási művelet végrehajtásához. A biztonsági másolatban szereplő elemek helyreállítási pontjai a [helyreállítási pontok listájának](/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) REST API hívásával is felvehetők. Ez egy GET művelet az összes releváns értékkel.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Állítsa be az URI-értékeket a következőképpen:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {ContainerName}: *storagecontainer; Storage; azurefiles; afsaccount*
* {protectedItemName}: *azurefileshare; azurefiles*
* {ResourceGroupName}: *azurefiles*

A GET URI az összes szükséges paraméterrel rendelkezik. Nincs szükség további kérelem törzsére.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response-for-fetch-recovery-points"></a>Példa a helyreállítási pontok beolvasására

Miután elküldte a GET URI-t, a rendszer egy 200-es választ ad vissza:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

A rendszer a fenti válaszban a (z) {Name} mezővel azonosítja a helyreállítási pontot.

## <a name="full-share-recovery-using-rest-api"></a>Teljes megosztás helyreállítása a REST API használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.
A RESTORE utasítás egy POST-kérelem, amely a [trigger Restore](/rest/api/backup/restores/trigger) REST API használatával hajthatja végre ezt a műveletet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A (z) {containerName} és a (z) {protectedItemName} értékek [itt](#fetch-containername-and-protecteditemname) vannak megadva, a recoveryPointID pedig a fent említett helyreállítási pont {Name} mezője.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Kérelem törzsének létrehozása

Egy Azure-fájlmegosztás visszaállításának elindításához a kérelem törzsének összetevői a következők:

Név |  Típus   |   Description
--- | ---- | ----
Tulajdonságok | AzureFileShareRestoreRequest | RestoreRequestResource tulajdonságai

A kérelem törzsének és egyéb részleteinek teljes listájáért tekintse meg az [trigger Restore REST API dokumentumot](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location"></a>Visszaállítás az eredeti helyre

#### <a name="request-body-example-for-restore-to-original-location"></a>Kérelem törzse – példa az eredeti helyre történő visszaállításra

A következő kérelem törzse definiálja az Azure-fájlmegosztás visszaállításának elindításához szükséges tulajdonságokat:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Visszaállítás másik helyre

A másodlagos hely helyreállításához a következő paramétereket kell megadni:

* **targetresourceid azonosítója**: a Storage-fiók, amelyhez a biztonsági másolat tartalma vissza lesz állítva. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **Name (név**): a célként megadott Storage-fiókon belüli fájlmegosztás, amelyhez a biztonsági másolat tartalma helyreáll.
* **targetFolderPath**: a fájlmegosztás alatt lévő mappa, amelyhez az adat vissza lett állítva.

#### <a name="request-body-example-for-restore-to-alternate-location"></a>Kérelem törzse – példa a visszaállítás másik helyre

A következő kérelem törzse visszaállítja a *azurefiles* fájlmegosztást a *afsaccount* Storage-fiókban a *azurefiles1* -fájlmegosztás számára a *afaccount1* Storage-fiókban.

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Reagálás

A visszaállítási művelet indítása [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.
Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 200 (OK), amikor a művelet befejeződik.

#### <a name="response-example"></a>Példa válaszra

Miután elküldte a *post* URI-t a visszaállítás elindításához, a kezdeti válasz 202 (elfogadva), egy Location fejlécet vagy egy Azure-aszinkron-fejlécet tartalmaz.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Ezután nyomon követheti az eredményül kapott műveletet a Location (hely) fejléc vagy a Azure-AsyncOperation fejléc használatával egy GET paranccsal.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

A művelet befejezése után a 200 (OK) értéket adja vissza a válasz törzsében létrejövő visszaállítási feladatokhoz tartozó AZONOSÍTÓval.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

A másodlagos hely helyreállításához a válasz törzse a következőhöz hasonló lesz:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Mivel a biztonsági mentési feladat hosszú ideig futó művelet, azt a [feladatok figyelése REST API dokumentum használatával](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint kell követni.

## <a name="item-level-recovery-using-rest-api"></a>Elemszintű helyreállítás REST API használatával

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A (z) {containerName} és a (z) {protectedItemName} értékek [itt](#fetch-containername-and-protecteditemname) vannak megadva, a recoveryPointID pedig a fent említett helyreállítási pont {Name} mezője.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body-for-item-level-recovery-using-rest-api"></a>Kérelem törzsének létrehozása elemszintű helyreállításhoz REST API használatával

Egy Azure-fájlmegosztás visszaállításának elindításához a kérelem törzsének összetevői a következők:

Név |  Típus   |   Description
--- | ---- | ----
Tulajdonságok | AzureFileShareRestoreRequest | RestoreRequestResource tulajdonságai

A kérelem törzsének és egyéb részleteinek teljes listájáért tekintse meg az [trigger Restore REST API dokumentumot](/rest/api/backup/restores/trigger#request-body).

### <a name="restore-to-original-location-for-item-level-recovery-using-rest-api"></a>Visszaállítás az eredeti helyre az elemszintű helyreállításhoz REST API használatával

A következő kérelem törzse a *azurefiles* -fájlmegosztás *Restoretest.txt* fájljának visszaállítása a *afsaccount* Storage-fiókban.

Kérelem törzsének létrehozása

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location-for-item-level-recovery-using-rest-api"></a>Az elemszintű helyreállítás másik helyre való visszaállítása REST API használatával

A következő kérelem törzse a *afsaccount* Storage-fiókban található *azurefiles* -fájlmegosztás *Restoretest.txt* fájljának visszaállítása a *afaccount1* Storage-fiókban található *azurefiles1* -fájlmegosztás *restoredata* mappájába.

Kérelem törzsének létrehozása

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

A választ ugyanúgy kell kezelni, mint a fentiekben ismertetett [teljes megosztást](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [kezelheti az Azure file share Backup szolgáltatást a REST API használatával](manage-azure-file-share-rest-api.md).
