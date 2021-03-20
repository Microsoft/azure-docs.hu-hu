---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 2474b8920c5387c7896b413f229c2f5b06cdafb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011183"
---
| Tulajdonság | Leírás |
|:--- |:---|
|**idő** | Az az egyezményes világidő (UTC), amikor a kérést a tároló fogadta. Példa: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | A Storage-fiók erőforrás-azonosítója. Például: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**Kategória** | A kért művelet kategóriája. Például: `StorageRead` , `StorageWrite` , vagy `StorageDelete` .|
|**operationName** | A végrehajtott REST-művelet típusa. <br> A műveletek teljes listáját lásd: [Storage Analytics naplózott műveletek és állapotüzenetek témakör](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | A tárolási szolgáltatás azon verziója, amely a kérelem végrehajtásakor lett megadva. Ez egyenértékű az **x-MS-Version** fejléc értékével. Példa: `2017-04-17`.|
|**sémaverzióval** | A napló séma-verziója. Példa: `1.0`.|
|**statusCode** | A kérelem HTTP-állapotkódot. Ha a kérés megszakad, lehet, hogy ez az érték értéke `Unknown` . <br> Például: `206` |
|**statusText** | A kért művelet állapota.  Az állapotüzenetek teljes listáját lásd: [Storage Analytics naplózott műveletek és állapotüzenetek témakör](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Az 2017-04-17-es és újabb verziókban az állapotjelző üzenet `ClientOtherError` nincs használatban. Ehelyett ez a mező hibakódot tartalmaz. Például: `SASSuccess`  |
|**Átl** | A kért művelet végrehajtásához szükséges teljes idő ezredmásodpercben kifejezve. Ide tartozik a beérkező kérelem olvasásának ideje, valamint a válasz küldése a kérelmezőnek. Példa: `12`.|
|**callerIpAddress** | A kérelmező IP-címe, beleértve a portszámot is. Példa: `192.100.0.102:4362`. |
|**correlationId** | A naplók erőforrások közötti összekapcsolásához használt azonosító. Példa: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**helyen** | A Storage-fiók helye. Példa: `North Europe`. |
|**protokoll**|A műveletben használt protokoll. Például: `HTTP` ,, `HTTPS` `SMB` , vagy `NFS`|
| **URI** | A kért egységes erőforrás-azonosító. |