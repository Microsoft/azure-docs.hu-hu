---
title: 'Rövid útmutató: ASP.NET webalkalmazás üzembe helyezése'
description: Ismerje meg, hogyan futtathat webalkalmazásokat a Azure App Service az első ASP.NET telepítéséhez.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762460"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Rövid útmutató: ASP.NET webalkalmazás üzembe helyezése

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe első ASP.NET webalkalmazását a [Azure App Service.](overview.md) App Service .NET-alkalmazások különböző verzióit támogatja, és nagymértékben skálázható, önjavítást támogató webes üzemeltetési szolgáltatást biztosít. ASP.NET webalkalmazások platformfüggetlenek, és Linux vagy Windows rendszeren is üzemeltetheti. Ha végzett, egy Azure-erőforráscsoporttal fog App Service üzemeltetési csomaggal és egy üzembe helyezett webalkalmazással App Service erőforráscsoporttal.

> [!TIP]
> A .NET Core 3.1 a .NET jelenlegi hosszú távú támogatása (LTS) kiadása. További információ: [.NET támogatási szabályzat.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

## <a name="prerequisites"></a>Előfeltételek

:::zone target="docs" pivot="development-environment-vs"

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019-ben</a> a ASP.NET **és webfejlesztési számítási feladatokkal.**

    Ha már telepítette a Visual Studio 2019-et:

    - Telepítse a legújabb frissítéseket a Visual Studio frissítések **keresése lehetőség**  >  **kiválasztásával.**
    - Adja hozzá a számítási feladatot a **Tools**  >  **Get Tools and Features (Eszközök és szolgáltatások lekérte) lehetőség kiválasztásával.**

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Az <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools</a> bővítmény.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Telepítse a legújabb .NET Core 3.1 SDK-t. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .NET 5.0 SDK-t. </a>

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Telepítse a .NET-keretrendszer 4.8 fejlesztői csomagot. </a>

> [!NOTE]
> Visual Studio Code platformfüggetlen, .NET-keretrendszer viszont nem. Ha a .NET-keretrendszer Code-ban fejleszt, fontolja meg egy Windows rendszerű gép Visual Studio a buildfüggőségek kielégítéséhez.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/dotnet)
- Az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI.</a>
- A .NET SDK (a futásidejűt és a CLI-t is tartalmazza).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Telepítse a legújabb .NET Core 3.1 SDK-t. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .NET 5.0 SDK-t. </a>

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .NET 5.0 SDK-t. </a> és <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> a .NET-keretrendszer 4.8 Fejlesztői csomag. </a>

> [!NOTE]
> A [.NET CLI](/dotnet/core/tools) azonban platformfüggetlen, .NET-keretrendszer nem. Ha .NET CLI-.NET-keretrendszer fejleszt, fontolja meg egy Windows rendszerű gép használatának a megfelelő buildfüggőségeket.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Nyissa Visual Studio, majd válassza **az Új projekt létrehozása lehetőséget.**
1. A **Create a new project (Új projekt létrehozása)** mezőben keresse meg és válassza ASP.NET Web Core App **(Web Core-alkalmazás létrehozása)** lehetőséget, majd kattintson a Next (Tovább) **gombra.**
1. Az **Új projekt konfigurálása mezőben adja** a _MyFirstAzureWebApp_ nevet az alkalmazásnak, majd válassza a Tovább **lehetőséget.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="A ASP.NET Core 3.1-webalkalmazás konfigurálása" border="true":::

1. Válassza **a .NET Core 3.1 (Hosszú távú támogatás) lehetőséget.**
1. Győződjön meg **arról, hogy a Hitelesítési típus** beállítása **Nincs.** Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – A Hitelesítés típusa beállításnál válassza a .NET Core 3.1 és a Nincs lehetőséget." border="true":::

1. A webalkalmazás Visual Studio válassza a **Hibakeresés** Indítás hibakeresés hibakeresés nélkül lehetőséget a  >   webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – A .NET Core 3.1 helyi tallózása" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Nyissa Visual Studio, majd válassza **az Új projekt létrehozása lehetőséget.**
1. A Create a new project (Új projekt **létrehozása) mezőben** keresse meg és válassza **ASP.NET Web Core App (Web Core-alkalmazás létrehozása)** lehetőséget, majd válassza a Next (Tovább) **lehetőséget.**
1. Az **Új projekt konfigurálása mezőben adja** a _MyFirstAzureWebApp_ nevet az alkalmazásnak, majd válassza a **Tovább lehetőséget.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – ASP.NET 5.0-s webalkalmazás konfigurálása." border="true":::

