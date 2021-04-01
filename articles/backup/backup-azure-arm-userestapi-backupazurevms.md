---
title: Azure-beli virtuális gépek biztonsági mentése REST API használatával
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja, kezdeményezheti és kezelheti az Azure-beli virtuális gépek biztonsági mentési műveleteit REST API használatával.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92171463"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Azure-beli virtuális gép biztonsági mentése Azure Backup használatával REST API

Ez a cikk bemutatja, hogyan kezelheti az Azure-beli virtuális gépek biztonsági másolatait a Azure Backup segítségével REST API használatával. Először konfigurálja a védelmet egy korábban nem védett Azure-beli virtuális gép számára, indítson el egy igény szerinti biztonsági mentést egy védett Azure-beli virtuális gép számára, és módosítsa a biztonsági másolatok tulajdonságait a REST API az itt leírtak szerint.

Tekintse meg a tár [létrehozása](backup-azure-arm-userestapi-createorupdatevault.md) és a szabályzat [létrehozása](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API oktatóanyagokat az új tárolók és házirendek létrehozásához.

Tegyük fel, hogy egy "testRG" erőforráscsoport alatt lévő "testVM" nevű virtuális gépet szeretne védelemmel ellátni Recovery Services egy "testVaultRG" erőforráscsoport "testVault" csoportjába, az alapértelmezett házirenddel ("DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Nem védett Azure-beli virtuális gép biztonsági mentésének konfigurálása REST API használatával

### <a name="discover-unprotected-azure-vms"></a>Nem védett Azure-beli virtuális gépek felderítése

Először a tárolónak képesnek kell lennie az Azure-beli virtuális gép azonosítására. Ez a [frissítési művelettel](/rest/api/backup/protectioncontainers/refresh)aktiválódik. Ez egy aszinkron *post*  művelet, amely gondoskodik arról, hogy a tároló megkapja az aktuális előfizetésben és a "gyorsítótárban" lévő összes nem védett virtuális gép legújabb listáját. Ha a virtuális gép gyorsítótárazva van, a helyreállítási szolgáltatások hozzáférhetnek majd a virtuális géphez, és megtehetik azt.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

A post URI a,, `{subscriptionId}` `{vaultName}` `{vaultresourceGroupName}` , `{fabricName}` paraméterek. Az az `{fabricName}` "Azure". A példa szerint a `{vaultName}` "testVault" és `{vaultresourceGroupName}` a "testVaultRG". Mivel az összes szükséges paraméter meg van adva az URI-ban, nincs szükség külön kérelem törzsére.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>A frissítési műveletre adott válaszok

A "refresh" művelet egy [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 200 (OK), amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|204 nincs tartalom     |         |  Nem visszaadott tartalommal rendelkező OK      |
|202 elfogadva     |         |     Elfogadva    |

##### <a name="example-responses-to-refresh-operation"></a>Példa a frissítési műveletre

A *post* kérelem elküldése után a rendszer egy 202 (elfogadott) választ ad vissza.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Az eredményül kapott művelet nyomon követése a "location" fejléc használatával egy egyszerű *Get* paranccsal

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Ha az összes Azure-beli virtuális gép fel van derítve, a GET parancs 204 (nincs tartalom) választ ad vissza. A tár mostantól képes felderíteni az előfizetésen belüli virtuális gépeket.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>A megfelelő Azure-beli virtuális gép kiválasztása

 A "gyorsítótárazás" megadásával megerősítheti az előfizetés alatt található [összes védhető elemet](/rest/api/backup/backupprotectableitems/list) , és megkeresheti a kívánt virtuális gépet a válaszban. [A művelet válasza](#example-responses-to-get-operation) információt nyújt arról, hogyan azonosítja Recovery Services a virtuális gépet.  Ha már ismeri a mintát, kihagyhatja ezt a lépést, és közvetlenül folytathatja a [védelem engedélyezését](#enabling-protection-for-the-azure-vm).

Ez a művelet egy *Get* művelet.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

A *Get* URI az összes szükséges paraméterrel rendelkezik. Nincs szükség további kérelem törzsére.

#### <a name="responses-to-get-operation"></a>Válaszok a lekérési műveletre

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Példák a lekérési műveletre

A *Get* kérelem elküldése után a rendszer egy 200 (ok) választ ad vissza.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> A *Get* válaszban lévő értékek száma az "oldal" 200-ra korlátozódik. A következő válaszok URL-címének lekéréséhez használja a "nextLink" mezőt.

A válasz tartalmazza az összes nem védett Azure-beli virtuális gép listáját, és mindegyik `{value}` tartalmazza az Azure Recovery Service által a biztonsági mentés konfigurálásához szükséges összes információt. A biztonsági mentés konfigurálásához jegyezze fel a `{name}` mezőt és a `{virtualMachineId}` mezőt a `{properties}` szakaszban. Két változót hozhat létre ezekből a mezőértékekből az alább leírtak szerint.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "virtuális gép;" + `{name}`
- `{virtualMachineId}`[a kérelem törzsében](#example-request-body) később használatos

A példában a fenti értékek a következőkre fordíthatók le:

- containerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "virtuális gép; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Az Azure-beli virtuális gép védelmének engedélyezése

Ha az érintett virtuális gép "gyorsítótárazva" és "azonosítva", válassza ki a védelemmel ellátni kívánt szabályzatot. A meglévő szabályzatok a tárolóban való megismeréséhez tekintse meg a [házirend-API listázása](/rest/api/backup/backuppolicies/list)című témakört. Ezután válassza ki a [megfelelő szabályzatot](/rest/api/backup/protectionpolicies/get) a szabályzat nevére hivatkozva. A szabályzatok létrehozásával kapcsolatban tekintse meg a [házirend létrehozása oktatóanyagot](backup-azure-arm-userestapi-createorupdatepolicy.md). Az alábbi példában a "DefaultPolicy" lehetőség van kiválasztva.

A védelem engedélyezése egy aszinkron *put* művelet, amely létrehoz egy "védett elemeket".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` és a `{protectedItemName}` fent kiépítve. Az az `{fabricName}` "Azure". A példánkban ez a következőt jelenti:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>A kérelem törzsének létrehozása

Védett elem létrehozásához kövesse a kérelem törzsének összetevőit.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem erőforrás tulajdonságai         |

A kérelem törzsének és egyéb részleteinek teljes listájáért tekintse meg a [védett elem REST API dokumentum létrehozása](/rest/api/backup/protecteditems/createorupdate#request-body)című témakört.

##### <a name="example-request-body"></a>Példa kérelem törzsére

A következő kérelem törzse a védett elemek létrehozásához szükséges tulajdonságokat definiálja.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

A `{sourceResourceId}` `{virtualMachineId}` fent említettek a [lista védhető elemeinek válasza](#example-responses-to-get-operation).

#### <a name="responses-to-create-protected-item-operation"></a>A védett elemek létrehozásához szükséges válaszok

A védett elemek létrehozása egy [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 200 (OK), amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 elfogadva     |         |     Elfogadva    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Példa a védett elemek létrehozásának műveletére

Miután elküldte a *put* kérelmet a védett elemek létrehozásához vagy frissítéséhez, a kezdeti válasz 202 (elfogadva), egy Location fejléctel vagy egy Azure-aszinkron-fejléccel.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután nyomon követheti az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával egy egyszerű *Get* paranccsal.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

A művelet befejezése után a 200 (OK) értéket adja vissza a válasz törzsében található védett elem tartalmával.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Ez megerősíti, hogy a virtuális gép védelmének engedélyezése engedélyezve van, és az első biztonsági mentés a házirend-ütemterv szerint lesz aktiválva.

### <a name="excluding-disks-in-azure-vm-backup"></a>Lemezek kizárása az Azure virtuális gép biztonsági mentésében

A Azure Backup a lemezek egy részhalmazának szelektív biztonsági mentését is lehetővé teszi az Azure-beli virtuális gépen. További részletek [itt](selective-disk-backup-restore.md)találhatók. Ha szelektív biztonsági mentést szeretne készíteni néhány lemezről a védelem engedélyezése során, akkor a védelem engedélyezése során a következő kódrészletnek kell lennie a [kérés törzsének](#example-request-body).

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

A fenti kérelem törzsében a biztonsági mentésre kerülő lemezek listáját a kiterjesztett tulajdonságok szakaszban találja.

|Tulajdonság  |Érték  |
|---------|---------|
|diskLunList     | A lemez LUN listája az *adatlemezek logikai egységeit* tartalmazó lista. **Az operációsrendszer-lemez mindig biztonsági mentés alatt áll, ezért nem kell megemlíteni**.        |
|IsInclusionList     | **Igaz** értéknek kell lennie ahhoz, hogy a logikai egységek szerepeljenek a biztonsági mentés során. Ha **hamis**, a fent említett logikai egységek ki lesznek zárva.         |

Ha tehát a követelmény, hogy csak az operációsrendszer-lemezt készítsen biztonsági másolatot, az _összes_ adatlemezt ki kell zárni. A legegyszerűbben azt mondhatjuk, hogy a rendszer nem tartalmaz adatlemezt. Így a lemez LUN listája üres lesz, a **IsInclusionList** pedig **igaz** lesz. Hasonlóképpen gondoljon arra is, hogy mi a könnyebb kiválasztani egy részhalmazt: néhány lemezt mindig ki kell zárni, vagy néhány lemezt mindig fel kell venni. Válassza ki a LUN-listát és a logikai változó értékét ennek megfelelően.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Igény szerinti biztonsági mentés elindítása egy védett Azure-beli virtuális gépen

Ha egy Azure-beli virtuális gép biztonsági mentésre van konfigurálva, a biztonsági mentések a szabályzat ütemezése szerint történnek. Megvárhatja az első ütemezett biztonsági mentést, vagy bármikor elindíthat egy igény szerinti biztonsági mentést. Az igény szerinti biztonsági mentések megtartása eltér a biztonsági mentési szabályzatok megőrzésének, és egy adott dátumra és időpontra is megadható. Ha nincs megadva, a rendszer azt feltételezi, hogy az igény szerinti biztonsági mentés napjától számított 30 nap.

Az igény szerinti biztonsági mentés aktiválás *utáni* művelet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

A `{containerName}` és a `{protectedItemName}` [fent](#responses-to-get-operation)kiépítve. Az az `{fabricName}` "Azure". A példánkban ez a következőt jelenti:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Kérelem törzsének létrehozása igény szerinti biztonsági mentéshez

Az igény szerinti biztonsági mentés elindításához kövesse a kérelem törzsének összetevőit.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource tulajdonságai         |

A kérelem törzsének és egyéb részleteinek teljes listájáért lásd: a [védett elemek biztonsági mentésének elindítása REST API dokumentum](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Példa kérési törzsre az igény szerinti biztonsági mentéshez

A következő kérelem törzse a védett elemek biztonsági mentésének elindításához szükséges tulajdonságokat határozza meg. Ha nincs megadva az adatmegőrzés, a biztonsági mentési feladathoz tartozó triggertől számított 30 napig megőrzi a rendszer.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Igény szerinti biztonsági mentésre adott válaszok

Az igény szerinti biztonsági mentés indítása [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 200 (OK), amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|202 elfogadva     |         |     Elfogadva    |

#### <a name="example-responses-for-on-demand-backup"></a>Például az igény szerinti biztonsági mentésre adott válaszok

Miután elküldte a *post* -kérést egy igény szerinti biztonsági mentéshez, a kezdeti válasz 202 (elfogadva), egy Location fejlécet vagy egy Azure-aszinkron-fejlécet tartalmaz.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ezután nyomon követheti az eredményül kapott műveletet a Location fejléc vagy a Azure-AsyncOperation fejléc használatával egy egyszerű *Get* paranccsal.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

A művelet befejezése után a 200 (OK) értéket adja vissza az eredményül kapott biztonsági mentési feladatoknak a válasz törzsében.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Mivel a biztonsági mentési feladat hosszú ideig futó művelet, azt a [feladatok figyelése REST API dokumentum használatával](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint kell követni.

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Védett Azure-beli virtuális gép biztonsági mentési konfigurációjának módosítása

### <a name="changing-the-policy-of-protection"></a>A védelem szabályzatának módosítása

A védelemmel ellátott virtuális géppel rendelkező házirend módosításához használhatja ugyanazt a formátumot, mint a [védelem engedélyezése](#enabling-protection-for-the-azure-vm). Csak adja meg az új házirend-azonosítót [a kérelem törzsében](#example-request-body) , és küldje el a kérelmet. Például: Ha módosítani szeretné a testVM szabályzatát a "DefaultPolicy" típusról a "ProdPolicy" értékre, adja meg a "ProdPolicy" azonosítót a kérelem törzsében.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

A válasz a [védelem engedélyezésével](#responses-to-create-protected-item-operation) megegyező formátumot fogja követni.

#### <a name="excluding-disks-during-azure-vm-protection"></a>Lemezek kizárása az Azure VM Protectionben

Ha az Azure-beli virtuális gép biztonsági mentése már megtörtént, megadhatja a biztonsági mentéshez vagy kizáráshoz szükséges lemezek listáját a védelem szabályzatának módosításával. Készítse elő a kérést ugyanúgy, mint a [Lemezek kizárása a védelem engedélyezése során](#excluding-disks-in-azure-vm-backup)

> [!IMPORTANT]
> A fenti kérelem törzse mindig a kizárni vagy belefoglalt adatlemezek végső másolata. Ez a beállítás nem az előző konfigurációhoz van *hozzáadva* . Például: Ha először frissíti a védelmet "az 1. adatlemez kihagyása" beállításnál, majd a "2. adatlemez kizárása" értékkel ismétlődik, a rendszer *csak a 2. adatlemezt zárja ki* a következő biztonsági másolatokban, és az 1. adatlemezt is tartalmazza. Ez mindig a végső lista, melyet a rendszer a következő biztonsági másolatokban tartalmaz/kizár.

A kizárt vagy belefoglalt lemezek aktuális listájának lekéréséhez szerezze be a védett elem adatait az [itt](/rest/api/backup/protecteditems/get)leírtak szerint. A válasz megadja az adatlemez-logikai egységek listáját, és jelzi, hogy azok bekerülnek vagy kizárnak-e.

### <a name="stop-protection-but-retain-existing-data"></a>Védelem leállítása, de meglévő adat megőrzése

Ha el szeretné távolítani a védelmet egy védett virtuális gépen, de megtartja a már biztonsági mentést, távolítsa el a szabályzatot a kérelem törzsében, és küldje el a kérelmet. Ha a társítás a szabályzattal megszűnik, a biztonsági mentések már nem aktiválódnak, és nem jön létre új helyreállítási pont.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

A válasz az [igény szerinti biztonsági mentés elindításához használt](#example-responses-for-on-demand-backup)formátumot fogja követni. Az eredő feladatot nyomon kell követni a [feladatok figyelése REST API dokumentum használatával](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint.

### <a name="stop-protection-and-delete-data"></a>Védelem leállítása és az adattörlés

A védett virtuális gépek védelmének eltávolításához és a biztonsági másolati adatok törléséhez végezze el az [itt](/rest/api/backup/protecteditems/delete)részletezett törlési műveletet.

A védelem leállítása és az adattörlés egy *törlési* művelet.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` és a `{protectedItemName}` [fent](#responses-to-get-operation)kiépítve. `{fabricName}` az "Azure". A példánkban ez a következőt jelenti:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Törlési védelemre adott válaszok

A védelem *törlése* [aszinkron művelet](../azure-resource-manager/management/async-operations.md). Ez azt jelenti, hogy ez a művelet egy másik műveletet hoz létre, amelyet külön kell nyomon követni.

Két választ ad vissza: 202 (elfogadva), ha egy másik művelet jön létre, majd 204 (nincs tartalom), amikor a művelet befejeződik.

|Név  |Típus  |Leírás  |
|---------|---------|---------|
|204 tartalom     |         |  Nincs tartalom       |
|202 elfogadva     |         |     Elfogadva    |

> [!IMPORTANT]
> A véletlen törlési forgatókönyvek elleni védelem érdekében a Recovery Services-tárolóhoz rendelkezésre áll egy, a [törlést biztosító funkció](use-restapi-update-vault-properties.md#soft-delete-state) . Ha a tároló Soft-delete állapota engedélyezve értékre van állítva, akkor a törlési művelet nem fogja azonnal törölni az adatmennyiséget. A rendszer 14 napig őrzi meg, majd véglegesen törli. Ezt a 14 napos időszakot nem terheljük a tárterület. A törlési művelet visszavonásához tekintse meg a [Visszavonás-törlés szakaszt](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Törlés visszavonása

A véletlen törlés visszavonása hasonló a biztonsági mentési elemek létrehozásához. A törlés visszavonása után az elem megmarad, de a rendszer nem indít el jövőbeli biztonsági másolatokat.

A visszavonás törlése egy *put* művelet, amely nagyon hasonlít [a szabályzat módosítására](#changing-the-policy-of-protection) és/vagy [a védelem engedélyezésére](#enabling-protection-for-the-azure-vm). Csak adja meg a szándékot, hogy visszavonja a törlést a *isRehydrate*  változóval [, és küldje](#example-request-body) el a kérelmet. Például: a testVM törlésének visszavonásához a következő kérelem törzsét kell használni.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

A válasz az [igény szerinti biztonsági mentés elindításához használt](#example-responses-for-on-demand-backup)formátumot fogja követni. Az eredő feladatot nyomon kell követni a [feladatok figyelése REST API dokumentum használatával](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)című részben leírtak szerint.

## <a name="next-steps"></a>Következő lépések

[Adatok visszaállítása egy Azure-beli virtuális gép biztonsági másolatából](backup-azure-arm-userestapi-restoreazurevms.md).

A Azure Backup REST API-kkal kapcsolatos további információkért tekintse meg a következő dokumentumokat:

- [Azure Recovery Services-szolgáltató REST API](/rest/api/recoveryservices/)
- [Bevezetés az Azure REST API használatába](/rest/api/azure/)