---
title: Gyorsítótár ASP.NET munkamenet-szolgáltatója
description: Megtudhatja, hogyan tárolhatja a memóriában tárolt ASP.NET munkamenet-állapotot az Azure cache for Redis használatával.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544528"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Az Azure Cache for Redis ASP.NET munkamenetállapot-szolgáltatója

A Redis készült Azure cache olyan munkamenet-szolgáltatót biztosít, amellyel a memóriában tárolt munkamenet-állapotot SQL Server-adatbázis helyett az Azure cache használatával tárolhatja a Redis. A gyorsítótárazási munkamenet-állapot szolgáltatójának használatához először konfigurálja a gyorsítótárat, majd konfigurálja a ASP.NET-alkalmazást a gyorsítótárhoz az Azure cache használatával a Redis munkamenet-állapot NuGet csomagjához. ASP.NET Core alkalmazások esetében olvassa el a [munkamenet és az állapot kezelése a ASP.net Coreban című szakaszt](/aspnet/core/fundamentals/app-state).

Ez gyakran nem praktikus a valós Felhőbeli alkalmazásokban, így elkerülhető a felhasználói munkamenetek valamilyen állapotának tárolása, de bizonyos módszerek nagyobb mértékben befolyásolják a teljesítményt és a méretezhetőséget, mint mások. Ha meg kell őriznie az állapotot, a legjobb megoldás az, hogy a kis mennyiségű állapotot tárolja, és a cookie-kat tárolja. Ha ez nem valósítható meg, a következő legjobb megoldás a ASP.NET munkamenet-állapotának használata a szolgáltatóval elosztott, memórián belüli gyorsítótárban. A teljesítmény-és skálázhatósági szempontból a legrosszabb megoldás egy adatbázis-alapú munkamenet-szolgáltató használata. Ez a témakör útmutatást nyújt az Azure cache-hez készült ASP.NET-munkamenet-szolgáltató használatához a Redis. További információ a munkamenet-állapottal kapcsolatos egyéb lehetőségekről: [ASP.NET munkamenet-állapotának beállításai](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Store ASP.NET session state in the cache (ASP.NET munkamenet-állapot tárolása a gyorsítótárban)

Ha a Visual Studióban szeretné konfigurálni az ügyfélalkalmazás-t a Redis munkamenet-állapot NuGet-csomagjának Azure cache szolgáltatásával, kattintson a **NuGet csomagkezelő**, **csomagkezelő konzol** elemre az **eszközök** menüből.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Ha a prémium szintről használja a fürtözési funkciót, akkor a [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1-es vagy újabb verzióját kell használnia, vagy kivételt kell eldobnia. A csak a 2.0.1-es vagy újabb verzióra való áttérés megszakítási változást mutat be; További információ: [v 2.0.0 Breaking Change details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). A cikk frissítésének időpontjában a csomag aktuális verziója a következő: 2.2.3.
> 
> 

A Redis munkamenet-szolgáltató NuGet csomagjának függősége van a StackExchange. Redis. StrongName csomagtól. Ha a StackExchange. Redis. StrongName csomag nem szerepel a projektben, az telepítve van.

>[!NOTE]
>Az erős névvel ellátott StackExchange. Redis. StrongName csomag mellett a StackExchange. Redis nem erős nevű verziója is létezik. Ha a projekt a nem erős névvel ellátott StackExchange. Redis verziót használja, akkor el kell távolítania azt, ellenkező esetben az elnevezési ütközések is bekerülnek a projektbe. További információ ezekről a csomagokról: [.net gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

A NuGet csomag letölti és hozzáadja a szükséges szerelvény-hivatkozásokat, és hozzáadja a következő szakaszt a web.config-fájlhoz. Ez a szakasz tartalmazza a ASP.NET alkalmazás szükséges konfigurációját, hogy az Azure cache-t használja a Redis munkamenet-szolgáltatóhoz.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

A kommentált szakasz az egyes attribútumok attribútumaira és mintái beállításaira mutat példát.

Konfigurálja az attribútumokat a Microsoft Azure Portal cache paneljének értékeivel, és szükség szerint konfigurálja a többi értéket. A gyorsítótár tulajdonságainak elérésére vonatkozó utasításokért tekintse meg az [Azure cache konfigurálása a Redis-beállításokhoz](cache-configure.md#configure-azure-cache-for-redis-settings)című témakört.

* **gazdagép** – adja meg a gyorsítótár végpontját.
* **port** – a TLS-beállításoktól függően használja a nem TLS/SSL-portot vagy a TLS/SSL-portot.
* **accessKey** – használja a gyorsítótár elsődleges vagy másodlagos kulcsát.
* **SSL** – igaz, ha biztonságos gyorsítótárazást/ügyfél-kommunikációt szeretne biztosítani a TLS-vel; Ellenkező esetben hamis. Ügyeljen arra, hogy a megfelelő portot határozza meg.
  * A nem TLS port alapértelmezés szerint le van tiltva az új gyorsítótárak esetében. Ha ezt a beállítást szeretné használni a TLS-portot használja, akkor az igaz értéket kell megadni. A nem TLS port engedélyezésével kapcsolatos további információkért tekintse meg a [gyorsítótár konfigurálása](cache-configure.md) témakör [elérési portok](cache-configure.md#access-ports) című szakaszát.
* **throwOnError** – igaz, ha egy hiba miatt kivételt szeretne kiváltani, vagy hamis értéket, ha azt szeretné, hogy a művelet csendesen meghiúsuljon. A hiba ellenőrzéséhez ellenőrizze a statikus Microsoft. Web. Redis. RedisSessionStateProvider. LastException tulajdonságot. Az alapértelmezett érték a True (igaz).
* **retryTimeoutInMilliseconds** – a sikertelen műveleteket a rendszer az adott intervallumban, ezredmásodpercben megadva hajtja végre. Az első újrapróbálkozás 20 ezredmásodperc után történik meg, majd az újrapróbálkozások másodpercenként, amíg a retryTimeoutInMilliseconds időköz lejár. Az intervallum után azonnal újrapróbálkozik a művelettel. Ha a művelet továbbra is sikertelen, a kivétel a throwOnError beállításától függően visszakerül a hívóba. Az alapértelmezett érték 0, ami azt jelenti, hogy nem próbálkozik újra.
* **databaseId** – megadja, hogy melyik adatbázist kell használni a kimeneti adatokat a gyorsítótárban. Ha nincs megadva, a rendszer az alapértelmezett 0 értéket használja.
* **applicationName** – a kulcsok a Redis-ben tárolódnak `{<Application Name>_<Session ID>}_Data` . Ez az elnevezési séma lehetővé teszi, hogy több alkalmazás is ugyanazt a Redis-példányt használja. Ez a paraméter nem kötelező, és ha nem adja meg az alapértelmezett értéket, a rendszer nem használja.
* **connectionTimeoutInMilliseconds** – ezzel a beállítással felülbírálhatja a StackExchange. Redis ügyfél connectTimeout-beállítását. Ha nincs megadva, a rendszer a 5000 alapértelmezett connectTimeout-beállítást használja. További információ: [StackExchange. Redis konfigurációs modell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – ezzel a beállítással felülbírálhatja a StackExchange. Redis ügyfél syncTimeout-beállítását. Ha nincs megadva, a rendszer a 1000 alapértelmezett syncTimeout-beállítást használja. További információ: [StackExchange. Redis konfigurációs modell](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – ezzel a beállítással megadhatja a Redis számára eljuttatott munkamenet-tartalmak egyéni szerializálását. A megadott típusnak meg kell valósítania `Microsoft.Web.Redis.ISerializer` , és deklarálnia kell a nyilvános paraméter nélküli konstruktort. Alapértelmezés szerint  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` használatban van.

További információ ezekről a tulajdonságokról: az eredeti blogbejegyzés a [Redis ASP.NET munkamenet-szolgáltatójának](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)bejelentése.

Ne felejtse el kipróbálni a standard InProc munkamenet-szolgáltató szakaszt a web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

A lépések végrehajtása után az alkalmazás úgy van konfigurálva, hogy az Azure cache-t használja a Redis munkamenet-szolgáltatóhoz. Ha munkamenet-állapotot használ az alkalmazásban, az a Redis-példányhoz tartozó Azure cache-ben tárolódik.

> [!IMPORTANT]
> A gyorsítótárban tárolt adatmennyiségnek szerializálható kell lennie, ellentétben az alapértelmezett memóriabeli ASP.NET munkamenet-szolgáltatóban tárolható adattal. Ha a Redis munkamenet-szolgáltatóját használja, ügyeljen arra, hogy a munkamenet-állapotban tárolt adattípusok szerializálható legyenek.
> 
> 

## <a name="aspnet-session-state-options"></a>ASP.NET munkamenet-állapotának beállításai

* A memória munkamenet-szolgáltatójában – ez a szolgáltató tárolja a munkamenet-állapotot a memóriában. A szolgáltató használatának előnyei egyszerűek és gyorsak. A Web Apps azonban nem méretezhető, ha a memória-szolgáltatót használja, mivel az nem terjeszthető.
* SQL Server munkamenet-szolgáltatója – ez a szolgáltató tárolja a munkamenet-állapotot az SQL Serverben. Akkor használja ezt a szolgáltatót, ha a munkamenet-állapotot állandó tárolóban szeretné tárolni. A webalkalmazás méretezése, de az SQL Server használata a munkamenet teljesítményére hatással lehet a webalkalmazásra. Ezt a szolgáltatót a [memóriában tárolt OLTP-konfigurációval](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) is használhatja a teljesítmény javítása érdekében.
* A memória munkamenet-állapotának szolgáltatója, például az Azure cache a Redis munkamenet-szolgáltató számára – ez a szolgáltató a legjobbat kínálja mindkét világból. A webalkalmazáshoz egyszerű, gyors és méretezhető munkamenet-szolgáltató is tartozhat. Mivel ez a szolgáltató tárolja a munkamenet-állapotot egy gyorsítótárban, az alkalmazásnak figyelembe kell vennie az elosztott memória-gyorsítótárban való kommunikációhoz kapcsolódó összes jellemzőt, például az átmeneti hálózati hibákat. A gyorsítótár használatának ajánlott eljárásaiért lásd: a Microsoft-minták [gyorsítótárazási útmutatója](/azure/architecture/best-practices/caching) & az [Azure Cloud Application tervezési és megvalósítási útmutatója](https://github.com/mspnp/azure-guidance).

A munkamenet-állapottal és az egyéb ajánlott eljárásokkal kapcsolatos további információkért lásd: [Webfejlesztés – ajánlott eljárások (Real-World felhőalapú alkalmazások fejlesztése az Azure-ban)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Harmadik féltől származó munkamenet-szolgáltatók

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Azure Cache ASP.NET kimeneti gyorsítótár-szolgáltatóját a Redis](cache-aspnet-output-cache-provider.md).