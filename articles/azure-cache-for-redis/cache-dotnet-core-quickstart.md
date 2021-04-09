---
title: 'Gyors útmutató: az Azure cache használata a Redis a .NET Core-ban'
description: Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure cache-t a Redis a .NET Core-alkalmazásaiban
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.custom: devx-track-csharp, mvc
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: 198580ad41b62cf044ade7b525728c38aabd0f9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720977"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-core"></a>Gyors útmutató: az Azure cache használata a Redis a .NET Core-ban

Ebben a rövid útmutatóban beépíti az Azure cache-t a Redis-ba egy .NET Core-alkalmazásba, hogy hozzáférhessen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető. A [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) -ügyfelet a C#-kóddal kifejezetten egy .net Core Console-alkalmazásban használhatja.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a [.net Core](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet-core) rövid útmutatóját a githubon.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Jegyezze fel a **GAZDAGÉP NEVÉT** és az **Elsődleges** hozzáférési kulcsot. Ezeket az értékeket később a *CacheConnection* titkos kódjának létrehozásához fogja használni.



## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

Nyisson meg egy új parancsablakot, és a következő parancs végrehajtásával hozzon létre egy új .NET Core-konzolalkalmazást:

```
dotnet new console -o Redistest
```

A parancsablakban váltson az új *Redistest* projektkönyvtárra.



## <a name="add-secret-manager-to-the-project"></a>A Secret Manager hozzáadása a projekthez

Ebben a szakaszban hozzáadja a [Secret Manager eszközt](/aspnet/core/security/app-secrets) a projekthez. A Secret Manager eszköz tárolja a projektfán kívüli fejlesztési feladatokhoz tartozó bizalmas adatokat. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait.

Nyissa meg a *Redistest.csproj* fájlt. Adjon hozzá egy `DotNetCliToolReference` elemet a *Microsoft.Extensions.SecretManager.Tools* belefoglalásához. Adjon hozzá egy `UserSecretsId` elemet is az alábbiakban látható módon, és mentse a fájlt.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>Redistest</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
</Project>
```

A következő parancs végrehajtásával adja hozzá a *Microsoft.Extensions.Configuration.UserSecrets* csomagot a projekthez:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

A csomagok visszaállításához hajtsa végre az alábbi parancsot:

```
dotnet restore
```

A parancsablakban futtassa a következő parancsot az új, *CacheConnection* nevű titkos kód tárolásához, miután kicserélte a helyőrzőket (a csúcsos zárójelekkel együtt) a saját gyorsítótárának nevére és elsődleges hozzáférési kulcsára:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<primary-access-key>"
```

Adja hozzá a következő `using` utasítást a *Program.cs* fájlhoz:

```csharp
using Microsoft.Extensions.Configuration;
```

Adja hozzá a következő tagokat a `Program` osztályhoz a *Program.cs* fájlban. Ez a kód egy olyan konfigurációt inicializál, amely hozzáfér az Azure cache Redis-kapcsolati karakterlánchoz tartozó felhasználói titkos kulcshoz.

```csharp
private static IConfigurationRoot Configuration { get; set; }
const string SecretName = "CacheConnection";

private static void InitializeConfiguration()
{
    var builder = new ConfigurationBuilder()
        .AddUserSecrets<Program>();

    Configuration = builder.Build();
}
```

## <a name="configure-the-cache-client"></a>A gyorsítótárügyfél konfigurálása

Ebben a szakaszban konfigurálja a konzolalkalmazást a .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) ügyfél használatára.

A parancsablakban hajtsa végre a következő parancsot a *Redistest* projektkönyvtárban:

```
dotnet add package StackExchange.Redis
```

A telepítés befejezése után a *StackExchange.Redis* gyorsítótárügyfél már használható a projekttel.


## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

Adja hozzá a következő `using` utasítást a *Program.cs* fájlhoz:

```csharp
using StackExchange.Redis;
```

A Redis készült Azure cache-hez való kapcsolódást a `ConnectionMultiplexer` osztály kezeli. Ezt az osztályt kell megosztani és újrafelhasználni az ügyfélalkalmazásban. Ne hozzon létre új kapcsolatot minden művelethez. 

A *Program.cs* fájlban adja hozzá a következő tagokat a konzolalkalmazás `Program` osztályához:

```csharp
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
        string cacheConnection = Configuration[SecretName];
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
}
```

A `ConnectionMultiplexer` példány alkalmazásban való megosztásának ez a módszere egy statikus tulajdonságot használ, amely egy csatlakoztatott példányt ad vissza. A kód egy szálbiztos módszert biztosít egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. `abortConnect` hamis értékre van állítva, ami azt jelenti, hogy a hívás akkor is sikeres, ha nem jön létre a Redis-hez készült Azure cache-hez való kapcsolódás. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

