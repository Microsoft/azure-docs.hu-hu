---
title: Megbízható szolgáltatás JavaScript
description: Ez a JavaScript-verzió a megbízható szolgáltatás létrehozásához a kommunikációs szolgáltatásokhoz.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3bf0831084b9dd6f16d8208def87033fc0be2900
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100645441"
---
## <a name="download-code"></a>Kód letöltése

A rövid útmutatóhoz tartozó véglegesített kód megkeresése a [githubon](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/Trusted%20Authentication%20Service)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), aktív LTS-és karbantartási LTS-verziók (10.14.1 ajánlott). A `node --version` parancs használatával vizsgálja meg a verziót. 
- A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Áttekintés

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="A megbízható szolgáltatás architektúrájának diagramja":::

Ebben az oktatóanyagban egy olyan Azure-függvényt hozunk létre, amely megbízható jogkivonat-kiépítési szolgáltatásként szolgál majd. Ez az oktatóanyag a saját jogkivonat-kiépítési szolgáltatás rendszerindítására használható.

Ez a szolgáltatás felelős a felhasználók Azure kommunikációs szolgáltatásokkal való hitelesítéséhez. A kommunikációs szolgáltatások alkalmazásainak felhasználói számára szükséges, `Access Token` hogy részt vegyenek a csevegési szálakban és a VoIP-hívásokban. Az Azure-függvény megbízható közvetítőként fog működni a felhasználó és a kommunikációs szolgáltatások között. Ez lehetővé teszi hozzáférési tokenek kiépítését anélkül, hogy az erőforrás-kapcsolati karakterláncot a felhasználók számára kiadja.

További információkért tekintse meg az [ügyféloldali architektúra](../../concepts/client-and-server-architecture.md) és a [hitelesítés és az engedélyezés](../../concepts/authentication.md) fogalmi dokumentációját.

## <a name="setting-up"></a>Beállítás

### <a name="azure-functions-set-up"></a>Azure Functions beállítása

Először állítsa be az Azure-függvény alapszintű szerkezetét. A beállítás részletes útmutatója itt található: [függvény létrehozása a Visual Studio Code használatával](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

Az Azure-függvény a következő konfigurációt igényli:

- Nyelv: JavaScript
- Sablon: HTTP-trigger
- Engedélyezési szint: névtelen (ez később is átkapcsolható, ha egy másik hitelesítési modellt szeretne megadni)
- Függvény neve: felhasználó által definiált

Miután követte a fenti konfigurációval [Azure functions utasításokat](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) , egy, a függvényt tartalmazó fájllal kell rendelkeznie a Visual Studio Code-ban az Azure-függvényhez tartozó projekttel `index.js` . A fájlon belüli kódnak a következőnek kell lennie:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

Most folytatjuk az Azure kommunikációs szolgáltatások kódtárainak telepítését.

### <a name="install-communication-services-libraries"></a>A kommunikációs szolgáltatások kódtárainak telepítése

A `Identity` könyvtárat a létrehozásához használjuk `User Access Tokens` .

A `npm install` paranccsal telepítheti az Azure Communication Services Felügyeleti ügyféloldali kódtárat a javascripthez.

```console

npm install @azure/communication-identity --save

```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

A `index.js` fájl tetején importálja a felületet a következőhöz: `CommunicationIdentityClient`

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>Hozzáférési jogkivonat létrehozása

Ahhoz, hogy lehetővé váljon az Azure-függvény létrehozása `User Access Tokens` , először a kapcsolati karakterláncot kell használnia a kommunikációs szolgáltatások erőforrásaihoz.

A kapcsolatok karakterláncának beolvasásával kapcsolatos további információkért látogasson el az [erőforrás-kiépítési](../../quickstarts/create-communication-resource.md) útmutatóba.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

Ezután módosítjuk az eredeti függvényt a létrehozáshoz `User Access Tokens` . 

`User Access Tokens` létrehozásához hozzon létre egy felhasználót a `createUser` metódusból. A felhasználó létrehozása után a `issueToken` metódus használatával létrehozhat egy jogkivonatot az adott felhasználó számára, amely az Azure-függvényt adja vissza.

Ebben a példában a jogkivonat hatókörét a következőre konfigurálja: `voip` . Az alkalmazáshoz további hatókörökre lehet szükség. További információ a [hatókörökről](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

A meglévő kommunikációs szolgáltatások esetében `CommunicationUser` kihagyhatja a létrehozási lépést, és egyszerűen létrehozhat egy hozzáférési jogkivonatot. További információ a [felhasználói hozzáférési tokenek létrehozása rövid útmutatóban](../../quickstarts/access-tokens.md)található.

## <a name="test-the-azure-function"></a>Az Azure-függvény tesztelése

Futtassa az Azure-függvényt helyileg a használatával `F5` . Ez a művelet helyileg inicializálja az Azure-függvényt, és az alábbi módon teszi elérhetővé: `http://localhost:7071/api/FUNCTION_NAME` . További dokumentáció a [helyi futtatásról](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)

Nyissa meg az URL-címet a böngészőjében, és meg kell jelennie a válasz törzsének a kommunikációs felhasználói AZONOSÍTÓval, a tokenrel és a token lejáratával.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Képernyőfelvétel: a létrehozott Azure-függvényre mutató válasz.":::

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

Az Azure-függvény üzembe helyezéséhez kövesse [az útmutató lépésről lépésre](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)

Összefoglalva, a következőkre lesz szüksége:
1. Bejelentkezés az Azure-ba a Visual studióból
2. Tegye közzé a projektjét az Azure-fiókjában. Itt ki kell választania egy meglévő előfizetést.
3. Hozzon létre egy új Azure Function-erőforrást a Visual Studio varázsló használatával, vagy használjon egy meglévő erőforrást. Új erőforrás esetén konfigurálnia kell azt a kívánt régióra, futtatókörnyezetre és egyedi azonosítóra.
4. Várakozás a központi telepítés véglegesítésére
5. Futtassa a függvényt 🎉

## <a name="run-azure-function"></a>Azure-függvény futtatása

Az Azure-függvény futtatása az URL-cím használatával `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`

Az URL-cím megkereséséhez kattintson a jobb gombbal a függvényre a Visual Studio Code-ban, és másolja a függvény URL-címét.

További információ az [Azure-függvény futtatásáról](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
