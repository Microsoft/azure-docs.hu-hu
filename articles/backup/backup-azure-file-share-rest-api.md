---
title: Azure-fájlmegosztás biztonsági mentése a REST API
description: Ismerje meg, hogyan használhatja a REST API az Azure-fájlmegosztás biztonsági mentésére az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88824865"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Azure-fájlmegosztás biztonsági mentése a Azure Backup használatával REST API-n keresztül

Ez a cikk azt ismerteti, hogyan készíthet biztonsági mentést egy Azure-fájlmegosztás Azure Backup használatával REST API segítségével.

Ez a cikk azt feltételezi, hogy már létrehozott egy Recovery Services-tárolót és házirendet a fájlmegosztás biztonsági mentésének konfigurálásához. Ha még nem tette meg, tekintse meg a [create vaultot](./backup-azure-arm-userestapi-createorupdatevault.md) , és [hozzon létre házirendet](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST API oktatóanyagokat új tárolók és házirendek létrehozásához.

Ebben a cikkben a következő erőforrásokat fogjuk használni:

- **RecoveryServicesVault**: *azurefilesvault*

- **Házirend:** *schedule1*

- **Erőforráscsoport**: *azurefiles*

- **Storage-fiók**: *testvault2*

- **Fájlmegosztás**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Nem védett Azure-fájlmegosztás biztonsági mentésének konfigurálása REST API használatával

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Nem védett Azure-fájlmegosztás esetén a Storage-fiókok felderítése

A tárolónak fel kell derítenie az előfizetés összes olyan Azure Storage-fiókját, amelyről biztonsági másolatot készíthet a Recovery Services tárolóba. Ez a [frissítési művelettel](/rest/api/backup/protectioncontainers/refresh)aktiválódik. Ez egy aszinkron *post* művelet, amely biztosítja, hogy a tároló megkapja az aktuális előfizetésben és a "gyorsítótárban" található összes nem védett Azure-fájlmegosztás legújabb listáját. Miután a fájlmegosztás gyorsítótárazva van, a helyreállítási szolgáltatások hozzáférhetnek a fájlmegosztáshoz, és megoszthatják azt.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

A post URI a,, `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` és `{fabricName}` paraméterekkel rendelkezik. A példánkban a különböző paraméterek értéke a következő lesz:

- `{fabricName}` az *Azure*

- `{vaultName}`*azurefilesvault*

- `{vaultresourceGroupName}`*azurefiles*

- $filter = backupManagementType EQ "AzureStorage"

Mivel az összes kötelező paraméter meg van adva az URI-ban, nincs szükség külön kérelem törzsére.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>A frissítési műveletre adott válaszok

A "refresh" művelet egy [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 200 (OK), amikor a művelet befejeződik.

##### <a name="example-responses-to-the-refresh-operation"></a>A frissítési műveletre adott válaszok – példa

A *post* kérelem elküldése után a rendszer egy 202 (elfogadott) választ ad vissza.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Az eredményül kapott művelet nyomon követése a "location" fejléc használatával egy egyszerű *Get* paranccsal

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Miután az összes Azure Storage-fiókot felderíti, a GET parancs 200 (nincs tartalom) választ ad vissza. A tár mostantól képes felderíteni az előfizetésen belül biztonsági mentésre alkalmas fájlmegosztás-fiókokat.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Azon Storage-fiókok listájának beolvasása, amelyekről biztonsági másolat készíthető Recovery Services-tárolóval

Annak ellenőrzéséhez, hogy a "gyorsítótárazás" megtörtént-e, sorolja fel az előfizetésben található összes olyan fájlmegosztást, amelyekről biztonsági másolat készíthető a Recovery Services-tárolóval. Ezután keresse meg a kívánt Storage-fiókot a válaszban. Ez a [ProtectableContainers lekérése](/rest/api/backup/protectablecontainers/list) művelettel történik.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

A *Get* URI az összes szükséges paraméterrel rendelkezik. Nincs szükség további kérelem törzsére.

Példa a válasz törzsére:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Mivel a rövid névvel megkeresheti a *testvault2* Storage-fiókot a válasz törzsében, a fentiekben végrehajtott frissítési művelet sikeres volt. A Recovery Services-tároló mostantól képes sikeresen felderíteni a nem védett fájlokkal rendelkező Storage-fiókokat ugyanabban az előfizetésben.

### <a name="register-storage-account-with-recovery-services-vault"></a>Storage-fiók regisztrálása a Recovery Services-tárolóval

Erre a lépésre csak akkor van szükség, ha korábban nem regisztrálta a Storage-fiókot a tárolóban. A tárolót a [ProtectionContainers-regisztráció művelettel](/rest/api/backup/protectioncontainers/register)lehet regisztrálni.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Állítsa be az URI változóit a következőképpen:

- {resourceGroupName} – *azurefiles*
- {fabricName} – *Azure*
- {vaultName} – *azurefilesvault*
- {containerName} – ez a Name attribútum a GET ProtectableContainers művelet válasz törzsében.
   A példánkban ez a *StorageContainer; Storage AzureFiles; testvault2*

>[!NOTE]
> Mindig adja meg a válasz Name (név) attribútumát, és töltse ki a kérelemben. Ne adjon meg merevlemezt, vagy hozza létre a tároló nevének formátumát. Ha létrehoz vagy feldolgoz egy kódot, az API-hívás sikertelen lesz, ha a tároló-név formátuma később megváltozik.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

A kérelem létrehozása törzs a következő:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

A kérelem törzsének és egyéb részleteinek teljes listájáért tekintse meg a [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)című témakört.

Ez egy aszinkron művelet, és visszaadja a következő két választ: "202 elfogadva", ha a művelet el van fogadva, és "200 OK", amikor a művelet befejeződött.  A művelet állapotának nyomon követéséhez használja a Location (hely) fejlécet a művelet legutóbbi állapotának lekéréséhez.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Példa a válasz törzsére a művelet befejezésekor:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

A válasz törzsében ellenőrizheti, hogy a regisztráció sikeres volt-e a *registrationstatus* paraméter értéke alapján. Esetünkben a *testvault2* regisztrált állapota jelenik meg, így a regisztrálási művelet sikeres volt.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Az összes nem védett fájl megosztásának lekérdezése egy Storage-fiókban

A Protected items ( [védelmi tárolók – lekérdezési](/rest/api/backup/protectioncontainers/inquire) művelet) használatával megtekintheti a Storage-fiókok védhető elemeit. Ez egy aszinkron művelet, és az eredményeket a Location fejléc használatával kell követni.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Állítsa be a fenti URI változóit a következőképpen:

- {vaultName} – *azurefilesvault*
- {fabricName} – *Azure*
- {containerName} – a ProtectableContainers beolvasása művelet válasz törzsében található Name attribútumra hivatkozik. A példánkban ez a *StorageContainer; Storage AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Ha a kérelem sikeres, az "OK" állapotkódot adja vissza.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Válassza ki azt a fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni

Az előfizetéshez tartozó összes védhető elemet listázhatja, és megkeresheti a kívánt fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni a [backupprotectableItems beolvasása](/rest/api/backup/backupprotectableitems/list) művelet használatával.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Hozza létre az URI-t a következőképpen:

- {vaultName} – *azurefilesvault*
- {$filter} – *backupManagementType EQ "AzureStorage"*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Példa a válaszra:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

A válasz tartalmazza az összes nem védett fájlmegosztás listáját, és az Azure Recovery szolgáltatás által a biztonsági mentés konfigurálásához szükséges összes információt tartalmazza.

### <a name="enable-backup-for-the-file-share"></a>A fájlmegosztás biztonsági mentésének engedélyezése

Ha a megfelelő fájlmegosztás "azonosítva" a rövid névvel, válassza ki a védelemmel ellátni kívánt szabályzatot. A meglévő szabályzatok a tárolóban való megismeréséhez tekintse meg a [házirend-API listázása](/rest/api/backup/backuppolicies/list)című témakört. Ezután válassza ki a [megfelelő szabályzatot](/rest/api/backup/protectionpolicies/get) a szabályzat nevére hivatkozva. A szabályzatok létrehozásával kapcsolatban tekintse meg a [házirend létrehozása oktatóanyagot](./backup-azure-arm-userestapi-createorupdatepolicy.md).

A védelem engedélyezése egy aszinkron *put* művelet, amely létrehoz egy "védett elemeket".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Állítsa be a **ContainerName** és a **protecteditemname** változót a Get backupprotectableitems művelet válasz törzsében található ID attribútum használatával.

A példában a védelemmel ellátni kívánt fájlmegosztás azonosítója a következő:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {ContainerName} – *storagecontainer; Storage; azurefiles; testvault2*
- {protectedItemName} – *azurefileshare; testshare*

Vagy a védelmi tároló és a védhető elemek válaszának **Name (név** ) attribútumára is hivatkozhat.

>[!NOTE]
>Mindig adja meg a válasz Name (név) attribútumát, és töltse ki a kérelemben. Ne Hard-Code vagy hozzon létre egy tároló-név formátumot vagy a védett elemnév formátumát. Ha létrehoz vagy rögzített kódot, az API-hívás sikertelen lesz, ha a tároló-név formátuma vagy a védett elem nevének formátuma megváltozik a jövőben.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Kérelem törzsének létrehozása:

A következő kérelem törzse a védett elemek létrehozásához szükséges tulajdonságokat definiálja.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

A **sourceresourceid azonosítónak** a Get backupprotectableItems **parentcontainerFabricID** válasza.

Mintaválasz

A védett elem létrehozása egy aszinkron művelet, amely egy másik műveletet hoz létre, amelyet nyomon kell követni. Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 200 (OK), amikor a művelet befejeződik.

Miután elküldte a *put* kérelmet a védett elemek létrehozásához vagy frissítéséhez, a kezdeti válasz 202 (elfogadva), a hely fejléce.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Ezután kövesse az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával egy  *Get* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

A művelet befejezése után a 200 (OK) értéket adja vissza a válasz törzsében található védett elem tartalmával.

Példa a válasz törzsére:

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

Ez megerősíti, hogy engedélyezve van a fájlmegosztás védelme, és az első biztonsági mentés a házirend-ütemtervnek megfelelően aktiválódik.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Igény szerinti biztonsági mentés indítása a fájlmegosztás számára

Ha egy Azure-fájlmegosztás biztonsági mentésre van konfigurálva, a biztonsági mentések a szabályzat ütemezése szerint futnak. Megvárhatja az első ütemezett biztonsági mentést, vagy bármikor elindíthat egy igény szerinti biztonsági mentést.

Az igény szerinti biztonsági mentés aktiválás utáni művelet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

a biztonsági mentés engedélyezése során a ({containerName} és a {protectedItemName}) a fentiek szerint lett kiépítve. A példánkban ez a következőt jelenti:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Kérelem törzsének létrehozása

Az igény szerinti biztonsági mentés elindításához kövesse a kérelem törzsének összetevőit.

| Név       | Típus                       | Description                       |
| ---------- | -------------------------- | --------------------------------- |
| Tulajdonságok | AzurefilesharebackupReques | BackupRequestResource tulajdonságai |

A kérelem törzsének és egyéb részleteinek teljes listájáért lásd: a [védett elemek biztonsági mentésének elindítása REST API dokumentum](/rest/api/backup/backups/trigger#request-body).

Példa a kérelem szövegtörzsére

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Az igény szerinti biztonsági mentési műveletre adott válaszok

Az igény szerinti biztonsági mentés indítása [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, és 200 (OK), amikor a művelet befejeződik.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Például az igény szerinti biztonsági mentési műveletre adott válaszok

Miután elküldte a *post* -kérést egy igény szerinti biztonsági mentéshez, a kezdeti válasz 202 (elfogadva), egy Location fejlécet vagy egy Azure-aszinkron-fejlécet tartalmaz.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Ezután kövesse az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával egy  *Get* paranccsal.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

A művelet befejezése után a 200 (OK) értéket adja vissza az eredményül kapott biztonsági mentési feladatoknak a válasz törzsében.

#### <a name="sample-response-body"></a>Példa a válasz törzsére

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Mivel a biztonsági mentési feladat hosszú ideig futó művelet, azt a [feladatok figyelése REST API dokumentum használatával](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint kell követni.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [állíthatja vissza az Azure-fájlmegosztást a REST API használatával](restore-azure-file-share-rest-api.md).
