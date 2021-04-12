---
title: Profil ASP.NET Core Azure Linux-webalkalmazások és a Application Insights Profiler között | Microsoft Docs
description: A Application Insights Profiler használatának elméleti áttekintése és részletes oktatóanyaga.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4c208a80a1f701cc3e0c1c5cd08a999f2f15815e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889076"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Core Azure Linux-webalkalmazások és a Application Insights Profiler

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Megtudhatja, hogy mennyi idő telik el az élő webalkalmazás egyes módszereiben [Application Insights](./app-insights-overview.md)használatakor. A Application Insights Profiler mostantól elérhető ASP.NET Core webalkalmazásokhoz, amelyek a Azure App Service Linux rendszeren futnak. Ez az útmutató lépésről lépésre bemutatja, hogyan gyűjthetők össze a Profiler-Nyomkövetések ASP.NET Core linuxos webalkalmazásokhoz.

Miután elvégezte a bemutatót, az alkalmazás összegyűjthet Profiler-nyomkövetéseket, például a képen látható nyomkövetéseket. Ebben a példában a Profiler-nyomkövetés azt jelzi, hogy egy adott webes kérelem lassú a várakozási idő miatt. Az alkalmazást lelassító *gyors elérési utat* egy láng ikon jelöli. A **HomeController** szakasz **About** metódusa lelassítja a webalkalmazást, mert a metódus hívja a **Thread. Sleep** függvényt.

![Profiler-Nyomkövetések](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Előfeltételek
Az alábbi utasítások minden Windows-, Linux-és Mac-alapú fejlesztői környezetre érvényesek:

* Telepítse a [.net Core SDK 2.1.2 vagy újabb verziót](https://dotnet.microsoft.com/download/archives).
* Telepítse a git-t az [első lépések-install git telepítésével](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)kapcsolatos utasításokat követve.

## <a name="set-up-the-project-locally"></a>A projekt helyi beállítása

1. Nyisson meg egy parancssori ablakot a gépen. Az alábbi utasítások minden Windows-, Linux-és Mac-alapú fejlesztési környezetben működnek.

1. ASP.NET Core MVC-Webalkalmazás létrehozása:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Módosítsa a munkakönyvtárat a projekt gyökérkönyvtárára.

1. Adja hozzá a NuGet-csomagot a Profiler-Nyomkövetések gyűjtéséhez:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. A Application Insights és a Profiler engedélyezése a Startup. cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry(); // Add this line of code to enable Application Insights.
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adjon hozzá egy sort a **HomeController. cs** szakaszban, hogy véletlenszerűen késleltess néhány másodpercet:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Mentse és véglegesítse a módosításokat a helyi tárházban:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>A Linux-Webalkalmazás létrehozása a projekt üzemeltetéséhez

1. A webalkalmazás-környezet létrehozása a Linux App Service használatával:

    ![A linuxos webes alkalmazás létrehozása](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. A központi telepítési hitelesítő adatok létrehozása:

    > [!NOTE]
    > Jegyezze fel a jelszavát, hogy később használhassa a webalkalmazást.

    ![A központi telepítési hitelesítő adatok létrehozása](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Válassza ki a központi telepítési beállításokat. Állítsa be a helyi git-tárházat a webalkalmazásban a Azure Portal utasításait követve. A rendszer automatikusan létrehoz egy git-tárházat.

    ![A git-tárház beállítása](./media/profiler-aspnetcore-linux/setup-git-repo.png)

További üzembe helyezési lehetőségek: [app Service dokumentáció](../../app-service/index.yml).

## <a name="deploy-your-project"></a>A projekt üzembe helyezése

1. A Parancssor ablakban keresse meg a projekt gyökérkönyvtárát. Vegyen fel egy git távoli tárházat, amely a App Service adattárára mutat:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Használja az üzembe helyezési hitelesítő adatok létrehozásához használt **felhasználónevet** .
    * Használja az **alkalmazás nevét** , amelyet a webalkalmazás létrehozásához használt a Linux app Service használatával.

2. A projekt üzembe helyezéséhez hajtsa végre a módosításokat az Azure-ban:

    ```console
    git push azure main
    ```

    Az alábbi példához hasonló kimenetnek kell megjelennie:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'main'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Application Insights hozzáadása a webalkalmazások figyeléséhez

1. [Hozzon létre egy Application Insights erőforrást](./create-new-resource.md).

2. Másolja a Application Insights erőforrás **rendszerállapotkulcsot** értékét, és állítsa be a következő beállításokat a webalkalmazásokban:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Az Alkalmazásbeállítások megváltozásakor a hely automatikusan újraindul. Az új beállítások alkalmazása után a Profiler azonnal két percig fut. A Profiler ezután óránként két percig fut.

3. A webhelyre irányuló adatforgalom előállítása. A forgalmat úgy is létrehozhatja **, hogy néhányszor frissít a webhelyet.**

4. Várjon kettő – öt percet, amíg az események összesítve Application Insights.

5. Keresse meg a Azure Portal Application Insights **teljesítmény** ablaktábláját. A Profiler-nyomkövetéseket a panel jobb alsó sarkában tekintheti meg.

    ![Profiler-Nyomkövetések megtekintése](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Következő lépések
Ha Azure App Service által üzemeltetett egyéni tárolókat használ, a Application Insights Profiler engedélyezéséhez kövesse az [ Service Profiler engedélyezése a tároló ASP.net Core alkalmazás számára](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) című témakör utasításait.

Jelentse be a Application Insights GitHub-adattárral kapcsolatos problémákat vagy javaslatokat: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
