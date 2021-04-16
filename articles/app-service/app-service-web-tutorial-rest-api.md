---
title: 'Oktatóanyag: RESTful API gazdagépe CORS használatával'
description: Ismerje meg, hogyan üzemeltethet CORS-támogatással rendelkező RESTful API-kat az Azure App Service-ben. App Service webalkalmazásokat és háttér-API-kat is képes gazdagépként is.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18, devx-track-azurecli
ms.openlocfilehash: 098ee1c8aac343be5ffdbe90d18c886ef74ed72f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480833"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>Oktatóanyag: CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Továbbá az App Service beépített támogatást nyújt az [Eltérő eredetű erőforrások megosztásához (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) a RESTful API-k esetében. Ez az oktatóanyag bemutatja, hogyan telepíthető ASP.NET Core API-alkalmazás az App Service-ben CORS-támogatással. Az alkalmazást parancssori eszközökkel állíthatja be, és a Git használatával helyezheti üzembe. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * App Service-erőforrások létrehozása az Azure CLI-vel
> * RESTful API üzembe helyezése az Azure-ban a Git használatával
> * Az App Service CORS-támogatásának engedélyezése

Az oktatóanyag lépései macOS, Linux és Windows rendszerre is vonatkoznak.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">A legújabb .NET Core 3.1 SDK telepítése</a>

## <a name="create-local-aspnet-core-app"></a>Hozzon létre egy helyi ASP.NET Core-alkalmazást.

Ebben a lépésben a helyi ASP.NET Core-projektet állíthatja be. Az App Service támogatja az API-k más nyelven írt azonos munkafolyamatait.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.  

Futtassa a következő parancsot a mintatárház klónozásához. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

