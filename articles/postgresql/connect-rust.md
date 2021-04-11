---
title: 'Gyors útmutató: kapcsolódjon a rozsda-Azure Database for PostgreSQL – egyetlen kiszolgálóval'
description: Ez a rövid útmutató olyan rozsda-kódrészleteket tartalmaz, amelyekkel összekapcsolhatók és lekérhető Azure Database for PostgreSQL – egyetlen kiszolgálóról származó adatok lekérdezése.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505852"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rövid útmutató: a rozsda használatával csatlakozhat és lekérdezheti Azure Database for PostgreSQL – egyetlen kiszolgáló

Ebből a cikkből megtudhatja, hogyan használhatja a [PostgreSQL-illesztőprogramot a Azure Database for PostgreSQL rozsda](https://github.com/sfackler/rust-postgres) használatához, ha a mintakód által MEGVALÓSÍTott szifilisz-(létrehozási, olvasási, frissítési, törlési) műveleteket vizsgálja. Végül helyileg is futtathatja az alkalmazást, hogy működés közben láthassa.

## <a name="prerequisites"></a>Előfeltételek
Ehhez a rövid útmutatóhoz a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free).
- A [rozsda](https://www.rust-lang.org/tools/install) legújabb verziója telepítve van.
- Egy Azure Database for PostgreSQL egyetlen kiszolgáló – hozzon létre egyet a [Azure Portal](./quickstart-create-server-database-portal.md) használatával <br/> vagy az [Azure CLI](./quickstart-create-server-database-azure-cli.md)-vel.
- Attól függően, hogy nyilvános vagy privát hozzáférést használ-e, a kapcsolat engedélyezéséhez hajtsa végre az alábbi műveletek **egyikét** .

  |Művelet| Kapcsolati mód|Útmutató|
  |:--------- |:--------- |:--------- |
  | **Tűzfalszabályok konfigurálása** | Nyilvános | [Portál](./howto-manage-firewall-using-portal.md) <br/> [Parancssori felület](./howto-manage-firewall-using-cli.md)|
  | **Szolgáltatási végpont konfigurálása** | Nyilvános | [Portál](./howto-manage-vnet-using-portal.md) <br/> [Parancssori felület](./howto-manage-vnet-using-cli.md)|
  | **Privát hivatkozás konfigurálása** | Személyes | [Portál](./howto-configure-privatelink-portal.md) <br/> [Parancssori felület](./howto-configure-privatelink-cli.md) |

- A [git](https://git-scm.com/downloads) telepítve van.

## <a name="get-database-connection-information"></a>Adatbázis-kapcsolatok adatainak beolvasása
Azure Database for PostgreSQL-adatbázishoz való csatlakozáshoz a teljes kiszolgálónév és a bejelentkezési hitelesítő adatok szükségesek. Ezt az információt a Azure Portalból kérheti le.

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza ki a Azure Database for PostgreSQL-kiszolgáló nevét.
1. A kiszolgáló **Áttekintés** lapján másolja a teljes **kiszolgálónevet** és a **rendszergazdai felhasználónevet**. A teljes **kiszolgálónév** mindig a form *\<my-server-name> . postgres.database.Azure.com*, a **rendszergazdai Felhasználónév** pedig mindig *\<my-admin-username>@\<my-server-name>* az űrlap.

## <a name="review-the-code-optional"></a>A kód áttekintése (nem kötelező)

Ha érdekli a kód működésének megismerése, tekintse át az alábbi kódrészleteket. Ellenkező esetben nyugodtan ugorjon [az alkalmazás futtatásához](#run-the-application).

### <a name="connect"></a>Csatlakozás

A `main` függvény a Azure Database for PostgreSQLhoz való csatlakozással indul el, és az alábbi környezeti változóktól függ a kapcsolati információk, a és a esetében `POSTGRES_HOST` `POSTGRES_USER` `POSTGRES_PASSWORD` `POSTGRES_DBNAME` . Alapértelmezés szerint a PostgreSQL-adatbázis szolgáltatás úgy van konfigurálva, hogy megkövetelje a `TLS` kapcsolódást. Letilthatja a szükséges beállítást, `TLS` Ha az ügyfélalkalmazás nem támogatja a `TLS` kapcsolódást. További részletekért tekintse [meg a TLS-kapcsolat konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón](./concepts-ssl-connection-security.md)című témakört.

A cikkben szereplő minta alkalmazás a TLS-t használja a [postgres-OpenSSL láda](https://crates.io/crates/postgres-openssl/)használatával. [postgres:: Client::](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) a kapcsolódási függvény a kapcsolat kezdeményezésére szolgál, és a program kilép abban az esetben, ha ez nem sikerül.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Tábla eldobása és létrehozása

A minta alkalmazás egy egyszerű táblázatot használ a `inventory` szifilisz (létrehozás, olvasás, frissítés, törlés) műveleteinek bemutatására.

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

A `drop_create_table` függvény először a táblát próbálja meg `DROP` `inventory` létrehozni egy új létrehozása előtt. Ez megkönnyíti a tanulást/kísérletezést, mivel mindig ismert (tiszta) állapottal kezdődik. Az [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) metódus a létrehozási és a eldobási műveletekhez használatos. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Adat beszúrása

`insert_data` bejegyzéseket szúr be a `inventory` táblába. Létrehoz egy [előkészített utasítást](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) az [előkészítési](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) függvénnyel. 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Jegyezze fel [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) metódus használatát is, amely lehetővé teszi, hogy a lekérdezési paraméterek típusai explicit módon legyenek megadva.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Végezetül a `for` rendszer hurkot használ `item-3` , `item-4` és `item-5` véletlenszerűen generált mennyiséggel adja hozzá őket.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Adatok lekérdezése

`query_data` a függvény azt mutatja be, hogyan lehet beolvasni az adatok a `inventory` táblából. A [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) metódussal egy elemmel lehet beolvasni az elemeket `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

A leltár tábla összes sorát a `select * from` [lekérdezési](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) módszer használatával lekéri a rendszer. A visszaadott sorok a [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get)paranccsal lesznek megismételve az egyes oszlopok értékének kinyeréséhez. 

> [!TIP]
> Figyelje `get` meg, hogy az oszlop a sorban lévő numerikus indexével vagy az oszlop nevével adható meg.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Adatok frissítése

A `update_date` függvény véletlenszerűen frissíti az összes elem mennyiségét. Mivel a `insert_data` függvény hozzáadott `5` sorokat, ugyanezt veszi figyelembe a `for` hurokban. `for n in 1..=5`

> [!TIP]
> Vegye figyelembe, hogy a helyett a következőt vesszük igénybe: az `query` `execute` `id` újonnan generált `quantity` ( [visszatérési záradék](https://www.postgresql.org/docs/current/dml-returning.html)használatával).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Adat törlése

Végül a `delete` függvény bemutatja, hogyan távolíthat el egy elemet a `inventory` táblából a használatával `id` . A `id` véletlenszerűen van kiválasztva – ez egy véletlenszerű egész szám `1` (a-ben `5` ), `5` mivel a `insert_data` függvény hozzáadott `5` sorokat a kezdéshez. 

> [!TIP]
> Vegye figyelembe, hogy a `query` helyett a `execute` következőt használjuk, mert vissza szeretnénk térni az imént törölt elemek adatairól ( [visszatérési záradék](https://www.postgresql.org/docs/current/dml-returning.html)használatával).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. A kezdéshez futtassa a következő parancsot a minta tárház klónozásához:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Állítsa be a szükséges környezeti változókat a Azure Portalból másolt értékekkel:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Az alkalmazás futtatásához váltson át abba a könyvtárba, ahová klónozott, majd végre kell hajtania a következőt `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. A megerősítéshez a [psql használatával](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) is csatlakozhat Azure Database for PostgreSQLhoz, és lekérdezéseket futtathat az adatbázison, például:

    ```sql
    select * from inventory;
    ```

[Problémák léptek fel? Tudassa velünk](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban használt összes erőforrás törléséhez törölje az erőforráscsoportot a következő parancs használatával:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL-kiszolgáló kezelése a portál használatával](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL-kiszolgáló kezelése a parancssori felület használatával](./how-to-manage-server-cli.md)<br/>

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/postgres-doc-feedback)
