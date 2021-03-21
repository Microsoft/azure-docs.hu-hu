---
title: 'Gyors útmutató: egyéni tároló futtatása App Serviceon'
description: Ismerkedjen meg Azure App Service tárolókkal az első egyéni tároló üzembe helyezésével.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745077"
---
# <a name="run-a-custom-container-in-azure"></a>Egyéni tároló futtatása az Azure-ban

::: zone pivot="container-windows"
Az [Azure App Service](overview.md) előre meghatározott, IIS-en futó alkalmazáscsoportokat biztosít Windows rendszeren, például az ASP.NET-et vagy a Node.js-t. Az előre konfigurált Windows-tároló környezet leállítja az operációs rendszert a rendszergazdai hozzáférés, a Szoftvertelepítés, a globális szerelvény-gyorsítótár változásai és így tovább. További információ: [operációs rendszer funkciójának Azure app Service](operating-system-functionality.md). Ha az alkalmazás az előre konfigurált környezet által engedélyezettnél nagyobb mértékű hozzáférést igényel, üzembe helyezhet egy egyéni Windows-tárolót.

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy ASP.NET-alkalmazást egy Windows-lemezképben a [Docker hub](https://hub.docker.com/) -ból a Visual studióból. Az alkalmazást a Azure App Service egy egyéni tárolójában futtatja.

> [!NOTE]
> A Windows-tárolók a Azure Filesra korlátozódnak, és jelenleg nem támogatják az Azure blobot.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://hub.docker.com/" target="_blank">Regisztráció Docker Hub-fiókra</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows rendszerhez készült Docker telepítése</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">A Docker átváltása Windows-tárolók futtatására</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Telepítse a Visual Studio 2019</a> -et a **ASP.net, valamint a webes fejlesztési** és az **Azure-fejlesztési** számítási feladatokkal. Ha már telepítette a Visual Studio 2019-et:

    - A   >  **frissítések keresése** lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban.
    - Adja hozzá a számítási feladatokat a Visual Studióban **az eszközök**  >  **beolvasása eszközök és szolgáltatások beszerzése lehetőség** kiválasztásával.

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

Hozzon létre egy ASP.NET-webalkalmazást a következő lépések végrehajtásával:

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet a C# számára, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** lapon nevezze el az alkalmazás _myfirstazurewebapp_, majd válassza a **Létrehozás** lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Bármilyen ASP.NET-webappot üzembe helyezhet az Azure-ban. Ebben a rövid útmutatóban válassza az **MVC** sablont.

1. Válassza a **Docker-támogatás** lehetőséget, és győződjön meg arról, hogy a hitelesítés beállítása **Nincs hitelesítés**. Válassza a **Létrehozás** lehetőséget.

   ![ASP.NET-Webalkalmazás létrehozása](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Ha a _Dockerfile_ fájl nem nyílik meg automatikusan, nyissa meg a **Solution Explorer** (Megoldáskezelő) lapról.

1. Szüksége van egy [támogatott szülő rendszerképre](configure-custom-container.md#supported-parent-images). Módosítsa a szülőrendszerképet a `FROM` sor a következő kódra való lecserélésével, majd mentse a fájlt:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Az alkalmazás futtatása helyileg](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Közzététel a Docker Hubon

1. A **megoldáskezelő** kattintson a jobb gombbal a **myfirstazurewebapp** projektre, és válassza a **Közzététel** lehetőséget.

1. Válassza a **app Service** lehetőséget, majd válassza a **Közzététel** lehetőséget.

1. A **közzétételi cél** kiválasztása lapon válassza ki a **Container Registry** és a **Docker hub** elemet, majd kattintson a **Közzététel** elemre.

   ![Közzététel a projekt áttekintő oldaláról](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Adja meg a Docker hub-fiók hitelesítő adatait, majd válassza a **Mentés** lehetőséget.

   Várjon, amíg az üzembe helyezés befejeződik. A **közzétételi** oldal mostantól megjeleníti az adattár nevét, amelyet később használni szeretne.

   ![Képernyőkép, amely kiemeli az adattár nevét.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Másolja az adattár nevét későbbi felhasználásra.

## <a name="create-a-windows-container-app"></a>Tárolóalkalmazás létrehozása Windowshoz

1. Jelentkezzen be az [Azure Portalra]( https://portal.azure.com).

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace-erőforrások listájának megadásához keresse meg a **Web App for containers**, és válassza a **Létrehozás** lehetőséget.

1. A **Webalkalmazás létrehozása lapon** válassza ki az előfizetését és egy **erőforráscsoportot**. Szükség esetén új erőforráscsoportot is létrehozhat.

1. Adja meg az alkalmazás nevét, például a *Win-Container-demót* , és válassza a **Windows** **operációs rendszer** lehetőséget. A folytatáshoz kattintson a **Tovább gombra: Docker** .

   ![Web App for Containers létrehozása](media/quickstart-custom-container/create-web-app-continer.png)

1. A **Képforráshoz** válassza a **Docker hub** lehetőséget, majd a **rendszerkép és a címke** mezőben adja meg a [Közzététel a Docker hub](#publish-to-docker-hub)-ban másolt adattár nevét.

   ![A Web App for Containers konfigurálása](media/quickstart-custom-container/configure-web-app-continer.png)

    Ha az egyéni lemezkép nem a webalkalmazásban, hanem például az [Azure Container Registryben](../container-registry/index.yml) vagy bármely egyéb privát adattárban található, itt konfigurálhatja.

1. Válassza az **Áttekintés és létrehozás** , majd **a létrehozás lehetőséget, és várja** meg, amíg az Azure létrehozza a szükséges erőforrásokat.

## <a name="browse-to-the-container-app"></a>A tárolóalkalmazás megkeresése

Értesítés jelenik meg, ha az Azure befejezte a művelet végrehajtását.

![Az üzembe helyezés sikerült](media/quickstart-custom-container/portal-create-finished.png)

1. Kattintson az **Erőforrás megnyitása** lehetőségre.

1. Az erőforrás áttekintésében kövesse az **URL-cím** melletti hivatkozást.

Megnyílik egy új böngésző oldal a következő lapra:

![Windows-tároló alkalmazás indítása](media/quickstart-custom-container/app-starting.png)

Várjon néhány percet, és próbálkozzon újra, amíg meg nem jelenik az ASP.NET alapértelmezett kezdőlapja:

![Windows-tároló alkalmazás fut](media/quickstart-custom-container/app-running-vs.png)

**Gratulálunk!** Első egyéni Windows-tárolója immár fut az Azure App Service-ben.

## <a name="see-container-start-up-logs"></a>A tároló rendszerindítási naplóinak megtekintése

A Windows-tároló betöltése hosszabb időbe telhet. Ha szeretné megtekinteni a folyamatot, keresse meg a következő URL-címet, *\<app_name>* és cserélje le az alkalmazás nevét.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

A streamelt naplók a következőképpen néznek ki:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Frissítés helyileg és ismételt üzembe helyezés

1. A Visual Studióban **Megoldáskezelőban** nyissa meg a **views**  >  **Home**  >  **index. cshtml**.

1. Keresse meg a `<div class="jumbotron">` HTML-címkét felül, és cserélje le az egész elemet az alábbi kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-ba történő ismételt üzembe helyezéshez kattintson a jobb gombbal a **myfirstazurewebapp** projektre **megoldáskezelő** , majd válassza a **Közzététel** lehetőséget.

1. A közzétételi oldalon válassza a **Publish** (Közzététel) elemet, és várja meg, amíg a közzététel végbemegy.

1. Ahhoz, hogy az App Service-nek előírhassa az új rendszerkép a Docker Hubból történő lekérését, az alkalmazás újraindítása szükséges. A portál alkalmazás lapján kattintson az **Újraindítás**  >  **Igen** gombra.

   ![Webalkalmazás újraindítása az Azure-ban](./media/quickstart-custom-container/portal-restart-app.png)

[A tárolóalkalmazás megkeresése](#browse-to-the-container-app) ismét. A weboldal frissítésekor az alkalmazásnak először vissza kell térnie az „Indítás” lapra, majd pár perc elteltével ismét meg kell jelenítenie a frissített weboldalt.

![Frissített webalkalmazás az Azure-ban](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áttelepítés az Azure-ban található Windows-tárolókba](tutorial-custom-container.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
A Linuxon futó App Service a Linuxon előre definiált alkalmazási kötegeket biztosít, például a .NET, a PHP, a Node.js és más nyelvek támogatásával. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy rendszerképet egy [Azure Container Registryból](../container-registry/index.yml) (ACR) a app Serviceba.

## <a name="prerequisites"></a>Előfeltételek

* [Azure-fiók](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* A [vs Code Azure app Service kiterjesztése](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Ezzel a bővítménnyel linuxos Web Apps hozhat létre, kezelhet és telepíthet az Azure platform szolgáltatásként (Péter).
* A [vs Code Docker-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Ezzel a bővítménnyel egyszerűsítheti a helyi Docker-rendszerképek és-parancsok kezelését, valamint az Azure-ba épített alkalmazás-lemezképek üzembe helyezését.

## <a name="create-an-image"></a>Rendszerkép létrehozása

A rövid útmutató elvégzéséhez szüksége lesz egy [Azure Container Registryban](../container-registry/index.yml)tárolt megfelelő webalkalmazás-rendszerképre. Kövesse a gyors útmutató [: privát tároló beállításjegyzékének létrehozása a Azure Portal használatával](../container-registry/container-registry-get-started-portal.md), de a `mcr.microsoft.com/azuredocs/go` rendszerkép helyett használja a rendszerképet `hello-world` . A [minta Docker az Azure Samples-](https://github.com/Azure-Samples/go-docs-hello-world)tárházban található.

> [!IMPORTANT]
> Ügyeljen arra, hogy a **rendszergazda felhasználó** beállítással **engedélyezze** a tároló-beállításjegyzék létrehozását. Azt is megteheti, hogy a beállításjegyzék oldalának **hozzáférési kulcsok** szakasza is be van állítva a Azure Portal. Ez a beállítás App Service hozzáféréshez szükséges.

## <a name="sign-in"></a>Bejelentkezés

Ezután indítsa el a VS Code-ot, és jelentkezzen be az Azure-fiókjába az App Service bővítmény használatával. Ehhez válassza ki az Azure-emblémát a tevékenység sávjában, navigáljon az **app Service** Explorerrel, majd válassza a **Bejelentkezés az Azure** -ba lehetőséget, és kövesse az utasításokat.

![Bejelentkezés az Azure-ba](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

Most megtekintheti, hogy az összes előfeltétel telepítve van-e, és megfelelően van-e konfigurálva.

A VS Code-ban az Azure-beli e-mail-cím jelenik meg az állapotsorban és az előfizetés az **app Service** Explorerben.

Ezután ellenőrizze, hogy a Docker telepítve van-e és fut-e. A következő parancs megjeleníti a Docker-verziót, ha az fut.

```bash
docker --version
```

Végezetül ellenőrizze, hogy a Azure Container Registry csatlakoztatva van-e. Ehhez válassza ki a Docker-emblémát a tevékenység sávjában, majd navigáljon **a** beállításjegyzékek elemre.

![Képernyőfelvétel: a beállításjegyzékek értéke az Azure Expanded értékkel, valamint egy, a dot i o filename bővítménnyel rendelkező fájl.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>A rendszerkép üzembe helyezése Azure App Service

Most, hogy minden be van állítva, telepítheti a lemezképet [Azure app Service](https://azure.microsoft.com/services/app-service/) közvetlenül a Docker bővítmény Explorerrel.

Keresse meg a rendszerképet **a** **Docker** Explorer beállításjegyzékek csomópontjában, és bontsa ki a címkék megjelenítéséhez. Kattintson a jobb gombbal a címkére, majd válassza a **lemezkép központi telepítése Azure app Service** lehetőséget.

Itt az alábbi utasításokat követve választhatja ki az előfizetést, egy globálisan egyedi alkalmazás nevét, egy erőforráscsoportot és egy App Service tervet. Válassza a **B1 alapszintű** lehetőséget a díjszabási szinthez és a régióhoz.

Az üzembe helyezés után az alkalmazás a következő címen érhető el: `http://<app name>.azurewebsites.net` .

Az **erőforráscsoport** az alkalmazás összes erőforrásának elnevezett gyűjteménye az Azure-ban. Egy erőforráscsoport például tartalmazhat egy webhelyre, egy adatbázisra és egy Azure-függvényre mutató hivatkozást is.

A **app Service-csomag** határozza meg a webhely üzemeltetéséhez használt fizikai erőforrásokat. Ez a rövid útmutató egy **alapszintű** üzemeltetési csomagot használ a **Linux** -infrastruktúrán, ami azt jelenti, hogy a webhely más webhelyeken található Linux-gépen lesz tárolva. Ha az **alapszintű** csomaggal kezdődik, akkor a Azure Portal a vertikális felskálázáshoz használhatja, hogy a tiéd legyen a gépen futó egyetlen hely.

## <a name="browse-the-website"></a>Böngészés a webhelyen

A rendszer az üzembe helyezés során megnyílik a **kimeneti** panel, hogy jelezze a művelet állapotát. Ha a művelet befejeződik, keresse meg az App **Service** Explorerben létrehozott alkalmazást, kattintson rá a jobb gombbal, majd válassza a **Tallózás webhely** lehetőséget a webhely böngészőben való megnyitásához.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) bővítmény csomag telepítésével vagy az összes beszerzésével.

Tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Egyéni tároló konfigurálása](configure-custom-container.md)

::: zone-end
