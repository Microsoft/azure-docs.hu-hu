---
title: Ismerkedés a webjobs SDK-val
description: Bevezetés a webjobs SDK-val eseményvezérelt háttérbeli feldolgozásra. Ismerje meg, hogyan érheti el az Azure-szolgáltatások és a harmadik féltől származó szolgáltatások adatszolgáltatásait.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91652040"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK első lépései az eseményalapú háttérfeldolgozáshoz

Ez a cikk bemutatja, hogyan használható a Visual Studio 2019 egy Azure WebJobs SDK-projekt létrehozásához, helyi futtatásához, majd a [Azure app Service](overview.md)üzembe helyezéséhez. A webjobs SDK 3. x verziója a .NET Core és a .NET Framework Console-alkalmazásokat egyaránt támogatja. További információ a webjobs SDK [használatáról: az Azure WEBJOBS SDK használata eseményvezérelt háttér-feldolgozáshoz](webjobs-sdk-how-to.md).

Ebből a cikkből megtudhatja, hogyan telepítheti a webjobs-t .NET Core Console-alkalmazásként. A webjobs .NET-keretrendszerbeli konzol alkalmazásként történő üzembe helyezéséhez lásd: [webjobs as .NET-keretrendszer Console apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Ha érdekli a webjobs SDK 2. x verziója, amely csak a .NET-keretrendszert támogatja, lásd: [webjobs-feladatok fejlesztése és üzembe helyezése a Visual Studio-Azure app Service használatával](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a Visual Studio 2019](/visualstudio/install/) -et az **Azure-fejlesztési** számítási feladattal. Ha már rendelkezik a Visual Studióval, de nem rendelkezik ezzel a számítási feladattal, adja hozzá a számítási feladatot úgy, hogy az eszközök **> eszközök és szolgáltatások lekérése** lehetőséget

* A webjobs SDK-projekt Azure-ba való közzétételéhez [Azure-fiókkal](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) kell rendelkeznie.

## <a name="create-a-project"></a>Projekt létrehozása

1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget.

2. Válassza ki a **Console app (.net Core)** elemet.

3. Nevezze el a projekt *WebJobsSDKSample*, majd válassza a **Létrehozás** lehetőséget.

   ![New Project (Új projekt) párbeszédpanel](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Webjobs NuGet-csomagok

1. Telepítse a [ `Microsoft.Azure.WebJobs.Extensions` NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)legújabb, stabil 3. x verzióját, amely tartalmazza a-t `Microsoft.Azure.WebJobs` .

     A **Package Manager konzol** parancsa:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Ebben a parancsban cserélje le a parancsot a `<3_X_VERSION>` csomag egy támogatott verziójára. 

## <a name="create-the-host"></a>A gazdagép létrehozása

A gazdagép az eseményindítókat figyelő és a hívási funkciókat figyelő függvények futásidejű tárolója. A következő lépések olyan gazdagépet hoznak létre, amely megvalósítja [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , amely a ASP.net Core általános gazdagépe.

1. A *program. cs programban* adja hozzá a következő `using` utasításokat:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Cserélje le az `Main` metódust az alábbi kódra:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

ASP.NET Core a gazdagép konfigurációit a példány metódusának meghívásával állítja be a rendszer [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . További információ: [.net általános gazdagép](/aspnet/core/fundamentals/host/generic-host). A `ConfigureWebJobs` bővítményi metódus inicializálja a Webjobs-gazdagépet. A-ben az `ConfigureWebJobs` adott Webjobs-bővítmények inicializálása és a bővítmények tulajdonságainak beállítása.  

## <a name="enable-console-logging"></a>Konzol naplózásának engedélyezése

Ebben a szakaszban a [ASP.net Core naplózási keretrendszert](/aspnet/core/fundamentals/logging)használó konzol-naplózást állít be.

1. Telepítse a [ `Microsoft.Extensions.Logging.Console` NuGet-csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)legújabb stabil verzióját, amely tartalmazza a-t `Microsoft.Extensions.Logging` .

   A **Package Manager konzol** parancsa:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   Ebben a parancsban cserélje le a parancsot a `<3_X_VERSION>` csomag támogatott 3. x verziójára.

1. A *program. cs programban* adjon hozzá egy `using` utasítást:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Hívja [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) meg a metódust a következőn: [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . A [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) metódus hozzáadja a konzol naplózását a konfigurációhoz.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    A `Main` metódus így néz ki:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    A frissítés a következő műveleteket végzi el:

    * Letiltja az [irányítópultok naplózását](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Az irányítópult egy örökölt figyelési eszköz, és az irányítópultok naplózása nem ajánlott nagy átviteli sebességű éles környezetekben.
    * Hozzáadja a konzol szolgáltatóját az alapértelmezett [szűréshez](webjobs-sdk-how-to.md#log-filtering).

Most hozzáadhat egy olyan függvényt, amelyet az Azure Storage-várólistába érkező üzenetek indítottak el.

## <a name="install-the-storage-binding-extension"></a>A Storage-kötésbővítmény telepítése

A 3. x verziótól kezdődően explicit módon telepítenie kell a webjobs SDK által igényelt tárolási kötési bővítményt. A korábbi verziók esetében a tárolási kötések szerepeltek az SDK-ban.

1. Telepítse a  [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-csomag (3. x verzió) legújabb stabil verzióját. 

    A **Package Manager konzol** parancsa:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Ebben a parancsban cserélje le a parancsot a `<3_X_VERSION>` csomag egy támogatott verziójára. 

2. A `ConfigureWebJobs` bővítmény metódusban hívja meg a `AddAzureStorage` metódust a [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példányon a tárolási bővítmény inicializálásához. Ezen a ponton a `ConfigureWebJobs` metódus a következő példához hasonlóan néz ki:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Függvény létrehozása

1. Kattintson a jobb gombbal a projektre , válassza az  >  **új elem hozzáadása...** lehetőséget, válassza az **osztály** lehetőséget, nevezze el az új C# Class file *functions. cs* fájlt, és válassza a **Hozzáadás** lehetőséget.

1. A functions. cs kódban cserélje le a generált sablont a következő kódra:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   Az `QueueTrigger` attribútum arra utasítja a futtatókörnyezetet, hogy hívja meg ezt a függvényt, amikor egy új üzenet íródik egy nevű Azure Storage-várólistán `queue` . A várólista-üzenet tartalma a paraméterben található metódus kódjához van megadva `message` . A metódus törzse az, ahol feldolgozza az aktiváló adatmennyiséget. Ebben a példában a kód csak az üzenetet naplózza.

   A `message` paraméternek nem kell karakterláncnak lennie. Egy JSON-objektumhoz, egy byte-tömbhöz vagy egy [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) objektumhoz is köthető. [Lásd: üzenetsor-trigger használata](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Az egyes kötési típusok (például a várólisták, a Blobok vagy a táblák) különböző típusú paramétereket tartalmazhatnak, amelyekhez kötést hozhat létre.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A helyileg futó Azure Storage-emulátor nem rendelkezik a webjobs SDK által igényelt összes szolgáltatással. Ebben a szakaszban egy Storage-fiókot hoz létre az Azure-ban, és konfigurálja a projektet a használatára. Ha már rendelkezik Storage-fiókkal, ugorjon a 6. lépésre.

1. Nyissa meg a **Server Explorert** a Visual Studióban, és jelentkezzen be az Azure-ba. Kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Kapcsolódás Microsoft Azure előfizetéshez** lehetőséget.

   ![Bejelentkezés az Azure-ba](./media/webjobs-sdk-get-started/sign-in.png)

1. A **Server Explorer** **Azure** csomópontja alatt kattintson a jobb gombbal a **Storage** elemre, majd válassza a **Storage-fiók létrehozása** lehetőséget.

   ![Storage-fiók létrehozása menü](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. A **Storage-fiók létrehozása** párbeszédpanelen adjon meg egy egyedi nevet a Storage-fiók számára.

1. Válassza ki ugyanazt a **régiót** , amelyet Ön hozott létre a app Service alkalmazásban, vagy egy régiót a közelben.

1. Válassza a **Létrehozás** lehetőséget.

   ![Storage-fiók létrehozása](./media/webjobs-sdk-get-started/create-storage-account.png)

1. A **Server Explorer** **tároló** csomópontja alatt válassza ki az új Storage-fiókot. A **Properties (Tulajdonságok** ) ablakban válassza a **kapcsolatok karakterlánc** értéke mező jobb oldalán található három pontot (**..**.).

   ![A kapcsolatok karakterláncának három pontja](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Másolja ki a kapcsolatok karakterláncát, és mentse el valahol, hogy könnyen másolhatja.

   ![A kapcsolatok karakterláncának másolása](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>A tárterület konfigurálása helyileg történő futtatásra

A webjobs SDK a Storage-kapcsolatok karakterláncát keresi az Azure-beli Alkalmazásbeállítások között. Ha helyileg futtatja, a rendszer ezt az értéket a helyi konfigurációs fájlban vagy a környezeti változókban keresi.

1. Kattintson a jobb gombbal a projektre , válassza az  >  **új elem hozzáadása...** lehetőséget, válassza a **JavaScript JSON konfigurációs fájl** elemet, nevezze el az új fájlt *appsettings.js* fájlt, majd kattintson a **Hozzáadás** gombra. 

1. Az új fájlban adjon hozzá egy `AzureWebJobsStorage` mezőt a következő példában látható módon:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Cserélje le a *{Storage-beli kapcsolatok karakterláncát* a korábban átmásolt kapcsolatok karakterlánccá.

1. Válassza ki a fájl *appsettings.js* megoldáskezelő és a **Tulajdonságok** ablakban állítsa be a **Másolás a kimeneti könyvtárba** a másolás, **Ha újabb** lehetőséget.

Később felveszi az alkalmazásában a Azure App Service.

## <a name="test-locally"></a>Helyi tesztelés

Ebben a szakaszban helyileg hozza létre és futtatja a projektet, és egy üzenetsor-üzenet létrehozásával aktiválja a függvényt.

1. A projekt futtatásához nyomja le a **CTRL + F5** billentyűkombinációt.

   A konzolon látható, hogy a futtatókörnyezet megtalálta a függvényt, és várakozik az üzenetsor-üzenetek indítására. A következő kimenetet hozza létre a v3. x gazdagép:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Zárjuk be a konzolablak ablakát.

1. A Visual Studióban a **Server Explorerben** bontsa ki a csomópontot az új Storage-fiókhoz, majd kattintson a jobb gombbal a **várólisták** elemre.

1. Válassza a **várólista létrehozása** lehetőséget.

1. Adja *meg a várólista nevét* a várólista neveként, majd kattintson az **OK gombra**.

   ![Képernyőkép, amely megjeleníti a várólista létrehozásának helyét és a "várólista" nevét. ](./media/webjobs-sdk-get-started/create-queue.png)

1. Kattintson a jobb gombbal az új várólista csomópontjára, majd válassza a **várólista megtekintése** lehetőséget.

1. Válassza az **üzenet hozzáadása** ikont.

   ![Képernyőkép, amely kiemeli az üzenet hozzáadása ikont.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Az **üzenet hozzáadása** párbeszédpanelen adja meg a következőt: *Hello World!* az **üzenet szövegeként**, majd kattintson **az OK gombra**. Most már van egy üzenet a várólistában.

   ![Várólista létrehozása](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Futtassa ismét a projektet.

   Mivel az `QueueTrigger` attribútumot használta a `ProcessQueueMessage` függvényben, a WeJobs SDK futtatókörnyezet az indításkor figyeli az üzenetsor-üzeneteket. Egy új üzenetsor-üzenetet talál *a várólista nevű* várólistában, és meghívja a függvényt.

   A [várólista-lekérdezések exponenciális leállítási](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)miatt előfordulhat, hogy a futtatókörnyezet csak 2 percet vesz igénybe, hogy megkeresse az üzenetet, és meghívja a függvényt. Ez a várakozási idő a [fejlesztési módban](webjobs-sdk-how-to.md#host-development-settings)való futtatással csökkenthető.

   A konzol kimenete a következőképpen néz ki:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Zárjuk be a konzolablak ablakát. 

1. Térjen vissza a várólista ablakhoz, és frissítse azt. Az üzenet el lett mentve, mert a funkció a helyileg futó függvény által lett feldolgozva. 

## <a name="add-application-insights-logging"></a>Application Insights naplózás hozzáadása

Ha a projekt az Azure-ban fut, a konzol kimenetének megtekintésével nem figyelheti a funkciók végrehajtását. Az általunk javasolt figyelési megoldás [Application Insights](../azure-monitor/app/app-insights-overview.md). További információ: [Azure functions figyelése](../azure-functions/functions-monitoring.md).

Ebben a szakaszban a következő feladatokat végezheti el a Application Insights naplózás beállításához az Azure-ba való üzembe helyezés előtt:

* Győződjön meg arról, hogy rendelkezik App Service-alkalmazással és egy Application Insights-példánnyal a következővel való együttműködéshez.
* Konfigurálja úgy a App Service alkalmazást, hogy az Application Insights példányt és a korábban létrehozott Storage-fiókot használja.
* Állítsa be a projektet a Application Insights való naplózáshoz.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service alkalmazás és Application Insights példány létrehozása

1. Ha még nem rendelkezik olyan App Service alkalmazással, amelyet használhat, [hozzon létre egyet](quickstart-dotnet-framework.md). Az alkalmazás létrehozásakor létrehozhat egy csatlakoztatott Application Insights-erőforrást is. Ha ezt teszi, a `APPINSIGHTS_INSTRUMENTATIONKEY` be van állítva az alkalmazásban.

1. Ha még nem rendelkezik olyan Application Insights erőforrással, amelyet használhat, [hozzon létre egyet](../azure-monitor/app/create-new-resource.md ). Állítsa az **alkalmazás típusát** **általános** értékre, majd ugorja át a kialakítási **kulcs másolását** követő szakaszokat.

1. Ha már rendelkezik egy használni kívánt Application Insights erőforrással, [másolja a](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)kialakítási kulcsot.

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása 

1. A Visual Studióban a **Server Explorerben** bontsa ki a **app Service** csomópontot az **Azure** alatt.

1. Bontsa ki azt az erőforráscsoportot, amelyen a App Service alkalmazás található, majd kattintson a jobb gombbal a App Service alkalmazásra.

1. Válassza a **beállítások megtekintése** lehetőséget.

1. Adja hozzá a következő bejegyzést a **kapcsolatok karakterláncok** mezőben.

   |Name  |kapcsolatok karakterlánca  |Adatbázis típusa|
   |---------|---------|------|
   |AzureWebJobsStorage | {a korábban átmásolt tárolási kapcsolatok karakterlánca}|Egyéni|

1. Ha az **Alkalmazásbeállítások** mezőhöz nem tartozik Application Insights kialakítási kulcs, adja hozzá a korábban átmásolt eszközt. (A rendszerállapot-kulcs már ott is lehet, attól függően, hogyan hozta létre a App Service alkalmazást.)

   |Name  |Érték  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {Instrumentation-kulcs} |

1. Cserélje le a *{Instrumentation Key}* elemet a használt Application Insights erőforrás rendszerállapot-kulcsára.

1. Kattintson a **Mentés** gombra.

1. Adja hozzá a Application Insights-kapcsolódást a projekthez, hogy helyileg is futtatható legyen. A fájl *appsettings.jsban* vegyen fel egy `APPINSIGHTS_INSTRUMENTATIONKEY` mezőt a következő példában látható módon:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Cserélje le a *{Instrumentation Key}* elemet a használt Application Insights erőforrás rendszerállapot-kulcsára.

1. Mentse a módosításokat.

### <a name="add-application-insights-logging-provider"></a>Application Insights naplózási szolgáltató hozzáadása

A [Application Insights](../azure-monitor/app/app-insights-overview.md) naplózási funkciójának kihasználása érdekében frissítse a naplózási kódot a következő műveletekhez:

* Adjon hozzá egy Application Insights naplózási szolgáltatót alapértelmezett [szűréssel](webjobs-sdk-how-to.md#log-filtering). Helyileg futtatva az összes információ és a magasabb szintű naplók a konzolra és a Application Insightsra is írhatók.
* Helyezze a [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) objektumot egy `using` blokkba, így biztosítva, hogy a napló kimenete ki legyen ürítve, amikor a gazdagép kilép.

1. Telepítse a [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)legújabb stabil 3. x verzióját.

   A **Package Manager konzol** parancsa:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Ebben a parancsban cserélje le a parancsot a `<3_X_VERSION>` csomag egy támogatott verziójára.

1. Nyissa meg a *program. cs programot* , és cserélje le a metódus kódját a `Main` következő kódra:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Ezzel hozzáadja a Application Insights szolgáltatót a naplózáshoz, az alkalmazás beállításaihoz korábban hozzáadott kulcs használatával.

## <a name="test-application-insights-logging"></a>Application Insights naplózás tesztelése

Ebben a szakaszban ismét helyileg futtatja annak ellenőrzéséhez, hogy a naplózási adatai mostantól Application Insights és a-konzolon is elérhetők lesznek.

1. A Visual Studióban a **Server Explorer** használatával hozzon létre egy üzenetsor-üzenetet, mint [korábban](#test-locally), a *Hello app bepillantást* beírni. az üzenet szövegeként.

1. Futtassa a projektet.

   A webjobs SDK dolgozza fel az üzenetsor-üzenetet, és a naplók a konzol ablakában jelennek meg.

1. Zárjuk be a konzolablak ablakát.

1. A Application Insights erőforrás megtekintéséhez lépjen a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza ki a **Application Insights**.

1. Válassza ki Application Insights-példányát.

1. Válassza a **Keresés** lehetőséget.

   ![Keresés kiválasztása](./media/webjobs-sdk-get-started/select-search.png)

1. Ha nem látja a *Hello app bepillantást!* üzenetben válassza a **frissítés** rendszeres időközönként több percig lehetőséget. (A naplók nem jelennek meg azonnal, mert eltarthat egy ideig, amíg a Application Insights-ügyfél kiüríti az informatikai folyamatokat.)

   ![Naplók Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zárjuk be a konzolablak ablakát.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Üzembe helyezés az Azure-ban

Az üzembe helyezés során létre kell hoznia egy app Service-példányt, amelyben a függvények futtatására kerül sor. Ha egy .NET Core Console alkalmazást tesz közzé App Service az Azure-ban, automatikusan Webjobs fut. További információ a közzétételről: [webjobs-feladatok fejlesztése és üzembe helyezése a Visual Studióval](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>A függvény elindítása az Azure-ban

1. Győződjön meg arról, hogy nem helyileg fut (a konzol ablak bezárásával, ha még meg van nyitva). Ellenkező esetben előfordulhat, hogy a helyi példány a létrehozott üzenetsor-üzenetek feldolgozásának első lépései.

1. A Visual Studio **üzenetsor** lapján adjon hozzá egy üzenetet a várólistához, mint korábban.

1. Frissítse a **várólista** lapot, és az új üzenet eltűnik, mert az Azure-ban futó függvény feldolgozta.

   > [!TIP]
   > Az Azure-ban végzett tesztelés során a [fejlesztési mód](webjobs-sdk-how-to.md#host-development-settings) használatával győződjön meg arról, hogy a várólista-eseményindító függvényt azonnal meghívja, és elkerülje a késést a [várólista-lekérdezés exponenciális leállítási](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)miatt.

### <a name="view-logs-in-application-insights"></a>Naplók megtekintése Application Insights

1. Nyissa meg a [Azure Portal](https://portal.azure.com/), és lépjen a Application Insights-erőforráshoz.

1. Válassza a **Keresés** lehetőséget.

1. Ha nem látja a *Hello Azure* -t! üzenetben válassza a **frissítés** rendszeres időközönként több percig lehetőséget.

   Ekkor megjelenik a Webjobs futó függvény naplófájljai, beleértve a *Hello Azure* -t! az előző szakaszban megadott szöveg

## <a name="add-an-input-binding"></a>Bemeneti kötés hozzáadása

A bemeneti kötések leegyszerűsítik a kódot, amely adatokat olvas be. Ebben a példában a várólista-üzenet a blob neve lesz, és a blob neve alapján megkeresheti és beolvashatja a blobot az Azure Storage-ban.

1. A *functions. cs* kódban cserélje le a `ProcessQueueMessage` metódust a következő kódra:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Ebben a kódban `queueTrigger` a egy [kötési kifejezés](../azure-functions/functions-bindings-expressions-patterns.md), ami azt jelenti, hogy a rendszer egy másik értékre oldja fel futásidőben.  Futásidőben a várólista-üzenet tartalma jelenik meg.

1. Adja hozzá a `using` következőket:

   ```cs
   using System.IO;
   ```

1. Hozzon létre egy BLOB-tárolót a Storage-fiókban.

   a. A Visual Studióban a **Server Explorerben** bontsa ki a Storage-fiók csomópontját **, kattintson a** jobb gombbal a Blobok elemre, majd válassza a **blob-tároló létrehozása** parancsot.

   b. A **blob-tároló létrehozása** párbeszédpanelen írja be a *tároló* nevet a tároló neveként, majd kattintson az **OK** gombra.

1. Töltse fel a *program. cs* fájlt a blob-tárolóba. (Ezt a fájlt példaként használjuk, feltölthet bármilyen szövegfájlt, és létrehozhat egy üzenetsor-üzenetet a fájl nevével.)

   a. A **Server Explorerben** kattintson duplán a létrehozott tároló csomópontjára.

   b. A **tároló** ablakban válassza a **feltöltés** gombot.

   ![BLOB feltöltése gomb](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Keresse meg és válassza ki a *program. cs* elemet, majd kattintson **az OK gombra**.

1. Hozzon létre egy üzenetsor-üzenetet a korábban létrehozott várólistán a *program. cs* néven az üzenet szövegeként.

   ![Üzenetsor-üzenet program. cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. A projekt helyi futtatása.

   Az üzenetsor-üzenet elindítja a függvényt, amely ezután beolvassa a blobot, és naplózza a hosszát. A konzol kimenete a következőképpen néz ki:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A kimeneti kötések leegyszerűsítik a kódot, amely adatokat ír. Ez a példa úgy módosítja az előzőt, hogy a blob egy másolatát megírta a méretének naplózása helyett. A blob Storage-kötések a korábban telepített Azure Storage-bővítmény csomag részét képezik.

1. Cserélje le az `ProcessQueueMessage` metódust az alábbi kódra:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Hozzon létre egy másik üzenetsor-üzenetet a *program. cs* néven az üzenet szövegeként.

1. A projekt helyi futtatása.

   Az üzenetsor-üzenet elindítja a függvényt, amely ezután beolvassa a blobot, naplózza a hosszát, és létrehoz egy új blobot. A konzol kimenete azonos, de ha a blob-tároló ablakra lép, és a **frissítés** lehetőséget választja, megjelenik egy *copy-program. cs* nevű új blob.

## <a name="republish-the-updates-to-azure"></a>A frissítések ismételt közzététele az Azure-ban

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** párbeszédpanelen ellenőrizze, hogy az aktuális profil van-e kiválasztva, majd válassza a **Közzététel** lehetőséget. A közzététel eredményei a **kimenet** ablakban vannak részletezve.
 
1. Ellenőrizze a függvényt az Azure-ban, hogy ismét feltölt egy fájlt a blob-tárolóba, és hozzáad egy üzenetet a várólistához, amely a feltöltött fájl neve. Megjelenik az üzenet, amelyet a rendszer eltávolít a sorból, és a blob-tárolóban létrehozott fájl másolatát. 

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan hozhat létre, futtathat és helyezhet üzembe egy webjobs SDK 3. x projektet.

> [!div class="nextstepaction"]
> [További információ a webjobs SDK-ról](webjobs-sdk-how-to.md)