---
title: 'Gyors útmutató: az Azure cache használata a Redis a .NET-keretrendszerben'
description: Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure cache-t a .NET-alkalmazások Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: ffc5c8ea647d4cadd2d151eb880c794ac5f4ebd4
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121439"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Gyors útmutató: az Azure cache használata a Redis a .NET-keretrendszerben

Ebben a rövid útmutatóban beépíti az Azure cache-t a Redis-ba egy .NET-keretrendszerbeli alkalmazásba, hogy hozzáférhessen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető. A [StackExchange. Redis](https://github.com/StackExchange/StackExchange.Redis) ügyfelet a C#-kóddal kifejezetten a .net-konzol alkalmazásban használhatja.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a [.NET-keretrendszer](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) rövid útmutatóját a githubon.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- A [.NET-keretrendszer 4-es vagy újabb](https://www.microsoft.com/net/download/dotnet-framework-runtime)verziója, amelyre a StackExchange. Redis ügyfélnek szüksége van.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Hozzon létre egy *CacheSecrets.config* nevű fájlt a számítógépen, majd mentse egy olyan helyre, ahonnan a mintaalkalmazás forráskódja nem fogja beolvasni. Ebben a rövid útmutatóban a *CacheSecrets.config* fájl a következő helyen található: *C:\AppSecrets\CacheSecrets.config*.

Módosítsa a *CacheSecrets.config* fájlt, és adja hozzá az alábbi tartalmakat:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

A `<cache-name>` karakterláncot cserélje le a gyorsítótár gazdagépének nevére.

Az `<access-key>` karakterláncot cserélje le a gyorsítótár elsődleges kulcsára.


## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

A Visual Studióban kattintson a **fájl**  >  **új**  >  **projekt** elemre.

Válassza a **konzolos alkalmazás (.NET-keretrendszer)** lehetőséget, és az alkalmazás konfigurálásához kattintson a **következő** elemre. Adja meg a **projekt nevét** , majd kattintson a **Létrehozás** gombra egy új konzol alkalmazás létrehozásához.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>A gyorsítótárügyfél konfigurálása

Ebben a szakaszban konfigurálja a konzolalkalmazást a .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) ügyfél használatára.

A Visual Studióban kattintson a **Tools**  >  **NuGet** Package Manager  >  **csomagkezelő konzol** elemre, majd futtassa a következő parancsot a Package Manager konzol ablakában.

```powershell
Install-Package StackExchange.Redis
```

A telepítés befejezése után a *StackExchange.Redis* gyorsítótárügyfél már használható a projekttel.


## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

Nyissa meg az *App.config* fájlt a Visual Studióban, és frissítse azt az `appSettings` `file` attribútummal, amely a *CacheSecrets.config* fájlra hivatkozik.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>
</configuration>
```

A Solution Explorer (Megoldáskezelő) ablaktáblában kattintson a jobb gombbal a **References (Hivatkozások)** lehetőségre, majd kattintson az **Add a reference (Hivatkozás hozzáadása)** elemre. Adjon hozzá egy, a **System.Configuration** szerelvényre mutató hivatkozást.

Adja hozzá a következő `using` utasításokat a *Program.cs* fájlhoz:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

A Redis készült Azure cache-hez való kapcsolódást a `ConnectionMultiplexer` osztály kezeli. Ezt az osztályt kell megosztani és újrafelhasználni az ügyfélalkalmazásban. Ne hozzon létre új kapcsolatot minden művelethez. 

Soha ne tároljon hitelesítő adatokat a forráskódban. Annak érdekében, hogy a minta egyszerű maradjon, csak egy külső titkos kódokat tartalmazó konfigurációs fájlt használok. Ennél még jobb megoldás lenne a [tanúsítványokkal rendelkező Azure Key Vault](/rest/api/keyvault/certificate-scenarios) használata.

A *Program.cs* fájlban adja hozzá a következő tagokat a konzolalkalmazás `Program` osztályához:

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
    return ConnectionMultiplexer.Connect(cacheConnection);
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```


