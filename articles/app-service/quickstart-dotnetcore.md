---
title: 'Gyors útmutató: ASP.NET-webalkalmazás üzembe helyezése'
description: Megtudhatja, hogyan futtathat webalkalmazásokat Azure App Service az első ASP.NET-alkalmazás üzembe helyezésével.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058251"
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

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Gyors útmutató: ASP.NET-webalkalmazás üzembe helyezése

Ebből a rövid útmutatóból megtudhatja, hogyan hozhatja létre és helyezheti üzembe az első ASP.NET-webalkalmazását [Azure app Service](overview.md). App Service támogatja a .NET-alkalmazások különböző verzióit, és egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. A ASP.NET Web Apps platformfüggetlen, és Linux vagy Windows rendszeren is üzemeltethető. Ha elkészült, egy App Service üzemeltetési csomagból és egy üzembe helyezett webalkalmazással rendelkező App Service álló Azure-erőforráscsoport lesz.

> [!TIP]
> A .NET Core 3,1 a .NET jelenlegi hosszú távú támogatás (LTS) kiadása. További információ: .net- [támogatási szabályzat](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Előfeltételek

:::zone target="docs" pivot="development-environment-vs"

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet).
- A <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> a **ASP.net és a webes fejlesztési** munkaterheléssel.

    Ha már telepítette a Visual Studio 2019-et:

    - A   >  **frissítések keresése** lehetőség kiválasztásával telepítse a legújabb frissítéseket a Visual Studióban.
    - A számítási feladat hozzáadásához **válassza az eszközök**  >  **beolvasása eszközök és szolgáltatások** lehetőséget.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- Az <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure Tools</a> bővítmény.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Telepítse a legújabb .NET Core 3,1 SDK-t. </a>

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .NET 5,0 SDK-t. </a>

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Telepítse a .NET-keretrendszer 4,8 Developer Pack csomagot. </a>

> [!NOTE]
> A Visual Studio Code platform platformfüggetlen, azonban a .NET-keretrendszer nem. Ha a .NET-keretrendszerbeli alkalmazásokat a Visual Studio Code-ban fejleszti, érdemes lehet Windows-gépet használni a létrehozási függőségek kielégítéséhez.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet).
- Az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>-vel.
- A .NET SDK (tartalmazza a futtatókörnyezetet és a CLI-t).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Telepítse a legújabb .NET Core 3,1 SDK-t. </a>

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .NET 5,0 SDK-t. </a>

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Telepítse a legújabb .net 5,0 SDK </a> -t. és <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> a .NET-keretrendszer 4,8 fejlesztői csomagja. </a>

> [!NOTE]
> A [.net parancssori](/dotnet/core/tools) felület platformfüggetlen, azonban a .NET-keretrendszer nem. Ha .net-keretrendszerbeli alkalmazásokat fejleszt a .NET parancssori felülettel, érdemes lehet Windows-gépet használni a létrehozási függőségek kielégítéséhez.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>ASP.NET-webapp létrehozása

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza a **ASP.net Web Core-alkalmazás** elemet, majd kattintson a **tovább** gombra.
1. Az **új projekt konfigurálása lapon** nevezze el az alkalmazás _MyFirstAzureWebApp_, majd kattintson a **tovább** gombra.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="ASP.NET Core 3,1-alapú webalkalmazás konfigurálása" border="true":::

1. Válassza a **.net Core 3,1 (hosszú távú támogatás)** lehetőséget.
1. Győződjön meg arról, hogy a **Hitelesítés típusa** **none** értékre van állítva. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – válassza a .NET Core 3,1 és a nincs lehetőséget a hitelesítési típushoz." border="true":::

1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – .NET Core 3,1 Tallózás helyileg" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen keresse meg és válassza a **ASP.net Web Core-alkalmazás** elemet, majd kattintson a **tovább** gombra.
1. Az **új projekt konfigurálása lapon** nevezze el az alkalmazás _MyFirstAzureWebApp_, majd kattintson a **tovább** gombra.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – ASP.NET 5,0-webalkalmazás konfigurálása." border="true":::

1. Válassza a **.net Core 5,0 (aktuális)** lehetőséget.
1. Győződjön meg arról, hogy a **Hitelesítés típusa** **none** értékre van állítva. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – további információ a .NET Core 5,0 kiválasztásakor." border="true":::

1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="A Visual Studio – ASP.NET Core 5,0 helyileg fut." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

