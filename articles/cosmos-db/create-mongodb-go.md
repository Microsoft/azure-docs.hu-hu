---
title: Go-alkalmazás összekötése Azure Cosmos DB API-MongoDB
description: Ez a rövid útmutató bemutatja, hogyan csatlakoztatható egy meglévő Go-alkalmazás a Azure Cosmos DB API-MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 92edfa148268db5a5458b2af4000bc9ffd9ecc83
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659953"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: go-alkalmazás összekötése Azure Cosmos DB API-MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel. Ebben a rövid útmutatóban egy Azure Cosmos DB fiókot hoz létre és kezel a Azure Cloud Shell használatával, klónozott egy meglévő minta alkalmazást a GitHubról, és úgy konfigurálja, hogy működjön a Azure Cosmos DBsal. 

A minta alkalmazás a `todo` Go-ban írt parancssori felügyeleti eszköz. A MongoDB Azure Cosmos DB API-je [kompatibilis a MongoDB Wire protokollal](./mongodb-introduction.md#wire-protocol-compatibility), így bármely MongoDB-ügyfél illesztőprogramja csatlakozhat hozzá. Ez az alkalmazás a [Go-illesztőprogramot a MongoDB](https://github.com/mongodb/mongo-go-driver) olyan módon használja, amely átlátható az alkalmazás számára, amelyet az adott Azure Cosmos db-adatbázisban tárolnak.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a kapcsolatok karakterláncával `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Lépjen](https://golang.org/) a számítógépre, és folytassa a munkát.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Az alábbi parancsok futtatásával klónozza a mintatárházat.

1. Nyisson meg egy parancssort, hozzon létre egy nevű új mappát `git-samples` , majd kattintson a parancssor bezárására.

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

Ez a lépés nem kötelező. Ha érdekli az alkalmazás működésének megismerése, tekintse át az alábbi kódrészleteket. Ellenkező esetben kihagyhatja [az alkalmazás futtatását](#run-the-application). Az alkalmazás elrendezése a következő:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Az alábbi kódrészletek mind a `todo.go` fájlból származnak.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Az alkalmazás csatlakoztatása az Azure Cosmos DB-hez

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) a Azure Cosmos DBhoz tartozó kapcsolati karakterláncot ágyazza be, amelyet a rendszer a környezeti változó használatával ad át (részletek a közelgő szakaszban). A kapcsolat inicializálva van [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) , amely a `clientOptions` példányt adja át. a rendszer meghívja a [ `Ping` függvényt](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) , hogy erősítse meg a sikeres kapcsolódást (ez egy nem gyors stratégia)

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
> A [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) konfiguráció használata fontos, anélkül, hogy a következő kapcsolódási hibaüzenetet kapja: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Elemek létrehozása `todo`

A létrehozásához `todo` egy leírót kapunk, [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) és meghívja a [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) függvényt. 

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

Egy olyan struct-t adunk át `Todo` , amely tartalmazza a leírást és az állapotot (amely eredetileg a `pending` következőre van beállítva)

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`Listaelemek

A TEENDŐket a feltételek alapján is listázhatja. A [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) létrehozva a szűrési feltételek beágyazásához

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

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) a a szűrőn alapuló dokumentumok keresésére szolgál, és az eredmény a következő szeletre lesz konvertálva: `Todo`

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

Végül az adatok táblázatos formátumban jelennek meg

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

### <a name="update-a-todo-item"></a>Egy `todo` tétel frissítése

A `todo` frissítése a alapján lehetséges `_id` . A rendszer létrehoz egy [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) szűrőt a `_id` és egy másik alapján a frissített információhoz, amely ebben az esetben új állapot ( `completed` vagy `pending` ). Végezetül [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) meghívja a függvényt a szűrővel és a frissített dokumentummal.

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

### <a name="delete-a-todo"></a>A törlése `todo`

A `todo` törlődik a alapján, `_id` és egy példány formájában van beágyazva [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) . [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) a rendszer meghívja a dokumentumot a dokumentum törlésére.

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

Váltson arra a könyvtárra, ahol klónozott az alkalmazást, és hozza létre (a használatával `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Annak ellenőrzéséhez, hogy az alkalmazás megfelelően lett-e kiépítve.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Telepítő Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítése] című témakört. 

