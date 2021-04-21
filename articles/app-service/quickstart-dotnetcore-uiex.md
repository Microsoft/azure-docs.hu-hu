---
title: 'Rövid útmutató: C# ASP.NET Core-alkalmazás létrehozása'
description: Megtudhatja, hogyan futtathat webalkalmazásokat a Azure App Service első alapalkalmazásának ASP.NET használatával.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769102"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: ASP.NET Core-webalkalmazás létrehozása az Azure-ban

::: zone pivot="platform-windows"  

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe első ASP.NET Core-webalkalmazását <abbr title="HTTP-alapú szolgáltatás webalkalmazások, REST API-k és mobil háttéralkalmazások üzemeltetéséhez.">Azure App Service</abbr>. App Service .NET 5.0-alkalmazásokat támogat.

Ha végzett, egy Azure-ral fog <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyek egy egységként kezelhetők.">erőforráscsoport</abbr>, amely egy <abbr title="A csomag, amely meghatározza az alkalmazást tartalmazó webkiszolgálófarm helyét, méretét és funkcióit.">App Service-csomag</abbr> és egy <abbr title="A webalkalmazás reprezentációja, amely tartalmazza az alkalmazás kódját, a DNS-állomásneveket, a tanúsítványokat és a kapcsolódó erőforrásokat.">App Service-alkalmazás</abbr> egy üzembe helyezett mintaalkalmazással ASP.NET Core-alkalmazással.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

- **Aktív Azure-fiók** létrehozása <abbr title="Az alapvető szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, általában egy szervezeten belüli egyéni vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet/)
- **Telepítse <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019-es</a>** ASP.NET és **webfejlesztési számítási feladatokkal.**

<details>
<summary>Már rendelkezik Visual Studio 2019-es Visual Studio?</summary>
Ha már telepítette Visual Studio 2019-et:

<ul>
<li><strong>Telepítse a legújabb frissítéseket</strong> a Visual Studio a <strong>Súgó frissítések</strong> keresése &gt; <strong>lehetőség kiválasztásával.</strong> A legújabb frissítések tartalmazzák a .NET 5.0 SDK-t.</li>
<li><strong>Adja hozzá a számítási feladatot</strong> a Tools Get Tools and Features <strong>(Eszközök</strong> és szolgáltatások &gt; <strong>lekérte) lehetőség kiválasztásával.</strong></li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. ASP.NET Core-webalkalmazás létrehozása

1. Nyissa Visual Studio, és válassza **az Új projekt létrehozása lehetőséget.**

1. Az **Új projekt létrehozása** alatt válassza a ASP.NET **Core-webalkalmazás** lehetőséget, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **Tovább lehetőséget.**

1. Az **Új projekt konfigurálása mezőben adja** a *myFirstAzureWebApp* nevet a webalkalmazás-projektnek, majd válassza a **Létrehozás lehetőséget.**

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. .NET 5.0-alkalmazáshoz válassza a **ASP.NET Core 5.0** lehetőséget. Ellenkező esetben használja az alapértelmezett értéket.

