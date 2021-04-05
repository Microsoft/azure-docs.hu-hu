---
title: Azure Maps Long-Running Operation API
description: Tudnivalók a hosszú ideig futó aszinkron feldolgozásról Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906199"
---
# <a name="creator-preview-long-running-operation-api"></a>Creator (előzetes verzió) Long-Running Operation API

> [!IMPORTANT]
> A Azure Maps Creator Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Maps egyes API-k [aszinkron Request-Reply mintát](/azure/architecture/patterns/async-request-reply)használnak. Ez a minta lehetővé teszi, hogy a Azure Maps a rendelkezésre álló és a rugalmas szolgáltatásokat biztosítson. Ez a cikk ismerteti az Azure Map adott implementációját a hosszú ideig futó aszinkron háttér-feldolgozáshoz.

## <a name="submitting-a-request"></a>Kérelem elküldése

Egy ügyfélalkalmazás egy HTTP API-n keresztüli szinkron hívással indít el egy hosszan futó műveletet. Ez a hívás általában egy HTTP POST-kérelem formájában történik. Az aszinkron számítási feladatok sikeres létrehozásakor az API egy HTTP `202` -állapotkódot ad vissza, amely azt jelzi, hogy a kérést elfogadták. Ez a válasz egy olyan `Location` végpontra mutató fejlécet tartalmaz, amelyet az ügyfél lekérdez a hosszan futó művelet állapotának vizsgálatához.

### <a name="example-of-a-success-response"></a>Sikeres válasz – példa

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Ha a hívás nem felel meg az érvényesítésnek, az API Ehelyett egy `400` hibás kéréshez tartozó http-választ ad vissza. A válasz törzse további információkat biztosít az ügyfélnek arról, hogy a kérés miért volt érvénytelen.

### <a name="monitoring-the-operation-status"></a>A művelet állapotának figyelése

Az elfogadott válasz fejlécekben megadott Location végpontot lekérdezheti a hosszan futó művelet állapotának vizsgálatához. A műveleti állapot kérésének válasz törzse mindig a `status` és a tulajdonságokat fogja tartalmazni `createdDateTime` . A `status` tulajdonság a hosszan futó művelet aktuális állapotát jeleníti meg. A lehetséges állapotok például a következők:,, `"NotStarted"` `"Running"` `"Succeeded"` és `"Failed"` . A `createdDateTime` tulajdonság azt az időpontot jeleníti meg, amikor a kezdeti kérelem a hosszan futó művelet elindítására szolgál. Ha az állapot `"NotStarted"` vagy vagy, akkor a rendszer `"Running"` egy `Retry-After` fejlécet is biztosít a válaszhoz. A `Retry-After` másodpercben mért fejléc alapján megállapítható, hogy mikor kell elvégezni a műveleti állapot API következő lekérdezési hívását.

### <a name="example-of-running-a-status-response"></a>Állapot-válasz futtatásának példája

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Kezelési művelet befejezése

A hosszan futó művelet befejezésekor a válasz állapota `"Succeeded"` vagy lesz `"Failed"` . Ha egy új erőforrást egy hosszan futó műveletből hozott létre, a sikeres válasz egy HTTP-állapotkódot ad vissza `201 Created` . A válasz egy olyan `Location` fejlécet is tartalmaz, amely az erőforráshoz tartozó metaadatokra mutat. Ha nem hoztak létre új erőforrást, a sikeres válasz egy HTTP- `200 OK` állapotkódot ad vissza. Hiba esetén a válasz állapotkód is a `200 OK` kód lesz. A válasz azonban egy tulajdonsággal fog rendelkezni `error` a törzsben. A hiba adatai betartják a OData-hiba specifikációját.

### <a name="example-of-success-response"></a>Sikeres válasz – példa

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Sikertelen válasz – példa

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```