Ha telepített Azure CLI-t használ, jelentkezzen be az Azure-előfizetésbe az az [login](/cli/azure/reference-index#az-login) paranccsal, és kövesse a képernyőn megjelenő utasításokat. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Telepített Azure-os parancssori felület használata esetén az `az` parancs futtatásával ellenőrizze, hogy a `cosmosdb` összetevő telepítve van-e. Ha a `cosmosdb` szerepel az alapparancsok listáján, lépjen tovább a következő parancsra. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

Ha a `cosmosdb` nincs az alapparancsok listáján, telepítse újra az [Azure CLI-t](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](/cli/azure/group#az-group-create)paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

Ha Azure Cloud Shell használ, válassza a **kipróbálás** lehetőséget, kövesse a képernyőn megjelenő utasításokat a bejelentkezéshez, majd másolja a parancsot a parancssorba.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Cosmos-fiókot az az [cosmosdb Create](/cli/azure/cosmosdb#az-cosmosdb-create) paranccsal.

A következő parancsban cserélje ki a saját egyedi Cosmos-fiókjának nevét, ahol megjelenik a `<cosmosdb-name>` helyőrző. Ezt az egyedi nevet fogja használni a Cosmos DB Endpoint () részeként `https://<cosmosdb-name>.documents.azure.com/` , így a névnek egyedinek kell lennie az Azure-beli Cosmos-fiókok között. 

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

A Cosmos-adatbázishoz való kapcsolódáshoz szükség van az adatbázis kulcsára. Az elsődleges kulcs lekéréséhez használja az az [cosmosdb Keys List](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) parancsot.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg a kimeneti adatokat. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportálja a kapcsolatok karakterláncát, a MongoDB-adatbázist és a gyűjtemény nevét környezeti változókként. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> A `ssl=true` beállítás Cosmos db követelmények miatt fontos. További információ: a [kapcsolatok karakterláncával kapcsolatos követelmények](connect-mongodb-account.md#connection-string-requirements).
>

A `MONGODB_CONNECTION_STRING` környezeti változó esetében cserélje le a és a helyőrzőit `<COSMOSDB_ACCOUNT_NAME>``<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: A létrehozott Azure Cosmos DB fiók neve
2. `<COSMOSDB_PASSWORD>`: Az előző lépésben kinyert adatbázis-kulcs

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Kiválaszthatja a kívánt értékeit, vagy megadhatja `MONGODB_DATABASE` `MONGODB_COLLECTION` őket.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozzon létre egy `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Ha a művelet sikeres, az újonnan létrehozott dokumentum MongoDB származó kimenetnek kell megjelennie `_id` :

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Hozzon létre egy másikat `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Az összes s listázása `todo`

```bash
./todo --list all
```

Látnia kell, hogy az imént hozzáadott táblázatos formátumban legyen szó

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

A `todo` (például az állapotra való váltás) állapotának frissítéséhez `completed` használja az azonosítót. `todo`

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Csak a befejezett k listázása `todo`

```bash
./todo --list completed
```

Látnia kell az imént frissített

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Azure Cosmos DBban tárolt adatkészletek a Azure Portal megtekintésére és lekérdezésére használhatók.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be a **Azure Cosmos db** kifejezést. Amikor megnyílik a Cosmos-fiók panel, válassza ki a Cosmos-fiókját. A bal oldali navigációs sávon válassza a **adatkezelő** lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Az újonnan létrehozott dokumentum megjelenítve az Adatkezelőben":::


A `todo` felhasználó azonosítójának törlése

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Az `todo` s jóváhagyása

```bash
./todo --list all
```

Az `todo` imént törölt érték nem lehet jelen

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

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB MongoDB API-fiókot a Azure Cloud Shell használatával, és hogyan hozhat létre és futtathat egy go parancssori alkalmazást az s-k kezeléséhez `todo` . Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)