---
title: Go-alkalmazás csatlakoztatása Azure Cosmos DB MongoDB-hez használható API-hoz
description: Ez a rövid útmutató bemutatja, hogyan csatlakoztathat egy meglévő Go-alkalmazást Azure Cosmos DB MongoDB API-jának alkalmazásához.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: c9829b49662c90df685388691c04b201a7010eb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765214"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Rövid útmutató: Go-alkalmazás csatlakoztatása Azure Cosmos DB MongoDB API-hoz
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi dokumentum-, tábla-, kulcs-érték és gráfadatbázisok gyors létrehozására és lekérdezésére globális elosztási és horizontális skáláztatási képességekkel. Ebben a rövid útmutatóban létrehoz és kezel egy Azure Cosmos DB-fiókot a Azure Cloud Shell használatával, klónoz egy meglévő mintaalkalmazást a GitHubról, és konfigurálja azt a Azure Cosmos DB. 

A mintaalkalmazás egy Go nyelven írt parancssori `todo` felügyeleti eszköz. Azure Cosmos DB MongoDB API-ja kompatibilis a [MongoDB](./mongodb-introduction.md#wire-protocol-compatibility)banki protokollal, így bármely MongoDB-ügyfélillesztő csatlakozhat hozzá. Ez az alkalmazás a [MongoDB-hez](https://github.com/mongodb/mongo-go-driver) használt Go-illesztőt olyan módon használja, amely transzparens módon tárolja az adatokat egy Azure Cosmos DB adatbázisban.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free) Vagy [próbálja Azure Cosmos DB azure-előfizetés nélkül,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen. A Azure Cosmos DB [Emulatort is használhatja](https://aka.ms/cosmosdb-emulator) a kapcsolati sztringben. `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`
- [A számítógépen](https://golang.org/) telepítve van, és a Go ismerete.
- [Git](https://git-scm.com/downloads): .
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Az alábbi parancsok futtatásával klónozza a mintatárházat.

1. Nyisson meg egy parancssort, hozzon létre egy nevű új `git-samples` mappát, majd zárja be a parancssort.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretne tanulni az alkalmazás működését, tekintse át az alábbi kódrészleteket. Ellenkező esetben ugorjon a [Run the application (Az alkalmazás futtatása) részhez.](#run-the-application) Az alkalmazás elrendezése a következő:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Az alábbi kódrészletek mind a `todo.go` fájlból származnak.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Az alkalmazás csatlakoztatása az Azure Cosmos DB-hez

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) A tartalmazza a Azure Cosmos DB kapcsolati sztringet, amelyet egy környezeti változóval ad át (részletek a következő szakaszban). A kapcsolat a használatával inicializálható, [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) amelyhez a példány át lesz `clientOptions` stb. [ `Ping` A függvény](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) a sikeres kapcsolat megerősítéséhez van meghívva (ez egy hibagyors stratégia)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> A konfiguráció használata fontos, amely nélkül [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) a következő kapcsolódási hiba jelenik meg: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Elem `todo` létrehozása

A létrehozásához `todo` lekértünk egy leírót a [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) függvényhez, és meghívjuk a [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) függvényt. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Átadunk egy strukturát, amely tartalmazza a leírást és az állapotot (amely kezdetben a `Todo` következőre van állítva: `pending` )

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`Listaelemek

A toDO-k a feltételek alapján listozhatóak. Létrejön [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) egy, a szűrési feltételeket beágyazó feltétel

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) A a szűrésen alapuló dokumentumok kereséséhez használható, az eredmény pedig a következő szelete lesz: `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Végül táblázatos formában jelenik meg az információ

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Elem `todo` frissítése

Az `todo` a alapján `_id` frissíthető. A rendszer létrehoz egy szűrőt a alapján, és egy másikat a frissített információkhoz, amely ebben az esetben egy új állapot [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) `_id` ( vagy ) `completed` `pending` lesz. Végül meg lesz hívva a függvény a [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) szűrővel és a frissített dokumentummal

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Töröljön egy `todo`

Az a alapján törlődik, és egy példány formájában van `todo` `_id` [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) beágyazva. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) A meghívása a dokumentum törléséhez.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Az alkalmazás létrehozása

Váltson arra a könyvtárra, amelybe klónozta az alkalmazást, és buildelte (a `go build` használatával).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Annak ellenőrzése, hogy az alkalmazás megfelelően lett-e felépítve.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Telepítési Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissítenie kell, lásd: [Az Azure CLI telepítése]. 

Ha telepített Azure CLI-t használ, jelentkezzen be az Azure-előfizetésbe [az az login](/cli/azure/reference-index#az_login) paranccsal, és kövesse a képernyőn megjelenő utasításokat. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Telepített Azure-os parancssori felület használata esetén az `az` parancs futtatásával ellenőrizze, hogy a `cosmosdb` összetevő telepítve van-e. Ha a `cosmosdb` szerepel az alapparancsok listáján, lépjen tovább a következő parancsra. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

Ha a `cosmosdb` nincs az alapparancsok listáján, telepítse újra az [Azure CLI-t](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy erőforráscsoportot](../azure-resource-manager/management/overview.md) [az az group create gombra.](/cli/azure/group#az_group_create) Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

Ha a parancsot Azure Cloud Shell, válassza a Try **It**(Próbálja ki) lehetőséget, kövesse a képernyőn megjelenő utasításokat a bejelentkezéshez, majd másolja a parancsot a parancssorba.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Cosmos-fiókot [az az cosmosdb create paranccsal.](/cli/azure/cosmosdb#az_cosmosdb_create)

A következő parancsban helyettesítse be a saját egyedi Cosmos-fióknevét a `<cosmosdb-name>` helyőrző helyére. Ez az egyedi név lesz a Cosmos DB végpont () részeként, ezért egyedinek kell lennie az `https://<cosmosdb-name>.documents.azure.com/` Azure-beli összes Cosmos-fiókban. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

A `--kind MongoDB` paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

Az Azure Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. 

> [!NOTE]
> Ez a példa az alapértelmezett JSON formátumot használja az Azure CLI kimeneti formátumaként. Más kimeneti formátum használatához lásd: [Az Azure CLI-parancsok kimeneti formátumai](/cli/azure/format-output-azure-cli). 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Az adatbáziskulcs lekérése

A Cosmos-adatbázishoz való csatlakozáshoz szüksége van az adatbáziskulcsra. Az [elsődleges kulcs lekérése az az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) paranccsal.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg a kimeneti adatokat. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportálja a kapcsolati sztringet, a MongoDB-adatbázist és a gyűjteményneveket környezeti változókként. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A `ssl=true` beállítás a követelmények miatt Cosmos DB fontos. További információ: Kapcsolati [sztring követelményei.](connect-mongodb-account.md#connection-string-requirements)
>

A környezeti változó esetében cserélje le a `MONGODB_CONNECTION_STRING` és a helyőrzőit `<COSMOSDB_ACCOUNT_NAME>``<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: A létrehozott Azure Cosmos DB neve
2. `<COSMOSDB_PASSWORD>`: Az előző lépésben kinyert adatbáziskulcs

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Kiválaszthatja a kívánt értékeket a számára, `MONGODB_DATABASE` `MONGODB_COLLECTION` vagy hagyhatja őket a megadott értéken.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozzon létre egy `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Ha a művelet sikeres, az újonnan létrehozott dokumentum MongoDB-nek `_id` megfelelő kimenetet kell látnia:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Másik létrehozása `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

List all `todo` the s

```bash
./todo --list all
```

Az éppen hozzáadottakat táblázatos formátumban kell látnia.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Egy állapotának frissítéséhez (például módosítsa `todo` állapotra) használja az `completed` `todo` azonosítót

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Csak a befejezettek `todo` felsorolása

```bash
./todo --list completed
```

Most az előbb frissítettnek kell lennie

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Azure Cosmos DB adatok megtekinthetők és lekérdezhetőek a Azure Portal.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be a **következőt: Azure Cosmos DB.** Amikor megnyílik a Cosmos-fiók panelje, válassza ki Cosmos-fiókját. A bal oldali navigációs sávon válassza **a** Adatkezelő lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Az újonnan létrehozott dokumentum megjelenítve az Adatkezelőben":::


Töröljön `todo` egy et az azonosítójával

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

List the `todo` s to confirm

```bash
./todo --list all
```

Az `todo` éppen törölt nem lehet jelen

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB MongoDB API-fiókot az Azure Cloud Shell használatával, és hogyan hozhat létre és futtathat Go parancssori alkalmazást az s `todo` kezeléséhez. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)