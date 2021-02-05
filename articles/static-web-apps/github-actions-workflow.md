---
title: GitHub-műveletek munkafolyamatai az Azure statikus Web Apps
description: Ismerje meg, hogyan állíthat be folyamatos üzembe helyezést az Azure statikus Web Apps a GitHub-Tárházak használatával.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: acdb635dec5abd73341cc1dda4991b58b82a18c0
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574516"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>GitHub-műveletek munkafolyamatok az Azure statikus Web Apps előzetes verziójában

Új Azure-beli statikus webalkalmazás-erőforrás létrehozásakor az Azure egy GitHub-műveletek munkafolyamatot hoz létre az alkalmazás folyamatos üzembe helyezésének vezérléséhez. A munkafolyamatot egy YAML-fájl vezérli. Ez a cikk a munkafolyamat-fájl szerkezetét és beállításait ismerteti.

A központi telepítéseket [Eseményindítók](#triggers)kezdeményezik, amelyek az egyes [lépések](#steps)által definiált [feladatokat](#jobs) futtatják.

## <a name="file-location"></a>Fájl helye

Amikor a GitHub-tárházat az Azure statikus Web Appshoz csatolja, a rendszer egy munkafolyamat-fájlt ad hozzá a tárházhoz.

A létrehozott munkafolyamat-fájl megtekintéséhez kövesse az alábbi lépéseket.

1. Nyissa meg az alkalmazás tárházát a GitHubon.
1. A _Code (kód_ ) lapon kattintson a `.github/workflows` mappára.
1. Kattintson a fájlra, amelynek a neve hasonlít `azure-static-web-apps-<RANDOM_NAME>.yml` .

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

Egy GitHub-művelet [elindítja](https://help.github.com/actions/reference/events-that-trigger-workflows) a GitHub-műveletek munkafolyamatot, hogy az eseményindítók alapján futtasson egy feladatot. Az eseményindítók a `on` munkafolyamat-fájl tulajdonsága alapján vannak felsorolva.

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

A tulajdonsághoz társított beállításokon keresztül `on` meghatározhatja, hogy mely ágak indítanak el egy feladatot, és hogyan állíthatók be eseményindítók a különböző lekéréses kérelmek állapotára.

Ebben a példában egy munkafolyamatot indít el a _fő_ ág módosításaival. A munkafolyamatot elindító módosítások közé tartozik a véglegesítések továbbítása és a lekéréses kérelmek megnyitása a kiválasztott ág esetében.

## <a name="jobs"></a>Feladatok

Minden esemény-eseményindítóhoz szükség van egy eseménykezelőre. A [feladatok](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) határozzák meg, hogy mi történjen egy esemény indításakor.

A statikus Web Apps munkafolyamat-fájlban két elérhető feladat van.

| Név  | Leírás |
|---------|---------|
|`build_and_deploy_job` | Végrehajtja a leküldéses végrehajtást, vagy egy lekéréses kérelmet nyit meg a `on` tulajdonságban felsorolt ág alapján. |
|`close_pull_request_job` | CSAK egy lekéréses kérelem lezárásakor hajtható végre, amely eltávolítja a lekéréses kérelmekből létrehozott átmeneti környezetet. |

## <a name="steps"></a>Lépések

A lépések a feladatok szekvenciális feladatai. A lépések olyan műveleteket hajtanak végre, mint például a függőségek telepítése, a tesztek futtatása és az alkalmazás üzembe helyezése éles környezetben.

A munkafolyamat-fájlok a következő lépéseket határozzák meg.

| Feladat  | Lépések  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Kikeresi a tárházat a művelet környezetében.<li>Létrehozza és telepíti a tárházat az Azure statikus Web Appsba.</ol>|
| `close_pull_request_job` | <ol><li>Értesíti az Azure statikus Web Apps, hogy egy lekéréses kérelem bezárult.</ol>|

## <a name="build-and-deploy"></a>Létrehozás és üzembe helyezés

A nevű lépés az `Build and Deploy` Azure statikus Web Apps példányára épít és helyez üzembe. A `with` szakasz alatt testreszabhatja az üzemelő példány következő értékeit.

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

| Tulajdonság | Leírás | Kötelező |
|---|---|---|
| `app_location` | Az alkalmazás kódjának helye.<br><br>Adja meg például, `/` hogy az alkalmazás forráskódja a tárház gyökerében található-e, vagy `/app` Ha az alkalmazás kódja egy nevű könyvtárban található `app` . | Yes |
| `api_location` | A Azure Functions kódjának helye.<br><br>Adja meg például a következőt:, `/api` Ha az alkalmazás kódja egy nevű mappában található `api` . Ha nem észleli Azure Functions alkalmazást a mappában, a Build nem sikerül, a munkafolyamat feltételezi, hogy nem szeretne API-t használni. | No |
| `output_location` | A Build kimeneti könyvtárának helye a következőhöz képest: `app_location` .<br><br>Ha például az alkalmazás forráskódja a (z) helyen található `/app` , és a Build szkript a mappába helyezi a fájlt, `/app/build` akkor a értékeként állítsa be a `build` `output_location` értéket. | No |

A `repo_token` , a `action` és az értékeket az `azure_static_web_apps_api_token` Azure statikus Web Apps állítja be, ezért nem szabad manuálisan módosítani.

## <a name="custom-build-commands"></a>Egyéni Build-parancsok

A központi telepítés során futtatott parancsok részletes szabályozása is megadható. A feladatok szakasza a következő parancsokat definiálhatja `with` .

Az üzembe helyezés mindig `npm install` minden egyéni parancs előtt meghívja a-t.

| Parancs            | Leírás |
|---------------------|-------------|
| `app_build_command` | A statikus tartalom alkalmazásának üzembe helyezése során futtatandó egyéni parancsot határozza meg.<br><br>Ha például egy szögletes alkalmazás üzemi buildjét szeretné beállítani, hozzon létre egy nevű NPM-parancsfájlt a `build-prod` futtatáshoz `ng build --prod` , és adja meg `npm run build-prod` az egyéni parancsként. Ha üresen hagyja, a munkafolyamat megpróbálja futtatni a `npm run build` vagy a `npm run build:Azure` parancsokat.  |
| `api_build_command` | A Azure Functions API-alkalmazás üzembe helyezése során futtatandó egyéni parancsot határozza meg. |

## <a name="route-file-location"></a>Útvonalfájl helye

Testreszabhatja a munkafolyamatot, hogy megkeresse a [routes.jsa](routes.md) tárház bármely mappájába. A következő tulajdonság definiálható a feladatok `with` szakasza alatt.

| Tulajdonság            | Leírás |
|---------------------|-------------|
| `routes_location` | Meghatározza azt a könyvtárat, ahol a _routes.js_ fájl található. Ez a hely a tárház gyökeréhez képest relatív. |

 Ha az előtér-keretrendszer létrehozási lépése nem helyezi át ezt a fájlt a (z) rendszerre, akkor különösen fontos, hogy a _routes.js_ fájljának helye legyen explicit `output_location` .

## <a name="environment-variables"></a>Környezeti változók

A Build környezeti változói a `env` feladatok konfigurációjának szakaszán keresztül állíthatók be.

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
          action: "upload"
          ###### Repository/Build Configurations
          app_location: "/"
          api_location: "api"
          output_location: "public"
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Monorepo-támogatás

A monorepo olyan tárház, amely egynél több alkalmazáshoz tartalmaz kódot. Alapértelmezés szerint a statikus Web Apps munkafolyamat-fájl egy adattár összes fájlját nyomon követi, de úgy is beállíthatja, hogy egyetlen alkalmazást is megcélozjon. Ezért a monorepos esetében minden statikus hely saját konfigurációs fájllal rendelkezik, amely az adattár *. git* mappájában egymás mellett él.

```files
├── .git
│   ├── azure-static-web-apps-purple-pond.yml
│   └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── readme.md
```

Ha egy munkafolyamat-fájlt egyetlen alkalmazásra kíván célozni, a és a szakaszok elérési útját kell megadnia `push` `pull_request` .

Az alábbi példa bemutatja, hogyan adhat hozzá egy `paths` csomópontot `push` `pull_request` egy _Azure-static-Web-Apps-Purple-Pond. YML_ nevű fájlhoz és részeihez.

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

Ebben az esetben csak a fájlok következő fájljain végrehajtott módosítások indítanak új buildet:

- A *App1* mappában található összes fájl
- A *api1* mappában található összes fájl
- Az alkalmazás *Azure-static-Web-Apps-Purple-Pond. YML* munkafolyamat-fájljának módosításai

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Lekéréses kérelmek áttekintése éles üzem előtti környezetekben](review-publish-pull-requests.md)
