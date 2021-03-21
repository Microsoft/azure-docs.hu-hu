---
title: Az Azure cache használata a Redis és a rozsda használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti az Azure cache-t a Redis a rozsda használatával.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121099"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Gyors útmutató: az Azure cache használata a Redis és a rozsda használatával

Ebből a cikkből megtudhatja, hogyan használhatja a [rozsda programozási nyelvét](https://www.rust-lang.org/) az [Azure cache Redis](./cache-overview.md)való interakcióhoz. Bemutatja a leggyakrabban használt Redis-adatstruktúrák példáit, például a [karakterláncot](https://redis.io/topics/data-types-intro#redis-strings), a [kivonatot](https://redis.io/topics/data-types-intro#redis-hashes), a [listázást](https://redis.io/topics/data-types-intro#redis-lists) stb. a Redis [Redis-RS](https://github.com/mitsuhiko/redis-rs) függvénytárának használata. Ez az ügyfél magas és alacsony szintű API-kat tesz elérhetővé, és a jelen cikkben ismertetett mintakód segítségével mindkét stílust megtekintheti működés közben.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha egyenesen a kódra szeretne ugrani, tekintse meg a [rozsda](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) rövid útmutatóját a githubon.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Rozsda](https://www.rust-lang.org/tools/install) (1,39-es vagy újabb verzió)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>A kód áttekintése (nem kötelező)

Ha érdekli a kód működésének megismerése, tekintse át az alábbi kódrészleteket. Ellenkező esetben nyugodtan ugorjon [az alkalmazás futtatásához](#run-the-application).

A `connect` függvény a Redis Azure cache-hez való kapcsolat létesítésére szolgál. A szolgáltatás az állomásnév és a jelszó (hozzáférési kulcs) értékét a környezeti változókon és a-n keresztül adja át `REDIS_HOSTNAME` `REDIS_PASSWORD` . A kapcsolati URL-cím formátuma: az `rediss://<username>:<password>@<hostname>` Azure cache for Redis csak a TLS 1,2 biztonságos kapcsolatokat fogadja el [a minimálisan szükséges verzióként](cache-remove-tls-10-11.md).

A [Redis:: Client:: Open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) meghívása alapszintű érvényesítést végez, miközben [get_connection ()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) ténylegesen kezdeményezi a kapcsolatot – a program leáll, ha a kapcsolat valamilyen okból kifolyólag meghiúsul, például helytelen jelszó miatt.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

A `basics` függvény a [set](https://redis.io/commands/set), a [Get](https://redis.io/commands/get)és a [incr](https://redis.io/commands/incr) parancsokat fedi le. Az alacsony szintű API a és a esetében használatos `SET` `GET` , amely beállítja és lekéri a nevű kulcs értékét `foo` . A `INCRBY` parancs végrehajtása egy magas szintű API-val történik, azaz a [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) megnöveli egy kulcs (named) értékét, amelyet a lekéréses `counter` `2` hívás követ. [](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get)

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Az alábbi kódrészlet a Redis-adatstruktúra funkcióit mutatja be `HASH` . A [HSET](https://redis.io/commands/hset) az alacsony szintű API-val hívja meg a `name` `version` `repo` Redis-illesztőprogramok (,) információinak tárolására (,). Például a rozsda-illesztőprogram részleteit egy [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) formájában rögzíti a rendszer, majd továbbítja az alacsony szintű API-nak. Ezt követően a rendszer a [HGETALL](https://redis.io/commands/hgetall)használatával kérdezi le.

`HSET` egy magas szintű API-val is végrehajtható a [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) használatával, amely a rekordok tömbjét fogadja el. Ekkor a rendszer végrehajtja az [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) egy adott attribútum (ebben az esetben) értékének beolvasásához `repo` .

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

Az alábbi függvényben láthatja, hogyan használható az `LIST` adatstruktúra. A rendszer végrehajtja a [LPUSH](https://redis.io/commands/lpush) (az alacsony szintű API-val), hogy hozzáadjon egy bejegyzést a listához, és a magas szintű [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) módszert használja a rendszer a listából. Ezt követően a [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) metódussal több bejegyzést is hozzáadhat a listához, amelyeket az alacsony szintű [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) metódussal lehet lekérni.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Itt láthat néhány `SET` műveletet. A [Sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (magas szintű API) metódussal néhány bejegyzést adhat hozzá egy `SET` névvel ellátotthoz `users` . Ekkor a rendszer végrehajtja a [SISMEMBER](https://redis.io/commands/hset) (alacsony szintű API) annak ellenőrzéséhez, hogy létezik-e `user1` . Végezetül a [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) egy vektor ([VEC <String> ](https://doc.rust-lang.org/std/vec/struct.Vec.html)) formájában az összes beállított bejegyzés beolvasására és megismétlésére szolgál.

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` az alábbi függvény a rendezett készlet adatstruktúráját mutatja be. A rendszer meghívja a [ZADD](https://redis.io/commands/zadd) (az alacsony szintű API-val) egy véletlenszerű egész pontszám hozzáadásához egy lejátszóhoz ( `player-1` ). Ezután a [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) metódus (magas szintű API) több játékos ( `player-2` a `player-5` ) és a hozzá tartozó (véletlenszerűen generált) pontszámok hozzáadására szolgál. A rendezett készlet bejegyzéseinek száma a [ZCARD](https://redis.io/commands/zcard) használatával van kiértékelve, és a [ZRANGE](https://redis.io/commands/zrange) -parancsra (az alacsony szintű API-val meghívott) vonatkozó korlátként van használva, hogy a játékosok növekvő sorrendbe kerüljenek a pontszámok.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás környezeti változók formájában fogadja el a kapcsolódási és a hitelesítő adatokat. 

1. A [Azure Portal](https://portal.azure.com/)Redis-példányához tartozó **állomásnév** és **elérési kulcsok** beolvasása az Azure cache-hez.

1. Állítsa be a megfelelő környezeti változókat:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. A terminál ablakban váltson a megfelelő mappára. Például:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. A terminálon futtassa az alábbi parancsot az alkalmazás elindításához.

    ```shell
    cargo run
    ```
    
    A kimenet a következőképpen fog megjelenni:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Ha egy adott függvényt szeretne futtatni, tegye megjegyzésbe a függvény egyéb funkcióit `main` :
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
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

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Redis-hez készült rozsda-illesztőprogramot a Redis-hez készült Azure cache-ben való kapcsolódáshoz és végrehajtáshoz.

> [!div class="nextstepaction"]
> [Hozzon létre egy egyszerű ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)
