---
title: 'Rövid útmutató: C# ASP.NET Core-alkalmazás létrehozása'
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az első ASP.NET Core-alkalmazás üzembe helyezésével.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 77e0768a617ef79ab8510f88bfdcd41d5647f9bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701637"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: ASP.NET Core Webalkalmazás létrehozása az Azure-ban

::: zone pivot="platform-windows"  

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe az első ASP.NET Core webalkalmazását [Azure app Service](overview.md). A App Service támogatja a .NET 5,0-alkalmazásokat.

Ha elkészült, egy App Service üzemeltetési csomagból és egy üzembe helyezett webalkalmazással rendelkező App Service álló Azure-erőforráscsoport lesz.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást a **ASP.net és a webes fejlesztési** munkaterheléssel.

  Ha már telepítette a Visual Studio 2019-et:

  - A   >  **frissítések keresése** lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban. A legújabb frissítések tartalmazzák a .NET 5,0 SDK-t.
  - A számítási feladat hozzáadásához **válassza az eszközök**  >  **beolvasása eszközök és szolgáltatások** lehetőséget.


## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core-webalkalmazás létrehozása

Hozzon létre egy ASP.NET Core webalkalmazást a Visual Studióban a következő lépések végrehajtásával:

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen válassza ki **ASP.net Core webalkalmazás** elemet, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** lapon nevezze el a webalkalmazás-projekt *myFirstAzureWebApp*, és válassza a **Létrehozás** lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Bármilyen típusú ASP.NET Core webalkalmazást üzembe helyezhet az Azure-ban, de ebben a rövid útmutatóban a **webalkalmazás** sablont is kiválaszthatja. Győződjön meg arról, hogy a **hitelesítés** **Nincs hitelesítés**, és nincs más lehetőség kiválasztva. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core Webalkalmazás létrehozása](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-locally.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen válassza ki **ASP.net Core webalkalmazás** elemet, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** lapon nevezze el a webalkalmazás-projekt *myFirstAzureWebApp*, és válassza a **Létrehozás** lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. .NET 5,0-alkalmazások esetén válassza a legördülő menüben a **ASP.NET Core 5,0** elemet.

1. Bármilyen típusú ASP.NET Core webalkalmazás üzembe helyezhető az Azure-ban, de ebben a rövid útmutatóban válassza a **ASP.net Core webalkalmazás** -sablont. Győződjön meg arról, hogy a **hitelesítés** **Nincs hitelesítés**, és nincs más lehetőség kiválasztva. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core Webalkalmazás létrehozása](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-locally.png)

---

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service, amelyre közzé teheti az alkalmazást. 

A App Service beállításának részeként a következőket fogja létrehozni:

