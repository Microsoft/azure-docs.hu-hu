---
title: Go-alkalmazás létrehozása Azure Cosmos DB Cassandra API használatával az gocql-ügyféllel
description: Ez a rövid útmutató bemutatja, hogyan használható a go-ügyfél a Azure Cosmos DB Cassandra API való interakcióhoz
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93076401"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Gyors útmutató: go-alkalmazás létrehozása az `gocql` ügyféllel a Azure Cosmos DB Cassandra API-alapú adatkezeléshez
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel. Ebben a rövid útmutatóban egy Azure Cosmos DB Cassandra API-fiók létrehozásával fog kezdődni. Ezután futtasson egy go-alkalmazást, és hozzon létre egy Cassandra-területet, egy táblázatot, és hajtson végre néhány műveletet. Ez a Go-alkalmazás az [gocql](https://github.com/gocql/gocql)-t használja, amely a go nyelvhez tartozó Cassandra-ügyfél. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) ingyen Azure-előfizetés nélkül.
- [Lépjen](https://golang.org/) a számítógépre, és folytassa a munkát.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Adatbázis létrehozása előtt létre kell hoznia egy Cassandra-fiókot Azure Cosmos DB használatával.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Először klónozással indítsa el az alkalmazást a GitHubról.

1. Nyisson meg egy parancssort, és hozzon létre egy nevű új mappát `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git-terminálablakot, például a git basht. A `cd` paranccsal váltson át az új mappába, és telepítse a minta alkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha szeretné megtudni, hogyan hozza létre az adatbázis-erőforrásokat a kód, tekintse át a következő kódrészleteket. Ellenkező esetben kihagyhatja [az alkalmazás futtatását](#run-the-application)

A `GetSession` függvény (része `utils\utils.go` ) egy olyan műveletet ad vissza, [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) amely a fürt műveleteinek végrehajtásához használható, például Beszúrás, keresés stb.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

A rendszer átadja a Azure Cosmos DB Cassandra-gazdagépet a [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) (z) függvénynek, [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) amely a Felhasználónév, a jelszó, a port és a megfelelő TLS-verzió ([https/SSL/TLS titkosítási biztonsági követelmény](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)) használatára van konfigurálva.

A `GetSession` függvényt a `main` függvény () hívja meg `main.go` .

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

A kapcsolódási adatokat és a hitelesítő adatokat környezeti változók formájában fogadjuk el (a metódusban megoldották `init` )

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Ezt követően különböző műveleteket hajthat végre `operations\setup.go` Azure Cosmos db a `keyspace` és a `table` létrehozásával.

Ahogy a neve is sugallja, a `DropKeySpaceIfExists` függvény csak akkor eldobja a funkciót, `keyspace` Ha létezik.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` a függvény a () létrehozásához használatos `keyspace` `user_profile` .

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Ezt követik a tábla létrehozása ( `user` ), amely gondoskodik a `CreateUserTable` függvényről

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

A térköz és a tábla létrehozása után a rendszer szifilisz-műveleteket (a részét) hívja meg `operations\crud.go` . 

`InsertUser` a létrehozásához használatos `User` . A felhasználó adatait (azonosító, név és város) a lekérdezési argumentumok használatával állítja be [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` egy adott felhasználói azonosítóval rendelkező felhasználó () keresésére szolgál, `model\user.go` miközben [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) a felhasználói attribútumokat (a Cassandra által visszaadottak) az egyes változókra ( `userid` , `name` ,) használja `city` – csak az egyik módszer, amellyel a keresési lekérdezési eredményként kapott eredményt lehet használni.

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` az összes felhasználó beolvasására szolgál. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) rövidítéssel van elfoglalva, hogy a felhasználó összes adatát egy szelet s betű formájában kapja meg `map` . Gondoljunk a `map` kulcs-érték párokra, ahol az oszlopnév (például `user_id` ) a kulcs a megfelelő értékkel együtt.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

A `map` rendszer az egyes felhasználói adatokat egy `User` using függvényre konvertálja `mapToUser` , amely egyszerűen kinyeri az értéket a megfelelő oszlopból, és felhasználja a struct egy példányának létrehozásához. `User`

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Ahogy korábban említettük, az alkalmazás a környezeti változók formájában fogadja el a kapcsolódási és a hitelesítő adatokat. 

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a **kapcsolatok karakterlánc** lehetőséget. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Részletek megtekintése és másolása a kapcsolati sztring lapról Azure Portal":::

Másolja a következő attribútumok ( `CONTACT POINT` , és) értékeit, `PORT` `USERNAME` `PRIMARY PASSWORD` és állítsa be őket a megfelelő környezeti változókra.

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

A terminál ablakban váltson a megfelelő mappára. Például:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. A terminálon futtassa az alábbi parancsot az alkalmazás elindításához.

```shell
go run main.go
```

3. A terminálablak megjeleníti a különböző műveletekkel kapcsolatos értesítéseket, beleértve a térközt és a tábla beállítását, a felhasználók létrehozását stb.

4. Ha megnyitja az **Adatkezelőt** az Azure Portalon, lekérdezheti és módosíthatja és használhatja az új adatokat. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Adatkezelő-Azure Cosmos DBban lévő adatmegjelenítés":::

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre egy Azure Cosmos DB fiókot Cassandra API használatával, és hogyan futtathat egy go-adatbázist és egy tárolót. Mostantól további adatait is importálhatja a Azure Cosmos DB-fiókjába. 

> [!div class="nextstepaction"]
> [Cassandra-adatok importálása az Azure Cosmos DB-be](cassandra-import-data.md)