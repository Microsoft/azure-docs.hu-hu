---
title: A Redis-CLI használata az Azure cache használatával a Redis
description: Megtudhatja, hogyan használhatja a *redis-cli.exe* parancssori eszközként az Redis-ügyfélként használt Azure cache-sel való interakcióhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: e4f5fc7290b45f65067f6711f70476e13a010223
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183386"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>A Redis parancssori eszköz használata az Azure cache használatával a Redis

A *redis-cli.exe* egy népszerű parancssori eszköz, amely a Redis-ügyfélként használható Azure cache-t használja. Ez az eszköz a Redis készült Azure cache szolgáltatással is használható.

Az eszköz a Windows-platformokhoz is elérhető a [Windows Redis parancssori eszközeinek](https://github.com/MSOpenTech/redis/releases/)letöltésével. 

Ha egy másik platformon szeretné futtatni a parancssori eszközt, töltse le az Azure cache-t a Redis [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Gyorsítótár-hozzáférési információk összegyűjtése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A gyorsítótár eléréséhez a következő három módszer használatával gyűjthet adatokat:

1. Azure CLI [az az Redis List-Keys](/cli/azure/redis#az-redis-list-keys) használatával
2. Azure PowerShell a [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey) használatával
3. Az Azure Portalon.

Ebben a szakaszban a kulcsokat a Azure Portal fogja lekérni.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>redis-cli.exe hozzáférésének engedélyezése

Az Azure cache for Redis esetében a rendszer alapértelmezés szerint csak a TLS-portot (6380) engedélyezi. A `redis-cli.exe` parancssori eszköz nem támogatja a TLS-t. Két konfigurációs lehetőség közül választhat:

1. [A nem TLS port engedélyezése (6379)](cache-configure.md#access-ports)  -  **Ez a konfiguráció nem ajánlott** , mert ebben a konfigurációban a hozzáférési kulcsokat a TCP protokollon keresztül, tiszta szövegként küldik el. Ez a módosítás a gyorsítótárhoz való hozzáférést is veszélyeztetheti. Az egyetlen forgatókönyv, ahol érdemes megfontolni ezt a konfigurációt, ha csak egy tesztelési gyorsítótárhoz fér hozzá.

2. Töltse le és telepítse a [stunnel](https://www.stunnel.org/downloads.html).

    Futtassa a **stunnel grafikus felhasználói felületét** , és indítsa el a kiszolgálót.

    Kattintson a jobb gombbal a stunnel-kiszolgáló tálcán látható ikonjára, majd kattintson a **napló megjelenítése ablakra**.

    Az aktuális konfigurációs fájl megnyitásához a stunnel-napló ablak menüjében **kattintson a konfiguráció**  >  **szerkesztése konfiguráció** elemre.

    Adja hozzá a következő bejegyzést a *redis-cli.exehoz* a **szolgáltatási definíciók** szakaszban. Szúrja be a tényleges gyorsítótár nevét a helyére `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárjuk be a konfigurációs fájlt. 
  
    A stunnel-napló ablak menüjében kattintson a **konfiguráció**  >  **újratöltésének konfigurációja** elemre.


## <a name="connect-using-the-redis-command-line-tool"></a>Kapcsolódjon a Redis parancssori eszköz használatával.

A stunnel használatakor futtassa *redis-cli.exe*, és csak a *portot*, a *hozzáférési kulcsot* (elsődleges vagy másodlagos) a gyorsítótárhoz való csatlakozáshoz adja át.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Képernyőkép, amely azt mutatja, hogy a gyorsítótárhoz való kapcsolódás sikeres.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Ha tesztelési gyorsítótárat használ a nem **biztonságos** TLS-porthoz, futtassa `redis-cli.exe` és adja át az *állomásnevet*, a *portot* és a *hozzáférési kulcsot* (elsődleges vagy másodlagos) a tesztelési gyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel és Redis – parancssori felület](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Következő lépések

További információ a Redis- [konzolnak](cache-configure.md#redis-console) a parancsok kiküldéséhez való használatáról.