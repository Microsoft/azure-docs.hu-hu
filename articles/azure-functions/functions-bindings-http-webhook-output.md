---
title: HTTP-kimeneti kötések Azure Functions
description: További információ a HTTP-válaszok visszaküldéséről a Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 6caf3847971c2dd39f1eea0a921891e4440373a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727467"
---
# <a name="azure-functions-http-output-bindings"></a>HTTP-kimeneti kötések Azure Functions

A http-kimeneti kötés használatával válaszolhat a HTTP-kérelem feladójának. A kötéshez HTTP-triggerre van szükség. Lehetővé teszi a trigger kéréséhez társított válasz testreszabását.

A HTTP által aktivált függvények alapértelmezett visszatérési értéke a következő:

- `HTTP 204 No Content` üres törzstel a 2. x és újabb függvényeknél
- `HTTP 200 OK` üres törzstel az 1. x függvényben

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.js* fájlon beállított kötési konfigurációs tulajdonságait ismerteti. C#-alapú kódtárak esetében nincsenek tulajdonságok, amelyek megfelelnek ezeknek *afunction.jsnak* .

|Tulajdonság  |Leírás  |
|---------|---------|
| **típusa** |Értékre kell állítani `http` . |
| **irányba** | Értékre kell állítani `out` . |
| **név** | A válasz kódjában használt változó neve, vagy `$return` a visszatérési érték használata. |

## <a name="usage"></a>Használat

HTTP-válasz küldéséhez használja a Language-standard Response mintákat. A C# vagy C# parancsfájlban állítsa vissza a függvény visszatérési típusát `IActionResult` vagy `Task<IActionResult>` . A C# nyelvben nincs szükség visszatérési érték attribútumra.

A válaszokat például az [trigger példája](./functions-bindings-http-webhook-trigger.md#example)tartalmazza.

## <a name="hostjson-settings"></a>Beállítások host.js

Ez a szakasz a kötéshez elérhető globális konfigurációs beállításokat ismerteti 2. x vagy újabb verziókban. Az alábbi példában szereplő host.jscsak a kötés 2. x verziójának beállításait tartalmazza. További információ a 2. x verzióban található globális konfigurációs beállításokról és azon kívül: [host.jsAzure functions](functions-host-json.md).

> [!NOTE]
> Az 1. x függvények host.jsának hivatkozását lásd:host.jsaz [ Azure functions 1. x](functions-host-json-v1.md#http)esetében.

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
| customHeaders|Nincs|Lehetővé teszi egyéni fejlécek beállítását a HTTP-válaszban. Az előző példa hozzáadja a `X-Content-Type-Options` fejlécet a válaszhoz, hogy elkerülje a tartalomtípus-elemzést. |
|dynamicThrottlesEnabled|igaz<sup>\*</sup>|Ha engedélyezve van, ez a beállítás hatására a kérelmek feldolgozási folyamata rendszeres időközönként ellenőrzi a rendszerteljesítmény-számlálókat, `connections/threads/processes/memory/cpu/etc` és ha ezek a számlálók egy beépített magas küszöbértéken (80%) vannak, akkor a rendszer a kérelmeket csak akkor utasítja el, ha `429 "Too Busy"` a számláló (k) a normál szintre tér vissza.<br/><sup>\*</sup>Az alapértelmezett érték a felhasználási terv `true` . Az alapértelmezett érték egy dedikált csomag `false` .|
|hsts|nincs engedélyezve|Ha a értékre `isEnabled` van állítva `true` , a [.net Core http Strict TRANSPORT Security (HSTS) viselkedése](/aspnet/core/security/enforcing-ssl?tabs=visual-studio#hsts) kényszerítve van, ahogy az a [ `HstsOptions` osztályban](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions)van meghatározva. A fenti példa azt is megadja [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) , hogy a tulajdonság 10 nap. A `hsts` a következők támogatott tulajdonságai: <table><tr><th>Tulajdonság</th><th>Leírás</th></tr><tr><td>excludedHosts</td><td>Az állomásnevek olyan karakterlánc-tömbje, amelyhez nincs hozzáadva a HSTS fejléce.</td></tr><tr><td>Altartományok belefoglalása</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc includeSubDomain paramétere.</td></tr><tr><td>maxAge</td><td>Karakterlánc, amely a Strict-Transport-Security fejléc Max-Age paraméterét határozza meg.</td></tr><tr><td>preload</td><td>Logikai érték, amely azt jelzi, hogy engedélyezve van-e a Strict-Transport-Security fejléc Preload paramétere.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|A párhuzamosan végrehajtott HTTP-függvények maximális száma. Ez az érték lehetővé teszi a Egyidejűség szabályozását, ami segíthet az erőforrások kihasználtságának kezelésében. Előfordulhat például, hogy olyan HTTP-függvénnyel rendelkezik, amely nagy mennyiségű rendszererőforrást (memóriát/processzort/szoftvercsatornát) használ, így problémát okoz, ha a párhuzamosság túl magas. Vagy lehet, hogy olyan függvényt használ, amely harmadik féltől származó szolgáltatásnak küldött kimenő kéréseket is tartalmaz, és a hívásokat korlátozni kell. Ezekben az esetekben a szabályozás alkalmazása segíthet. <br/><sup>*</sup>A felhasználási terv alapértelmezett értéke 100. Egy dedikált csomag alapértelmezett értéke nem kötött ( `-1` ).|
|maxOutstandingRequests|200<sup>\*</sup>|A függőben lévő kérések maximális száma, amelyek egy adott időpontban vannak tárolva. Ez a korlát olyan kérelmeket tartalmaz, amelyek várólistára kerülnek, de nem indult el, valamint folyamatban van a végrehajtás. Az ezen a korláton túli bejövő kérelmek elutasítása egy 429 "túl elfoglalt" választ tartalmaz. Ez lehetővé teszi, hogy a hívók időalapú újrapróbálkozási stratégiákat alkalmazzanak, és segítséget nyújt a kérelmek maximális késésének szabályozásához is. Ez csak a parancsfájl-gazdagép végrehajtási útvonalán belüli üzenetsor-kezelőt vezérli. Más várólisták, például a ASP.NET kérelmek várólistája továbbra is érvényben marad, és ezt a beállítást nem érinti. <br/><sup>\*</sup>A felhasználási terv alapértelmezett értéke 200. Egy dedikált csomag alapértelmezett értéke nem kötött ( `-1` ).|
|routePrefix|api|Az útvonal előtagja, amely az összes útvonalra vonatkozik. Az alapértelmezett előtag eltávolításához használjon üres karakterláncot. |

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása HTTP-kérelemből](./functions-bindings-http-webhook-trigger.md)