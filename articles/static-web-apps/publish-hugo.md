---
title: 'Oktatóanyag: Hugo-hely közzététele az Azure statikus Web Apps'
description: Megtudhatja, hogyan helyezhet üzembe egy Hugo-alkalmazást az Azure statikus Web Appson.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 4539c32a367bb0974212d989176a96b530da21a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652334"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Oktatóanyag: Hugo-hely közzététele az Azure statikus Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy [Hugo](https://gohugo.io/) -webalkalmazást az [Azure statikus Web Apps](overview.md). Az utolsó eredmény egy új, a kapcsolódó GitHub-műveletekkel rendelkező Azure-beli statikus webalkalmazás, amely lehetővé teszi az alkalmazás felépítésének és közzétételének vezérlését.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Hugo-alkalmazás létrehozása
> - Azure statikus Web Apps beállítása
> - A Hugo-alkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://github.com/join).

## <a name="create-a-hugo-app"></a>Hugo-alkalmazás létrehozása

Hugo-alkalmazás létrehozása a Hugo parancssori felület (CLI) használatával:

1. Kövesse a Hugo [telepítési útmutatóját](https://gohugo.io/getting-started/installing/) az operációs rendszeren.

1. Terminál megnyitása

1. Futtassa a Hugo CLI-t egy új alkalmazás létrehozásához.

   ```bash
   hugo new site static-app
   ```

1. Navigáljon az újonnan létrehozott alkalmazáshoz.

   ```bash
   cd static-app
   ```

1. Egy git-Tárház inicializálása.

   ```bash
    git init
   ```

1. Ezután vegyen fel egy témát a webhelyre egy téma git-almodulként való telepítésével, majd adja meg a Hugo konfigurációs fájlban.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Véglegesítse a módosításokat.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Az alkalmazás leküldése a GitHubra

Az Azure statikus Web Appshoz való csatlakozáshoz a GitHubon egy adattárra van szükség. A következő lépések bemutatják, hogyan hozhat létre tárházat a webhelyhez.

1. Hozzon létre egy üres GitHub-tárházat (ne hozzon létre README) a [https://github.com/new](https://github.com/new) named **Hugo-static-app** névvel.

1. Adja hozzá a GitHub-adattárat a helyi tárházhoz. Ügyeljen arra, hogy a GitHub-felhasználónevet a helyőrző helyén adja hozzá `<YOUR_USER_NAME>` a következő parancsban.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Küldje le a helyi tárházat a GitHubra.

   ```bash
   git push --set-upstream origin main
   ```

## <a name="deploy-your-web-app"></a>A webalkalmazás üzembe helyezése

A következő lépések bemutatják, hogyan hozhat létre egy új statikus webhely-alkalmazást, és hogyan telepítheti azt éles környezetben.

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. Kattintson az **Erőforrás létrehozása** gombra
1. Keressen rá a **Static Web Apps** kifejezésre
1. Kattintson a **Static Web Apps (előzetes verzió)** lehetőségre
1. Kattintson a **Létrehozás** gombra

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Azure statikus Web Apps-erőforrás létrehozása a portálon":::

1. Az **előfizetés** mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az _erőforráscsoport_ területen válassza az **új** lehetőséget. Az _Új erőforráscsoport neve_ mezőbe írja be a **Hugo-static-app** nevet, majd kattintson **az OK gombra**.

1. Ezután adja meg az alkalmazás nevét a **név** mezőben. Érvényes karakterek a következők:, `a-z` `A-Z` `0-9` és `-` .

1. A _régió_ területen válasszon ki egy elérhető régiót.

1. Az _SKU_ esetében válassza az **ingyenes** lehetőséget.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Részletek kitöltve":::

1. Kattintson a **Bejelentkezés a GitHub** gombbal gombra.

1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat.

1. Válassza ki a **Hugo-static-app** _adattárat_ .

1. A _ág_ válassza a **Main (fő**) lehetőséget.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Elkészült GitHub-információ":::

### <a name="build"></a>Létrehozás

Ezután adja hozzá azokat a konfigurációs beállításokat, amelyeket a létrehozási folyamat az alkalmazás létrehozásához használ. Az alábbi beállítások a GitHub-művelet munkafolyamat-fájlját konfigurálja.

1. Kattintson a **Tovább: Létrehozás >** gombra a létrehozási konfiguráció szerkesztéséhez

1. Az _alkalmazás helyének_ beállítása a következőre: **/** .

1. Az _alkalmazás-összetevő helyének_ beállítása **nyilvánosra**.

   Az API-k _helyének_ értéke nem szükséges, mivel jelenleg nem TELEPÍTenek API-t.

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

1. Kattintson a Létrehozás gombra az Azure statikus Web Apps létrehozásához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.

1. Várjon, amíg a GitHub-művelet befejeződik.

1. Az újonnan létrehozott Azure statikus Web Apps erőforrás Azure Portal _áttekintése_ ablakában kattintson az _URL-_ hivatkozásra a telepített alkalmazás megnyitásához.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Üzembe helyezett alkalmazás":::

#### <a name="custom-hugo-version"></a>Egyéni Hugo-verzió

Statikus webalkalmazás létrehozásakor létrejön egy munkafolyamat- [fájl](./github-actions-workflow.md) , amely az alkalmazás közzétételi konfigurációs beállításait tartalmazza. Az adott Hugo-verziót kijelölheti a munkafolyamat-fájlban úgy, hogy megadja a `HUGO_VERSION` `env` szakaszhoz tartozó értéket. A következő példa azt mutatja be, hogyan állítható be a Hugo beállítása egy adott verzióra.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](custom-domain.md)