A `ConnectionMultiplexer` példány alkalmazásban való megosztásának ez a módszere egy statikus tulajdonságot használ, amely egy csatlakoztatott példányt ad vissza. A kód egy szálbiztos módszert biztosít egyetlen csatlakoztatott `ConnectionMultiplexer`-példány inicializálásához. `abortConnect` hamis értékre van állítva, ami azt jelenti, hogy a hívás akkor is sikeres, ha nem jön létre a Redis-hez készült Azure cache-hez való kapcsolódás. A `ConnectionMultiplexer` egyik fontos szolgáltatása, hogy automatikusan visszaállítja a kapcsolatot a gyorsítótárral, amint a hálózati problémák vagy egyéb hibák elhárulnak.

A rendszer a *CacheConnection* alkalmazásbeállítás értékét használja ahhoz, hogy jelszóparaméterként hivatkozzon a gyorsítótár Azure Portalon található kapcsolati sztringjére.

## <a name="executing-cache-commands"></a>Gyorsítótárparancsok végrehajtása

Adja hozzá a következő kódot a konzolalkalmazás `Program` osztályának `Main` eljárásához:

```csharp
static void Main(string[] args)
{
    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

    // Perform cache operations using the cache object...

    // Simple PING command
    string cacheCommand = "PING";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

    // Simple get and put of integral data types into the cache
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
    Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires the allowAdmin=true
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint) Connection.GetEndPoints()[0];
    var server = Connection.GetServer(endpoint.Host, endpoint.Port);

    var clients = server.ClientList(); 
    Console.WriteLine("Cache response :");
    foreach (var client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    lazyConnection.Value.Dispose();
}
```

A Redis-hez készült Azure cache egy konfigurálható (alapértelmezett 16) adatbázist tartalmaz, amelyekkel logikailag elkülöníthető az Azure-gyorsítótárban lévő Redis. A kód az alapértelmezett adatbázishoz csatlakozik (DB 0). További információ: [Mik azok a Redis-adatbázisok?](cache-development-faq.md#what-are-redis-databases) és [A Redis-kiszolgáló alapértelmezett konfigurációja](cache-configure.md#default-redis-server-configuration).

A gyorsítótárelemek a `StringSet` és a `StringGet` metódussal tárolhatók és kérhetők le.

A Redis a legtöbb adatot Redis-sztringekként tárolja, azonban ezek a sztringek több adattípust is tartalmazhatnak, például szerializált bináris adatokat, amelyek akkor használhatók, ha .NET-objektumokat tárol a gyorsítótárban.

A konzolalkalmazás buildeléséhez és futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt.

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![Részleges konzolalkalmazás](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>.NET-objektumok használata a gyorsítótárban

A Redis-hez készült Azure cache gyorsítótárba helyezheti a .NET-objektumokat és az egyszerű adattípusokat, de a .NET-objektumok gyorsítótárazása előtt szerializálni kell. Ez a .NET-objektumszerializálás az alkalmazásfejlesztők feladata, akik így rugalmasan kiválaszthatják a szerializálót.

Az objektumok szerializálásának egy egyszerű módja, ha a [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) kódtárban található `JsonConvert` szerializálási metódusokat használja a JSON formátumból és a JSON formátumba szerializálásra. Ebben a szakaszban egy .NET-objektumot fog hozzáadni a gyorsítótárhoz.

A Visual Studióban kattintson a **Tools**  >  **NuGet** Package Manager  >  **csomagkezelő konzol** elemre, majd futtassa a következő parancsot a Package Manager konzol ablakában.

```powershell
Install-Package Newtonsoft.Json
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

    public Employee(string EmployeeId, string Name, int Age)
    {
        this.Id = EmployeeId;
        this.Name = Name;
        this.Age = Age;
    }
}
```

Másolja és illessze be a következő kódsorokat a *Program.cs* fájl `Main()` eljárásának végére, a `Dispose()` hívása elé, egy szerializált .NET-objektum gyorsítótárazásához és lekéréséhez:

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

A .NET-objektumok szerializálásának teszteléséhez buildelje és futtassa a konzolalkalmazást a **Ctrl+F5** billentyűkombináció lenyomásával. 

![Kész konzolalkalmazás](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.



<a name="next-steps"></a>

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Azure cache-t egy .NET-alkalmazásból származó Redis. Folytassa a következő rövid útmutatóval, hogy az Azure cache-t használja a Redis egy ASP.NET-webalkalmazással.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)