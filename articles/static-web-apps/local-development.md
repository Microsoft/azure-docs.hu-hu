---
title: Helyi fejlesztés beállítása az Azure statikus Web Apps
description: Ismerje meg, hogyan állíthatja be helyi fejlesztési környezetét az Azure statikus Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326167"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Helyi fejlesztés beállítása az Azure statikus Web Apps előzetes verziójához

Az Azure statikus Web Apps példányai két különböző típusú alkalmazásból állnak. Az első egy webes alkalmazás a statikus tartalomhoz. A webalkalmazások gyakran előtér-keretrendszerekkel és-tárakkal, illetve statikus hely generátorokkal hozhatók létre. A második szempont az API, amely egy olyan Azure Functions alkalmazás, amely sokoldalú fejlesztési környezetet biztosít.

A felhőben való futtatáskor az Azure statikus Web Apps zökkenőmentesen leképezi a kérelmeket a `api` webalkalmazásból a Azure functions alkalmazásba, anélkül, hogy CORS-konfigurációra lenne szükség. Helyileg, konfigurálnia kell az alkalmazást, hogy utánozza ezt a viselkedést.

Ez a cikk a helyi fejlesztés ajánlott bevált gyakorlatait mutatja be, beleértve a következő fogalmakat:

- A webes alkalmazás beállítása statikus tartalomhoz
- A Azure Functions alkalmazás konfigurálása az alkalmazás API-hoz
- Az alkalmazás hibakeresése és futtatása
- Ajánlott eljárások az alkalmazás fájl-és mappastruktúrát

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure functions-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code-hoz
- [Live Server-bővítmény](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) a Visual Studio Code-hoz
  - Csak akkor szükséges, ha nem használ előtér-JavaScript-keretrendszert vagy statikus site Generator parancssori felületét

## <a name="run-projects-locally"></a>Projektek helyi futtatása

Az Azure-beli statikus webalkalmazások helyi futtatása három folyamatot foglal magában, attól függően, hogy a projekt tartalmaz-e API-t.

- Helyi webkiszolgáló futtatása
- Az API futtatása
- A webes projekt csatlakoztatása az API-hoz

A webhelyek felépítésének módjától függően előfordulhat, hogy egy helyi webkiszolgáló vagy nem szükséges az alkalmazás futtatásához a böngészőben. Az előtér-JavaScript-keretrendszerek és a statikus site-generátorok használatakor ez a funkció a megfelelő CLIs (parancssori felületek) van beépítve. A következő hivatkozások a CLI-referenciára mutatnak a keretrendszerek, könyvtárak és generátorok kiválasztásához.

### <a name="javascript-frameworks-and-libraries"></a>JavaScript-keretrendszerek és-kódtárak

