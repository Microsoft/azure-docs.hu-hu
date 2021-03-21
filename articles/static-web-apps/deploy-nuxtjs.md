---
title: 'Oktatóanyag: kiszolgáló által megjelenített Nuxt.js webhelyek üzembe helyezése az Azure statikus Web Apps'
description: Nuxt.js dinamikus helyek előállítása és üzembe helyezése az Azure statikus Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: 8f21f5fa8ee9035fe594cecff37a63b1ef2115cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563466"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>Kiszolgáló által megjelenített Nuxt.js webhelyek üzembe helyezése az Azure statikus Web Apps előzetes verziójában

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy [Nuxt.js](https://nuxtjs.org) generált statikus webhelyet az [Azure statikus Web Apps](overview.md). A kezdéshez megtudhatja, hogyan állíthat be, konfigurálhat és helyezhet üzembe egy Nuxt.js alkalmazást. A folyamat során megtudhatja, hogyan kezelheti azokat a gyakori kihívásokat, amelyekkel gyakran szembesülnek statikus lapok Nuxt.js használatával történő létrehozásakor

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. [Hozzon létre egy fiókot ingyenesen](https://github.com/join).
- [Node.js](https://nodejs.org) telepítve.

## <a name="set-up-a-nuxtjs-app"></a>Nuxt.js alkalmazás beállítása

Beállíthat egy új Nuxt.js projektet a használatával `create-nuxt-app` . Új projekt helyett ebben az oktatóanyagban egy meglévő tárház klónozásával kezdődik. Ez a tárház úgy van beállítva, hogy bemutassa, hogyan helyezhet üzembe egy dinamikus Nuxt.js alkalmazást statikus helyként.

1. Hozzon létre egy új tárházat a GitHub-fiókjában egy sablon adattárból.
1. Navigáljon a [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate)
1. A tárház **nuxtjs neve – kezdő**
1. Ezután klónozott az új tárházat a gépre. Ügyeljen rá, hogy a fiók nevével <YOUR_GITHUB_ACCOUNT_NAME> cserélje le.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Navigáljon az újonnan klónozott Nuxt.js alkalmazáshoz:

   ```bash
   cd nuxtjs-starter
   ```

1. Telepítési függőségek:

    ```bash
    npm install
    ```

1. Nuxt.js alkalmazás elindítása a fejlesztés során:

    ```bash
    npm run dev
    ```

`http://localhost:3000`Nyissa meg az alkalmazást az alkalmazás megnyitásához, ahol a következő webhelyet kell megnyitnia az előnyben részesített böngészőben:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Nuxt.js alkalmazás elindítása":::

Ha a keretrendszerre vagy a könyvtárra kattint, megjelenik a kijelölt elem részleteit tartalmazó oldal:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Részletek lap":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Statikus webhely létrehozása Nuxt.js buildből

Nuxt.js-hely a használatával történő létrehozásakor `npm run build` az alkalmazás hagyományos webalkalmazásként, nem pedig statikus helyként van felépítve. Statikus hely létrehozásához használja a következő alkalmazás-konfigurációt.

1. Frissítse a _package.jsa_ Build parancsfájlban, hogy csak statikus helyet hozzon létre a `nuxt generate` parancs használatával:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    A parancs végrehajtása után a statikus Web Apps minden alkalommal futtatja a `build` parancsfájlt, amikor elküld egy végrehajtást.

1. Statikus hely létrehozása:

    ```bash
    npm run build
    ```

    A Nuxt.js létrehozza a statikus helyet, és átmásolja a munkakönyvtár gyökerének egyik _dist_ mappájába.

    > [!NOTE]
    > Ez a mappa a _. gitignore_ fájlban jelenik meg, mert az üzembe helyezéskor CI/CD-vel kell létrehoznia.

## <a name="push-your-static-website-to-github"></a>A statikus webhely leküldése a GitHubra

Az Azure statikus Web Apps egy GitHub-tárházból helyezi üzembe az alkalmazást, és minden leküldés után megtartja a kijelölt ágat. Használja a következő parancsokat a GitHubon végzett módosítások szinkronizálásához.

1. Az összes módosított fájl előkészítése:

    ```bash
    git add .
    ```

1. Minden módosítás véglegesítve

    ```bash
    git commit -m "Update build config"
    ```

1. A módosítások elküldése a GitHubra.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>A statikus webhely üzembe helyezése

Az alábbi lépések bemutatják, hogyan lehet összekapcsolni a GitHubra leküldött alkalmazást az Azure statikus Web Appsba. Az Azure-ban az alkalmazást éles környezetben is üzembe helyezheti.

### <a name="create-an-azure-static-web-apps-preview-resource"></a>Azure statikus Web Apps előzetes verzió erőforrásának létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. Kattintson az **Erőforrás létrehozása** gombra
1. Keressen rá a **Static Web Apps** kifejezésre
1. Kattintson a **Static Web Apps (előzetes verzió)** lehetőségre
1. Kattintson a **Létrehozás** gombra

1. Válasszon egy előfizetést az *előfizetés* legördülő listából, vagy használja az alapértelmezett értéket.
1. Kattintson az *erőforráscsoport* legördülő lista alatti **új** hivatkozásra. Az *Új erőforráscsoport neve* mezőbe írja be a **mystaticsite** nevet, majd kattintson **az OK** gombra.
1. A **név** szövegmezőben adjon meg egy globálisan egyedi nevet az alkalmazásnak. Érvényes karakterek a következők:,, `a-z` `A-Z` `0-9` és `-` . Ezt az értéket használja a statikus alkalmazás URL-előtagja a következő formátumban: `https://<APP_NAME>.azurestaticapps.net` .
1. A *régió* legördülő menüben válassza ki az Önhöz legközelebb eső régiót.
1. Válassza az **ingyenes** lehetőséget az SKU legördülő menüből.

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="Statikus webalkalmazás létrehozása":::

### <a name="add-a-github-repository"></a>GitHub-adattár hozzáadása

Az új statikus Web Apps fióknak hozzá kell férnie a tárházhoz a Nuxt.js alkalmazással, hogy automatikusan üzembe lehessen helyezni a commit.

1. Kattintson a **Bejelentkezés a GitHub gombra**
1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat a Nuxt.js projekthez, amely lehet a GitHub-felhasználóneve.
1. Keresse meg és válassza ki a korábban létrehozott adattár nevét.
1. Válassza a **Main** elemet *az ág legördülő menüből.*

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="A GitHub csatlakoztatása":::

### <a name="configure-the-build-process"></a>A létrehozási folyamat konfigurálása

Az Azure statikus Web Apps úgy lett felépítve, hogy automatikusan végezze el a gyakori feladatokat, például a NPM-modulok telepítését és az `npm run build` egyes telepítések során futó műveleteket Vannak azonban olyan beállítások, mint például az alkalmazás forrásának mappája, valamint a manuálisan konfigurálni kívánt Build célmappája.

1. A statikus kimeneti mappa konfigurálásához kattintson a **build (létrehozás** ) lapra.

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="Build lap":::

1. Írja be a **dist** értéket az alkalmazás-összetevők *helye* szövegmezőbe.

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Kattintson a Létrehozás elemre az erőforrás létrehozásának elindításához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.
1. Az üzembe helyezés befejezése után kattintson az **erőforrás keresése** elemre.
1. Az _Áttekintés_ ablakban kattintson az *URL* hivatkozásra az üzembe helyezett alkalmazás megnyitásához. 

Ha a webhely azonnal betöltődik, a háttérben futó GitHub-műveletek munkafolyamata továbbra is fut. A munkafolyamat befejezése után a böngésző frissítése gombra kattintva megtekintheti a webalkalmazást.

A műveletek munkafolyamatok állapotát az adattár műveleteire való navigálással tekintheti meg:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Módosítások szinkronizálása

Az alkalmazás létrehozásakor az Azure statikus Web Apps létrehozott egy GitHub-műveletek munkafolyamat-fájlt a tárházban. Ezt a fájlt le kell állítania a helyi tárházba, hogy a git előzményei szinkronizálva legyenek.

Térjen vissza a terminálhoz, és futtassa a következő parancsot `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Dinamikus útvonalak konfigurálása

Navigáljon az újonnan telepített webhelyre, és kattintson az egyik keretrendszer vagy könyvtár emblémára. A Részletek lap beszerzése helyett egy 404-es hibaüzenet jelenik meg.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="404 dinamikus útvonalakon":::

Ennek az az oka, Nuxt.js a statikus helyet generálta, csak a kezdőlapon. A Nuxt.js minden lapozófájlhoz egyenértékű statikus fájlokat tud előállítani `.html` `.vue` , de kivétel történt. 

Ha az oldal egy dinamikus lap, például `_id.vue` nem rendelkezik elegendő információval ahhoz, hogy statikus HTML-t lehessen készíteni az adott dinamikus lapról. Explicit módon meg kell adnia a dinamikus útvonalak lehetséges elérési útját.

## <a name="generate-static-pages-from-dynamic-routes"></a>Statikus lapok előállítása a dinamikus útvonalakból

1. Frissítse a _nuxt.config.js_ fájlt, hogy a Nuxt.js az összes rendelkezésre álló adattal listát használja az egyes keretrendszerekhez/könyvtárakhoz tartozó statikus lapok létrehozásához:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` egy aszinkron függvény, amellyel a függvény API-kérelmét elvégezheti, és a visszaadott listát használhatja az elérési utak létrehozásához.

2. Küldje le az új módosításokat a GitHub-tárházba, és várjon néhány percet, amíg a GitHub-műveletek újra felépítik a helyet. A Build befejezése után a 404-es hiba eltűnik.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="404 a dinamikus útvonalakon kijavítva":::

> [!div class="nextstepaction"]
> [Egyéni tartomány beállítása](custom-domain.md)
