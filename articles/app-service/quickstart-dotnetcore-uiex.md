---
title: 'Rövid útmutató: C# ASP.NET Core-alkalmazás létrehozása'
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az első ASP.NET Core-alkalmazás üzembe helyezésével.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2a789b4ca1261c79e8e6eb93a4ed44e7e8e9272e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214235"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rövid útmutató: ASP.NET Core Webalkalmazás létrehozása az Azure-ban

::: zone pivot="platform-windows"  

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe az első ASP.NET Core webalkalmazását a következőre: <abbr title="HTTP-alapú szolgáltatás webes alkalmazások, REST API-k és mobil háttérbeli alkalmazások üzemeltetéséhez.">Azure App Service</abbr>. A App Service támogatja a .NET 5,0-alkalmazásokat.

Ha elkészült, Azure-beli <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr>, amely egy <abbr title="Az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és szolgáltatásait meghatározó csomag.">App Service-csomag</abbr> és egy <abbr title="A webalkalmazás ábrázolása, amely az alkalmazás kódját, DNS-állomásnevét, tanúsítványait és kapcsolódó erőforrásait tartalmazza.">App Service-alkalmazás</abbr> egy üzembe helyezett minta ASP.NET Core alkalmazással.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

- Aktív **Azure-fiók beszerzése** <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- **Telepítse a <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> alkalmazást** a **ASP.net és a webes fejlesztési** munkaterheléssel.

<details>
<summary>Már rendelkezik Visual Studio 2019-rel?</summary>
Ha már telepítette a Visual Studio 2019-et:

<ul>
<li>A frissítések keresése lehetőség <strong>kiválasztásával</strong> <strong>telepítse a legújabb frissítéseket</strong> a Visual Studióban &gt; <strong></strong>. A legújabb frissítések tartalmazzák a .NET 5,0 SDK-t.</li>
<li><strong>A számítási feladat hozzáadásához válassza az</strong> <strong>eszközök</strong> &gt; <strong>beolvasása eszközök és szolgáltatások</strong>lehetőséget.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. ASP.NET Core Webalkalmazás létrehozása

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen válassza ki **ASP.net Core webalkalmazás** elemet, és győződjön meg arról, hogy a **C#** szerepel a választott nyelveken, majd válassza a **tovább** lehetőséget.