Ez az adattár a következő oktatóanyag alapján létrehozott alkalmazást tartalmaz: [Swaggert használó ASP.NET Core webes API-k súgóoldalai](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio). Ez egy Swagger-generátort használ a [Swagger felhasználói felület](https://swagger.io/swagger-ui/) és a Swagger JSON-végpont kiszolgálásához.

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez, adatbázisok migrálásához és az alkalmazás elindításához.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

Egy böngészőben nyissa meg a `http://localhost:5000/swagger` címet a Swagger felhasználói felületének kipróbálásához.

![Helyileg futó ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

Nyissa meg a `http://localhost:5000/api/todo` oldalt, és tekintse meg a teendő JSON-elemek listáját.

Nyissa meg a `http://localhost:5000` címet, és próbálja ki a böngészőalkalmazást. Később a böngészőalkalmazást egy távoli API-ra irányítja az App Service-ben a CORS-funkciók teszteléséhez. A böngészőalkalmazás kódja az adattár _wwwroot_ könyvtárában található.

Ha bármikor le szeretné állítani az ASP.NET Core-t, nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben az SQL Database-hez csatlakoztatott .NET Core-alkalmazást helyezi üzembe az App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Keresse meg az Azure-alkalmazást

Egy böngészőben nyissa meg a `http://<app_name>.azurewebsites.net/swagger` címet a Swagger felhasználói felületének kipróbálásához.

![Az Azure App Service-ben futó ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

Lépjen a `http://<app_name>.azurewebsites.net/swagger/v1/swagger.json` címre az üzembe helyezett API-hoz tartozó _swagger.json_ megtekintéséhez.

A `http://<app_name>.azurewebsites.net/api/todo` címen megtekintheti az üzembe helyezett API-t működés közben.

## <a name="add-cors-functionality"></a>CORS-funkció hozzáadása

A következő lépésben engedélyezi az App Service beépített CORS-támogatását az API-hoz.

### <a name="test-cors-in-sample-app"></a>A CORS tesztelése egy mintaalkalmazásban

A helyi adattárban nyissa meg a _wwwroot/index.html_ fájlt.

Az 51. sorban állítsa be az `apiEndpoint` változót az üzembe helyezett API URL-címére (`http://<app_name>.azurewebsites.net`). Cserélje _\<appname>_ le a helyére az alkalmazás nevét a App Service.

A helyi terminálablakban futtassa ismét a mintaalkalmazást.

```bash
dotnet run
```

Lépjen a `http://localhost:5000` helyen lévő böngészőalkalmazáshoz. Nyissa meg a fejlesztői eszközök ablakát a böngészőben `Ctrl` + `Shift` + `i` (a Windowshoz használt Chrome-ban), és vizsgálja meg a **Konzol** lapot. Megjelenik a következő hibaüzenet: `No 'Access-Control-Allow-Origin' header is present on the requested resource` .

![CORS-hiba a böngészőügyfélben](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

A böngészőalkalmazás (`http://localhost:5000`) és a távoli erőforrás (`http://<app_name>.azurewebsites.net`) közötti tartományi eltérések miatt, és mivel az App Service-ben lévő API nem küldi el az `Access-Control-Allow-Origin` fejlécet, a böngésző megakadályozta a különböző tartományokból származó tartalom betöltését a böngészőalkalmazásban.

Éles környezetben a böngészőalkalmazás egy nyilvános URL-címmel rendelkezne a localhost URL-cím helyett, de a CORS ugyanúgy engedélyezhető a localhost URL-címeken, mint a nyilvános URL-címeken.

### <a name="enable-cors"></a>CORS engedélyezése 

A Cloud Shell engedélyezze a CORS-t az ügyfél URL-címéhez az [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) paranccsal. Cserélje le _&lt; az alkalmazásnevet>_ helyőrzőre.

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

Több ügyfél URL-címét is beállíthatja a `properties.cors.allowedOrigins` (`"['URL1','URL2',...]"`) tulajdonságban. Az összes URL-címet is engedélyezheti a `"['*']"` érték megadásával.

> [!NOTE]
> Ha az alkalmazás olyan hitelesítő adatokat igényel, mint a cookie-k vagy hitelesítési jogkivonatok, a böngészőnek szüksége lehet a válasz `ACCESS-CONTROL-ALLOW-CREDENTIALS` fejlécére. Ennek engedélyezéséhez a App Service állítsa a következőre `properties.cors.supportCredentials` `true` a CORS-konfigurációban: . Ez nem engedélyezhető, ha tartalmazza a `allowedOrigins` következőt: `'*'` .

### <a name="test-cors-again"></a>A CORS újbóli tesztelése

Frissítse a böngészőalkalmazást a `http://localhost:5000` címen. A **Konzol** ablak hibaüzenete eltűnt, és megjelentek az üzembe helyezett API adatai, amelyeket használhat. A távoli API már támogatja a CORS-t a helyileg futtatott böngészőalkalmazásban. 

![Sikeres CORS a böngészőügyfélben](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

Gratulálunk, sikeresen beállította az API CORS-támogatással való futtatását az Azure App Service-ben.

## <a name="app-service-cors-vs-your-cors"></a>Az App Service-beli és a saját CORS összehasonlítása

A nagyobb rugalmasság érdekében saját CORS-segédprogramjait is használhatja az App Service CORS helyett. Előfordulhat például, hogy másik engedélyezett származási helyet szeretne megadni különböző elérési utakhoz és metódusokhoz. Mivel az App Service CORS lehetővé teszi, hogy minden API elérési úthoz és metódushoz az elfogadott származási helyek egyetlen készletét állítsa be, a saját CORS-kódját érdemes használnia. Az [eltérő eredetű kérelmek (CORS) engedélyezését](/aspnet/core/security/cors) ismertető részben tekintheti meg, hogy működik ez az ASP.NET Core-ban.

> [!NOTE]
> Ne próbálja együtt használni az App Service CORS-t és a saját CORS-kódját. Együttes használat esetén az App Service CORS az elsődleges, így a saját CORS-kód nem lép érvénybe.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * App Service-erőforrások létrehozása az Azure CLI-vel
> * RESTful API üzembe helyezése az Azure-ban a Git használatával
> * Az App Service CORS-támogatásának engedélyezése

A következő oktatóanyag a felhasználók hitelesítését és engedélyezését mutatja be.

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználók teljes körű hitelesítése és engedélyezése](tutorial-auth-aad.md)
