---
title: Event Grid aszinkron műveletek állapota
description: Útmutatás Event Grid aszinkron műveletek nyomon követéséhez az Azure-ban. Megjeleníti a hosszan futó művelet állapotának lekéréséhez használt értékeket.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: baae7b097a0b696d405c0e7ea3d3bdeb326f23b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011684"
---
# <a name="track-event-grid-asynchronous-azure-operations"></a>Az aszinkron Azure-műveletek nyomon követése Event Grid
Bizonyos Azure REST-műveletek aszinkron módon futnak, mert a művelet nem hajtható végre gyorsan. Ez a cikk bemutatja, hogyan követheti nyomon az aszinkron műveletek állapotát a válaszban visszaadott értékek alapján.  

## <a name="status-codes-for-asynchronous-operations"></a>Az aszinkron műveletekhez tartozó állapotkódok
Egy aszinkron művelet először a HTTP-állapotkódot adja vissza a következők közül:

* 201 (létrehozva)
* 202 (elfogadva) 

A művelet sikeres befejezését követően a a következők egyikét adja vissza:

* 200 (OK)
* 204 (nincs tartalom) 

Tekintse meg az [REST API dokumentációját](/rest/api/) , hogy láthassa a végrehajtandó műveletre adott válaszokat.

## <a name="monitor-status-of-operation"></a>A művelet állapotának figyelése
Az aszinkron REST-műveletek adja vissza a fejléc értékeit, amelyek segítségével meghatározhatja a művelet állapotát. A megvizsgálható három fejléc-érték is lehetséges:

* `Azure-AsyncOperation` -URL a művelet folyamatos állapotának ellenőrzéséhez. Ha a művelet visszaadja ezt az értéket, mindig használja (hely helyett) a művelet állapotának nyomon követéséhez.
* `Location` -A művelet befejezését meghatározó URL-cím. Ezt az értéket csak akkor használja, ha a Azure-AsyncOperation nem adja vissza.
* `Retry-After` – Az aszinkron művelet állapotának ellenőrzése előtt megvárni kívánt másodpercek száma.

Azonban nem minden aszinkron művelet adja vissza ezeket az értékeket. Előfordulhat például, hogy ki kell értékelnie egy művelet Azure-AsyncOperation fejlécének értékét és egy másik művelet Helykód értékét. 

A fejléc értékeinek lekérése a kérelem fejlécének bármely értékének lekérése esetén. A C# nyelvben például lekéri a fejléc értékét egy `HttpWebResponse` nevű objektumból `response` a következő kóddal:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation kérelem és válasz

Az aszinkron művelet állapotának lekéréséhez küldjön egy GET kérelmet az URL-címre Azure-AsyncOperation fejléc értékében.

A művelettől kapott válasz törzse információkat tartalmaz a műveletről. A következő példa a művelet által visszaadott lehetséges értékeket mutatja:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

`status`A rendszer csak az összes válasz eredményét adja vissza. A rendszer a hiba objektumot adja vissza, ha az állapot meghiúsul vagy meg lett szakítva. Az összes többi érték nem kötelező; Ezért a kapott válasz a példától eltérő lehet.

## <a name="provisioningstate-values"></a>provisioningState-értékek

Az erőforrások létrehozására, frissítésére vagy törlésére (PUT, javítás, törlés) vonatkozó műveletek általában értéket adnak vissza `provisioningState` . Egy művelet befejezését követően a következő három érték egyikét adja vissza: 

* Sikeres
* Sikertelen
* Megszakítva

Az összes többi érték azt jelzi, hogy a művelet még fut. Az erőforrás-szolgáltató egy testreszabott értéket adhat vissza, amely jelzi az állapotát. Előfordulhat például, hogy **fogadja el** a kérés fogadása és futtatása esetén elfogadott értesítést.


## <a name="example-requests-and-responses"></a>Példa kérelmekre és válaszokra

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuális gép indítása (202 Azure-AsyncOperation)
Ebből a példából megtudhatja, hogyan határozhatja meg a virtuális gépek **indítási** műveletének állapotát. A kezdeti kérelem formátuma a következő:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

A válasz törzse a művelet állapotát tartalmazza:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Erőforrások üzembe helyezése (201 Azure-AsyncOperation)

Ebből a példából megtudhatja, hogyan határozhatja meg a **központi telepítések** állapotát az erőforrások Azure-ba történő telepítéséhez. A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

A 201 állapotkódot adja vissza. A válasz törzse a következőket tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

A válasz törzse a művelet állapotát tartalmazza:

```json
{"status":"Running"}
```

Ha a telepítés befejeződött, a válasz a következőket tartalmazza:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Storage-fiók létrehozása (202, hely és újrapróbálkozás után)

Ebből a példából megtudhatja, hogyan határozhatja meg a **létrehozási** művelet állapotát a Storage-fiókok esetében. A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

A kérés törzse a Storage-fiók tulajdonságait tartalmazza:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő két érték látható:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Az újrapróbálkozást követően megadott másodpercek számának várakozása után az aszinkron művelet állapotának ellenőrzéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Ha a kérelem még fut, a 202 állapotkódot kapja meg. Ha a kérelem befejeződött, a kapott állapotkód 200, a válasz törzse pedig a létrehozott Storage-fiók tulajdonságait tartalmazza.

## <a name="next-steps"></a>Következő lépések

* Az egyes REST-műveletekkel kapcsolatos dokumentációért lásd: [REST API dokumentáció](/rest/api/).
* A sablonok Resource Manager-REST API használatával történő telepítésével kapcsolatos információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../azure-resource-manager/templates/deploy-rest.md).