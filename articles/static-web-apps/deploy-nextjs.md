---
title: 'Oktatóanyag: statikusan renderelt Next.js webhelyek üzembe helyezése az Azure statikus Web Apps'
description: Next.js dinamikus helyek előállítása és üzembe helyezése az Azure statikus Web Apps.
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.custom: devx-track-js
ms.openlocfilehash: a22d06137c3ec17851280605ac85c94ef8b342cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97563075"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps-preview"></a>Statikusan renderelt Next.js webhelyek üzembe helyezése az Azure statikus Web Apps előzetes verziójában

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy [Next.js](https://nextjs.org) generált statikus webhelyet az [Azure statikus Web Apps](overview.md). A kezdéshez megtudhatja, hogyan állíthat be, konfigurálhat és helyezhet üzembe egy Next.js alkalmazást. A folyamat során megtudhatja, hogyan kezelheti azokat a gyakori kihívásokat, amelyekkel gyakran szembesülnek statikus lapok Next.js használatával történő létrehozásakor

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. [Hozzon létre egy fiókot ingyenesen](https://github.com/join).
- [Node.js](https://nodejs.org) telepítve.

## <a name="set-up-a-nextjs-app"></a>Next.js alkalmazás beállítása

Ahelyett, hogy az Next.js CLI-t használja egy alkalmazás létrehozásához, használhat egy meglévő Next.js alkalmazást tartalmazó indító tárházat. Ez a tárház egy Next.js alkalmazást tartalmaz dinamikus útvonalakkal, amely általános telepítési problémát mutat be. A dinamikus útvonalaknak további telepítési konfigurációra van szükségük, amelyet egy pillanat alatt többet is megtudhat.

Első lépésként hozzon létre egy új tárházat a GitHub-fiókjában egy sablon adattárból.

1. Navigáljon a [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate)
1. A tárház **nextjs neve – kezdő**
1. Ezután klónozott az új tárházat a gépre. Ügyeljen rá, hogy a helyére a `<YOUR_GITHUB_ACCOUNT_NAME>` fiók nevét írja.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navigáljon az újonnan klónozott Next.js alkalmazáshoz:

   ```bash
   cd nextjs-starter
   ```

1. Telepítési függőségek:

    ```bash
    npm install
    ```

1. Next.js alkalmazás elindítása a fejlesztés során:

    ```bash
    npm run dev
    ```

`http://localhost:3000`Nyissa meg az alkalmazást az alkalmazás megnyitásához, ahol a következő webhelyet kell megnyitnia az előnyben részesített böngészőben:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Next.js alkalmazás elindítása":::

Ha a keretrendszerre vagy a könyvtárra kattint, megjelenik a kijelölt elem részleteit tartalmazó oldal:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Részletek lap":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Statikus webhely létrehozása Next.js buildből

Next.js-hely a használatával történő létrehozásakor `npm run build` az alkalmazás hagyományos webalkalmazásként, nem pedig statikus helyként van felépítve. Statikus hely létrehozásához használja a következő alkalmazás-konfigurációt.

1. A statikus útvonalak konfigurálásához hozzon létre egy _next.config.js_ nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá a következő kódot.

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```
    
      Ez a konfiguráció az `/` útvonalhoz kiszolgált Next.js lapra mutat `/` , amely a _pages/index.js_ lapozófájl.

1. Frissítse a _package.jsa_ Build parancsfájlban, hogy a létrehozás után is létrehoz egy statikus helyet a `next export` parancs használatával. A `export` parancs létrehoz egy statikus helyet.

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    A parancs végrehajtása után a statikus Web Apps minden alkalommal futtatja a `build` parancsfájlt, amikor elküld egy végrehajtást.

1. Statikus hely létrehozása:

    ```bash
    npm run build
    ```

    A rendszer létrehoz egy statikus helyet, és átmásolja a munkakönyvtár gyökerének egy _kimenő_ mappájába.

    > [!NOTE]
    > Ez a mappa a _. gitignore_ fájlban jelenik meg, mert az üzembe helyezéskor CI/CD-vel kell létrehoznia.

## <a name="push-your-static-website-to-github"></a>A statikus webhely leküldése a GitHubra

Az Azure statikus Web Apps egy GitHub-tárházból helyezi üzembe az alkalmazást, és minden leküldés után megtartja a kijelölt ágat. Használja a következő parancsokat a GitHubon végzett módosítások szinkronizálásához.

1. Az összes módosított fájl előkészítése

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

### <a name="create-a-static-app"></a>Statikus alkalmazás létrehozása

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

   :::image type="content" source="media/deploy-nextjs/create-static-web-app.png" alt-text="Statikus webalkalmazás létrehozása":::

### <a name="add-a-github-repository"></a>GitHub-adattár hozzáadása

Az új statikus Web Apps fióknak hozzá kell férnie a tárházhoz a Next.js alkalmazással, hogy automatikusan üzembe lehessen helyezni a commit.

1. Kattintson a **Bejelentkezés a GitHub gombra**
1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat a Next.js projekthez, amely lehet a GitHub-felhasználóneve.
1. Keresse meg és válassza ki a korábban létrehozott adattár nevét.
1. Válassza a **Main** elemet *az ág legördülő menüből.*

   :::image type="content" source="media/deploy-nextjs/connect-github.png" alt-text="A GitHub csatlakoztatása":::

### <a name="configure-the-build-process"></a>A létrehozási folyamat konfigurálása

Az Azure statikus Web Apps úgy lett felépítve, hogy automatikusan végezze el a gyakori feladatokat, például a NPM-modulok telepítését és az `npm run build` egyes telepítések során futó műveleteket Vannak azonban olyan beállítások, mint például az alkalmazás forrásának mappája, valamint a manuálisan konfigurálni kívánt Build célmappája.

1. A statikus kimeneti mappa konfigurálásához kattintson a **build (létrehozás** ) lapra.

   :::image type="content" source="media/deploy-nextjs/build-tab.png" alt-text="Build lap":::

2. Írja **be az** *alkalmazás* -összetevő szövegmezőbe.

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Kattintson a Létrehozás elemre az erőforrás létrehozásának elindításához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.
1. Az üzembe helyezés befejezése után kattintson az **erőforrás keresése** elemre.
1. Az _Áttekintés_ ablakban kattintson az *URL* hivatkozásra az üzembe helyezett alkalmazás megnyitásához. 

Ha a webhely azonnal betöltődik, a háttérben futó GitHub-műveletek munkafolyamata továbbra is fut. A munkafolyamat befejezése után a böngésző frissítése gombra kattintva megtekintheti a webalkalmazást.
Ha a webhely azonnal betöltődik, a háttérben futó GitHub-műveletek munkafolyamata továbbra is fut. A munkafolyamat befejezése után a böngésző frissítése gombra kattintva megtekintheti a webalkalmazást.

A műveletek munkafolyamatok állapotát az adattár műveleteire való navigálással tekintheti meg:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>Módosítások szinkronizálása

Az alkalmazás létrehozásakor az Azure statikus Web Apps létrehozott egy GitHub-műveletek munkafolyamat-fájlt a tárházban. Ezt a fájlt le kell állítania a helyi tárházba, hogy a git előzményei szinkronizálva legyenek.

Térjen vissza a terminálhoz, és futtassa a következő parancsot `git pull origin main` .

## <a name="configure-dynamic-routes"></a>Dinamikus útvonalak konfigurálása

Navigáljon az újonnan telepített webhelyre, és kattintson az egyik keretrendszer vagy könyvtár emblémára. A Részletek lap beszerzése helyett egy 404-es hibaüzenet jelenik meg.

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="404 dinamikus útvonalakon":::

Ennek a hibának az oka az, hogy Next.js csak az alkalmazás konfigurációja alapján hozta létre a kezdőlapot.

## <a name="generate-static-pages-from-dynamic-routes"></a>Statikus lapok előállítása a dinamikus útvonalakból

1. Frissítse a _next.config.js_ fájlt, hogy a Next.js az összes rendelkezésre álló adattal listát használja az egyes keretrendszerekhez/könyvtárakhoz tartozó statikus lapok létrehozásához:

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > A `exportPathMap` függvény egy aszinkron függvény, így a függvényben egy API-ra kérhet egy kérést, és a visszaadott lista használatával létrehozhatja az elérési utakat.

2. Küldje le az új módosításokat a GitHub-tárházba, és várjon néhány percet, amíg a GitHub-műveletek újra felépítik a helyet. A Build befejezése után a 404-es hiba eltűnik.

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="404 a dinamikus útvonalakon kijavítva":::

> [!div class="nextstepaction"]
> [Egyéni tartomány beállítása](custom-domain.md)