1. Nyissa meg a Visual studiót, majd válassza **az új projekt létrehozása** lehetőséget.
1. Az **új projekt létrehozása** lapon keresse meg és válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **tovább** gombra.
1. Az **új projekt konfigurálása** lapon nevezze el az alkalmazás _MyFirstAzureWebApp_, majd válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – a ASP.NET-keretrendszer 4,8 webalkalmazásának konfigurálása." border="true":::

1. Válassza ki az **MVC** sablont.
1. Győződjön meg arról, hogy a **hitelesítés** beállítása **Nincs hitelesítés**. Válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – válassza ki az MVC sablont." border="true":::

1. A Visual Studio menüjében válassza a **hibakeresés**  >  **Indítás hibakeresés nélkül** lehetőséget a webalkalmazás helyi futtatásához.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="A Visual Studio-ASP.NET Framework 4,8 helyileg fut." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Hozzon létre egy _MyFirstAzureWebApp_ nevű új mappát, és nyissa meg a Visual Studio Code-ban. Nyissa meg a <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminál</a> ablakot, és hozzon létre egy új .net-webalkalmazást a [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) paranccsal.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> A `--target-framework-override` jelző a projekthez tartozó cél-keretrendszer moniker (TFM) szabadszöveges cseréje, amely *nem garantálja* , hogy a támogató sablon létezik vagy le van fordítva. .NET-keretrendszerbeli alkalmazásokat csak Windows rendszeren hozhat létre és futtathat.

---

A Visual Studio Code-ban lévő **terminálon** futtassa az alkalmazást helyileg a [`dotnet run`](/dotnet/core/tools/dotnet-run) parancs használatával.

```dotnetcli
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Ekkor megjelenik az oldalon megjelenő ASP.NET Core 3,1-es webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – a .NET Core 3,1 futtatása helyileg a böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Ekkor megjelenik az oldalon megjelenő ASP.NET Core 5,0-es webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – a .NET 5,0 futtatása helyileg a böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Ekkor megjelenik az oldalon megjelenő ASP.NET-keretrendszer 4,8-es webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – a .NET 4,8 futtatása helyileg a böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Nyisson meg egy terminál ablakot a gépen egy munkakönyvtárra. Hozzon létre egy új .NET-webalkalmazást a [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) paranccsal, majd módosítsa a címtárakat az újonnan létrehozott alkalmazásba.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> A `--target-framework-override` jelző a projekthez tartozó cél-keretrendszer moniker (TFM) szabadszöveges cseréje, amely *nem garantálja* , hogy a támogató sablon létezik vagy le van fordítva. Windows rendszeren csak .NET-keretrendszerbeli alkalmazásokat hozhat létre.

---

Ugyanabból a terminál-munkamenetből futtassa az alkalmazást helyileg a [`dotnet run`](/dotnet/core/tools/dotnet-run) parancs használatával.

```dotnetcli
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Ekkor megjelenik az oldalon megjelenő ASP.NET Core 3,1-es webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code-ASP.NET Core 3,1 a helyi böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Ekkor megjelenik az oldalon megjelenő ASP.NET Core 5,0-es webalkalmazás sablonja.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code-ASP.NET Core 5,0 a helyi böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Ekkor megjelenik az oldalon megjelenő ASP.NET-keretrendszer 4,8-es webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code-ASP.NET Framework 4,8 a helyi böngészőben." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Webalkalmazás közzététele

A webalkalmazás közzétételéhez először létre kell hoznia és konfigurálnia kell egy új App Service, amelyre közzé teheti az alkalmazást.

A App Service beállításának részeként a következőket fogja létrehozni:

- Egy új [erőforráscsoport](../azure-resource-manager/management/overview.md#terminology) , amely a szolgáltatáshoz tartozó összes Azure-erőforrást tartalmazza.
- Új [üzemeltetési csomag](overview-hosting-plans.md) , amely meghatározza az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és funkcióit.

Kövesse az alábbi lépéseket a App Service létrehozásához és a webalkalmazás közzétételéhez:

:::zone target="docs" pivot="development-environment-vs"

1. A **megoldáskezelő** kattintson a jobb gombbal a **MyFirstAzureWebApp** projektre, és válassza a **Közzététel** lehetőséget.
1. A **Közzététel** területen válassza az **Azure** lehetőséget, majd a **tovább** gombot.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – tegye közzé a webalkalmazást és az Azure célját." border="true":::

1. A lehetőségei attól függnek, hogy már bejelentkezett-e az Azure-ba, és hogy van-e egy Azure-fiókhoz társított Visual Studio-fiókja. Válassza a **fiók hozzáadása** lehetőséget, vagy **Jelentkezzen** be az Azure-előfizetésbe való bejelentkezéshez. Ha már bejelentkezett, válassza ki a kívánt fiókot.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – válassza a bejelentkezés az Azure-ba párbeszédpanelt.":::

1. Válassza ki az **adott célt**, vagy **Azure app Service (Linux)** vagy **Azure app Service (Windows)**.

    > [!IMPORTANT]
    > Az 4,8-es ASP.NET-keretrendszer célzásakor **Azure app Service (Windows) rendszert** kell használni.

1. **App Service példányok** jobb oldalán válassza a elemet **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – új App Service alkalmazás párbeszédpanel.":::

1. Az **előfizetés** mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.
1. Az **erőforráscsoport** területen válassza az **új** lehetőséget. Az **Új erőforráscsoport neve** mezőbe írja be a *myResourceGroup* nevet, majd kattintson **az OK gombra**.
1. **Üzemeltetési csomag** esetén válassza az **új** lehetőséget.
1. A **üzemeltetési tervben: hozzon létre új** párbeszédpanelt, és adja meg a következő táblázatban megadott értékeket:

    | Beállítás          | Ajánlott érték          | Leírás                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Szolgáltatási csomag** | *MyFirstAzureWebAppPlan* | Az App Service-csomag neve.                                         |
    | **Hely**     | *Nyugat-Európa*            | Az adatközpont, ahol a webalkalmazást üzemeltetik.                           |
    | **Méret**         | *Ingyenes*                   | A [tarifacsomag][app-service-pricing-tier] meghatározza az üzemeltetési funkciókat. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Új üzemeltetési csomag létrehozása":::

1. A **név** mezőben adjon meg egy egyedi nevet, amely csak az érvényes karaktereket tartalmazza:,, `a-z` `A-Z` `0-9` és `-` . Elfogadhatja az automatikusan létrehozott egyedi nevet is. A webalkalmazás URL-címe `http://<app-name>.azurewebsites.net`, amelyben az `<app-name>` az alkalmazás neve.
1. Válassza a **Létrehozás** lehetőséget az Azure-erőforrások létrehozásához.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – alkalmazás-erőforrások létrehozása párbeszédpanel.":::

   A varázsló befejezése után létrejön az Azure-erőforrások, és készen áll a közzétételre.

1. A varázsló bezárásához kattintson a **Befejezés** gombra.
1. A **közzétételi** lapon válassza a **Közzététel** lehetőséget. A Visual Studio létrehozza, becsomagolja és közzéteszi az alkalmazást az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik a lapon megjelenő ASP.NET Core 3,1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Megjelenik a lapon megjelenő ASP.NET Core 5,0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Megjelenik az oldalon megjelenő ASP.NET Framework 4,8 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio-ASP.NET Framework 4,8 Web App az Azure-ban.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

A webalkalmazás üzembe helyezése a Visual Studio Azure Tools bővítmény használatával:

1. A Visual Studio Code-ban nyissa meg a [**parancssort**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), majd a <kbd>CTRL</kbd> + <kbd>SHIFT</kbd> + <kbd>P</kbd>billentyűkombinációt.
1. Keresse meg és válassza ki a "Azure App Service: üzembe helyezés a webalkalmazásban" lehetőséget.
1. Válaszoljon a kérésekre az alábbiak szerint:

    - A központi telepítéshez válassza a *MyFirstAzureWebApp* mappát.
    - Ha a rendszer kéri, válassza a **konfiguráció hozzáadása** lehetőséget.
    - Ha a rendszer kéri, jelentkezzen be meglévő Azure-fiókjába.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code – jelentkezzen be az Azure-ba." border="true":::

    - Válassza ki az **előfizetését**.
    - Válassza az **új Webalkalmazás létrehozása... lehetőséget. Speciális**.
    - A **globálisan egyedi név megadása** esetén használjon az összes Azure-ban egyedi nevet (*érvényes karakterek:, `a-z` `0-9` és `-`*). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
    - Válassza az **Új erőforráscsoport létrehozása** lehetőséget, és adja meg a kívánt nevet `myResourceGroup` .
    - Amikor a rendszer kéri, hogy **válasszon ki egy futtatókörnyezetet**:
      - *.Net core 3,1* esetén válassza a **.net Core 3,1 (LTS) elemet.**
      - *.Net 5,0* esetén válassza a **.net 5** elemet.
      - A *.NET-keretrendszer 4,8*-es **verziójában válassza a ASP.net v 4.8** elemet.
    - Válasszon operációs rendszert (Windows vagy Linux).
        - A *.NET-keretrendszer 4,8* esetén a Windows implicit módon lesz kiválasztva.
    - Válassza az **új App Service terv létrehozása** lehetőséget, adjon meg egy nevet, és válassza ki az **F1 ingyenes** [díjszabási szintjét][app-service-pricing-tier].
    - Válassza a **kihagyás most** lehetőséget a Application Insights erőforráshoz.
    - Válasszon egy közeli helyet.

1. Ha a közzététel befejeződött, válassza az értesítésben a **Tallózás webhely** lehetőséget, majd a **megnyíló** gombra kattintva válassza a Megnyitás lehetőséget.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik a lapon megjelenő ASP.NET Core 3,1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code-ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Megjelenik a lapon megjelenő ASP.NET Core 5,0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code-ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Megjelenik az oldalon megjelenő ASP.NET Framework 4,8 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code-ASP.NET Framework 4,8 Web App az Azure-ban.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

A kód üzembe helyezése a helyi *MyFirstAzureWebApp* könyvtárban a [`az webapp up`](/cli/azure/webapp#az_webapp_up) parancs használatával:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Ha a `az` parancs nem ismerhető fel, ellenőrizze, hogy az Azure CLI telepítve van-e az [Előfeltételek](#prerequisites)szakaszban leírtak szerint.
- Cserélje le a `<app-name>` karaktert az összes Azure-beli egyedi névre (*érvényes karakterek: `a-z` , `0-9` és `-`*). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.
- Az `--sku F1` argumentum a webalkalmazást az **ingyenes** [díjszabási][app-service-pricing-tier]szinten hozza létre. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához, amely óradíjat eredményez.
- Cserélje le `<os>` a `linux` vagy a-t `windows` . Az 4,8-es `windows` *ASP.NET-keretrendszer* célzásához kell használnia.
- Igény szerint megadhatja az argumentumot `--location <location-name>` , ahol az `<location-name>` egy elérhető Azure-régió. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) .

A parancs végrehajtása több percet is igénybe vehet. A futtatása közben üzeneteket biztosít az erőforráscsoport létrehozásához, a App Service tervhez és az üzemeltetési alkalmazáshoz, a naplózás konfigurálásához, majd a ZIP-telepítés elindításához. Ezután egy üzenetet küld az alkalmazás URL-címével:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Nyisson meg egy webböngészőt, és navigáljon a következő URL-címre:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Megjelenik a lapon megjelenő ASP.NET Core 3,1 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Megjelenik a lapon megjelenő ASP.NET Core 5,0 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Megjelenik az oldalon megjelenő ASP.NET Framework 4,8 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="A CLI-ASP.NET keretrendszer 4,8 webalkalmazása az Azure-ban.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A webalkalmazás frissítéséhez és újbóli üzembe helyezéséhez kövesse az alábbi lépéseket:

:::zone target="docs" pivot="development-environment-vs"

1. **Megoldáskezelő** a projekt alatt nyissa meg az *index. cshtml*.
1. Cserélje le az első `<div>` elemet a következő kódra:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Mentse a módosításokat.

1. Az Azure-ba történő ismételt üzembe helyezéshez kattintson a jobb gombbal a **MyFirstAzureWebApp** projektre **megoldáskezelő** , és válassza a **Közzététel** lehetőséget.
1. Az összefoglalás **közzététele** lapon válassza a **Közzététel** lehetőséget.

    Miután a közzététel befejeződött, a Visual Studio tallózza a webalkalmazás URL-címét.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik az oldalon megjelenő frissített ASP.NET Core 3,1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – frissített ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Megjelenik az oldalon megjelenő frissített ASP.NET Core 5,0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – frissített ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Ekkor megjelenik a frissített ASP.NET Framework 4,8 webalkalmazás az oldalon.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – frissített ASP.NET Framework 4,8 Web App az Azure-ban.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Nyissa meg az *Index.cshtml-t*.
1. Cserélje le az első `<div>` elemet a következő kódra:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Mentse a módosításokat.

1. Nyissa meg a Visual Studio Code **oldali sávot**, és válassza ki az **Azure** ikont a lehetőségeinek kibontásához.
1. Az **app Service** csomópont alatt bontsa ki az előfizetést, és kattintson a jobb gombbal a **MyFirstAzureWebApp**.
1. Válassza az **üzembe helyezés a webalkalmazásban.**.. lehetőséget.
1. Ha a rendszer kéri, válassza a **telepítés** lehetőséget.
1. Ha a közzététel befejeződött, válassza az értesítésben a **Tallózás webhely** lehetőséget, majd a **megnyíló** gombra kattintva válassza a Megnyitás lehetőséget.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Megjelenik az oldalon megjelenő frissített ASP.NET Core 3,1 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – frissített ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

    ### <a name="net-50"></a>[.NET 5,0](#tab/net50)

    Megjelenik az oldalon megjelenő frissített ASP.NET Core 5,0 webalkalmazás.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – frissített ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

    ### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

    Ekkor megjelenik a frissített ASP.NET Framework 4,8 webalkalmazás az oldalon.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – frissített ASP.NET Framework 4,8 Web App az Azure-ban.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

A helyi könyvtárban nyissa meg az *index. cshtml* fájlt. Cserélje le az első `<div>` elemet:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Mentse a módosításokat, majd telepítse újra az alkalmazást a `az webapp up` parancs használatával:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

A ASP.NET Core 3,1 platformfüggetlen, a korábbi telepítéstől függően a vagy a értékre vált `<os>` `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

A ASP.NET Core 5,0 platformfüggetlen, a korábbi telepítéstől függően a vagy a értékre vált `<os>` `linux` `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

A ASP.NET Framework 4,8 keretrendszer-függőségekkel rendelkezik, és Windows rendszeren kell futnia.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Ha a .NET-alkalmazások Linux rendszeren való üzemeltetését érdekli, érdemes lehet áttelepíteni a [ASP.NET-keretrendszert a ASP.net Corera](/aspnet/core/migration/proper-to-2x).

---

Ez a parancs azokat az értékeket használja, amelyek helyileg vannak gyorsítótárazva a *. Azure/config* fájlban, beleértve az alkalmazás nevét, az erőforráscsoportot és a app Service tervet.

Az üzembe helyezés befejezése után váltson vissza a böngészőablakban megnyíló böngészőablakra, és kattintson a frissítés **gombra** .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Megjelenik az oldalon megjelenő frissített ASP.NET Core 3,1 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – frissített ASP.NET Core 3,1 webalkalmazás az Azure-ban.":::

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

Megjelenik az oldalon megjelenő frissített ASP.NET Core 5,0 webalkalmazás.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – frissített ASP.NET Core 5,0 webalkalmazás az Azure-ban.":::

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

Ekkor megjelenik a frissített ASP.NET Framework 4,8 webalkalmazás az oldalon.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – frissített ASP.NET Framework 4,8 Web App az Azure-ban.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

A webalkalmazás kezeléséhez nyissa meg a [Azure Portal](https://portal.azure.com), és keresse meg, majd válassza a **app Services** lehetőséget.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal – válassza a App Services lehetőséget.":::

A **app Services** lapon válassza ki a webalkalmazás nevét.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal – App Services oldalon egy példaként kiválasztott webalkalmazást.":::

A webalkalmazás **áttekintő** lapja olyan alapszintű felügyeleti lehetőségeket tartalmaz, mint a Tallózás, Leállítás, indítás, újraindítás és törlés. A bal oldali menü további lapokat biztosít az alkalmazás konfigurálásához.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal – App Service – Áttekintés lap.":::

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

Ebben a rövid útmutatóban létrehozott és telepített egy ASP.NET-webalkalmazást, hogy Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás az SQL Database-sel](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 3,1-alkalmazás konfigurálása](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5,0](#tab/net50)

A következő cikkből megtudhatja, hogyan hozhat létre .NET Core-alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET Core alkalmazás az SQL Database-sel](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 5,0-alkalmazás konfigurálása](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET-keretrendszer 4.8](#tab/netframework48)

A következő cikkből megtudhatja, hogyan hozhat létre .NET-keretrendszerbeli alkalmazást, és hogyan csatlakoztatható SQL Databasehoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: ASP.NET-alkalmazás és SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET-keretrendszer alkalmazás konfigurálása](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