1. Bármilyen típusú ASP.NET Core-webalkalmazást üzembe helyezhet az Azure-ban, de ebben a rövid útmutatóban válassza a **ASP.NET Core Web App sablont.** Győződjön meg **arról, hogy** a Hitelesítés beállítása **Nincs hitelesítés,** és hogy nincs más lehetőség kijelölve. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core-webalkalmazás létrehozása](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. A webalkalmazás Visual Studio válassza a **Hibakeresés** indítás hibakeresése hibakeresés nélkül lehetőséget a  >   webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. A webalkalmazás közzététele

1. A **Megoldáskezelő** kattintson a jobb gombbal a **myFirstAzureWebApp projektre,** és válassza a Publish (Közzététel) **lehetőséget.** 

1. A **Publish (Közzététel) menüben** válassza az **Azure lehetőséget,** majd kattintson **a Next (Tovább) gombra.**

1. A lehetőségek attól függnek, hogy már be van-e jelentkezve az Azure-ba, és hogy rendelkezik-e Visual Studio azure-fiókhoz kapcsolt fiókkal. Válassza a **Fiók hozzáadása vagy a** **Bejelentkezés** lehetőséget az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

   ![Bejelentkezés az Azure-ba](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. A példányok **jobb App Service kattintson a** **+** elemre.

   ![Új App Service alkalmazás](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Az **Előfizetés** mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az **Erőforráscsoport mezőben** válassza az Új **lehetőséget.** Az **Új erőforráscsoport neve mezőben adja** meg a *myResourceGroup nevet,* majd kattintson az **OK gombra.** 

1. A **Hosting Plan (Üzemeltetési csomag) mezőben** válassza az **Új lehetőséget.** 

1. A Hosting **Plan: Create new (Üzemeltetési csomag: Új** létrehozása) párbeszédpanelen adja meg a következő táblázatban megadott értékeket:

   | Beállítás  | Ajánlott érték |
   | -------- | --------------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* |
   | **Hely**      | *Nyugat-Európa* |
   | **Méret**          | *Ingyenes* |
   
   ![Új üzemeltetési csomag létrehozása](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. A **Név alatt** adjon meg egy egyedi alkalmazásnevet.

    <details>
        <summary>Milyen karaktereket használhatok?</summary>
        Érvényes karakterek: a-z, A-Z, 0-9 és -. Elfogadhatja az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe <code>&lt;app-name&gt;.azurewebsites.net</code> http://, ahol <code>&lt;app-name&gt;</code> az alkalmazás neve.
    </details>

1. Az **Azure-erőforrások** létrehozásához válassza a Létrehozás lehetőséget. 

   ![Alkalmazás-erőforrások létrehozása](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Várja meg, amíg a varázsló befejezi az Azure-erőforrások létrehozását. A **varázsló befejezéséhez** válassza a Befejezés lehetőséget.

1. A projekt **üzembe helyezéséhez** a Publish (Közzététel) lapon kattintson a **Publish (Közzététel)** elemre. 

    <details>
        <summary>Mi a Visual Studio?</summary>
        Visual Studio, csomagokat hoz létre és tesz közzé az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.
    </details>

   ![Közzétett ASP.NET Azure-ban futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Az alkalmazás frissítése és ismételt üzembe állása

1. A **Megoldáskezelő** alatt nyissa meg a **Pages**  >  **Index.cshtml fájlját.**

1. Cserélje le a teljes `<div>` címkét a következő kódra:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Az Azure-beli ismételt üzembe helyezéshez kattintson a jobb gombbal a **myFirstAzureWebApp** projektre a **Solution Explorer** (Megoldáskezelő) lapon, és válassza a **Publish** (Közzététel) elemet.

1. A Közzététel **összegzése** lapon válassza a Közzététel **lehetőséget.**

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

    ![Frissített ASP.NET Azure-ban futó webalkalmazás](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Az Azure-alkalmazás kezelése

1. Keresse meg a [Azure Portal,](https://portal.azure.com)keresse meg és válassza a **App Services.**

    ![Válassza a App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. A **App Services** válassza ki a webalkalmazás nevét.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Képernyőkép a App Services egy kiválasztott példa webalkalmazással.":::

1. A **webalkalmazás** Áttekintés lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a tallózás, leállítás, indítás, újraindítás és törlés. A bal oldali menü további oldalakat biztosít az alkalmazás konfigurálásához.

    ![App Service a Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Erőforrások tisztítása

1. A Azure Portal vagy **Kezdőlapján** válassza az **Erőforráscsoportok lehetőséget.** Ezután az **Erőforráscsoportok lapon** válassza a **myResourceGroup lehetőséget.**

1. A **myResourceGroup** lapon ellenőrizze, hogy a felsorolt erőforrásokat szeretné-e törölni.

1. Válassza **az Erőforráscsoport törlése lehetőséget,** írja be a **myResourceGroup** szöveget a szövegmezőbe a megerősítéshez, majd válassza a **Törlés lehetőséget.**

<hr/> 

## <a name="next-steps"></a>Következő lépések

A következő cikk azt is bemutatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztathatja azt egy SQL Database:

- [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Az ASP.NET Core-alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Ez a rövid útmutató bemutatja, hogyan hozhat létre [.NET Core-alkalmazást](/aspnet/core/) a <abbr title="App Service on Linux egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával.">App Service Linux rendszeren</abbr>. Az alkalmazást az [Azure CLI](/cli/azure/get-started-with-azure-cli)használatával hozhatja létre, a Git használatával pedig üzembe helyezheti a .NET Core-kódot az alkalmazásban.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

- **Azure-fiók létrehozása** aktív előfizetéssel. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet/)
- **Telepítse** a <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">legújabb .NET Core 3.1 SDK-t</a> vagy <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">a .NET 5.0 SDK-t.</a>
- **<a href="/cli/azure/install-azure-cli" target="_blank">Telepítse a legújabb Azure CLI-t.</a>**

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Az alkalmazás helyi létrehozása

1. A `mkdir hellodotnetcore` könyvtár létrehozásához futtassa a következőt: .

    ```bash
    mkdir hellodotnetcore
    ```

1. A `cd hellodotnetcore` könyvtárat az futtatásával módosíthatja. 

    ```bash
    cd hellodotnetcore
    ```

1. Hozzon `dotnet new web` létre egy új .NET Core-alkalmazást az futtatásával.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Az alkalmazás helyi futtatása

1. Futtassa `dotnet run` az futtatását, hogy lássa, hogyan néz ki az Azure-ban való üzembe helyezéskor.

    ```bash
    dotnet run
    ```
    
1. **Nyisson meg egy webböngészőt,** és navigáljon az alkalmazáshoz a következő webhelyen: `http://localhost:5000` .

![Tesztelés böngészővel](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Bejelentkezés az Azure-ba

Jelentkezzen `az login` be az Azure-ba az futtatásával.

```azurecli
az login
```

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. Az alkalmazás üzembe helyezése

1. **Futtatás** `az webapp up` a helyi mappában. **Cserélje** <alkalmazásnév> egy globálisan egyedi névre.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Hibaelhárítás</summary>
    <ul>
    <li>Ha a parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a <code>az</code> <a href="#1-prepare-your-environment">környezet előkészítésével () leírtak szerint.</a></li>
    <li>Cserélje le a helyére a teljes Azure-ban egyedi nevet ( érvényes <code>&lt;app-name&gt;</code> <em> karakterek: <code>a-z</code> , és <code>0-9</code> <code>-</code> </em> ). Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.</li>
    <li>A <code>--sku F1</code> argumentum létrehozza a webalkalmazást az Ingyenes tarifacsomagban. Ezt az argumentumot kihagyva gyorsabb prémium szintet használjon, amely óránkénti költségeket használ.</li>
    <li>Igény szerint meg is használhatja a <code>--location &lt;location-name&gt;</code> argumentumot, ahol az egy elérhető <code>&lt;location-name&gt;</code> Azure-régió. Az Azure-fiókhoz elérhető régiók listáját az parancs futtatásával lehet <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> lekérni.</li>
    </ul>
    </details>
    
1. Várjon, amíg a parancs befejeződik. Ez eltarthat néhány percig, és a következőre végződik: "Az alkalmazást http:// &lt; &gt; .azurewebsites.net".

    <details>
    <summary>Mi a <code>az webapp up</code> baj?</summary>
    <p>Az <code>az webapp up</code> parancs a következő műveleteket hajtja végre:</p>
    <ul>
    <li>Egy alapértelmezett erőforráscsoport létrehozása.</li>
    <li>Hozzon létre egy App Service tervet.</li>
    <li><a href="/cli/azure/webapp#az_webapp_create">Hozzon létre App Service alkalmazást</a> a megadott névvel.</li>
    <li><a href="/azure/app-service/deploy-zip">Tömörítsen</a> fájlokat az aktuális munkakönyvtárból az alkalmazásba.</li>
    <li>A futtatása közben üzeneteket biztosít az erőforrások létrehozásáról, naplózásról és a ZIP üzembe helyezéséről.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Az az webapp up parancs példakimenete](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Az az webapp up parancs példakimenete](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Tallózással keresse meg az alkalmazást

**Tallózással keresse meg az üzembe helyezett alkalmazást** a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. A kód frissítése és ismételt üzembe állása

1. **Nyissa meg _a Startup.cs_ fájlt a** helyi könyvtárban. 

1. **Módosítsa egy kicsit a** metódushívás `context.Response.WriteAsync` szövegét.

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Mentse a módosításokat.**

1. **Futtatás** `az webapp up` az ismételt üzembe álláshoz:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Mi a <code>az webapp up</code> jó ebben az esetben?</summary>
    A parancs első futtatáskor az alkalmazás nevét, az erőforráscsoportot és a App Service <i>az .azure/config</i> fájlba mentette a projekt gyökerében. Amikor újra futtatja a projekt gyökerében, az <i>az .azure/config</i>fájlban mentett értékeket használja, észleli, hogy a App Service-erőforrások már léteznek, és ismét végrehajtja a Zip üzembe helyezését.
    </details>
    
1. Az üzembe helyezés befejezése után kattintson **a frissítés** gombra a korábban megnyitott böngészőablakban.

    ![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Az új Azure-alkalmazás kezelése

1. Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. A bal oldali menüben kattintson a **App Services** elemre, majd az Azure-alkalmazás nevére.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Képernyőkép a App Services oldalról, melyen egy példa Azure-alkalmazás van kiválasztva.":::

1. Az Áttekintés lapon olyan alapvető felügyeleti feladatokat hajthat végre, mint a tallózás, leállítás, indítás, újraindítás és törlés. A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

    ![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Erőforrások megtisztítása

**Futtatás** `az group delete --name myResourceGroup` az erőforráscsoport törléséhez.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Problémákat? Tudajuk meg.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: ASP.NET Core-alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Az ASP.NET Core-alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end
