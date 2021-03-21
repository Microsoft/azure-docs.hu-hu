---
title: ASP.NET-Webalkalmazás létrehozása az Azure cache Redis
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre ASP.NET-webalkalmazást az Azure cache használatával a Redis
author: yegu-ms
ms.service: cache
ms.topic: quickstart
ms.date: 09/29/2020
ms.author: yegu
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 19c54ad62e45ecf6e31b46d0291f61dca8e8d9b3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722463"
---
# <a name="quickstart-use-azure-cache-for-redis-with-an-aspnet-web-app"></a>Gyors útmutató: az Azure cache használata a Redis ASP.NET-webalkalmazással 

Ebben a rövid útmutatóban a Visual Studio 2019 segítségével hozhat létre olyan ASP.NET-webalkalmazást, amely az Azure cache-hez csatlakozik a Redis-hoz az adatok tárolásához és lekéréséhez a gyorsítótárból. Ezután központilag telepíti az alkalmazást Azure App Service.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a [ASP.net](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/aspnet) rövid útmutatóját a githubon.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
- A [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.net, valamint a webes fejlesztési** és az **Azure-fejlesztési** számítási feladatokkal.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. Nyissa meg a Visual studiót, majd válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

2. A **create a New Project (új projekt létrehozása** ) párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Projekt létrehozása](./media/cache-web-app-howto/cache-create-project.png)

    a. A keresőmezőbe írja be a _következőt: C# ASP.net-webalkalmazás_.

    b. Válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** elemet.

    c. Válassza a **Tovább** gombot.

3. A **projekt neve** mezőben adja meg a projekt nevét. Ebben a példában a **ContosoTeamStats** nevet használtunk.

4. Ellenőrizze, hogy be van-e jelölve a **.NET-keretrendszer 4.6.1** vagy újabb verziója.

5. Válassza a **Létrehozás** lehetőséget.
   
6. A projekt típusaként válassza az **MVC** lehetőséget.

7. Ügyeljen arra, hogy az **Authentication** (Hitelesítés) beállításai között a **No Authentication** (Nincs hitelesítés) legyen megadva. A Visual Studio verziójától függően az **Authentication** (Hitelesítés) alapértelmezett beállítása lehet más. A beállítás módosításához kattintson a **Change Authentication** (Hitelesítés módosítása) gombra, és válassza a **No Authentication** (Nincs hitelesítés) lehetőséget.

8. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása

A következő lépésben létrehozza az alkalmazás gyorsítótárát.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

#### <a name="to-edit-the-cachesecretsconfig-file"></a>A *CacheSecrets.config* fájl módosítása

1. Hozzon létre egy fájlt a *CacheSecrets.config* nevű számítógépen. Helyezze olyan helyre, ahol a minta alkalmazás forráskódja nem lesz bejelentkezve. Ebben a rövid útmutatóban a *CacheSecrets.config* fájl a következő helyen található: *C:\AppSecrets\CacheSecrets.config*.

1. Szerkessze a *CacheSecrets.config* fájlt. Ezután adja hozzá a következő tartalmat:

    ```xml
    <appSettings>
        <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
    </appSettings>
    ```

1. A `<cache-name>` karakterláncot cserélje le a gyorsítótár gazdagépének nevére.

1. Az `<access-key>` karakterláncot cserélje le a gyorsítótár elsődleges kulcsára.

    > [!TIP]
    > A másodlagos hozzáférési kulcsot a kulcscsere során használhatja alternatív kulcsként, amíg Ön újragenerálja az elsődleges hozzáférési kulcsot.
   >
1. Mentse a fájlt.

## <a name="update-the-mvc-application"></a>Az MVC alkalmazás frissítése

Ebben a szakaszban frissíti az alkalmazást egy olyan új nézet támogatásához, amely egyszerű tesztet jelenít meg a Redis készült Azure cache-ben.

