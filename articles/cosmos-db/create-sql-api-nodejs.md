---
title: Gyors útmutató – Node.js használata Azure Cosmos DB SQL API-fiókból való lekérdezéshez
description: Hogyan használható a Node.js egy olyan alkalmazás létrehozásához, amely Azure Cosmos DB SQL API-fiókhoz csatlakozik, és lekérdezi az adatlekérdezéseket.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090205"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: a Node.js használata az adatok Azure Cosmos DB SQL API-fiókból való összekapcsolásához és lekérdezéséhez
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot hoz létre és felügyel a Azure Portalból, és egy, a GitHubról klónozott Node.js alkalmazás használatával. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="walkthrough-video"></a>Útmutató videó

Tekintse meg ezt a videót a cikk tartalmának teljes bemutatójában.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Használhatja a [Azure Cosmos db emulátort](https://aka.ms/cosmosdb-emulator) is a `https://localhost:8081` és a kulcs URI-ja használatával `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos-fiók létrehozása

Ehhez a rövid útmutatóhoz az [ingyenes kipróbálás Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használhatja Azure Cosmos-fiók létrehozásához.

1. Navigáljon az [ingyenes kipróbálás Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) oldalra.

1. Válassza ki az **SQL** API-fiókot, és válassza a **Létrehozás** lehetőséget. Jelentkezzen be Microsoft-fiók használatával.

1. A bejelentkezés sikeres befejezését követően az Azure Cosmos-fióknak készen kell állnia. Válassza a **Megnyitás lehetőséget a Azure Portal** az újonnan létrehozott fiók megnyitásához.

Az "ingyenes Azure Cosmos DB ingyen" lehetőséghez nincs szükség Azure-előfizetésre, és az Azure Cosmos-fiókot egy 30 napos időszakra is felkínálja. Ha hosszabb ideig szeretné használni az Azure Cosmos-fiókot, ehelyett [hozzon létre egy fiókot](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) az Azure-előfizetésén belül.

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához.

1. Válassza ki **adatkezelő**  >  **új tárolót**.

   A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

   | Beállítás           | Ajánlott érték | Leírás                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Adatbázis-azonosító**   | Feladatok           | Az új adatbázisnak adja a _Feladatok_ nevet. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
   | **Átviteli sebesség**    | 400             | Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.                                                                                                                                                                                                                                                    |
   | **Tároló azonosítója**  | Elemek           | Adja meg az _elemeket_ az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.                                                                                                                                                                                                                                                               |
   | **Partíciókulcs** | /kategória       | A cikkben ismertetett minta a _/category_ használja a partíciós kulcsként.                                                                                                                                                                                                                                                                                                           |

   Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.

   Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy Node.js alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha szeretné megtanulni, hogyan jönnek létre az Azure Cosmos Database-erőforrások a kódban, tekintse át az alábbi kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

Ha már ismeri az SQL JavaScript SDK korábbi verzióját, akkor a feltételek _gyűjteményét_ és _dokumentumát_ is használhatja. Mivel Azure Cosmos DB [több API-modellt](introduction.md)is támogat, [a JavaScript SDK 2.0](https://www.npmjs.com/package/@azure/cosmos) -s vagy újabb verziója az általános feltételek _tárolóját_ használja, amely lehet gyűjtemény, gráf vagy tábla, valamint a tároló tartalmának leírására szolgáló _elem_ .

A Cosmos DB JavaScript SDK neve " @azure/cosmos ", és telepíthető a NPM...

```bash
npm install @azure/cosmos
```

Az alábbi kódrészletek mind a _app.js_ fájlból származnak.

- A a `CosmosClient` NPM-csomagból lett importálva `@azure/cosmos` .

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- Egy új `CosmosClient` objektum inicializálása megtörtént.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Válassza ki a "feladatok" adatbázist.

  ```javascript
  const database = client.database(databaseId);
  ```

- Válassza ki az "Items" tárolót/gyűjteményt.

  ```javascript
  const container = database.container(containerId);
  ```

- Válassza ki az összes elemet az "Items" tárolóban.

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- Új elem létrehozása

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- Elem módosítása

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- Elem törlése

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> A "frissítés" és a "Törlés" metódusban az elemet a meghívásával kell kiválasztani az adatbázisból `container.item()` . Az átadott két paraméter az elemek és az elemek partíciós kulcsának azonosítója. Ebben az esetben a egyenlőség kulcs a "Category" (kategória) mező értéke.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Most lépjen vissza a Azure Portalra az Azure Cosmos-fiók kapcsolati sztring adatainak beszerzéséhez. Másolja a kapcsolati karakterláncot az alkalmazásba, hogy az képes legyen csatlakozni az adatbázishoz.

1. A [Azure Portal](https://portal.azure.com/)Azure Cosmos db-fiókjában válassza a bal oldali navigációs menüben a **kulcsok** elemet, majd válassza az **írási/olvasási kulcsok** elemet. A következő lépésben a képernyő jobb oldalán található másolási gombok használatával másolja az URI-t és az elsődleges kulcsot a _app.js_ fájlba.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén":::

2. Nyissa meg a _config.js_ fájlt.

3. Másolja az URI-értéket a portálról (a másolás gomb használatával), és adja meg a végpont kulcs értékét a _config.jsban_.

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Ezután másolja ki az elsődleges kulcs értékét a portálról, és adja meg aconfig.jsértékének értékét `config.key` . __ Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Futtassa a parancsot `npm install` egy terminálban a " @azure/cosmos " NPM-csomag telepítéséhez

2. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

3. Az ebben a rövid útmutatóban korábban létrehozott két elem ki van listázva. Létrejön egy új tétel. Az elem "isComplete" jelzője "true" (igaz) értékre frissül, és végül az elem törlődik.

Ezzel a minta-alkalmazással folytathatja a kísérletet, vagy visszatérhet az adataihoz Adatkezelő, módosíthat és dolgozhat.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre egy tárolót a Adatkezelő használatával, és hogyan futtathat Node.js alkalmazást. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [Adatimportálás az Azure Cosmos db-be](import-data.md)