- [Angular CLI](https://angular.io/cli)
- [Vue parancssori felület](https://cli.vuejs.org/guide/creating-a-project.html)
- [CLI-reagálás](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Statikus hely generátorai

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Ha CLI-eszközt használ a hely kiszolgálására, ugorjon az [API futtatása](#run-api-locally) szakaszra.

### <a name="running-a-local-web-server-with-live-server"></a>Helyi webkiszolgáló futtatása élő kiszolgálóval

A Visual Studio Code-hoz készült Live Server-bővítmény egy helyi fejlesztési webkiszolgálót biztosít, amely statikus tartalmat szolgál ki.

#### <a name="create-a-repository"></a>Adattár létrehozása

1. Győződjön meg arról, hogy be van jelentkezve a GitHubba, és navigáljon a [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) **Vanilla-API** nevű új GitHub-projekthez, és hozzon létre egy, a sablon használatával.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub – új tárház ablak":::

1. Nyissa meg a Visual Studio Code-ot.

1. Nyissa meg a Parancskatalógust az **F1** billentyűvel.

1. A keresőmezőbe írja be a **klón** kifejezést, majd válassza a **git: Clone** lehetőséget.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="git-klón lehetőség a Visual Studio Code-ban":::

1. Adja meg a következő értéket a **tárház URL-címéhez**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Válassza ki az új projekthez tartozó mappa helyét.

1. Amikor a rendszer a klónozott adattár megnyitására kéri, válassza a **Megnyitás** gombot.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Megnyitás új ablakban":::

A Visual Studio Code megnyitja a klónozott projektet a szerkesztőben.

### <a name="run-the-website-locally-with-live-server"></a>A webhely helyi futtatása az élő kiszolgálóval

1. Nyissa meg a Parancskatalógust az **F1** billentyűvel.

1. Írja be a **Live Server** kifejezést a keresőmezőbe, és válassza az **élő kiszolgáló: Megnyitás élő** kiszolgálóval lehetőséget.

    Megnyílik egy böngésző lap az alkalmazás megjelenítéséhez.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="A böngészőben futó egyszerű statikus hely":::

    Ez az alkalmazás HTTP-kérelmet tesz elérhetővé a `api/message` végpontnak. Jelenleg a kérelem meghiúsul, mert az alkalmazás API-részének el kell indulnia.

### <a name="run-api-locally"></a>API helyi futtatása

Az Azure statikus Web Apps API-kat Azure Functions működteti. Az API Azure statikus Web Apps projekthez való hozzáadásával kapcsolatos részletekért lásd: [API hozzáadása az Azure statikus Azure Functions web Appshoz](add-api.md) .

Az API-létrehozási folyamat részeként létrejön egy indítási konfiguráció a Visual Studio Code-hoz. Ez a konfiguráció a _. vscode_ mappában található. Ez a mappa az API helyi létrehozásához és futtatásához szükséges összes beállítást tartalmazza.

1. A Visual Studio Code-ban nyomja le az **F5** billentyűt az API elindításához.

1. Megnyílik egy új terminál-példány, amely az API-létrehozási folyamat kimenetét mutatja.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="A Visual Studio Code terminalban futó API":::

   A Visual Studio Code-ban az állapotjelző sáv már narancssárga. Ez a szín azt jelzi, hogy az API már fut, és a hibakereső csatolva van.

1. Ezután nyomja le a **CTRL/cmd** billentyűkombinációt, és kattintson a terminál URL-címére, és nyissa meg az API-t meghívó böngészőablakot.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Az API-hívás eredményének megjelenítése a böngészőben":::

### <a name="debugging-the-api"></a>Az API hibakeresése

1. Nyissa meg az _API/GetMessage/index.js_ fájlt a Visual Studio Code-ban.

1. A Töréspont beállításához kattintson a 2. sorban lévő bal oldali margóra. Megjelenik egy piros pont, amely azt jelzi, hogy a Töréspont be van állítva.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Töréspont a Visual Studio Code-ban":::

1. A böngészőben frissítse a következő helyen futó oldalt: <http://127.0.0.1:7071/api/message> .

1. A rendszer szünetelteti a töréspontot a Visual Studio Code-ban, és a program végrehajtása szünetel.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Töréspont találat a Visual Studio Code-ban":::

   A [Visual Studio Code-ban teljes körű hibakeresési élmény érhető](https://code.visualstudio.com/Docs/editor/debugging) el az API-hoz.

1. A végrehajtás folytatásához nyomja meg a **Continue (folytatás** ) gombot a hibakeresési sávon.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Folytatás gomb a Visual Studio Code-ban":::

### <a name="calling-the-api-from-the-application"></a>Az API meghívása az alkalmazásból

Telepítésekor az Azure statikus Web Apps automatikusan leképezi ezeket a kéréseket az _API_ mappában lévő végpontokra. Ez a leképezés biztosítja, hogy az alkalmazástól érkező kérések az alábbi példához hasonlóan legyenek az API-hoz.

```javascript
let response = await fetch("/api/message");
```

Attól függően, hogy az alkalmazás a JavaScript-keretrendszer CLI-vel van-e felépítve, kétféleképpen konfigurálható az útvonal útvonala az `api` alkalmazás helyi futtatásakor.

- Környezeti konfigurációs fájlok (JavaScript-keretrendszerekhez és-tárakhoz ajánlott)
- Helyi proxy

### <a name="environment-configuration-files"></a>Környezeti konfigurációs fájlok

Ha a parancssori felülettel rendelkező előtér-keretrendszerekkel készíti elő az alkalmazást, használja a környezeti konfigurációs fájlokat. Az egyes keretrendszerek vagy könyvtárak eltérő módon kezelik ezeket a környezeti konfigurációs fájlokat. Gyakori, hogy van egy olyan konfigurációs fájlja, amely az alkalmazás helyi futtatásakor használatos, és egy olyan éles környezethez, amelyet az alkalmazás éles környezetben való futtatásakor használ. Az Ön által használt JavaScript-keretrendszer vagy statikus site Generator parancssori felülete automatikusan fogja tudni használni a fejlesztői fájlt helyileg és az éles fájlon, ha az alkalmazást az Azure statikus Web Appsja.

A fejlesztői konfigurációs fájlban megadhatja az API elérési útját, amely arra a helyi helyre mutat, `http:127.0.0.1:7071` ahol a helyhez tartozó API helyileg fut.

```
API=http:127.0.0.1:7071/api
```

Az éles konfigurációs fájlban határozza meg az API elérési útját `api` . Így az alkalmazás az "yoursite.com/api" használatával hívja meg az API-t, ha éles környezetben fut.

```
API=api
```

Ezek a konfigurációs értékek a webalkalmazás JavaScript-beli csomópont-környezeti változói is szerepelhetnek.

```js
let response = await fetch(`${process.env.API}/message`);
```

Amikor a CLI-t a webhely fejlesztési módban történő futtatására vagy a hely éles környezetbe való felépítésére használja, a `process.env.API` rendszer a megfelelő konfigurációs fájlból cseréli ki az értéket.

Az előtér-JavaScript-keretrendszerek és-kódtárak környezeti fájljainak konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Szögletes környezeti változók](https://angular.io/guide/build#configuring-application-environments)
- [Reagálás – egyéni környezeti változók hozzáadása](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue – üzemmódok és környezeti változók](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Élő kiszolgáló újraindítása

1. Az **F1** billentyű lenyomásával megnyithatja a parancs palettáját a Visual Studio Code-ban.

1. Írja be az élő **kiszolgáló** elemet, és válassza az élő kiszolgáló **: az élő kiszolgáló leállítása** lehetőséget.

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Az élő kiszolgáló parancs leállítása a Visual Studio Command paletta-ban":::

1. Nyissa meg a Parancskatalógust az **F1** billentyűvel.

1. Írja be az élő **kiszolgáló** elemet, és válassza **az élő kiszolgáló: Megnyitás élő kiszolgálóval** lehetőséget.

1. Frissítse a (z) rendszert futtató alkalmazást `http://locahost:3000` . A böngésző most megjeleníti az API által visszaadott üzenetet.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Hello a böngészőben megjelenő API-ból":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásbeállítások konfigurálása](application-settings.md)
