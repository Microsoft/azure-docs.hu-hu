---
title: GitHub Actions munkafolyamatok a Azure Static Web Apps
description: Megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezést GitHub-adattárak használatával a Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791246"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub Actions előzetes verzióhoz Azure Static Web Apps munkafolyamatok

Amikor új erőforrást Azure Static Web Apps, az Azure létrehoz egy GitHub Actions munkafolyamatot az alkalmazás folyamatos üzembe helyezésének vezérléséhez. A munkafolyamatot egy YAML-fájl vezéreli. Ez a cikk a munkafolyamat-fájl szerkezetét és beállításait részletezi.

A központi telepítéseket a [eseményindítók indítják](#triggers)el, amelyek az egyes lépések által meghatározott feladatokat [futtatnak.](#steps) [](#jobs)

> [!NOTE]
> Azure Static Web Apps támogatja az Azure DevOpsot is. A folyamatok beállításával kapcsolatos információkért lásd: Közzététel az [Azure DevOps](publish-devops.md) használatával.

## <a name="file-location"></a>Fájl helye

Amikor összekapcsolja a GitHub-adattárat a Azure Static Web Apps, a rendszer hozzáad egy munkafolyamat-fájlt az adattárhoz.

Kövesse az alábbi lépéseket a létrehozott munkafolyamat-fájl megtekintéséhez.

1. Nyissa meg az alkalmazás adattárát a GitHubon.
1. A _Kód lapon_ kattintson a `.github/workflows` mappára.
1. Kattintson a fájlra a következő névvel: `azure-static-web-apps-<RANDOM_NAME>.yml` .

A tárházban található YAML-fájl az alábbi példához hasonló lesz:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>Triggerek

A GitHub Actions [eseményindító](https://help.github.com/actions/reference/events-that-trigger-workflows) értesíti a GitHub Actions munkafolyamatot, hogy eseményindítók alapján futtatassa a feladatot. Az eseményindítók a `on` munkafolyamat-fájl tulajdonságának használatával vannak felsorolva.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

A tulajdonsághoz társított beállításokkal meghatározhatja, hogy mely ágak aktiválnak egy feladatot, és beállíthatja, hogy az eseményindítók különböző lekéréses kérelmek különböző `on` államainál aktiválódnak.

Ebben a példában a fő ág változásának hatékonyan elindul _egy_ munkafolyamat. A munkafolyamatot elvezető módosítások közé tartozik a véglegesítések lekérése és a kiválasztott ágra vonatkozó lekéréses kérelmek megnyitása.

## <a name="jobs"></a>Feladatok

Minden eseményindítóhoz eseménykezelő szükséges. [A](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) feladatok határozzák meg, hogy mi történik, ha egy esemény aktiválódik.

A Static Web Apps fájlban két feladat áll rendelkezésre.

| Név                     | Leírás                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Akkor hajtja végre a rendszer, amikor lekultál egy véglegesítést, vagy lekéréses kérelmet nyit meg a tulajdonságban felsorolt `on` ágon.          |
| `close_pull_request_job` | Csak lekéréses kérelem bezárásakor lesz végrehajtva, ami eltávolítja a lekéréses kérelmekből létrehozott átmeneti környezetet. |

## <a name="steps"></a>Lépések

A lépések egy feladat szekvenciális feladatai. Egy lépés olyan műveleteket végez el, mint a függőségek telepítése, tesztek futtatása és az alkalmazás éles környezetben való üzembe helyezése.

A munkafolyamat-fájl a következő lépéseket határozza meg.

| Feladat                      | Lépések                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>A művelet környezetében ellenőrzi az adattárat.<li>Felépíti és üzembe Azure Static Web Apps.</ol> |
| `close_pull_request_job` | <ol><li>Értesíti Azure Static Web Apps, hogy egy lekéréses kérelem lezárult.</ol>                                                        |

## <a name="build-and-deploy"></a>Létrehozás és üzembe helyezés

A builds nevű lépés a saját Azure Static Web Apps `Build and Deploy` üzembe. A szakaszban `with` a következő értékeket szabhatja testre az üzemelő példányhoz.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

A , és értékeket a megadott Azure Static Web Apps `repo_token` `action` `azure_static_web_apps_api_token` manuálisan nem lehet módosítani.

## <a name="custom-build-commands"></a>Egyéni buildparancsok

Az üzembe helyezés során futtatott parancsok szabályozása finomhangoltan szabályozható. A következő parancsok a feladat szakaszában `with` határozhatóak meg.

Az üzemelő példány mindig az `npm install` egyéni parancsok előtt hívja meg a parancsot.

| Parancs             | Leírás                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Definiál egy egyéni parancsot, amely a statikus tartalomalkalmazás üzembe helyezése során fut.<br><br>Ha például egy éles buildet konfigurál egy Angular-alkalmazáshoz, hozzon létre egy nevű npm-szkriptet a futtatáshoz, majd írja be az parancsot `build-prod` `ng build --prod` egyéni `npm run build-prod` parancsként. Ha üresen hagyja, a munkafolyamat megpróbálja futtatni a `npm run build` vagy `npm run build:azure` a parancsot. |
| `api_build_command` | Definiál egy egyéni parancsot, amely a Azure Functions API-alkalmazás üzembe helyezése során fog futni.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Alkalmazás buildének kihagyása

Ha teljes körű vezérlésre van szüksége az előoldali alkalmazás felépítéséhez, hozzáadhat egyéni build lépéseket a munkafolyamathoz. Ezután konfigurálhatja a Static Web Apps, hogy megkerülje az automatikus buildfolyamatot, és egyszerűen üzembe helyezheti az előző lépésben felépített alkalmazást.

Az alkalmazás építésének kihagyása előtt állítsa a és a helyére az üzembe helyezni `skip_app_build` `true` kívánt mappa `app_location` helyét.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Tulajdonság         | Leírás                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Állítsa az értéket `true` értékre, hogy kihagyja az előoldali alkalmazás építését. |

> [!NOTE]
> Csak az előoldali alkalmazás buildét hagyhatja ki. Ha az alkalmazás rendelkezik API-val, akkor is a GitHub-művelet Static Web Apps fogja építeni.

## <a name="route-file-location"></a>Útvonalfájl helye

A munkafolyamatot testreszabhatja úgy, hogy az [routes.js](routes.md) az adattár bármely mappájában keresse a fájlt. Az alábbi tulajdonság a feladat szakaszában `with` határozható meg.

| Tulajdonság          | Leírás                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Meghatározza a könyvtár helyét,routes.js _a fájlban_ található fájlokat. Ez a hely az adattár gyökerétől relatív. |

A fájlban való routes.jskülönösen fontos, ha az előoldali keretrendszer buildlépése nem az alapértelmezett helyre áthelyezi ezt `output_location` a fájlt.

> [!IMPORTANT]
> A fájlon _routes.jsfunkció_ elavult. A [(Azure Static Web Apps)](./configuration.md) konfigurációs fájljában további információt _staticwebapp.config.jsa fájlban._

## <a name="environment-variables"></a>Környezeti változók

A build környezeti változóit a feladat `env` konfigurációjának szakaszában állíthatja be.

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
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo-támogatás

A monorepo egy olyan adattár, amely egynél több alkalmazás kódját tartalmazza. Alapértelmezés szerint a Static Web Apps munkafolyamat-fájl nyomon követi az adattár összes fájlját, de módosíthatja úgy, hogy egyetlen alkalmazást céloz meg. Ezért a monoreposok esetén minden statikus alkalmazás saját konfigurációs fájllal rendelkezik, amely egymás mellett található az adattár _.github/workflows_ mappájában.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Ha egy munkafolyamat-fájlt egyetlen alkalmazásra céloz, adja meg az elérési utakat a és a `push` `pull_request` szakaszban.

Az alábbi példa bemutatja, hogyan adhat hozzá egy csomópontot `paths` `push` az `pull_request` _azure-static-web-apps-purple-purple-yml_ nevű fájl és szakaszaihoz.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

Ebben az esetben csak a következő fájlokon végrehajtott módosítások aktiválnak egy új buildet:

- Az _app1_ mappában lévő fájlok
- Az _api1_ mappában található fájlok
- Az alkalmazás _azure-static-web-apps-purple-lil.yml munkafolyamat-fájljában_ történt módosítások

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Lekéréses kérelmek áttekintése éles üzem előtti környezetekben](review-publish-pull-requests.md)
