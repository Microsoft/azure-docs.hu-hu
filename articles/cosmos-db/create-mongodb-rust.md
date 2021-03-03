---
title: Rozsda-alkalmazás összekötése Azure Cosmos DB API-MongoDB
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Cosmos DB API-MongoDB által támogatott rozsda-alkalmazást.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: b5dbb8498157096c8e3178175f827eb47591c9de
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659919"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: rozsda-alkalmazás összekötése Azure Cosmos DB API-MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel. A cikkben bemutatott minta egy egyszerű parancssori alkalmazás, amely a [MongoDB-hez készült rozsda-illesztőprogramot](https://github.com/mongodb/mongo-rust-driver)használja. Mivel a MongoDB Azure Cosmos DB API-je [kompatibilis a MongoDB Wire protokollal](./mongodb-introduction.md#wire-protocol-compatibility), lehetséges, hogy bármely MongoDB-ügyfél illesztőprogramja csatlakozhat hozzá.

Ebből a cikkből megtudhatja, hogyan használhatja a MongoDB API-ját a MongoDB rozsda-Azure Cosmos DB illesztőprogram használatával a mintakód által megvalósított szifilisz-(létrehozási, olvasási, frissítési, törlési) műveletek feltárásával. Végül helyileg is futtathatja az alkalmazást, hogy működés közben láthassa.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a kapcsolatok karakterláncával `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Rozsda](https://www.rust-lang.org/tools/install) (1,39-es vagy újabb verzió)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB beállítása

Azure Cosmos DB-fiók beállításához kövesse az [alábbi utasításokat](create-mongodb-dotnet.md). Az alkalmazásnak szüksége lesz a MongoDB-kapcsolatok karakterláncára, amelyet a Azure Portal használatával lehet beolvasni. Részletekért lásd: [a MongoDB kapcsolati karakterláncának beolvasása a testreszabáshoz](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Az alkalmazás futtatása

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Az alábbi parancsok futtatásával klónozza a mintatárházat.

1. Nyisson meg egy parancssort, hozzon létre egy nevű új mappát `git-samples` , majd kattintson a parancssor bezárására.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

A bináris fájl felépítése:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Az alkalmazás konfigurálása 

Exportálja a kapcsolatok karakterláncát, a MongoDB-adatbázist és a gyűjtemény nevét környezeti változókként. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A `ssl=true` beállítás Cosmos db követelmények miatt fontos. További információ: a [kapcsolatok karakterláncával kapcsolatos követelmények](connect-mongodb-account.md#connection-string-requirements).
>

A `MONGODB_URL` környezeti változó esetében cserélje le a és a helyőrzőit `<COSMOSDB_ACCOUNT_NAME>``<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: A létrehozott Azure Cosmos DB fiók neve
- `<COSMOSDB_PASSWORD>`: Az előző lépésben kinyert adatbázis-kulcs

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Kiválaszthatja a kívánt értékeit, vagy megadhatja `MONGODB_DATABASE` `MONGODB_COLLECTION` őket.

Az alkalmazás futtatásához váltson a megfelelő mappára (ahol létezik az alkalmazás bináris fájlja):

```bash
cd target/release
```

Hozzon létre egy `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Ha a művelet sikeres, az újonnan létrehozott dokumentum MongoDB származó kimenetnek kell megjelennie `_id` :

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Hozzon létre egy másikat `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Az összes s listázása `todo`

```bash
./todo list all
```

Ekkor meg kell jelennie az imént hozzáadottnak:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Egy (például a status) állapotának frissítéséhez `todo` `completed` használja az azonosítót az alábbiak `todo` szerint:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Csak a befejezett k listázása `todo`

```bash
./todo list completed
```

Látnia kell az imént frissített

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

A `todo` felhasználó azonosítójának törlése

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Az `todo` s jóváhagyása

```bash
./todo list all
```

Az `todo` imént törölt érték nem lehet jelen.

### <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Azure Cosmos DBban tárolt adatkészletek a Azure Portal megtekintésére és lekérdezésére használhatók.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be a **Azure Cosmos db** kifejezést. Amikor megnyílik a Cosmos-fiók panel, válassza ki a Cosmos-fiókját. A bal oldali navigációs sávon válassza a **adatkezelő** lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket.

## <a name="review-the-code-optional"></a>A kód áttekintése (nem kötelező)

Ha érdekli az alkalmazás működésének megismerése, tekintse át a kódrészleteket ebben a szakaszban. Az alábbi kódrészletek a `src/main.rs` fájlból származnak.

A `main` függvény az alkalmazás belépési pontja `todo` . A rendszer a környezeti változó által a MongoDB számára elérhető Azure Cosmos DB API-hoz tartozó kapcsolódási URL-címet várja `MONGODB_URL` . Létrejön egy új példány `TodoManager` , amelyet egy olyan [ `match` kifejezés](https://doc.rust-lang.org/book/ch06-02-match.html) követ, amely a `TodoManager` felhasználó által választott művelet alapján a megfelelő metódusra delegál `create` `update` `list` `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` a egy `struct` [mongodb:: Sync:: Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Ha a függvény használatával próbál létrehozni egy `TodoManager` , a `new` Azure Cosmos db API-MongoDB való kapcsolódást kezdeményez.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

A legfontosabb, `TodoManager` hogy olyan metódusokkal rendelkezik, amelyek segítik az `todo` s kezelését. Nézzük át őket egymástól.

A `add_todo` metódus a `todo` felhasználó által megadott leírást veszi fel, és létrehoz egy `Todo` struct-példányt, amely az alábbihoz hasonlóan néz ki. A [serde](https://github.com/serde-rs/serde) -keretrendszer a BSON-példányok (szerializálás/deszerializálás) szerkezetek példányaiba való leképezésére szolgál `Todo` . Figyelje `serde` meg, hogy a rendszer hogyan használja a mezők attribútumait a szerializálási/serialzation folyamat testreszabásához. Például `todo_id` a ToDo mezője egy, `struct` és a `ObjectId` MongoDB-ben tárolódik `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) elfogad egy [dokumentumot](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) , amely a `todo` felvenni kívánt adatokat jelképezi. Vegye figyelembe, hogy a és a közötti átalakítás `Todo` `Document` kétlépéses folyamat, amely [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) és [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document)együttes használatával érhető el.

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

A [Collection. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) az *összes* ok beolvasása, `todo` illetve a felhasználó által megadott állapot ( `pending` vagy) alapján történő szűrők beolvasására szolgál. `completed` Vegye figyelembe, hogy a `while` hurokban a `Document` Keresés eredményeként kapott összes eredmény a `Todo` [bson:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html)használatával konvertálódik struktúrába. Ez a metódusban történtek ellentéte `add_todo` .

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

Az `todo` állapot frissíthető ( `pending` `completed` vagy fordítva) a használatával. A a következő `todo` bson konvertálódik [:: OID:: ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) , amelyet ezután a[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) metódus használ a frissítendő dokumentum megkereséséhez.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

A `todo` [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) metódus használatával egyszerűen törölhető egy.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB MongoDB API-fiókot a Azure Cloud Shell használatával, és hogyan hozhat létre és futtathat egy rozsda parancssori alkalmazást az s kezeléséhez `todo` . Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)