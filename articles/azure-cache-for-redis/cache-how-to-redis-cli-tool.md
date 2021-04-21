---
title: A redis-cli használata a Azure Cache for Redis
description: Megtudhatja, hogyan *használhatja aredis-cli.exe* parancssori eszközként a Azure Cache for Redis ügyfélként való interakcióhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 042db51ab51e9da586f028b8aa99e66ffaa36639
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775672"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>A Redis parancssori eszköz használata Azure Cache for Redis

*redis-cli.exe* egy népszerű parancssori eszköz, amely ügyfélként Azure Cache for Redis egy alkalmazással. Ez az eszköz a Azure Cache for Redis.

Az eszköz Windows-platformokon a [Redis windowsos parancssori](https://github.com/MSOpenTech/redis/releases/)eszközeinek letöltésével érhető el. 

Ha a parancssori eszközt egy másik platformon szeretné futtatni, töltse le a Azure Cache for Redis [https://redis.io/download](https://redis.io/download) ről.

## <a name="gather-cache-access-information"></a>Gyorsítótár-hozzáférési információk gyűjtése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A gyorsítótár eléréséhez szükséges adatokat három módszerrel gyűjtheti össze:

1. Azure CLI az [az redis list-keys használatával](/cli/azure/redis#az_redis_list_keys)
2. Azure PowerShell [Get-AzRedisCacheKey használatával](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Az Azure Portalon.

Ebben a szakaszban lekéri a kulcsokat a Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Hozzáférés engedélyezése a redis-cli.exe

A Azure Cache for Redis alapértelmezés szerint csak a TLS-port (6380) van engedélyezve. A `redis-cli.exe` parancssori eszköz nem támogatja a TLS-t. Két konfigurációs lehetőség közül választhat:

1. [A nem TLS-port engedélyezése (6379)](cache-configure.md#access-ports)  -  **Ez a konfiguráció nem ajánlott,** mert ebben a konfigurációban a hozzáférési kulcsokat a TCP-n keresztül, tiszta szövegként küldi el a rendszer. Ez a módosítás veszélyeztetheti a gyorsítótárhoz való hozzáférést. Ezt a konfigurációt csak akkor érdemes megfontolni, ha csak egy tesztgyorsítótárhoz fér hozzá.

2. Töltse le és telepítse a [stunnel programot.](https://www.stunnel.org/downloads.html)

    Futtassa **a stunnel GUI Startot** a kiszolgáló indításhoz.

    Kattintson a jobb gombbal a helyettes kiszolgáló tálcájának ikonjára, majd kattintson **a Naplóablak megjelenítése parancsra.**

    A helyettes naplóablak menüjében kattintson a **Konfiguráció** szerkesztése elemre az aktuális konfigurációs fájl  >   megnyitásához.

    Adja hozzá a következő bejegyzést *aredis-cli.exe* a **Szolgáltatásdefiníciók szakaszban.** Szúrja be a gyorsítótár tényleges nevét a `yourcachename` helyére. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárja be a konfigurációs fájlt. 
  
    A helyettes naplóablak menüjében kattintson a **Konfiguráció**  >  **újratöltése elemre.**


## <a name="connect-using-the-redis-command-line-tool"></a>Csatlakozás a Redis parancssori eszköz használatával.

A stunnel használata esetén futtassa *aredis-cli.exe,* és csak a *portot* és a hozzáférési kulcsot *(elsődleges* vagy másodlagos) adja át a gyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![A gyorsítótárral való kapcsolat sikeres csatlakozását bemutató képernyőkép.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Ha nem biztonságos TLS-porttal használ egy tesztgyorsítótárat, futtassa és adja át a gazdagépnevet, a  portot és a hozzáférési kulcsot (elsődleges vagy másodlagos) a tesztgyorsítótárhoz való `redis-cli.exe` csatlakozáshoz.   

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel és redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Következő lépések

További információ a [Redis-konzol parancsok](cache-configure.md#redis-console) kiadásához való használatával kapcsolatban.