1. Válassza **a .NET Core 5.0 (aktuális) lehetőséget.**
1. Győződjön meg **arról, hogy a Hitelesítési típus** beállítása **Nincs.** Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – További információ a .NET Core 5.0 kiválasztásakor." border="true":::

1. A webalkalmazás Visual Studio válassza a **Hibakeresés** Indítás hibakeresés hibakeresés nélkül lehetőséget a  >   webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – ASP.NET Core 5.0 helyileg fut." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

1. Nyissa Visual Studio, majd válassza **az Új projekt létrehozása lehetőséget.**
1. Az **Új projekt létrehozása mezőben** keresse meg és válassza ASP.NET **Webalkalmazás (.NET-keretrendszer)** lehetőséget, majd kattintson a **Tovább gombra.**
1. Az **Új projekt konfigurálása mezőben adja** a _MyFirstAzureWebApp_ nevet az alkalmazásnak, majd válassza a **Létrehozás lehetőséget.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – A ASP.NET Framework 4.8-webalkalmazás konfigurálása." border="true":::

1. Válassza ki az **MVC sablont.**
1. Győződjön meg **arról, hogy a** Hitelesítés beállítása **Nincs hitelesítés.** Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – Válassza ki az MVC sablont." border="true":::

1. A webalkalmazás Visual Studio válassza a **Hibakeresés** indítás hibakeresése hibakeresés nélkül lehetőséget a  >   webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio – ASP.NET 4.8-as keretrendszer helyileg fut." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Hozzon létre egy _új mappát MyFirstAzureWebApp_ névvel, és nyissa meg az Visual Studio Code-ban. Nyissa meg <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">a Terminál</a> ablakot, és hozzon létre egy új .NET-webalkalmazást az [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) paranccsal.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> A jelző a projekt célkeretrendszeri `--target-framework-override` monikerének (TFM) szabad szöveges  helyettesítője, és nem garantálja, hogy a támogató sablon létezik vagy fordítható. A windowsos alkalmazások csak .NET-keretrendszer futtathatók.

---

Futtassa  helyileg az alkalmazást a Visual Studio Terminálból az [`dotnet run`](/dotnet/core/tools/dotnet-run) paranccsal.

```dotnetcli
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Az oldalon megjelenik a Core 3.1 ASP.NET alkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – futtassa helyileg a böngészőben a .NET Core 3.1-et." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Az oldalon megjelenik ASP.NET Core 5.0-webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – futtassa helyileg a böngészőben a .NET 5.0-t." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Az oldalon megjelenik a ASP.NET Framework 4.8 webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – futtassa helyileg a böngészőben a .NET 4.8-at." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Nyisson meg egy terminálablakot a gépen egy munkakönyvtárba. Hozzon létre egy új .NET-webalkalmazást az paranccsal, majd módosítsa a [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) könyvtárakat az újonnan létrehozott alkalmazásra.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> A jelző a projekt célkeretrendszeri `--target-framework-override` monikerének (TFM) szabad szöveges  helyettesítője, és nem garantálja, hogy a támogató sablon létezik vagy fordítható. Csak Windowson .NET-keretrendszer alkalmazásokat.

---

Ugyanabban a terminál-munkamenetben futtassa helyileg az alkalmazást az [`dotnet run`](/dotnet/core/tools/dotnet-run) paranccsal.

```dotnetcli
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Az oldalon megjelenik ASP.NET Core 3.1-webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 3.1 helyi böngészőben való használatával." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Az oldalon megjelenik ASP.NET Core 5.0-webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 5.0 a helyi böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Az oldalon megjelenik a ASP.NET Framework 4.8 webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – ASP.NET Framework 4.8 helyi böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>A webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service, amelybe közzéteheti az alkalmazást.

Az alkalmazás beállításának App Service a következőt fogja létrehozni:

