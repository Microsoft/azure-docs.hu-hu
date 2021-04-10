---
title: Aszinkron műveletek állapota
description: Útmutatás az aszinkron műveletek nyomon követéséhez az Azure-ban. Megjeleníti a hosszan futó művelet állapotának lekéréséhez használt értékeket.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: f5823bc8f6c0d5b334a638d4ad350eb2f9ef2b28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932743"
---
# <a name="track-asynchronous-azure-operations"></a>Aszinkron Azure-műveletek nyomon követése

Bizonyos Azure REST-műveletek aszinkron módon futnak, mert a művelet nem hajtható végre gyorsan. Ez a cikk bemutatja, hogyan követheti nyomon az aszinkron műveletek állapotát a válaszban visszaadott értékek alapján.  

## <a name="status-codes-for-asynchronous-operations"></a>Az aszinkron műveletekhez tartozó állapotkódok

Egy aszinkron művelet először a HTTP-állapotkódot adja vissza a következők közül:

* 201 (létrehozva)
* 202 (elfogadva)

A művelet sikeres befejezését követően a a következők egyikét adja vissza:

* 200 (OK)
* 204 (nincs tartalom)

Tekintse meg az [REST API dokumentációját](/rest/api/azure/) , hogy láthassa a végrehajtandó műveletre adott válaszokat.

Az 201-es vagy a 202-es válasz kódjának beolvasását követően készen áll a művelet állapotának figyelésére.

## <a name="url-to-monitor-status"></a>Az állapot figyelésének URL-címe

Az aszinkron művelet állapotának figyelésére két különböző módszer áll rendelkezésre. A helyes megközelítést úgy állapíthatja meg, hogy megvizsgálja az eredeti kérelemből visszaadott fejléc-értékeket. Először keresse meg a következőt:

* `Azure-AsyncOperation` -URL a művelet folyamatos állapotának ellenőrzéséhez. Ha a művelet visszaadja ezt az értéket, használja a művelet állapotának nyomon követésére.
* `Retry-After` – Az aszinkron művelet állapotának ellenőrzése előtt megvárni kívánt másodpercek száma.

Ha `Azure-AsyncOperation` nem a fejléc értékeinek egyike, akkor keresse meg a következőt:

* `Location` -A művelet befejezését meghatározó URL-cím. Csak akkor használja ezt az értéket, ha a Azure-AsyncOperation nem adja vissza.
* `Retry-After` – Az aszinkron művelet állapotának ellenőrzése előtt megvárni kívánt másodpercek száma.

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation kérelem és válasz

Ha a fejléc értéke URL-címmel rendelkezik `Azure-AsyncOperation` , küldjön egy Get kérelmet erre az URL-címre. A értékének használatával `Retry-After` ütemezze, hogy az állapot milyen gyakran legyen ellenőrizhető. A művelet állapotát jelző Response objektum jelenik meg. A művelet állapotának az URL-címmel való ellenőrzésekor a rendszer másik választ ad vissza `Location` . A hely URL-címére adott válaszról további információt a [Storage-fiók létrehozása (202, hely és újrapróbálkozás után)](#create-storage-account-202-with-location-and-retry-after)című témakörben talál.

A válasz tulajdonságai eltérőek lehetnek, de mindig tartalmazzák az aszinkron művelet állapotát.

```json
{
    "status": "{status-value}"
}
```

Az alábbi példa a művelet által visszaadott egyéb értékeket mutatja be:

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

A rendszer a hiba objektumot adja vissza, ha az állapot meghiúsul vagy meg lett szakítva. Az összes többi érték megadása nem kötelező. A kapott válasz a példától eltérő lehet.

## <a name="provisioningstate-values"></a>provisioningState-értékek

Az erőforrások létrehozására, frissítésére vagy törlésére (PUT, javítás, törlés) vonatkozó műveletek általában értéket adnak vissza `provisioningState` . Egy művelet befejezését követően a következő három érték egyikét adja vissza:

* Sikeres
* Sikertelen
* Megszakítva

Az összes többi érték azt jelzi, hogy a művelet még fut. Az erőforrás-szolgáltató egy testreszabott értéket adhat vissza, amely jelzi az állapotát. Előfordulhat például, hogy **fogadja el** a kérés fogadása és futtatása esetén elfogadott értesítést.

## <a name="example-requests-and-responses"></a>Példa kérelmekre és válaszokra

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuális gép indítása (202 Azure-AsyncOperation)

Ebből a példából megtudhatja, hogyan határozhatja meg a [virtuális gépek indítási műveletének](/rest/api/compute/virtualmachines/start)állapotát. A kezdeti kérelem formátuma a következő:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
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

Ebből a példából megtudhatja, hogyan határozhatja meg a [központi telepítések](/rest/api/resources/resources/deployments/createorupdate) állapotát az erőforrások Azure-ba történő telepítéséhez. A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

A 201 állapotkódot adja vissza. A válasz törzse a következőket tartalmazza:

```json
"provisioningState":"Accepted",
```

A fejléc értékei között a következő jelenik meg:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Az aszinkron művelet állapotának megtekintéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

A válasz törzse a művelet állapotát tartalmazza:

```json
{
    "status": "Running"
}
```

Ha a telepítés befejeződött, a válasz a következőket tartalmazza:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Storage-fiók létrehozása (202, hely és újrapróbálkozás után)

Ebből a példából megtudhatja, hogyan határozhatja meg a [létrehozási művelet állapotát a Storage-fiókok esetében](/rest/api/storagerp/storageaccounts/create). A kezdeti kérelem formátuma a következő:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

A kérés törzse a Storage-fiók tulajdonságait tartalmazza:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

A 202 állapotkódot adja vissza. A fejléc értékei között a következő két érték látható:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Az újrapróbálkozást követően megadott másodpercek számának várakozása után az aszinkron művelet állapotának ellenőrzéséhez küldjön egy másik kérelmet erre az URL-címre.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Ha a kérelem még fut, a 202 állapotkódot kapja meg. Ha a kérelem befejeződött, a kapott állapotkód 200, a válasz törzse pedig a létrehozott Storage-fiók tulajdonságait tartalmazza.

## <a name="next-steps"></a>Következő lépések

* Az egyes REST-műveletekkel kapcsolatos dokumentációért lásd: [REST API dokumentáció](/rest/api/azure/).
* A sablonok Resource Manager-REST API használatával történő telepítésével kapcsolatos információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../templates/deploy-rest.md).