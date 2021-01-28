---
title: Entitások szűrése, rendezése és lapozása
description: Ismerje meg Azure Media Services v3 entitások szűrését, rendezését és lapozását.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 19febc833f087afdfecde3274a1044598d082521
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955309"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Media Services entitások szűrése, rendezése és lapozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a témakör a OData-lekérdezési lehetőségeket és a tördelési támogatást ismerteti Azure Media Services v3 entitások listázásakor.

## <a name="considerations"></a>Megfontolandó szempontok

* A típusú entitások tulajdonságai `Datetime` mindig UTC formátumban jelennek meg.
* A lekérdezési karakterláncban található üres területnek URL-kódolással kell rendelkeznie, mielőtt elküld egy kérést.

## <a name="comparison-operators"></a>Összehasonlító operátorok

A következő operátorok segítségével hasonlíthatja össze a mezőket egy állandó értékkel:

Esélyegyenlőségi operátorok:

- `eq`: Megvizsgálhatja, hogy egy mező *egyenlő-* e az állandó értékkel.
- `ne`: Ellenőrizze, hogy egy mező *nem egyenlő-* e állandó értékkel.

Tartomány operátorai:

- `gt`: Annak tesztelése, hogy egy mező nagyobb-e, *mint* egy konstans érték.
- `lt`: Ellenőrizze, hogy a mező értéke kisebb-e, *mint* egy konstans érték.
- `ge`: Ellenőrizze, hogy a mező értéke *nagyobb-e, vagy egyenlő-* e az állandó értékkel.
- `le`: Megvizsgálhatja, hogy egy mező *kisebb vagy egyenlő-e, mint* egy konstans érték.

## <a name="filter"></a>Szűrő

A használatával `$filter` OData szűrő paramétert adhat meg, amely csak azokat az objektumokat keresi meg, amelyekre kíváncsi.

A következő REST-példa szűrők `alternateId` egy eszköz értékén:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

A következő C#-példa az eszköz létrehozási dátumához tartozó szűrőket:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Rendezési sorrend

Ezzel a paranccsal `$orderby` rendezheti a visszaadott objektumokat a megadott paraméterrel. Például:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Az eredmények növekvő vagy csökkenő sorrendbe rendezéséhez fűzze hozzá a `asc` mezőt vagy a `desc` nevet a mezőhöz szóközzel elválasztva. Példa: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token kihagyása

Ha egy lekérdezés válasza sok elemet tartalmaz, a szolgáltatás a `$skiptoken` `@odata.nextLink` következő oldalának beolvasásához használt () értéket adja vissza. A teljes eredményhalmaz használatával használhatja a lapot.

A Media Services V3 esetében nem konfigurálható az oldalméret. Az oldalméret az entitás típusától függően változik. Olvassa el a részleteket követő egyes szakaszokat.

Ha entitásokat hoznak létre vagy törölnek a gyűjteményen belüli lapozás során, a módosítások a visszaadott eredményekben jelennek meg (ha ezek a módosítások a gyűjtemény nem letöltött részét képezik).

> [!TIP]
> Mindig használja `nextLink` a gyűjtemény enumerálására, és nem függ egy adott oldalméret méretétől.
>
> Az `nextLink` érték csak akkor jelenik meg, ha az entitások több oldala is van.

Vegye figyelembe az alábbi példát, ahol a `$skiptoken` használatban van. Ügyeljen rá, hogy a *amstestaccount* cserélje le a fiók nevére, és állítsa be az *API-Version* értéket a legújabb verzióra.

Ha a következőhöz hasonló eszközök listáját kéri:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Ehhez hasonló választ fog kapni:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Ezután a következő lapot kell kérnie a Get kérelem elküldésével:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

A következő C#-példa azt szemlélteti, hogyan lehet enumerálni a fiókban lévő összes streaming-lokátoron.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>A logikai operátorok használata a lekérdezési beállítások egyesítéséhez

Media Services v3 támogatja a **vagy** a és **a és a** logikai operátorokat. 

A következő REST-példa ellenőrzi a feladatok állapotát:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

A következőhöz hasonló lekérdezést kell létrehoznia a C#-ban: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Entitások szűrése és rendezési lehetőségei

A következő táblázat bemutatja, hogyan alkalmazhatja a szűrési és a rendezési beállításokat különböző entitásokra:

|Entitás neve|Tulajdonság neve|Szűrő|Sorrend|
|---|---|---|---|
|[Adategységek](/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` és `desc`|
||Properties. alternateId |`eq`||
||Properties. assetId |`eq`||
||tulajdonságok. létrehozva| `eq`, `gt`, `lt`| `asc` és `desc`|
|[Tartalomkulcsszabályok](/rest/api/media/contentkeypolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. Leírás    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Properties. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||Properties. policyId|`eq`, `ne`||
|[Feladatok](/rest/api/media/jobs)| name  | `eq`            | `asc` és `desc`|
||tulajdonságok. állapot        | `eq`, `ne`        |                         |
||tulajdonságok. létrehozva      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
||Properties. lastModified | `gt`, `ge`, `lt`, `le` | `asc` és `desc`| 
|[Streameléskeresők](/rest/api/media/streaminglocators)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. végső Befejezés    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Streamelési szabályzatok](/rest/api/media/streamingpolicies)|name|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Átalakítások](/rest/api/media/transforms)| name | `eq`            | `asc` és `desc`|
|| tulajdonságok. létrehozva      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
|| Properties. lastModified | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|

## <a name="next-steps"></a>Következő lépések

* [Eszközök listázása](/rest/api/media/assets/list)
* [A tartalmi kulcs házirendjeinek listázása](/rest/api/media/contentkeypolicies/list)
* [Feladatok listázása](/rest/api/media/jobs/list)
* [Folyamatos átviteli szabályzatok listázása](/rest/api/media/streamingpolicies/list)
* [Streaming-lokátorok listázása](/rest/api/media/streaminglocators/list)
* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Kvóták és korlátok](limits-quotas-constraints.md)