* [A web.config fájl frissítése egy gyorsítótárhoz tartozó alkalmazásbeállítással](#update-the-webconfig-file-with-an-app-setting-for-the-cache)
* Az alkalmazás konfigurálása a StackExchange.Redis ügyfél használatára
* A HomeController és az elrendezés frissítése
* Új RedisCache nézet hozzáadása

### <a name="update-the-webconfig-file-with-an-app-setting-for-the-cache"></a>A web.config fájl frissítése egy gyorsítótárhoz tartozó alkalmazásbeállítással

Amikor helyileg futtatja az alkalmazást, a *CacheSecrets.configban* található információk az Azure cache Redis-példányhoz való kapcsolódásához használatosak. Később ezt az alkalmazást fogja üzembe helyezni az Azure-ban. Ekkor konfigurálni fog egy alkalmazásbeállítást az Azure-ban, amelyet az alkalmazás a fájl helyett fog használni a gyorsítótár kapcsolati információinak lekéréséhez. 

Mivel a *CacheSecrets.config* fájl nem lesz üzembe helyezve az Azure-on az alkalmazással együtt, csak akkor fogja használni, amikor helyben teszteli az alkalmazást. Ezt az információt a lehető legbiztonságosabb módon kell tárolni, hogy mások ne férhessenek hozzá károkozási szándékkal a gyorsítótárazott adatokhoz.

#### <a name="to-update-the-webconfig-file"></a>A *web.config* fájl frissítése
1. A **Megoldáskezelőben** nyissa meg duplán a *web.config* fájlt dupla kattintással.

    ![Web.config](./media/cache-web-app-howto/cache-web-config.png)

2. A *web.config* fájlban keresse meg az `<appSetting>` elemet. Ezután adja hozzá a következő `file` attribútumot. Ha más fájlnevet vagy helyet használ, cserélje le a példában látható értékeket.

* Előtte: `<appSettings>`
* Után  `<appSettings file="C:\AppSecrets\CacheSecrets.config">`

Az ASP.NET futtatási környezet a külső fájl tartalmát egyesíti az `<appSettings>` elem kódjával. Ha a megadott fájl nem található, a futtatási környezet figyelmen kívül hagyja a fájlattribútumot. A titkos kulcsok (a gyorsítótárhoz tartozó kapcsolati sztringek) nem képezik részét az alkalmazás forráskódjának. Amikor üzembe helyezi a webalkalmazást az Azure-ban, a *CacheSecrets.config* fájl nincs telepítve.

### <a name="to-configure-the-application-to-use-stackexchangeredis"></a>Az alkalmazás konfigurálása a StackExchange.Redis használatára

1. Ha konfigurálni kívánja az alkalmazást a Visual Studióhoz készült [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) NuGet-csomag használatára, válassza az **Eszközök > NuGet-csomagkezelő > Csomagkezelő konzol** elemet.

2. Futtassa az alábbi parancsot a `Package Manager Console` ablakából:

    ```powershell
    Install-Package StackExchange.Redis
    ```

3. A NuGet-csomag letölti és hozzáadja az ügyfélalkalmazás számára szükséges szerelvény-hivatkozásokat az Azure cache Redis való eléréséhez a StackExchange. Azure cache Redis-ügyfélhez való hozzáféréséhez. Ha inkább a `StackExchange.Redis` ügyfélkönyvtár erős elnevezésű verzióját kívánja használni, telepítse a `StackExchange.Redis.StrongName` csomagot.

### <a name="to-update-the-homecontroller-and-layout"></a>A HomeController és az elrendezés frissítése

1. A **Megoldáskezelőben** bontsa ki a **Controllers** (Vezérlők) mappát, majd nyissa meg a *HomeController.cs* fájlt.

2. Adja hozzá a következő `using` utasításokat a fájl elejéhez.

    ```csharp
    using StackExchange.Redis;
    using System.Configuration;
    using System.Net.Sockets;
    using System.Text;
    using System.Threading;
    ```

3. Adja hozzá a következő tagokat az `HomeController` osztályhoz egy új `RedisCache` művelet támogatásához, amely bizonyos parancsokat futtat az új gyorsítótáron.

    ```csharp
    public ActionResult RedisCache()
    {
        ViewBag.Message = "A simple example with Azure Cache for Redis on ASP.NET.";

        IDatabase cache = GetDatabase();

        // Perform cache operations using the cache object...

        // Simple PING command
        ViewBag.command1 = "PING";
        ViewBag.command1Result = cache.Execute(ViewBag.command1).ToString();

        // Simple get and put of integral data types into the cache
        ViewBag.command2 = "GET Message";
        ViewBag.command2Result = cache.StringGet("Message").ToString();

        ViewBag.command3 = "SET Message \"Hello! The cache is working from ASP.NET!\"";
        ViewBag.command3Result = cache.StringSet("Message", "Hello! The cache is working from ASP.NET!").ToString();

        // Demonstrate "SET Message" executed as expected...
        ViewBag.command4 = "GET Message";
        ViewBag.command4Result = cache.StringGet("Message").ToString();

        // Get the client list, useful to see if connection list is growing...
        // Note that this requires allowAdmin=true in the connection string
        ViewBag.command5 = "CLIENT LIST";
        StringBuilder sb = new StringBuilder();
        var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
        IServer server = GetServer(endpoint.Host, endpoint.Port);
        ClientInfo[] clients = server.ClientList();

        sb.AppendLine("Cache response :");
        foreach (ClientInfo client in clients)
        {
            sb.AppendLine(client.Raw);
        }

        ViewBag.command5Result = sb.ToString();

        return View();
    }

    private static long lastReconnectTicks = DateTimeOffset.MinValue.UtcTicks;
    private static DateTimeOffset firstErrorTime = DateTimeOffset.MinValue;
    private static DateTimeOffset previousErrorTime = DateTimeOffset.MinValue;

    private static readonly object reconnectLock = new object();

    // In general, let StackExchange.Redis handle most reconnects,
    // so limit the frequency of how often ForceReconnect() will
    // actually reconnect.
    public static TimeSpan ReconnectMinFrequency => TimeSpan.FromSeconds(60);

    // If errors continue for longer than the below threshold, then the
    // multiplexer seems to not be reconnecting, so ForceReconnect() will
    // re-create the multiplexer.
    public static TimeSpan ReconnectErrorThreshold => TimeSpan.FromSeconds(30);

    public static int RetryMaxAttempts => 5;

    private static Lazy<ConnectionMultiplexer> lazyConnection = CreateConnection();

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

    private static Lazy<ConnectionMultiplexer> CreateConnection()
    {
        return new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    }

    private static void CloseConnection(Lazy<ConnectionMultiplexer> oldConnection)
    {
        if (oldConnection == null)
            return;

        try
        {
            oldConnection.Value.Close();
        }
        catch (Exception)
        {
            // Example error condition: if accessing oldConnection.Value causes a connection attempt and that fails.
        }
    }

    /// <summary>
    /// Force a new ConnectionMultiplexer to be created.
    /// NOTES:
    ///     1. Users of the ConnectionMultiplexer MUST handle ObjectDisposedExceptions, which can now happen as a result of calling ForceReconnect().
    ///     2. Don't call ForceReconnect for Timeouts, just for RedisConnectionExceptions or SocketExceptions.
    ///     3. Call this method every time you see a connection exception. The code will:
    ///         a. wait to reconnect for at least the "ReconnectErrorThreshold" time of repeated errors before actually reconnecting
    ///         b. not reconnect more frequently than configured in "ReconnectMinFrequency"
    /// </summary>
    public static void ForceReconnect()
    {
        var utcNow = DateTimeOffset.UtcNow;
        long previousTicks = Interlocked.Read(ref lastReconnectTicks);
        var previousReconnectTime = new DateTimeOffset(previousTicks, TimeSpan.Zero);
        TimeSpan elapsedSinceLastReconnect = utcNow - previousReconnectTime;

        // If multiple threads call ForceReconnect at the same time, we only want to honor one of them.
        if (elapsedSinceLastReconnect < ReconnectMinFrequency)
            return;

        lock (reconnectLock)
        {
            utcNow = DateTimeOffset.UtcNow;
            elapsedSinceLastReconnect = utcNow - previousReconnectTime;

            if (firstErrorTime == DateTimeOffset.MinValue)
            {
                // We haven't seen an error since last reconnect, so set initial values.
                firstErrorTime = utcNow;
                previousErrorTime = utcNow;
                return;
            }

            if (elapsedSinceLastReconnect < ReconnectMinFrequency)
                return; // Some other thread made it through the check and the lock, so nothing to do.

            TimeSpan elapsedSinceFirstError = utcNow - firstErrorTime;
            TimeSpan elapsedSinceMostRecentError = utcNow - previousErrorTime;

            bool shouldReconnect =
                elapsedSinceFirstError >= ReconnectErrorThreshold // Make sure we gave the multiplexer enough time to reconnect on its own if it could.
                && elapsedSinceMostRecentError <= ReconnectErrorThreshold; // Make sure we aren't working on stale data (e.g. if there was a gap in errors, don't reconnect yet).

            // Update the previousErrorTime timestamp to be now (e.g. this reconnect request).
            previousErrorTime = utcNow;

            if (!shouldReconnect)
                return;

            firstErrorTime = DateTimeOffset.MinValue;
            previousErrorTime = DateTimeOffset.MinValue;

            Lazy<ConnectionMultiplexer> oldConnection = lazyConnection;
            CloseConnection(oldConnection);
            lazyConnection = CreateConnection();
            Interlocked.Exchange(ref lastReconnectTicks, utcNow.UtcTicks);
        }
    }

    // In real applications, consider using a framework such as
    // Polly to make it easier to customize the retry approach.
    private static T BasicRetry<T>(Func<T> func)
    {
        int reconnectRetry = 0;
        int disposedRetry = 0;

        while (true)
        {
            try
            {
                return func();
            }
            catch (Exception ex) when (ex is RedisConnectionException || ex is SocketException)
            {
                reconnectRetry++;
                if (reconnectRetry > RetryMaxAttempts)
                    throw;
                ForceReconnect();
            }
            catch (ObjectDisposedException)
            {
                disposedRetry++;
                if (disposedRetry > RetryMaxAttempts)
                    throw;
            }
        }
    }

    public static IDatabase GetDatabase()
    {
        return BasicRetry(() => Connection.GetDatabase());
    }

    public static System.Net.EndPoint[] GetEndPoints()
    {
        return BasicRetry(() => Connection.GetEndPoints());
    }

    public static IServer GetServer(string host, int port)
    {
        return BasicRetry(() => Connection.GetServer(host, port));
    }
    ```

4. A **Megoldáskezelőben** bontsa ki a **Views** (Nézetek)  > **Shared** (Közös) mappát. Ezután nyissa meg a *_Layout.cshtml* fájlt.

    Cserélje le ezt:
    
    ```csharp
    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

    a következőre:

    ```csharp
    @Html.ActionLink("Azure Cache for Redis Test", "RedisCache", "Home", new { area = "" }, new { @class = "navbar-brand" })
    ```

### <a name="to-add-a-new-rediscache-view"></a>Új RedisCache-nézet hozzáadása

1. A **Megoldáskezelőben** bontsa ki a **Nézetek** mappát, majd kattintson a jobb gombbal a **Kezdőmappára**. Válassza a nézet **hozzáadása**  >  **...** lehetőséget.

2. A **Nézet hozzáadása** párbeszédpanelen a Nézet neve mezőben adja meg a **RedisCache** nevet. Ezután válassza a **Hozzáadás** elemet.

3. Cserélje le a *RedisCache.cshtml* fájlban lévő kódot az alábbira:

    ```csharp
    @{
        ViewBag.Title = "Azure Cache for Redis Test";
    }

    <h2>@ViewBag.Title.</h2>
    <h3>@ViewBag.Message</h3>
    <br /><br />
    <table border="1" cellpadding="10">
        <tr>
            <th>Command</th>
            <th>Result</th>
        </tr>
        <tr>
            <td>@ViewBag.command1</td>
            <td><pre>@ViewBag.command1Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command2</td>
            <td><pre>@ViewBag.command2Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command3</td>
            <td><pre>@ViewBag.command3Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command4</td>
            <td><pre>@ViewBag.command4Result</pre></td>
        </tr>
        <tr>
            <td>@ViewBag.command5</td>
            <td><pre>@ViewBag.command5Result</pre></td>
        </tr>
    </table>
    ```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Alapértelmezés szerint a projekt úgy van konfigurálva, hogy a teszteléshez és a hibakereséshez [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) helyileg tárolja az alkalmazást.

### <a name="to-run-the-app-locally"></a>Az alkalmazás futtatása helyben
1. A Visual Studióban válassza a **hibakeresés**  >  **indításkor** lehetőséget, hogy a teszteléshez és a hibakereséshez helyileg hozza létre és indítsa el az alkalmazást.

2. A böngészőben válassza az **Azure cache lehetőséget a Redis teszteléséhez** a navigációs sávon.

3. A következő példában a `Message` kulcs korábban egy gyorsítótárazott értékkel rendelkezett, amelyet a portálon a Redis-konzol Azure cache szolgáltatásával állítottak be. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

    ![Egyszerű teszt eredménye – helyi](./media/cache-web-app-howto/cache-simple-test-complete-local.png)

## <a name="publish-and-run-in-azure"></a>Közzététel és futtatás az Azure-ban

Az alkalmazás sikeres helyi tesztelése után az Azure-beli üzembe helyezés és a felhőben történő futtatás következik.

### <a name="to-publish-the-app-to-azure"></a>Az alkalmazás közzététele az Azure-ban

1. A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektcsomópontra. Ezután válassza a **Publish** (Közzététel) lehetőséget.

    ![Közzététel](./media/cache-web-app-howto/cache-publish-app.png)

2. Válassza a **Microsoft Azure App Service**, majd a **Create New** (Új létrehozása) elemet, végül pedig a **Publish** (Közzététel) lehetőséget.

    ![Közzététel az App Service-ben](./media/cache-web-app-howto/cache-publish-to-app-service.png)

3. A **Create App Service** (App Service létrehozása) párbeszédpanelen hajtsa végre a következő módosításokat:

    | Beállítás | Javasolt érték | Leírás |
    | ------- | :---------------: | ----------- |
    | **Alkalmazás neve** | Használja az alapértelmezettet. | Az alkalmazásnév lesz az alkalmazás gazdagépneve, amikor üzembe helyezi az Azure-ban. Ha a név egyediségéhez ez szükséges, a rendszer időbélyeg-utótagot fűzhet hozzá. |
    | **Előfizetés** | Válassza ki az Azure-előfizetését. | A kapcsolódó üzemeltetési költségek ennél az előfizetésnél lesznek felszámítva. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze, hogy a kívánt előfizetés van-e kiválasztva.|
    | **Erőforráscsoport** | Használja ugyanazt az erőforráscsoportot, mint amelyben a gyorsítótárat létrehozta (pl.: *TestResourceGroup*). | Az erőforráscsoport segítségével az összes erőforrást egy csoportként kezelheti. Később, amikor törölni kívánja az alkalmazást, elég csak a csoportot törölnie. |
    | **App Service-csomag** | Válassza az **Új** elemet, és hozzon létre egy *TestingPlan* nevű új App Service-csomagot. <br />Használja ugyanazt a **Helyet**, mint amelyet a gyorsítótár létrehozásakor használt. <br />A méretnél válassza az **Ingyenes** lehetőséget. | Az App Service-csomag meghatároz egy futtatáshoz használható számításierőforrás-készletet a webalkalmazás számára. |

    ![App Service párbeszédpanel](./media/cache-web-app-howto/cache-create-app-service-dialog.png)

4. Az App Service üzemeltetési beállításainak konfigurálása után válassza a **Létrehozás** lehetőséget.

5. A közzététel állapotát a Visual Studio **Kimenet** ablakában ellenőrizheti. Az alkalmazás közzététele után a rendszer naplózza az alkalmazás URL-címét:

    ![A közzététel kimenete](./media/cache-web-app-howto/cache-publishing-output.png)

### <a name="add-the-app-setting-for-the-cache"></a>A gyorsítótárhoz tartozó alkalmazásbeállítás hozzáadása

Az új alkalmazás közzététele után adjon hozzá egy új alkalmazásbeállítást. A rendszer ezt a beállítást használja a gyorsítótár kapcsolati adatainak tárolására. 

#### <a name="to-add-the-app-setting"></a>Az alkalmazásbeállítás hozzáadása 

1. Írja be az alkalmazás nevét az Azure Portal tetején található keresősávba a létrehozott új alkalmazás megkereséséhez.

    ![Alkalmazás keresése](./media/cache-web-app-howto/cache-find-app-service.png)

2. Adjon hozzá egy **CacheConnection** nevű új alkalmazásbeállítást, amelyet az alkalmazás a gyorsítótárhoz való csatlakozáshoz fog használni. Használja ugyanazt az értéket, mint amelyet a `CacheConnection` számára konfigurált a *CacheSecrets.config* fájlban. Az érték tartalmazza a gyorsítótár gazdagépének nevét és a hozzáférési kulcsot.

    ![Alkalmazásbeállítás hozzáadása](./media/cache-web-app-howto/cache-add-app-setting.png)

### <a name="run-the-app-in-azure"></a>Az alkalmazás futtatása az Azure-ban

Lépjen a böngészőben az alkalmazás URL-címére. Az URL-cím megjelenik a Visual Studio kimeneti ablakában a közzétételi művelet eredményei között. Az Azure Portalon, a létrehozott alkalmazás áttekintőoldalán is megtalálható.

A gyorsítótár-hozzáférés teszteléséhez válassza a navigációs sávon a **Redis-teszt Azure cache** elemét.

![Egyszerű teszt eredménye – Azure](./media/cache-web-app-howto/cache-simple-test-complete-azure.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport törlésekor a benne foglalt összes erőforrás véglegesen törlődik. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.

### <a name="to-delete-a-resource-group"></a>Erőforráscsoport törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Szűrés név alapján** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az erőforráscsoport eredménylistájában válassza a **...**, majd az **Erőforráscsoport törlése** lehetőséget.

    ![Törlés](./media/cache-web-app-howto/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését kéri. A megerősítéshez írja be az erőforráscsoport nevét, és válassza a **Törlést**.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyagban az Azure cache-t a Redis reálisabb forgatókönyvben használhatja az alkalmazások teljesítményének növelése érdekében. Frissíti az alkalmazást, hogy gyorsítótárazza egy ranglista eredményeit a gyorsítótár-feltöltési minta, az ASP.NET és egy adatbázis együttes használatával.

> [!div class="nextstepaction"]
> [Gyorsítótár-feltöltési ranglista létrehozása az ASP.NET-en](cache-web-app-cache-aside-leaderboard.md)