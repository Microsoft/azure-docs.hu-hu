---
title: 'Rövid útmutató: A Azure Cache for Redis használata Javában'
description: Ebben a rövid útmutatóban egy új Java-alkalmazást fog létrehozni, amely a Azure Cache for Redis
author: yegu-ms
ms.author: yegu
ms.date: 05/22/2020
ms.topic: quickstart
ms.service: cache
ms.devlang: java
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 7128ff1e20439c57d3e6212f29e6f871997584c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538614"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Rövid útmutató: A Azure Cache for Redis használata Javában

Ebben a rövid útmutatóban a Azure Cache for Redis egy Java-alkalmazásba építi be a [Jedis](https://github.com/xetorthio/jedis) Redis-ügyfelet használva, hogy hozzáférjen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-ban bármely alkalmazásból elérhető.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a [GitHubon](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/java) elérhető Java rövid útmutatót.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Az Azure Cache for Redis létrehozása

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>A munkakörnyezet beállítása 

Az operációs rendszertől függően adjon hozzá környezeti változókat a **gazdagépnévhez** és az **elsődleges hozzáférési kulcshoz.** Nyisson meg egy parancssort vagy egy terminálablakot, és állítsa be a következő értékeket:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Cserélje le a helyőrzőket a következő értékekre:

- `<YOUR_HOST_NAME>`: A DNS-állomásnév,  amely a fürt erőforrásának Tulajdonságok Azure Cache for Redis a Azure Portal.
- `<YOUR_PRIMARY_ACCESS_KEY>`: Az elsődleges hozzáférési kulcs, amely a Azure Cache for Redis erőforrásÁnak Hozzáférési kulcsok szakaszában Azure Portal. 

## <a name="create-a-new-java-app"></a>Új Java-alkalmazás létrehozása

A Maven használatával hozzon létre egy új gyorsindító alkalmazást:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Váltson át az új *redistest* projektkönyvtárra.

Nyissa meg a *pom.xml* fájlt, és adjon hozzá egy függőséget a [Jedis](https://github.com/xetorthio/jedis) paraméterhez:

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>3.2.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Mentse a *pom.xml* fájlt.

Nyissa meg az *App.java* fájlt, és cserélje le a kódot a következőre:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Ez a kód bemutatja, hogyan csatlakozhat egy Azure Cache for Redis-példányhoz a gyorsítótár gazdagépnevével és a fő környezeti változók használatával. A kód emellett tárolja és lekéri gyorsítótár egyik sztringértékét. A rendszer a `PING` és a `CLIENT LIST` parancsot is végrehajtja. 

Mentse az *App.java* fájlt.

## <a name="build-and-run-the-app"></a>Készítsen buildet és futtassa az alkalmazást

Hajtsa végre a következő Maven-parancsot az alkalmazás buildeléséhez és elindításához:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![Azure Cache for Redis alkalmazás befejeződött](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

1. A Szűrés **név alapján szövegmezőbe** írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában az erőforráscsoportban válassza a **...** lehetőséget, majd **az Erőforráscsoport törlése lehetőséget.**

   ![Azure-erőforráscsoport törölve](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. Írja be az erőforráscsoport nevét a megerősítéshez, majd válassza a **Törlés lehetőséget.**

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja Azure Cache for Redis Java-alkalmazásokból származó alkalmazásból származó alkalmazásokat. Folytassa a következő rövid útmutatóval, és használja Azure Cache for Redis egy ASP.NET webalkalmazással.

> [!div class="nextstepaction"]
> [Hozzon létre ASP.NET webalkalmazást, amely egy Azure Cache for Redis.](./cache-web-app-howto.md)