A *CacheConnection* titkos kód értékéhez a Secret Manager konfigurációszolgáltatóval lehet hozzáférni, és jelszóparaméterként használható.

## <a name="handle-redisconnectionexception-and-socketexception-by-reconnecting"></a>RedisConnectionException és SocketException kezelése újrakapcsolattal

Ajánlott eljárás a metódusok meghívásakor, `ConnectionMultiplexer` hogy a rendszer a `RedisConnectionException` `SocketException` kapcsolat bezárásával és visszaállításával automatikusan megpróbálja feloldani a kivételeket és a kivételeket.

Adja hozzá a következő `using` utasításokat a *Program.cs* fájlhoz:

```csharp
using System.Net.Sockets;
using System.Threading;
```

A *program. cs programban* adja hozzá a következő tagokat a `Program` osztályhoz:

```csharp
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

## <a name="executing-cache-commands"></a>Gyorsítótárparancsok végrehajtása

A *Program.cs* fájlban adja hozzá a következő kódot a konzolalkalmazás `Program` osztályának `Main` eljárásához:

```csharp
static void Main(string[] args)
{
    InitializeConfiguration();

    IDatabase cache = GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires allowAdmin=true in the connection string
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint)GetEndPoints()[0];
    IServer server = GetServer(endpoint.Host, endpoint.Port);
    ClientInfo[] clients = server.ClientList();

    Console.WriteLine("Cache response :");
    foreach (ClientInfo client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    CloseConnection(lazyConnection);
}
```

Mentse a *Program.cs* fájlt.

A Redis-hez készült Azure cache egy konfigurálható (alapértelmezett 16) adatbázist tartalmaz, amelyekkel logikailag elkülöníthető az Azure-gyorsítótárban lévő Redis. A kód az alapértelmezett adatbázishoz csatlakozik (DB 0). További információ: [Mik azok a Redis-adatbázisok?](cache-development-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration).

A gyorsítótárelemek a `StringSet` és a `StringGet` metódussal tárolhatók és kérhetők le.

A Redis a legtöbb adatot Redis-sztringekként tárolja, azonban ezek a sztringek több adattípust is tartalmazhatnak, például szerializált bináris adatokat, amelyek akkor használhatók, ha .NET-objektumokat tárol a gyorsítótárban.

Hajtsa végre a következő parancsot a parancsablakban az alkalmazás létrehozásához:

```
dotnet build
```

Ezután futtassa az alkalmazást a következő paranccsal:

```
dotnet run
```

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![Részleges konzolalkalmazás](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>.NET-objektumok használata a gyorsítótárban

A Redis-hez készült Azure cache gyorsítótárba helyezheti a .NET-objektumokat és az egyszerű adattípusokat, de a .NET-objektumok gyorsítótárazása előtt szerializálni kell. Ez a .NET-objektumszerializálás az alkalmazásfejlesztők feladata, akik így rugalmasan kiválaszthatják a szerializálót.

Az objektumok szerializálásának egy egyszerű módja, ha a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) kódtárban található `JsonConvert` szerializálási metódusokat használja a JSON formátumból és a JSON formátumba szerializálásra. Ebben a szakaszban egy .NET-objektumot fog hozzáadni a gyorsítótárhoz.

A következő parancs végrehajtásával adja hozzá az alkalmazáshoz a *Newtonsoft.json* csomagot:

```
dotnet add package Newtonsoft.json
```

Adja hozzá a következő `using` utasítást a *Program.cs* fájl elejéhez:

```csharp
using Newtonsoft.Json;
```

Adja hozzá a következő `Employee` osztálydefiníciót a *Program.cs* fájlhoz:

```csharp
class Employee
{
    public string Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }

    public Employee(string employeeId, string name, int age)
    {
        Id = employeeId;
        Name = name;
        Age = age;
    }
}
```

Másolja és illessze be a következő kódsorokat a *Program.cs* fájl `Main()` eljárásának végére, a `CloseConnection()` hívása elé, egy szerializált .NET-objektum gyorsítótárazásához és lekéréséhez:

```csharp
    // Store .NET object to cache
    Employee e007 = new Employee("007", "Davide Columbo", 100);
    Console.WriteLine("Cache response from storing Employee .NET object : " + 
    cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

    // Retrieve .NET object from cache
    Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
    Console.WriteLine("Deserialized Employee .NET object :\n");
    Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
    Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
    Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Mentse a *Program.cs* fájlt, és hozza létre újra az alkalmazást a következő paranccsal:

```
dotnet build
```

Futtassa az alkalmazást a következő paranccsal a .NET-objektumok szerializálásának teszteléséhez:

```
dotnet run
```

![Kész konzolalkalmazás](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.



<a name="next-steps"></a>

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Azure cache-t egy .NET Core-alkalmazásból származó Redis. Folytassa a következő rövid útmutatóval, hogy az Azure cache-t használja a Redis egy ASP.NET-webalkalmazással.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)