- Egy új [erőforráscsoport,](../azure-resource-manager/management/overview.md#terminology) amely a szolgáltatás összes Azure-erőforrását tartalmazza.
- Egy új [üzemeltetési csomag,](overview-hosting-plans.md) amely meghatározza az alkalmazást üzemeltető webkiszolgálófarm helyét, méretét és funkcióit.

Kövesse az alábbi lépéseket a App Service létrehozásához és a webalkalmazás közzétételéhez:

:::zone target="docs" pivot="development-environment-vs"

1. A **Megoldáskezelő** kattintson a jobb gombbal a **MyFirstAzureWebApp** projektre, és válassza a **Publish (Közzététel) lehetőséget.**
1. A **Közzététel mezőben** válassza az **Azure,** majd a **Tovább lehetőséget.**

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – A webalkalmazás közzététele és az Azure megcélzott közzététele." border="true":::

1. A lehetőségek attól függnek, hogy már be van-e jelentkezve az Azure-ba, és hogy van-e Visual Studio azure-fiókhoz csatolt fiókkal. Válassza a **Fiók hozzáadása vagy a** **Bejelentkezés** lehetőséget az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – Válassza a Bejelentkezés az Azure-ba párbeszédpanel lehetőséget.":::

1. Válassza az **Adott cél** lehetőséget, **Azure App Service (Linux)** vagy **Azure App Service (Windows)** lehetőséget.

    > [!IMPORTANT]
    > A ASP.NET 4.8-as verzió megcélzásakor a **következőt fogja használni: Azure App Service (Windows).**

1. Az új példányok **jobb App Service válassza** a **+** lehetőséget.

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – App Service alkalmazás párbeszédpanel.":::

1. Az **Előfizetés** mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.
1. Az **Erőforráscsoport mezőben** válassza az Új **lehetőséget.** Az **Új erőforráscsoport neve mezőben adja** meg a *myResourceGroup nevet,* majd kattintson az **OK gombra.**
1. A **Csomag üzemeltetése beállításnál** válassza az Új **lehetőséget.**
1. A Hosting **Plan: Create new (Üzemeltetési csomag: Új** létrehozása) párbeszédpanelen adja meg a következő táblázatban megadott értékeket:

    | Beállítás          | Ajánlott érték          | Leírás                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Szolgáltatási csomag** | *MyFirstAzureWebAppPlan* | Az App Service-csomag neve.                                         |
    | **Hely**     | *Nyugat-Európa*            | Az adatközpont, ahol a webalkalmazást üzemeltetik.                           |
    | **Méret**         | *Ingyenes*                   | A [tarifacsomag][app-service-pricing-tier] meghatározza az üzemeltetési funkciókat. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Új üzemeltetési csomag létrehozása":::

1. A **Név alatt** adjon meg egy egyedi alkalmazásnevet, amely csak az érvényes karaktereket tartalmazza: , , és `a-z` `A-Z` `0-9` `-` . Elfogadhatja az automatikusan létrehozott egyedi nevet. A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve.
1. Az **Azure-erőforrások** létrehozásához válassza a Létrehozás lehetőséget.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – Alkalmazás-erőforrások létrehozása párbeszédpanel.":::

   Miután a varázsló befejeződött, az Azure-erőforrások létrejönnek, és készen áll a közzétételre.

1. A **varázsló befejezéséhez** válassza a Befejezés lehetőséget.
1. A Közzététel **lapon** válassza a Közzététel **lehetőséget.** Visual Studio, csomagokat hoz létre és tesz közzé az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Az oldalon megjelenik ASP.NET Core 3.1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 3.1-webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Az oldalon megjelenik ASP.NET Core 5.0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 5.0-webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Az oldalon megjelenik a ASP.NET Framework 4.8 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – ASP.NET Framework 4.8 webalkalmazás az Azure-ban.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

A webalkalmazás üzembe helyezése az Azure Tools Visual Studio használatával:

1. A Visual Studio kódban nyissa meg a [**Parancskatapaletta**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd>Shift P + <kbd>billentyűkombinációt.</kbd> + <kbd></kbd>
1. Keresse meg és válassza a "Azure App Service: Deploy to Web App" (Üzembe helyezés a webalkalmazásban) lehetőséget.
1. Válaszoljon az utasításokat a következőképpen:

    - Válassza *ki a MyFirstAzureWebApp* mappát az üzembe helyezéshez.
    - Amikor **a rendszer kéri, válassza** a Konfiguráció hozzáadása lehetőséget.
    - Ha a rendszer kéri, jelentkezzen be meglévő Azure-fiókjába.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Kód – Bejelentkezés az Azure-ba." border="true":::

    - Válassza ki az **előfizetését.**
    - Válassza **az Új webalkalmazás létrehozása... lehetőséget. Speciális**.
    - A **Adjon meg egy globálisan egyedi** nevet: , adjon meg egy egyedi nevet az Azure-ban ( érvényes *karakterek: `a-z` `0-9` `-` , és*). Jó minta a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.
    - Válassza **az Új erőforráscsoport létrehozása lehetőséget,** és adjon meg egy nevet( `myResourceGroup` ).
    - Amikor a rendszer rákérdez **a Select a runtime stack (Futásidejű verem kiválasztása) kérdésre:**
      - A *.NET Core 3.1-hez válassza* a **.NET Core 3.1 (LTS) lehetőséget**
      - A *.NET 5.0-ban válassza* a **.NET 5 lehetőséget.**
      - A *.NET-keretrendszer 4.8-as ASP.NET* **v4.8 lehetőséget**
    - Válasszon operációs rendszert (Windows vagy Linux).
        - A *.NET-keretrendszer 4.8 esetén* a Windows implicit módon lesz kiválasztva.
    - Válassza **az Új App Service létrehozása lehetőséget,** adjon meg egy nevet, majd válassza az **F1 ingyenes** [tarifacsomagot.][app-service-pricing-tier]
    - Válassza **a Kihagyás most lehetőséget** a Application Insights erőforráshoz.
    - Válasszon egy közeli helyet.

1. Amikor a közzététel befejeződött, válassza a **Webhely tallózása** lehetőséget az értesítésben, majd válassza **a** Megnyitás lehetőséget, amikor a rendszer erre kéri.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Az oldalon megjelenik ASP.NET Core 3.1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 3.1-webalkalmazás használata az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Az oldalon megjelenik ASP.NET Core 5.0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – ASP.NET Core 5.0-webalkalmazás használata az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Az oldalon megjelenik a ASP.NET Framework 4.8 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – ASP.NET Framework 4.8 webalkalmazás az Azure-ban.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Telepítse a kódot a helyi *MyFirstAzureWebApp könyvtárban* az [`az webapp up`](/cli/azure/webapp#az_webapp_up) paranccsal:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Ha a parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van az `az` [Előfeltételek alatt leírtak szerint.](#prerequisites)
- Cserélje le a helyére a teljes Azure-ban egyedi nevet ( érvényes `<app-name>` *karakterek: `a-z` , `0-9` és `-`*). Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.
- A `--sku F1` argumentum létrehozza a webalkalmazást az Ingyenes [tarifacsomagban.][app-service-pricing-tier]  Ezt az argumentumot kihagyva gyorsabb prémium szintet használhat, amely óránkénti költségeket használ.
- Cserélje le `<os>` a helyére a vagy `linux` a helyére a `windows` következőt: . A keretrendszer `windows` *4.8-as ASP.NET a* következőt kell használnia: .
- Igény szerint meg is használhatja a `--location <location-name>` argumentumot, ahol az egy elérhető `<location-name>` Azure-régió. Az Azure-fiókhoz elérhető régiók listáját az parancs futtatásával [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) kaphatja meg.

A parancs befejezése eltarthat néhány percig. A futtatás közben üzeneteket biztosít az erőforráscsoport, a App Service-csomag és az alkalmazás üzemeltetése, a naplózás konfigurálása, majd a ZIP üzembe helyezése végrehajtásához. Ezután egy üzenetet küld az alkalmazás URL-címével:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Nyisson meg egy webböngészőt, és navigáljon az URL-címre:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Az oldalon megjelenik ASP.NET Core 3.1 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 3.1-webalkalmazás használata az Azure-ban.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Az oldalon megjelenik ASP.NET Core 5.0 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – ASP.NET Core 5.0-webalkalmazás használata az Azure-ban.":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Az oldalon megjelenik a ASP.NET Framework 4.8 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI – ASP.NET Framework 4.8 webalkalmazás az Azure-ban.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

Kövesse az alábbi lépéseket a webalkalmazás frissítéséhez és ismételt üzembe lépéséhez:

:::zone target="docs" pivot="development-environment-vs"

1. A **Megoldáskezelő** a projekt alatt nyissa meg az *Index.cshtml et.*
1. Cserélje le az `<div>` első elemet a következő kódra:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Mentse a módosításokat.

1. Az Azure-ban való ismételt üzembe álláshoz kattintson a jobb gombbal a **MyFirstAzureWebApp** projektre a **Megoldáskezelő** közzététel **parancsra.**
1. A Közzététel **összegzése** lapon válassza a Közzététel **lehetőséget.**

    Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik a frissített ASP.NET Core 3.1-es webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – Frissített ASP.NET Core 3.1-webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Megjelenik a frissített ASP.NET Core 5.0-webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – Frissített ASP.NET Core 5.0-webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Az oldalon megjelenik a ASP.NET Framework 4.8 frissített webalkalmazása.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – Frissített ASP.NET Framework 4.8 webalkalmazás az Azure-ban.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Nyissa meg az *Index.cshtml-t*.
1. Cserélje le az `<div>` első elemet a következő kódra:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Mentse a módosításokat.

1. Nyissa meg Visual Studio code **side bar (Kódoldali sáv) menüt,** és válassza az **Azure** ikont a lehetőségek kibontásához.
1. Az **APP SERVICE csomópont alatt** bontsa ki az előfizetését, és kattintson a jobb gombbal a **MyFirstAzureWebApp elemre.**
1. Válassza a **Deploy to Web App... (Üzembe helyezés a webalkalmazásban... )** lehetőséget.
1. Amikor **a rendszer kéri,** válassza az Üzembe helyezés lehetőséget.
1. Amikor a közzététel befejeződött, válassza a **Webhely tallózása** lehetőséget az értesítésben, majd válassza **a** Megnyitás lehetőséget, amikor a rendszer erre kéri.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik a frissített ASP.NET Core 3.1-es webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – Frissített ASP.NET Core 3.1-webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Megjelenik a frissített ASP.NET Core 5.0-webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – Frissített ASP.NET Core 5.0-webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Az oldalon megjelenik a ASP.NET Framework 4.8 frissített webalkalmazása.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – Frissített ASP.NET Framework 4.8-webalkalmazás az Azure-ban.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

A helyi könyvtárban nyissa meg az *Index.cshtml* fájlt. Cserélje le az első `<div>` elemet:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Mentse a módosításokat, majd az paranccsal ismét üzembe kell `az webapp up` mentenie az alkalmazást:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 platformfüggetlen, az előző üzemelő példánytól függően a vagy `<os>` a `linux` `windows` helyett.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 platformfüggetlen, az előző üzemelő példánytól függően a vagy `<os>` a `linux` `windows` helyett.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

ASP.NET Framework 4.8 keretrendszer függőségekkel rendelkezik, és Windows rendszeren kell üzemeltetni.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Ha érdekli a .NET-alkalmazások Linuxon való üzemeltetése, fontolja meg a ASP.NET Frameworkről a [Core-ra ASP.NET áttelepítést.](/aspnet/core/migration/proper-to-2x)

---

Ez a parancs olyan értékeket használ, amelyek helyileg vannak gyorsítótárazva az *.azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a App Service tervet.

Az üzembe helyezés befejezése után váltson vissza az  alkalmazás tallózása lépésben megnyitott böngészőablakra, és kattintson a frissítés gombra.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Az oldalon megjelenik ASP.NET Core 3.1-es webalkalmazás frissített frissítése.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – Frissített ASP.NET Core 3.1-webalkalmazás az Azure-ban.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Az oldalon megjelenik ASP.NET Core 5.0-webalkalmazás frissített frissítése.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – Frissített ASP.NET Core 5.0-webalkalmazás az Azure-ban.":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Az oldalon megjelenik a ASP.NET Framework 4.8 frissített webalkalmazása.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – Frissítve ASP.NET Framework 4.8 webalkalmazás az Azure-ban.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez keresse meg a [Azure Portal,](https://portal.azure.com)majd keresse meg és válassza a **App Services.**

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal – Válassza App Services lehetőséget.":::

A **App Services** válassza ki a webalkalmazás nevét.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal – App Services egy kiválasztott példa webalkalmazással.":::

A **webalkalmazás** Áttekintés lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a tallózás, leállítás, indítás, újraindítás és törlés. A bal oldali menü további oldalakat biztosít az alkalmazás konfigurálásához.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal – App Service áttekintési oldal.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott és üzembe helyezett egy ASP.NET webalkalmazást a Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztathatja azt egy SQL Database:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core-alkalmazás használata SQL Database-adatbázissal](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [A ASP.NET Core 3.1-alkalmazás konfigurálása](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztathatja azt egy SQL Database:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core-alkalmazás használata SQL Database-adatbázissal](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [A ASP.NET Core 5.0-alkalmazás konfigurálása](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

A következő cikkből megtudhatja, hogyan hozhat létre .NET-keretrendszer alkalmazást, és hogyan csatlakoztathatja azt egy SQL Database:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET használata SQL Database-adatbázissal](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [A ASP.NET Framework alkalmazás konfigurálása](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
