---
title: Váltás Express.jsról Azure Functionsra
description: Ismerkedjen meg a Azure Functions Express.js végpontok újrabontásával.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87810224"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Váltás Express.jsról Azure Functionsra

Express.js az egyik legnépszerűbb Node.js-keretrendszer a webfejlesztők számára, és kiváló választás marad az API-végpontokat kiszolgáló alkalmazások létrehozásához.

A kód kiszolgáló nélküli architektúrára való áttelepítésekor a Express.js-végpontok újrabontása a következő területeket érinti:

- **Middleware**: a Express.js a middleware robusztus gyűjteményét tartalmazza. A Azure Functions és az [Azure API Management](../api-management/api-management-key-concepts.md) képességeinek fényében számos middleware-modulra már nincs szükség. A végpontok áttelepítése előtt győződjön meg arról, hogy replikálja vagy lecseréli az alapvető middleware által kezelt logikát.

- **Eltérő API**-k: a kérelmek és válaszok feldolgozásához használt API különbözik Azure Functions és Express.js között. A következő példa részletezi a szükséges módosításokat.

- **Alapértelmezett útvonal**: alapértelmezés szerint Azure functions végpontok elérhetők az `api` útvonalon. Az útválasztási szabályok a [ `routePrefix` _host.js_ fájlon](./functions-bindings-http-webhook-output.md#hostjson-settings)keresztül konfigurálhatók.

- **Konfigurálás és konvenciók**: a functions-alkalmazás a fájl _function.js_ használja a http-műveletek definiálásához, a biztonsági házirendek definiálásához, valamint a függvény [bemenetének és kimenetének](./functions-triggers-bindings.md)konfigurálásához. Alapértelmezés szerint a fájlnevet tartalmazó mappa neve határozza meg a végpont nevét, de a nevet a `route` fájl [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) tulajdonságán keresztül is módosíthatja.

> [!TIP]
> Ismerkedjen meg az interaktív oktatóanyag [-újraNode.jsával, és az Azure functions segítségével a kiszolgáló nélküli API-kkal expressz API-](/learn/modules/shift-nodejs-express-apis-serverless/)kat.

## <a name="example"></a>Példa

### <a name="expressjs"></a>Express.js

Az alábbi példa egy tipikus Express.js `GET` végpontot mutat be.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

`GET`Kérelem küldésekor a `/hello` `HTTP 200` rendszer a visszaadott választ tartalmazza `Success` . Ha a végpont hibát észlel, a válasz a `HTTP 500` hiba részleteivel együtt jelenik meg.

### <a name="azure-functions"></a>Azure Functions

Azure Functions a konfigurációs és a kód fájljait egyetlen mappába rendezi az egyes függvények számára. Alapértelmezés szerint a mappa neve diktálja a függvény nevét.

Egy nevű függvény például `hello` egy mappát tartalmaz a következő fájlokkal.

``` files
| - hello
|  - function.json
|  - index.js
```

A következő példa ugyanazt az eredményt valósítja meg, mint a fenti Express.js végpont, de a Azure Functions.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

A függvényekbe való áttéréskor a következő módosítások történnek:

- **Modul:** A függvény kódja JavaScript-modulként van megvalósítva.

- **Környezet és válasz objektum**: a [`context`](./functions-reference-node.md#context-object) lehetővé teszi, hogy kommunikáljon a függvény futtatókörnyezetével. A környezetből elolvashatja a kérelmek adatait, és megadhatja a függvény válaszát. A szinkron kód megköveteli a `context.done()` végrehajtás befejezését, míg `asyc` a függvények implicit módon oldják fel a kérést.

- **Elnevezési konvenció**: a Azure functions fájlok tárolására használt mappa neve alapértelmezés szerint a végpont neve lesz (ez felülbírálható a [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)).

- **Konfiguráció**: megadhatja a http-műveleteket a [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) fájlban (például `POST` vagy `PUT` ).

A fájl következő _function.js_ a függvény konfigurációs információit tartalmazza.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A `get` `methods` tömbben definiálva a függvény a HTTP-kérelmek számára érhető el `GET` . Ha azt szeretné, hogy az API a támogatási `POST` kérelmeket fogadja el, `post` a tömböt is hozzáadhatja a tömbhöz.

## <a name="next-steps"></a>Következő lépések

- További információ az interaktív oktatóanyag-újrabontási [Node.js és a kiszolgáló nélküli API-k expressz API-kkal Azure functions](/learn/modules/shift-nodejs-express-apis-serverless/)