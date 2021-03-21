---
title: Az Azure cache használata az Redis és a go használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre olyan go-alkalmazást, amely Azure cache-t használ a Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 04b582b5ef31e61039c5513ea2a4aa60f1c638e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121337"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Gyors útmutató: az Azure cache használata a go-Redis

Ebből a cikkből megtudhatja, hogyan hozhat létre olyan REST API a Go-ban, amely a [Redis készült Azure cache](./cache-overview.md)-ben tárolja és lekéri a [kivonatoló](https://redis.io/topics/data-types-intro#redis-hashes) adatstruktúra által támogatott felhasználói adatokat. 

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a gyors [útmutató a](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) githubon című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (lehetőleg 1,13-es vagy újabb verzió)
- [Git](https://git-scm.com/downloads)
- Ilyen [curl](https://curl.se/) http-ügyfél

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>A kód áttekintése (nem kötelező)

Ha érdekli a kód működésének megismerése, tekintse át az alábbi kódrészleteket. Ellenkező esetben nyugodtan ugorjon [az alkalmazás futtatásához](#run-the-application).

A nyílt forráskódú [Go-Redis](https://github.com/go-redis/redis) függvénytár az Azure cache Redis való együttműködésére szolgál.

A `main` függvény elindul az Azure cache Redis-példányhoz tartozó állomásnév és jelszó (hozzáférési kulcs) beolvasásával.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Ezután kapcsolatot létesít az Azure cache-vel a Redis-hez. Vegye figyelembe, hogy [tls.Config](https://golang.org/pkg/crypto/tls/#Config) használatban van – az Azure cache for Redis csak [a minimálisan szükséges verzióként](cache-remove-tls-10-11.md)fogadja el a TLS 1,2 biztonságos kapcsolatait.

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Ha a kapcsolódás sikeres, a [http-kezelők](https://golang.org/pkg/net/http/#HandleFunc) a kezelésre `POST` és `GET` a működésre vannak KONFIGURÁLVA, és a HTTP-kiszolgáló elindult. 

> [!NOTE] 
> a [gorilla mux könyvtára](https://github.com/gorilla/mux) az útválasztáshoz használatos (bár ez nem feltétlenül szükséges, és a szabványos függvénytár használatával a minta alkalmazáshoz).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` a struct egy Redis ágyaz be [. ](https://pkg.go.dev/github.com/go-redis/redis/v8#Client)A, a által használt-ügyfél, a módszerek `createUser` `getUser` kódja nem része a rövidség kedvéért. 

- `createUser`: elfogad egy JSON-adattartalmat (amely tartalmazza a felhasználói adatokat), és `HASH` Az Azure cache-ben menti a Redis.
- `getUser`: a felhasználói adatok beolvasása `HASH` vagy egy http-válasz beolvasása, `404` Ha nem található.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Először klónozással indítsa el az alkalmazást a GitHubról.

1. Nyisson meg egy parancssort, és hozzon létre egy nevű új mappát `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Nyisson meg egy git-terminálablakot, például a git basht. A `cd` paranccsal váltson át az új mappába, ahol a minta alkalmazást klónozás útján fogja használni.

    ```bash
    cd "C:\git-samples"
    ```

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás környezeti változók formájában fogadja el a kapcsolódási és a hitelesítő adatokat. 

1. Az Azure cache for Redis példányhoz tartozó **állomásnév** és **hozzáférési kulcsok** beolvasása az [Azure Portal](https://portal.azure.com/)

1. Állítsa be a megfelelő környezeti változókat:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. A terminál ablakban váltson a megfelelő mappára. Például:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. A terminálon futtassa az alábbi parancsot az alkalmazás elindításához.

    ```shell
    go run main.go
    ```

A HTTP-kiszolgáló a porton fog kezdődni `8080` .

## <a name="test-the-application"></a>Az alkalmazás tesztelése

1. Hozzon létre néhány felhasználói bejegyzést. Az alábbi példa a curl-t használja:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Meglévő felhasználó beolvasása az alábbiakkal `id` :

    ```bash
    curl -i localhost:8080/users/1
    ```

    A JSON-választ a következőképpen kell lekérnie:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Ha nem létező felhasználót próbál meg beolvasni, HTTP-t fog kapni `404` . Például:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte az Azure-erőforráscsoportot és az ebben a rövid útmutatóban létrehozott erőforrásokat, törölheti őket a díjak elkerülése érdekében.

> [!IMPORTANT]
> Egy erőforráscsoport törlése nem vonható vissza, és az erőforráscsoport és az ahhoz tartozó összes erőforrás véglegesen törölve lesz. Ha létrehozta az Azure cache-t a Redis-példányhoz egy meglévő erőforráscsoporthoz, amelyet meg szeretne őrizni, törölheti a gyorsítótár **Áttekintés** lapján található **Törlés** lehetőség kiválasztásával. 

Az erőforráscsoport és az Azure-példány Redis Cacheának törlése:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.
1. A **szűrés név alapján** szövegmezőbe írja be annak az erőforráscsoportnak a nevét, amely tartalmazza a gyorsítótár-példányt, majd válassza ki a keresési eredmények közül. 
1. Az erőforráscsoport lapon válassza az **erőforráscsoport törlése** elemet.
1. Írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.
   
   ![Az Azure cache Redis tartozó erőforráscsoport törlése](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan kezdheti el az Azure cache használatát a Redis. Egy egyszerű REST API-alapú alkalmazást konfigurált és futtatott egy Redis-adatstruktúra által támogatott felhasználói adatok létrehozására és lekérésére `HASH` .

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)
