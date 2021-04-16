---
title: 'Rövid útmutató: Az Azure Cache for Redis használata Pythonban'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan Python-alkalmazást, amely Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: cache
ms.devlang: python
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: 40990dfb651817cf52cd5b5a039566e3209d6ac7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532042"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Rövid útmutató: Az Azure Cache for Redis használata Pythonban

Ebben a cikkben a Azure Cache for Redis egy Python-alkalmazásba építi be, hogy hozzáférjen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-ban bármely alkalmazásból elérhető.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a [Python GitHubon elérhető rövid](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) útmutatóját.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- [Python 2 vagy 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Új Azure Cache for Redis létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>A redis-py telepítése

[A Redis-py](https://github.com/andymccurdy/redis-py) egy Python-felület a Azure Cache for Redis. A *redis-py* csomag parancssorból való telepítéséhez használja a python packages *eszközt (pip).* 

Az alábbi példa a *pip3* python 3-hoz való használatával telepíti a *redis-py* Windows 10 egy rendszergazdai parancssorból.

![A redis-py Python-felület telepítése a Azure Cache for Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Olvasás és írás a gyorsítótárban

Futtassa a Pythont a parancssorból, és tesztelje a gyorsítótárat az alábbi kóddal. Cserélje le a és a `<Your Host Name>` `<Your Access Key>` értékeket a Azure Cache for Redis értékeire. Az állomásnév *\<DNS name> .redis.cache.windows.net.*

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> A Azure Cache for Redis 3.0-s vagy újabb verziója esetén a TLS/SSL-tanúsítvány ellenőrzése kényszerítve van. ssl_ca_certs kell beállítani, amikor a felhasználó Azure Cache for Redis. RedHat Linux esetén ssl_ca_certs az */etc/pki/tls/certs/ca-bundle.crt* tanúsítványmodulban található.

## <a name="create-a-python-sample-app"></a>Python-mintaalkalmazás létrehozása

Hozzon létre egy új szövegfájlt, adja hozzá a következő szkriptet, és mentse a fájlt *az PythonApplication1.py.* Cserélje le a és a `<Your Host Name>` `<Your Access Key>` értékeket a Azure Cache for Redis értékeire. Az állomásnév *\<DNS name> .redis.cache.windows.net.*

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Futtassa *PythonApplication1.py* Pythonnal. Az alábbi példához hasonló eredményeket kell látnia:

![Python-szkript futtatása a gyorsítótár-hozzáférés tesztelésére](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az ebben a rövid útmutatóban létrehozott Azure-erőforráscsoportot és -erőforrásokat, a díjak elkerülése érdekében törölheti őket.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszafordítható, és az erőforráscsoport és a benne elérhető összes erőforrás véglegesen törlődik. Ha a saját Azure Cache for Redis egy meglévő erőforráscsoportban hozta létre, csak a gyorsítótárat  törölheti a gyorsítótár Áttekintés lapján a Törlés **lehetőség kiválasztásával.** 

Az erőforráscsoport és az azure-Redis Cache törlése:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza az **Erőforráscsoportok lehetőséget.**
1. A Szűrés **név alapján szövegmezőbe** írja be a gyorsítótárpéldányt tartalmazó erőforráscsoport nevét, majd válassza ki a keresési eredmények közül. 
1. Az erőforráscsoport oldalán válassza az **Erőforráscsoport törlése lehetőséget.**
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**
   
   ![Erőforráscsoport törlése a Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hozzon létre ASP.NET webalkalmazást, amely egy Azure Cache for Redis.](./cache-web-app-howto.md)
