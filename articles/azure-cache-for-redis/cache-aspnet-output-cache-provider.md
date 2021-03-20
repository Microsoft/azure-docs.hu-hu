---
title: ASP.NET kimeneti gyorsítótár-szolgáltató az Azure cache-hez a Redis
description: Ismerje meg, hogyan gyorsítótárazhatja a ASP.NET az Azure cache használatával a Redis. A Redis kimeneti gyorsítótár-szolgáltatója egy folyamaton kívüli tárolási mechanizmus a kimeneti gyorsítótár-adattároláshoz.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993406"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET kimeneti gyorsítótár-szolgáltató az Azure cache-hez a Redis

A Redis kimeneti gyorsítótár-szolgáltatója egy folyamaton kívüli tárolási mechanizmus a kimeneti gyorsítótár-adattároláshoz. Ez az adat kifejezetten a teljes HTTP-válaszokra vonatkozik (az oldal kimeneti gyorsítótárazása). A szolgáltató a 4. ASP.NET bevezetett új kimeneti gyorsítótár-szolgáltatói bővíthetőségi pontra csatlakozik. ASP.NET Core alkalmazások esetében olvassa el a [Válasz gyorsítótárazása a ASP.net Coreban című részt](/aspnet/core/performance/caching/response). 

A Redis kimeneti gyorsítótár-szolgáltató használatához először konfigurálja a gyorsítótárat, majd konfigurálja a ASP.NET alkalmazást a Redis kimeneti gyorsítótár-szolgáltató NuGet csomagjának használatával. Ez a témakör útmutatást nyújt az alkalmazás konfigurálásához a Redis kimeneti gyorsítótár-szolgáltató használatára. Az Azure cache Redis-példány létrehozásával és konfigurálásával kapcsolatos további információkért lásd: [gyorsítótár létrehozása](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET-oldal kimenetének tárolása a gyorsítótárban

Ha a Visual Studióban szeretné konfigurálni az ügyfélalkalmazás-t a Redis munkamenet-állapot NuGet-csomagjának Azure cache szolgáltatásával, kattintson a **NuGet csomagkezelő**, **csomagkezelő konzol** elemre az **eszközök** menüből.

Futtassa az alábbi parancsot a `Package Manager Console` ablakából.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

A Redis kimeneti gyorsítótár-szolgáltató NuGet csomagja a StackExchange. Redis. StrongName csomagtól függ. Ha a StackExchange. Redis. StrongName csomag nem szerepel a projektben, az telepítve van. A Redis kimeneti gyorsítótár-szolgáltató NuGet csomaggal kapcsolatos további információkért tekintse meg a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) -NuGet lapot.

