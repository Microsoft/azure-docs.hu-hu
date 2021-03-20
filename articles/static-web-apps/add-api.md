---
title: API hozzáadása az Azure statikus Web Appshoz Azure Functions
description: 'Az Azure statikus Web Apps használatának első lépései: kiszolgáló nélküli API hozzáadása a statikus webalkalmazáshoz Azure Functions használatával.'
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 8a42ca4cf69515c99bb2e0bd28bb62dfed0e18f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650684"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>API hozzáadása az Azure statikus Web Apps előzetes verziójához Azure Functions

Kiszolgáló nélküli API-kat adhat hozzá az Azure statikus Web Appshoz a Azure Functions-nal való integráció segítségével. Ez a cikk bemutatja, hogyan adhat hozzá és helyezhet üzembe egy API-t egy Azure-beli statikus Web Apps-webhelyhez.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók.
  - Ha nincs fiókja, létrehozhat [egyet ingyen](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure functions-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code-hoz
- [Élő kiszolgáló Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) bővítmény.
- [Node.js](https://nodejs.org/download/) az API-alkalmazás helyi futtatásához

## <a name="create-a-git-repository"></a>Git-adattár létrehozása

A következő lépések bemutatják, hogyan hozhat létre új tárházat, és hogyan klónozott fájlokat a számítógépére.

1. Győződjön meg arról, hogy bejelentkezett a GitHubba, és navigáljon [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) egy új tárház létrehozásához.
1. Az _adattár neve_ mezőbe írja be **a My-Vanilla-API** értéket.
1. Kattintson **a tárház létrehozása sablonból** elemre.

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Új Tárház létrehozása a vaníliatől – alapszintű":::

A projekt létrehozása után másolja az URL-címet a böngészőben az új tárházba. Ezt az URL-címet a Visual Studio Code-ban használhatja a git-tárház klónozásához.

1. Az **F1** billentyű lenyomásával nyissa meg a parancsot a parancs palettáján.
1. Illessze be az URL-címet a _git: Clone_ parancssorba, majd nyomja le az **ENTER** billentyűt.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="GitHub-projekt klónozása a Visual Studio Code használatával":::

    Az utasításokat követve válassza ki a tárház helyét a projekt klónozásához.

## <a name="create-the-api"></a>Az API létrehozása

Ezután létre kell hoznia egy Azure Functions projektet az alkalmazás API-ját. 

1. Hozzon létre egy **API** nevű almappát a _My-Vanilla-API_ projekten belül.
1. Nyomja meg az **F1** billentyűt a parancs paletta megnyitásához
1. Írja be a **Azure functions: új projekt létrehozása...**
1. Nyomja le az **Enter** billentyűt
1. **Tallózás** kiválasztása
1. Válassza ki az **API** -mappát a projekt-munkaterület könyvtáraként
1. Válassza a **kiválasztás** lehetőséget.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="A képernyőképen a P I mappa és a kiválasztás gomb látható.":::

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    - _Válasszon nyelvet_: **JavaScript** kiválasztása
    - _Válasszon sablont a projekt első függvényéhez_: válassza a **http-trigger** lehetőséget.
    - _Adja meg a függvény nevét_: írja be a **GetMessage**
    - _Engedélyezési szint_: válassza a **Névtelen** lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját.
        - Az engedélyezési szintekről az [engedélyezési kulcsok](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)című témakörben olvashat bővebben.

A Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP által aktivált függvénysel.

Az alkalmazás mostantól az alábbi példához hasonló projekt-szerkezettel rendelkezik.

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

Ezután módosítsa a `GetMessage` függvényt, hogy egy üzenetet ad vissza az előtérben.

1. Frissítse a `GetMessage` függvényt az _API/GetMessage/index.js_ alatt a következő kóddal.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Frissítse a `GetMessage` konfigurációt a `api/GetMessage/function.json` következő beállításokkal.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

A fenti beállításokkal az API-végpont:

- Akkor aktiválódik, ha a függvény HTTP-kérést végez
- Minden kérelem számára elérhető, a hitelesítési állapottól függetlenül
- A _/API/Message_ útvonalon keresztül elérhető

## <a name="run-the-api-locally"></a>Az API helyi futtatása

A Visual Studio Code integrálva van [Azure functions Core Tools](../azure-functions/functions-run-local.md) , hogy lehetővé tegye a projekt futtatását a helyi fejlesztési számítógépen, mielőtt közzéteszi az Azure-ban.

> [!TIP]
> A folytatás előtt győződjön meg arról, hogy az [Előfeltételek](#prerequisites) szakaszban felsorolt összes erőforrás szerepel a telepítés előtt.

1. Futtassa a függvényt az **F5** billentyű lenyomásával a functions alkalmazás elindításához.

1. Ha Azure Functions Core Tools még nincs telepítve, válassza a **telepítés** lehetőséget a parancssorban.

    Az alapvető eszközök a futó alkalmazás kimenetét jelenítik meg a _terminál_ panelen. A kimenet részeként megtekintheti a helyileg futtatott HTTP-triggeres függvény URL-végpontját.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Képernyőfelvétel: a terminál lap, amelyen megtekintheti az U R L-t.":::

1. A-t futtató alapvető eszközökkel navigáljon a következő URL-címre, és ellenőrizze, hogy az API helyesen fut-e: `http://localhost:7071/api/message` .

   A böngészőben a válasznak az alábbi példához hasonlóan kell kinéznie:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Az ALT textScreenshot szöveges választ jelenít meg egy böngészőben.":::

1. A hibakeresési munkamenet leállításához nyomja le a **SHIFT + F5** billentyűkombinációt.

### <a name="call-the-api-from-the-application"></a>Az API meghívása az alkalmazásból

Az Azure-ba való üzembe helyezéskor az API-ra irányuló kérelmek automatikusan átkerülnek a functions alkalmazásba az útvonalra küldött kérésekhez `api` . Helyi munkavégzés esetén az Alkalmazásbeállítások konfigurálását a helyi API-ra kell konfigurálnia.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>HTML-fájlok frissítése az API-hoz való hozzáféréshez

1. Ezután frissítse a _index.html_ fájl tartalmát a következő kóddal, hogy beolvassa a szöveget az API-függvényből, és megjeleníti a képernyőn:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. Az API-projekt elindításához nyomja le az **F5** billentyűt.

1. Nyomja le az **F1** billentyűt, és válassza **az élő kiszolgáló: Megnyitás az élő kiszolgálóval** lehetőséget.

    Ekkor megjelenik az API-üzenet a weblapon.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="A képernyőképen az a P I üzenet jelenik meg egy böngészőben.":::

   > [!NOTE]
   > A fájl kiszolgálásához más HTTP-kiszolgálókat vagy proxykat is használhat `index.html` . A `index.html` rendszerhez való hozzáférés `file:///` nem fog működni.

1. Az API-projekt leállításához nyomja le a **SHIFT + F5** billentyűkombinációt.

### <a name="commit-and-push-your-changes-to-github"></a>A módosítások elvégzése és leküldése a GitHubra

A Visual Studio Code használatával véglegesítheti és leküldheti a módosításokat a távoli git-tárházba.

1. Nyomja meg az **F1** billentyűt a parancs paletta megnyitásához
1. Írja be **a git: ALL commit**
1. Véglegesítő üzenet hozzáadása, majd az **ENTER** billentyű lenyomása
1. Nyomja meg az **F1** billentyűt
1. Írja be a **git: leküldéses** és nyomja le az **ENTER** billentyűt

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. Kattintson az **Erőforrás létrehozása** gombra
1. **Statikus webalkalmazás** keresése
1. Kattintson a **statikus webalkalmazás (előzetes verzió)** elemre.
1. Kattintson a **Létrehozás** gombra

Ezután adja hozzá az alkalmazásra vonatkozó beállításokat.

1. Azure- _előfizetés_ kiválasztása
1. Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
1. Nevezze el az alkalmazást a **My-Vanilla-API** néven.
1. Válassza ki az Önhöz legközelebb eső _régiót_
1. Válassza ki az **ingyenes** _SKU_ -t
1. Kattintson a **Bejelentkezés GitHub-fiókkal** gombra, majd végezzen hitelesítést a GitHub-fiókkal
1. Válassza ki az előnyben részesített _szervezetet_
1. Válassza a **My-Vanilla-API** elemet az _adattár_ legördülő menüből
1. Válassza ki a **fő** elemet az _ág_ legördülő menüből
1. Válassza ki a konfiguráció létrehozásához választott keretrendszert a Build- _előállítók_ legördülő menüből.

 > Ezek a mezők az alkalmazás típusának alapértelmezett projekt-struktúráját tükrözik. Módosítsa az értékeket az alkalmazás igényeinek megfelelően.

Ezután adja hozzá a következő Build-adatokat.

1. Adja meg **/** az _alkalmazás helyét_.
1. Adja meg az **API** -t az _API helye_ mezőben.
1. Törölje az alapértelmezett értéket az alkalmazás-összetevő _helyéről_, hagyja üresen a mezőt.
1. Kattintson a **Felülvizsgálat + létrehozás** elemre.
1. Kattintson a **Létrehozás** gombra

    Ha a _Létrehozás_ gombra kattint, az Azure két dolgot tesz. Először az alapul szolgáló Cloud Services jön létre az alkalmazás támogatásához. Ezután a háttérben futó folyamat megkezdi az alkalmazás felépítését és üzembe helyezését.

1. Kattintson az **Ugrás erőforrásra** gombra a webalkalmazás _áttekintő_ oldalának átvételéhez.

    Mivel az alkalmazás a háttérben jön létre, kattintson a szalagcímre, amely tartalmaz egy hivatkozást a Build állapotának megtekintéséhez.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub-munkafolyamat":::

1. Az üzembe helyezés befejezését követően a szervezeti egység a webalkalmazáshoz navigálva az _Áttekintés_ oldalon megjelenő _URL_ hivatkozásra kattintva érheti el.

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="A statikus alkalmazás URL-címének elérése a Azure Portal":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné tovább használni ezt az alkalmazást, a következő lépésekkel törölheti az Azure statikus webalkalmazást és a kapcsolódó erőforrásokat.

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. A felső keresési sávban írja be az **erőforráscsoportok** kifejezést.
1. Kattintson az **erőforráscsoportok** elemre.
1. **MyResourceGroup** kiválasztása
1. A _myResourceGroup_ lapon győződjön meg arról, hogy a felsorolt erőforrások közül azokat törölni kívánja.
1. Válassza a **Törlés** elemet
1. Írja be a **myResourceGroup** szöveget a szövegmezőbe
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásbeállítások konfigurálása](./application-settings.md)