1. Az **új projekt konfigurálása** lapon nevezze el a webalkalmazás-projekt *myFirstAzureWebApp*, és válassza a **Létrehozás** lehetőséget.

   ![A webalkalmazás-projekt konfigurálása](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. .NET 5,0-alkalmazások esetén válassza a legördülő menüben a **ASP.NET Core 5,0** elemet. Ellenkező esetben használja az alapértelmezett értéket.

1. Bármilyen típusú ASP.NET Core webalkalmazás üzembe helyezhető az Azure-ban, de ebben a rövid útmutatóban válassza a **ASP.net Core webalkalmazás** -sablont. Győződjön meg arról, hogy a **hitelesítés** **Nincs hitelesítés**, és nincs más lehetőség kiválasztva. Ezután kattintson a **Létrehozás** elemre.

   ![Új ASP.NET Core Webalkalmazás létrehozása](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   ![Helyileg futó webalkalmazás](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. a webes alkalmazás közzététele

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

   | Beállítás  | Ajánlott érték |
   | -------- | --------------- |
   | **Szolgáltatási csomag**  | *myFirstAzureWebAppPlan* |
   | **Hely**      | *Nyugat-Európa* |
   | **Méret**          | *Ingyenes* |
   
   ![Új üzemeltetési csomag létrehozása](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. A **név** mezőben adjon meg egy egyedi nevet.

    <details>
        <summary>Milyen karaktereket használhatok?</summary>
        Érvényes karakterek: a-z, A-Z, 0-9 és-. Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe a http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , ahol az <code>&lt;app-name&gt;</code> alkalmazás neve.
    </details>

1. Válassza a **Létrehozás** lehetőséget az Azure-erőforrások létrehozásához. 

   ![Alkalmazás-erőforrások létrehozása](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Várjon, amíg a varázsló befejezi az Azure-erőforrások létrehozását. A varázsló bezárásához kattintson a **Befejezés** gombra.

1. A **Közzététel** lapon kattintson a **Közzététel** gombra a projekt telepítéséhez. 

    <details>
        <summary>Mi a Visual Studio?</summary>
        A Visual Studio létrehozza, becsomagolja és közzéteszi az alkalmazást az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.
    </details>

   ![Az Azure-ban futó közzétett ASP.NET-webalkalmazás](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. az alkalmazás frissítése és újbóli üzembe helyezése

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

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. az Azure-alkalmazás kezelése

1. Lépjen a [Azure Portalra](https://portal.azure.com), és keresse meg és válassza ki a **app Services**.

    ![App Services kiválasztása](./media/quickstart-dotnetcore/app-services.png)
    
1. A **app Services** lapon válassza ki a webalkalmazás nevét.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Képernyőkép a App Services oldalon egy példaként kiválasztott webalkalmazással.":::

1. A webalkalmazás **áttekintő** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü további lapokat biztosít az alkalmazás konfigurálásához.

    ![App Service a Azure Portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. erőforrások törlése

1. Az Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforráscsoportok** lehetőséget. Ezután az **erőforráscsoportok** lapon válassza a **myResourceGroup** lehetőséget.

1. A **myResourceGroup** lapon győződjön meg arról, hogy a felsorolt erőforrások közül azokat törölni kívánja.

1. Válassza az **erőforráscsoport törlése** elemet, írja be a **myResourceGroup** szöveget a megerősítéshez, majd válassza a **Törlés** lehetőséget.

<hr/> 

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

- [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Ez a rövid útmutató bemutatja, hogyan hozhat létre [.net Core](/aspnet/core/) -alkalmazást a <abbr title="A Linuxon App Service a Linux operációs rendszert használó, jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt.">App Service Linux rendszeren</abbr>. Az alkalmazást az [Azure CLI](/cli/azure/get-started-with-azure-cli)-vel hozza létre, és a git használatával helyezi üzembe a .net Core-kódot az alkalmazásban.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

- Aktív előfizetéssel rendelkező **Azure-fiók beszerzése** . [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- **Telepítse** a legújabb <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.net Core 3,1 SDK</a> -t vagy a <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.net 5,0 SDK</a>-t.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Telepítse a legújabb Azure CLI</a>**-t.

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. az alkalmazás helyi létrehozása

1. Futtassa `mkdir hellodotnetcore` a parancsot a könyvtár létrehozásához.

    ```bash
    mkdir hellodotnetcore
    ```

1. Futtassa `cd hellodotnetcore` a parancsot a könyvtár módosításához. 

    ```bash
    cd hellodotnetcore
    ```

1. `dotnet new web`Új .net Core-alkalmazás létrehozásához futtassa a parancsot.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. az alkalmazás helyi futtatása

1. A futtatásával `dotnet run` megtekintheti, hogyan néz ki a rendszer az Azure-ba való üzembe helyezéskor.

    ```bash
    dotnet run
    ```
    
1. **Nyisson meg egy webböngészőt**, és navigáljon az alkalmazáshoz a következő címen: `http://localhost:5000` .

![Tesztelés böngészővel](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Jelentkezzen be az Azure-ba

`az login`Az Azure-ba való bejelentkezéshez futtassa a parancsot.

```azurecli
az login
```

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. az alkalmazás üzembe helyezése

1. **Futtatás** `az webapp up` a helyi mappában. **Cserélje le** az <app-Name> globálisan egyedi névre.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Hibaelhárítás</summary>
    <ul>
    <li>Ha a <code>az</code> parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a <a href="#1-prepare-your-environment">környezet előkészítése</a>című témakörben leírtak szerint.</li>
    <li>Cserélje le a <code>&lt;app-name&gt;</code> karaktert az összes Azure-beli egyedi névre ( <em> érvényes karakterek: <code>a-z</code> , <code>0-9</code> és <code>-</code> </em> ). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.</li>
    <li>Az <code>--sku F1</code> argumentum a webalkalmazást az ingyenes díjszabási szinten hozza létre. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához, amely óradíjat eredményez.</li>
    <li>Igény szerint megadhatja az argumentumot <code>--location &lt;location-name&gt;</code> , ahol az <code>&lt;location-name&gt;</code> egy elérhető Azure-régió. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> .</li>
    </ul>
    </details>
    
1. Várjon, amíg a parancs befejeződik. Néhány percet is igénybe vehet, és a következővel végződik: "az alkalmazás elindítható a http:// &lt; app-name &gt; . azurewebsites.net" címen.

    <details>
    <summary>Mi történik <code>az webapp up</code> ?</summary>
    <p>Az <code>az webapp up</code> parancs a következő műveleteket hajtja végre:</p>
    <ul>
    <li>Egy alapértelmezett erőforráscsoport létrehozása.</li>
    <li>Hozzon létre egy alapértelmezett App Service csomagot.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Hozzon létre egy app Service alkalmazást</a> a megadott névvel.</li>
    <li>A <a href="/azure/app-service/deploy-zip">zip telepíti</a> a fájlokat az aktuális munkakönyvtárból az alkalmazásba.</li>
    <li>A futtatása közben üzeneteket biztosít az erőforrás-létrehozással, a naplózással és a ZIP-telepítéssel kapcsolatban.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Példa az az WebApp up parancs kimenetére](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5,0](#tab/net50)

![Példa az az WebApp up parancs kimenetére](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. tallózással keresse meg az alkalmazást

**Tallózással keresse meg az üzembe helyezett alkalmazást** a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. a kód frissítése és újbóli üzembe helyezése

1. **Nyissa meg a _Startup. cs_ fájlt** a helyi könyvtárban. 

1. A metódus hívásában végezze el a szöveg **kis módosítását** `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Mentse a módosításokat**.

1. **Futtatás** `az webapp up` az újbóli üzembe helyezéshez:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Mi <code>az webapp up</code> Ez az idő?</summary>
    Amikor először futtatta a parancsot, a projekt gyökeréből mentette az alkalmazás nevét, az erőforráscsoportot és a App Service tervet <i>.</i> Amikor újra futtatja a projekt gyökeréből, a a <i>. Azure/config fájlban</i>mentett értékeket használja, észleli, hogy a app Service-erőforrások már léteznek, és újra végrehajtja a zip-telepítést.
    </details>
    
1. Az üzembe helyezés befejezése után **kattintson a frissítés** elemre a korábban megnyitott böngészőablakban.

    ![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. az új Azure-alkalmazás kezelése

1. Nyissa meg az <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. A bal oldali menüben kattintson a **app Services** elemre, majd kattintson az Azure-alkalmazás nevére.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Képernyőkép a App Services lapról, amely egy példaként kiválasztott Azure-alkalmazást mutat be.":::

1. Az Áttekintés oldalon olyan alapszintű felügyeleti feladatokat hajthat végre, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

    ![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. erőforrások törlése

**Futtatás** `az group delete --name myResourceGroup` az erőforráscsoport törlése.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: ASP.NET Core alkalmazás SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core alkalmazás konfigurálása](configure-language-dotnetcore.md)

::: zone-end