>[!NOTE]
>Az erős névvel ellátott StackExchange. Redis. StrongName csomag mellett a StackExchange. Redis nem erős nevű verziója is létezik. Ha a projekt a nem erős névvel ellátott StackExchange. Redis verziót használja, el kell távolítania azt; Ellenkező esetben a projekt elnevezési ütközéseit fogja tapasztalni. További információ ezekről a csomagokról: [.net gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

A NuGet csomag letölti és hozzáadja a szükséges szerelvény-hivatkozásokat, és hozzáadja a következő szakaszt a web.config-fájlhoz. Ez a szakasz tartalmazza a ASP.NET alkalmazás szükséges konfigurációját a Redis kimeneti gyorsítótár-szolgáltató használatához.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Konfigurálja az attribútumokat a Microsoft Azure Portal cache paneljének értékeivel, és szükség szerint konfigurálja a többi értéket. A gyorsítótár tulajdonságainak elérésére vonatkozó utasításokért tekintse meg az [Azure cache konfigurálása a Redis-beállításokhoz](cache-configure.md#configure-azure-cache-for-redis-settings)című témakört.

| Attribútum | Típus | Alapértelmezett | Description |
| --------- | ---- | ------- | ----------- |
| *gazdagép* | sztring | localhost | A Redis-kiszolgáló IP-címe vagy állomásneve |
| *Port* | pozitív egész szám | 6379 (nem TLS/SSL)<br/>6380 (TLS/SSL) | Redis-kiszolgáló portja |
| *accessKey* | sztring | "" | Redis-kiszolgáló jelszavának engedélyezése, ha engedélyezve van a Redis engedélyezése. Alapértelmezés szerint az érték üres karakterlánc, ami azt jelenti, hogy a munkamenet-állapot szolgáltatója nem használ jelszót a Redis-kiszolgálóhoz való csatlakozáskor. **Ha a Redis-kiszolgáló nyilvánosan elérhető hálózatban (például Azure Redis Cache) található, ügyeljen arra, hogy a biztonság növelése érdekében engedélyezze a Redis-engedélyezést, és adjon meg egy biztonságos jelszót.** |
| *SSL* | boolean | **hamis** | Azt jelzi, hogy a TLS-kapcsolaton keresztül csatlakozik-e a Redis-kiszolgálóhoz Alapértelmezés szerint ez az érték **hamis** , mert a Redis nem támogatja a TLS-t a dobozból. **Ha olyan Azure Redis Cache használ, amely támogatja az SSL-t a jelölőnégyzetből, ügyeljen rá, hogy a biztonság növelése érdekében állítsa igaz értékre.**<br/><br/>A nem TLS port alapértelmezés szerint le van tiltva az új gyorsítótárak esetében. Ha ezt a beállítást szeretné használni, a nem TLS portot használja a **true (igaz** ) érték megadásával. A nem TLS port engedélyezésével kapcsolatos további információkért tekintse meg a [gyorsítótár konfigurálása](cache-configure.md) témakör [elérési portok](cache-configure.md#access-ports) című szakaszát. |
| *databaseIdNumber* | pozitív egész szám | 0 | *Ez az attribútum csak web.config vagy AppSettings használatával adható meg.*<br/><br/>Határozza meg, hogy melyik Redis-adatbázist kívánja használni. |
| *connectionTimeoutInMilliseconds* | pozitív egész szám | A StackExchange. Redis által biztosított | A *ConnectTimeout* beállítására szolgál a StackExchange. Redis. ConnectionMultiplexer létrehozásakor. |
| *operationTimeoutInMilliseconds* | pozitív egész szám | A StackExchange. Redis által biztosított | A *SyncTimeout* beállítására szolgál a StackExchange. Redis. ConnectionMultiplexer létrehozásakor. |
| *ConnectionString* (érvényes StackExchange. Redis kapcsolati sztring) | sztring | *n/a* | Vagy egy AppSettings vagy web.config paraméterre mutató hivatkozás, vagy egy érvényes StackExchange. Redis-kapcsolódási karakterlánc. Ez az attribútum a *gazdagép*, a *port*, az *accessKey*, az *SSL* és más StackExchange. Redis attribútumok értékeit biztosítja. A *ConnectionString* részletesebb ismertetését lásd: a [ConnectionString beállítása](#setting-connectionstring) az [attribútumok megjegyzései](#attribute-notes) szakaszban. |
| *settingsClassName*<br/>*settingsMethodName* | sztring<br/>sztring | *n/a* | *Ezek az attribútumok csak web.config vagy AppSettings adhatók meg.*<br/><br/>Ezekkel az attribútumokkal adhat meg egy kapcsolódási karakterláncot. a *settingsClassName* a *settingsMethodName* által megadott metódust tartalmazó szerelvény minősített osztályának kell lennie.<br/><br/>A *settingsMethodName* által megadott metódusnak nyilvános, statikus és Void értékűnek kell lennie (nem kell paramétereket megadnia) a **karakterlánc** visszatérési típusával. Ez a metódus a tényleges kapcsolatok karakterláncát adja vissza. |
| *loggingClassName*<br/>*loggingMethodName* | sztring<br/>sztring | *n/a* | *Ezek az attribútumok csak web.config vagy AppSettings adhatók meg.*<br/><br/>Ezekkel az attribútumokkal hibakeresést végezhet az alkalmazásban, ha naplókat biztosít a munkamenet-állapot/kimeneti gyorsítótárból, valamint a StackExchange. Redis naplókat. a *loggingClassName* a *loggingMethodName* által megadott metódust tartalmazó szerelvény minősített osztályának kell lennie.<br/><br/>A *loggingMethodName* által megadott metódusnak nyilvános, statikus és Void értékűnek kell lennie (nem kell paramétereket megadnia) a **System. IO. TextWriter** visszatérési típusával. |
| *applicationName* | sztring | Az aktuális folyamat moduljának neve vagy "/" | *Csak SessionStateProvider*<br/>*Ez az attribútum csak web.config vagy AppSettings használatával adható meg.*<br/><br/>A Redis cache-ben használandó alkalmazásnév-előtag. Az ügyfél különböző célokra használhatja ugyanazt a Redis cache-gyorsítótárat. Annak biztosításához, hogy a munkamenetkulcsok ne ütköznek, az alkalmazás neve előtaggal megadható. |
| *throwOnError* | boolean | true | *Csak SessionStateProvider*<br/>*Ez az attribútum csak web.config vagy AppSettings használatával adható meg.*<br/><br/>Azt határozza meg, hogy hiba esetén kivételt kell-e kidobni.<br/><br/>További információ a *throwOnError*: [Megjegyzések a *ThrowOnError*](#notes-on-throwonerror) -ben az [attribútumok megjegyzései](#attribute-notes) szakaszban. |>*Microsoft. Web. Redis. RedisSessionStateProvider. LastException*. |
| *retryTimeoutInMilliseconds* | pozitív egész szám | 5000 | *Csak SessionStateProvider*<br/>*Ez az attribútum csak web.config vagy AppSettings használatával adható meg.*<br/><br/>Ennyi ideig próbálkozzon újra egy művelet sikertelensége esetén. Ha ez az érték kisebb, mint a *operationTimeoutInMilliseconds*, akkor a szolgáltató nem próbálkozik újra.<br/><br/>További információ a *retryTimeoutInMilliseconds*: [Megjegyzések a *RetryTimeoutInMilliseconds*](#notes-on-retrytimeoutinmilliseconds) -ben az [attribútumok megjegyzései](#attribute-notes) szakaszban. |
| *redisSerializerType* | sztring | *n/a* | Meghatározza a Microsoft. Web. Redis által megvalósított osztály szerelvényének minősített típusának nevét. ISerializer, amely az értékek szerializálására és deszerializálására szolgáló egyéni logikát tartalmazza. További információ: a [ *redisSerializerType* ismertetése](#about-redisserializertype) az [attribútumok megjegyzései](#attribute-notes) szakaszban. |

## <a name="attribute-notes"></a>Attribútumok megjegyzései

### <a name="setting-connectionstring"></a>*ConnectionString* beállítása

A *ConnectionString* értéke kulcsként szolgál a tényleges kapcsolati karakterlánc lekéréséhez a appSettings-ből, ha van ilyen karakterlánc a appSettings-ben. Ha nem található a AppSettings-ben, a *ConnectionString* értéket fogja használni a rendszer a tényleges kapcsolati karakterlánc beolvasásához a web.config **ConnectionString** szakaszban, ha ez a szakasz létezik. Ha a kapcsolati karakterlánc nem létezik a AppSettings vagy a web.config **ConnectionString** szakaszban, a *ConnectionString* karakterlánc értéke a kapcsolati sztring lesz a StackExchange. Redis. ConnectionMultiplexer létrehozásakor.

Az alábbi példák bemutatják a *ConnectionString* használatának módját.

#### <a name="example-1"></a>1\. példa

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

A-ben `web.config` használja a fenti kulcs paraméter értékét a tényleges érték helyett.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>2\. példa

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

A-ben `web.config` használja a fenti kulcs paraméter értékét a tényleges érték helyett.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>3\. példa

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Megjegyzések a *throwOnError*

Ha a munkamenet-művelet során hiba lép fel, a munkamenet-állapot szolgáltatója kivételt fog kiváltani. Ezzel leállítja az alkalmazást.

Ez a viselkedés olyan módon módosult, amely támogatja a meglévő ASP.NET munkamenet-szolgáltatói felhasználók elvárásait, miközben szükség esetén kivételeket is megadhat. Az alapértelmezett viselkedés továbbra is kivételt jelez, ha hiba történik, összhangban a többi ASP.NET munkamenet-szolgáltatóval; a meglévő kódnak ugyanúgy kell működnie, mint korábban.

Ha a *throwOnError* false ( **hamis**) értékre van állítva, akkor a kivételek eldobása helyett a művelet csendes hibát jelez. Ha meg szeretné tudni, hogy hiba történt-e, és ha igen, keresse meg a kivételt, ellenőrizze a *Microsoft. Web. Redis. RedisSessionStateProvider. LastException* statikus tulajdonságát.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Megjegyzések a *retryTimeoutInMilliseconds*

Ez némi újrapróbálkozási logikát biztosít ahhoz, hogy leegyszerűsítse az esetet, amikor egy munkamenet-művelet a hálózati hiba miatt meghiúsul, de az újrapróbálkozási időtúllépés vagy az újrapróbálkozások teljes körű ellenőrzése is lehetővé teszi.

Ha a *retryTimeoutInMilliseconds* egy számra (például 2000-re) állítja be, akkor amikor egy munkamenet-művelet meghiúsul, a 2000 ezredmásodperc elteltével újra próbálkozik, mielőtt hibát észlel. Annak érdekében, hogy a munkamenet-szolgáltató alkalmazza ezt az újrapróbálkozási logikát, csak konfigurálja az időkorlátot. Az első újrapróbálkozás 20 ezredmásodperc után történik, ami a legtöbb esetben elegendő a hálózati hiba bekövetkezésekor. Ezt követően a rendszer másodpercenként újra próbálkozik, amíg ki nem fejeződik. Az időtúllépést követően még egyszer újra próbálkozik, hogy a rendszer ne vágja le az időtúllépést (legfeljebb) egy másodpercen belül.

Ha nem hiszem, hogy újra kell próbálkoznia (például ha a Redis-kiszolgálót az alkalmazással megegyező gépen futtatja), vagy ha saját kezűleg szeretné kezelni az újrapróbálkozási logikát, állítsa a *retryTimeoutInMilliseconds* 0-ra.

### <a name="about-redisserializertype"></a>A *redisSerializerType* névjegye

Alapértelmezés szerint a Redis lévő értékek tárolására szolgáló szerializálás a **BinaryFormatter** osztály által megadott bináris formátumban történik. A *redisSerializerType* használatával adja meg a **Microsoft. Web. Redis. ISerializer** nevű osztály szerelvényének minősített típusának nevét, és az egyéni logikával szerializálhatja és deszerializálhatja az értékeket. Íme például egy JSON-szerializáló osztály a JSON.NET használatával:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Feltételezve, hogy ez az osztály egy **MyCompanyDll** nevű szerelvényben van definiálva, a *redisSerializerType* paramétert a következő használatára állíthatja be:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Kimeneti gyorsítótár direktíva

Adjon hozzá egy OutputCache-direktívát minden olyan laphoz, amelyhez gyorsítótárazni kívánja a kimenetet.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

Az előző példában a gyorsítótárazott lap az 60 másodperces gyorsítótárban marad, és az oldal egy másik verzióját gyorsítótárazza az egyes paraméterek kombinációjára. A OutputCache direktívával kapcsolatos további információkért lásd: [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)) .

A lépések elvégzése után az alkalmazás a Redis kimeneti gyorsítótár-szolgáltató használatára van konfigurálva.

## <a name="third-party-output-cache-providers"></a>Külső gyártótól származó kimeneti gyorsítótár-szolgáltatók

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Azure Cache ASP.NET munkamenet-szolgáltatóját a Redis](cache-aspnet-session-state-provider.md).