- Egy új [erőforráscsoport](../azure-resource-manager/management/overview.md#terminology) , amely a szolgáltatáshoz tartozó összes Azure-erőforrást tartalmazza.
- Új [üzemeltetési csomag](./overview-hosting-plans.md) , amely meghatározza az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és funkcióit.

Kövesse az alábbi lépéseket a App Service létrehozásához és a webalkalmazás közzétételéhez:

1. A **megoldáskezelő** kattintson a jobb gombbal a **myFirstAzureWebApp** projektre, és válassza a **Közzététel** lehetőséget. 

1. A **Közzététel** területen válassza az **Azure** lehetőséget, majd kattintson a **tovább** gombra.

1. A lehetőségei attól függnek, hogy már bejelentkezett-e az Azure-ba, és hogy van-e egy Azure-fiókhoz társított Visual Studio-fiókja. Válassza a **fiók hozzáadása** lehetőséget, vagy **Jelentkezzen** be az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

   ![Bejelentkezés az Azure-ba](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. **App Service példányok** jobb oldalán kattintson a elemre **+** .

   ![Új App Service alkalmazás](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Az **előfizetés** mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az **erőforráscsoport** területen válassza az **új** lehetőséget. Az **Új erőforráscsoport neve** mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**. 

1. **Üzemeltetési csomag** esetén válassza az **új** lehetőséget. 

1. A **üzemeltetési tervben: hozzon létre új** párbeszédpanelt, és adja meg a következő táblázatban megadott értékeket:

   | Beállítás  | Ajánlott érték | Description |
   | -------- | --------------- | ----------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* | Az App Service-csomag neve. |
   | **Hely**      | *Nyugat-Európa* | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
   | **Méret**          | *Ingyenes* | A [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) meghatározza az üzemeltetési funkciókat. |
   
   ![Új üzemeltetési csomag létrehozása](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. A **név** mezőben adjon meg egy egyedi nevet, amely csak az érvényes karaktereket tartalmazza:,, `a-z` `A-Z` `0-9` és `-` . Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve.

2. Válassza a **Létrehozás** lehetőséget az Azure-erőforrások létrehozásához.

   ![Alkalmazás-erőforrások létrehozása](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

   A varázsló befejezése után létrejön az Azure-erőforrások, és készen áll a közzétételre.

3. A varázsló bezárásához kattintson a **Befejezés** gombra.

1. A **közzétételi** lapon kattintson a **Közzététel** elemre. A Visual Studio létrehozza, becsomagolja és közzéteszi az alkalmazást az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

   ![Az Azure-ban futó közzétett ASP.NET-webalkalmazás](./media/quickstart-dotnetcore/web-app-running-live.png)

**Gratulálunk!** A ASP.NET Core webalkalmazása Azure App Serviceon fut.

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A webalkalmazás frissítéséhez és újbóli üzembe helyezéséhez kövesse az alábbi lépéseket:

1. **Megoldáskezelő** a projekt alatt nyissa meg a **Pages**  >  **index. cshtml** lapot.

1. Cserélje le a teljes `<div>` címkét a következő kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. Az összefoglalás **közzététele** lapon válassza a **Közzététel** lehetőséget.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

    ![Az Azure-ban futó ASP.NET-webalkalmazás frissítve](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg, majd válassza a **app Services** lehetőséget.

![App Services kiválasztása](./media/quickstart-dotnetcore/app-services.png)

A **app Services** lapon válassza ki a webalkalmazás nevét.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Képernyőkép a App Services oldalon egy példaként kiválasztott webalkalmazással.":::

A webalkalmazás **áttekintő** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü további lapokat biztosít az alkalmazás konfigurálásához.

![App Service a Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Visual studiót használta egy ASP.NET Core webalkalmazás létrehozásához és üzembe helyezéséhez Azure App Service.

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
A [linuxon app Service](overview.md#app-service-on-linux) a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Ez a rövid útmutató bemutatja, hogyan hozhat létre [.net Core](/aspnet/core/) -alkalmazást, és hogyan helyezhet üzembe egy Linux rendszerű app Service az [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

A cikk lépéseit Mac, Windows vagy Linux rendszert futtató gépen is követheti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Az oktatóanyag elvégzéséhez:

* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Telepítse a legújabb .net Core 3,1 SDK</a>-t.
* <a href="/cli/azure/install-azure-cli" target="_blank">A legújabb Azure CLI telepítése</a>.

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

Az oktatóanyag elvégzéséhez:

* <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">Telepítse a legújabb .net 5,0 SDK</a>-t.
* <a href="/cli/azure/install-azure-cli" target="_blank">A legújabb Azure CLI telepítése</a>.

---

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="create-the-app-locally"></a>Az alkalmazás helyi létrehozása

A gép egy terminálablakában hozzon létre egy `hellodotnetcore` nevű könyvtárat, és módosítsa erre a jelenlegi könyvtárat.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Hozzon létre egy új .NET Core-alkalmazást.

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. 

```bash
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `http://localhost:5000`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![Tesztelés böngészővel](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="sign-into-azure"></a>Bejelentkezés az Azure-ba
A terminál ablakban jelentkezzen be az Azure-ba a következő paranccsal:

```azurecli
az login
```

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

Telepítse a kódot a helyi mappájába (*hellodotnetcore*) a következő `az webapp up` parancs használatával:

```azurecli
az webapp up --sku F1 --name <app-name>
```

- Ha a `az` parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a [kezdeti környezet beállítása](#set-up-your-initial-environment)című témakörben leírtak szerint.
- Cserélje le a `<app-name>` karaktert az összes Azure-beli egyedi névre (*érvényes karakterek: `a-z` , `0-9` és `-`*). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
- Az `--sku F1` argumentum a webalkalmazást az ingyenes díjszabási szinten hozza létre. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához, amely óradíjat eredményez.
- Igény szerint megadhatja az argumentumot `--location <location-name>` , ahol az `<location-name>` egy elérhető Azure-régió. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) .

A parancs végrehajtása több percet is igénybe vehet. A futtatása közben üzeneteket biztosít az erőforráscsoport létrehozásához, a App Service terv és az üzemeltetési alkalmazáshoz, a naplózás konfigurálásához, majd a ZIP-telepítés elindításához. Ezután a következő üzenet jelenik meg: "az alkalmazás elindítható a http:// &lt; app-name &gt; . azurewebsites.net" néven, amely az alkalmazás URL-címe az Azure-ban.

# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Példa az az WebApp up parancs kimenetére](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

<!-- Deploy the code in your local folder (*hellodotnetcore*) using the `az webapp up` command:

```azurecli
az webapp up --sku B1 --name <app-name> --os-type linux
```

- If the `az` command isn't recognized, be sure you have the Azure CLI installed as described in [Set up your initial environment](#set-up-your-initial-environment).
- Replace `<app-name>` with a name that's unique across all of Azure (*valid characters are `a-z`, `0-9`, and `-`*). A good pattern is to use a combination of your company name and an app identifier.
- The `--sku B1` argument creates the web app in the Basic pricing tier, which incurs an hourly cost. Omit this argument to use a faster premium tier, which costs more.
- You can optionally include the argument `--location <location-name>` where `<location-name>` is an available Azure region. You can retrieve a list of allowable regions for your Azure account by running the [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) command.

The command may take a few minutes to complete. While running, it provides messages about creating the resource group, the App Service plan and hosting app, configuring logging, then performing ZIP deployment. It then gives the message, "You can launch the app at http://&lt;app-name&gt;.azurewebsites.net", which is the app's URL on Azure. -->

![Példa az az WebApp up parancs kimenetére](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A .NET Core mintakód App Service Linux rendszeren fut, beépített képpel.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulálunk!** Elvégezte az első .NET Core-alkalmazás üzembe helyezését a Linuxon futó App Service-ben.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A helyi könyvtárban nyissa meg a _Startup.cs_ fájlt. Végezzen el egy kis módosítást a `context.Response.WriteAsync` metódushívás szövegében:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Mentse a módosításokat, majd telepítse újra az alkalmazást a `az webapp up` parancs használatával:

```azurecli
az webapp up --os-type linux
```

Ez a parancs azokat az értékeket használja, amelyek helyileg vannak gyorsítótárazva a *. Azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a app Service tervet.

Az üzembe helyezés befejezése után váltson vissza a böngészőablakban megnyíló böngészőablakra, és kattintson a frissítés **gombra** .

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott alkalmazás kezeléséhez lépjen a <a href="https://portal.azure.com" target="_blank">Azure Portal</a> .

A bal oldali menüben kattintson a **app Services** elemre, majd kattintson az Azure-alkalmazás nevére.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Képernyőkép a App Services lapról, amely egy példaként kiválasztott Azure-alkalmazást mutat be.":::

Megtekintheti az alkalmazás áttekintés lapját. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview-up.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end
