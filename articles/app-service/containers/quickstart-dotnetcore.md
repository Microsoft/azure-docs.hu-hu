---
title: Az ASP.NET Core-alkalmazás létrehozása linuxon – az Azure App Service |} A Microsoft Docs
description: Percek alatt üzembe helyezheti az első .NET Core Hello World alkalmazását a Linuxon futó App Service-ben.
keywords: azure app service, webalkalmazás, dotnet, core, linux, oss
services: app-service
documentationCenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: da4d246e098751f650eb6315de3794ad957884d6
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543893"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>ASP.NET Core-alkalmazás létrehozása Linuxon futó App Service-ben

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. Az App Service-ben üzembe _Windows_, lásd: [ASP.NET Core-alkalmazás létrehozása az Azure-ban](../app-service-web-get-started-dotnet.md).
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez a rövid útmutató bemutatja, hogyan hozhat létre [.NET Core](https://docs.microsoft.com/aspnet/core/)-alkalmazást Linuxon futó App Service-ben. Létrehozhatja a a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), és a Git segítségével üzembe helyezheti a .NET Core-kódot az alkalmazást.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

A cikk lépéseit Mac, Windows vagy Linux rendszert futtató gépen is követheti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://www.microsoft.com/net/core/" target="_blank">A .NET Core telepítése</a>

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

Állítsa vissza a NuGet-csomagokat, és futtassa az alkalmazást.

```bash
dotnet run
```

Nyisson meg egy webböngészőt, majd keresse fel az alkalmazást a következő címen: `http://localhost:5000`.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World** üzenetnek kell megjelennie.

![Tesztelés böngészővel](media/quickstart-dotnetcore/dotnet-browse-local.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez. Inicializáljon egy Git-adattárat a .NET Core-projekthez.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Keresse meg az újonnan létrehozott alkalmazást. Cserélje le  _&lt;alkalmazásnév >_ az alkalmazás nevére.

```bash
http://<app name>.azurewebsites.net
```

Íme, mi az új alkalmazás hasonlóan kell kinéznie:

![Üres alkalmazás lap](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 22, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (18/18), done.
Writing objects: 100% (22/22), 51.21 KiB | 3.94 MiB/s, done.
Total 22 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '741f16d1db'.
remote: Generating deployment script.
remote: Project file path: ./hellodotnetcore.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: ...............................................................................................
remote:   Restoring packages for /home/site/repository/hellodotnetcore.csproj...
remote: ....................................
remote:   Installing System.Xml.XPath 4.0.1.
remote:   Installing System.Diagnostics.Tracing 4.1.0.
remote:   Installing System.Threading.Tasks.Extensions 4.0.0.
remote:   Installing System.Reflection.Emit.ILGeneration 4.0.1.
remote:   ...
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://cephalin-dotnetcore.scm.azurewebsites.net/cephalin-dotnetcore.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A .NET Core-mintakód fut az App Service Linux rendszeren egy beépített rendszerképpel rendelkező.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gratulálunk!** Elvégezte az első .NET Core-alkalmazás üzembe helyezését a Linuxon futó App Service-ben.

## <a name="update-and-redeploy-the-code"></a>A kód frissítése és ismételt üzembe helyezése

A helyi könyvtárban nyissa meg a _Startup.cs_ fájlt. Végezzen el egy kis módosítást a `context.Response.WriteAsync` metódushívás szövegében:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure master
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse azt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

Nyissa meg a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> kezelheti a létrehozott alkalmazást.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-dotnetcore/portal-app-service-list.png)

Az alkalmazás áttekintése oldal jelenik meg. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés. 

![Az App Service lap az Azure Portalon](media/quickstart-dotnetcore/portal-app-overview.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az ASP.NET Core-alkalmazás és SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Az ASP.NET Core-alkalmazás konfigurálása](configure-language-dotnetcore.md)
