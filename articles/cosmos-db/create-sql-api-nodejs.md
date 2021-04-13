---
title: Rövid útmutató – Lekérdezés Node.js SQL API Azure Cosmos DB fiókból
description: A Node.js az SQL API-fiókhoz Azure Cosmos DB és adatokat lekérdező alkalmazás létrehozásához.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: ead4004813cf6415dfa1c7da3d308d93ea49a773
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365909"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Rövid útmutató: Csatlakozás és Node.js SQL API-fiókból Azure Cosmos DB adatok lekérdezése Azure Cosmos DB használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Spark v3-összekötő](create-sql-api-spark.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

Ebben a rövid útmutatóban egy Azure Cosmos DB SQL API-fiókot fog létrehozni és kezelni a Azure Portal-ból, valamint egy GitHubról klónozott Node.js használatával. Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amellyel gyorsan hozhat létre és lekérdezhet dokumentum-, tábla-, kulcs-érték és gráfadatbázisokat globális elosztási és horizontális skálázású képességekkel.

## <a name="walkthrough-video"></a>Bemutató videó

Tekintse meg ezt a videót a cikk tartalmának teljes körű bemutatójért.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja Azure Cosmos DB azure-előfizetés nélkül,](https://azure.microsoft.com/try/cosmosdb/) ingyenesen. Használhatja a [Azure Cosmos DB Emulatort](https://aka.ms/cosmosdb-emulator) a és a kulcs `https://localhost:8081` URI-ját `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` is.
- [Node.js 6.0.0+ .](https://nodejs.org/)
- [Git](https://www.git-scm.com/downloads): .

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos-fiók létrehozása

Ebben a rövid útmutatóban használhatja az ingyenes [Azure Cosmos DB lehetőséget](https://azure.microsoft.com/try/cosmosdb/) egy Azure Cosmos-fiók létrehozásához.

1. Lépjen az ingyenes [Azure Cosmos DB oldalára.](https://azure.microsoft.com/try/cosmosdb/)

1. Válassza ki az **SQL** API-fiókot, majd válassza a **Létrehozás lehetőséget.** Jelentkezzen be az Microsoft-fiók.

1. A sikeres bejelentkezés után az Azure Cosmos-fióknak készen kell állnia. Válassza **a Megnyitás lehetőséget Azure Portal** az újonnan létrehozott fiók megnyitásához.

A "Azure Cosmos DB ingyenes" lehetőség nem igényel Azure-előfizetést, és korlátozott, 30 napos időszakra kínál Azure Cosmos-fiókot. Ha hosszabb ideig szeretné használni az Azure Cosmos-fiókot, ehelyett az Azure-előfizetésen belül kell létrehoznia a fiókot. [](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)

## <a name="add-a-container"></a>Tároló hozzáadása

Most már használhatja a Adatkezelő eszközét egy Azure Portal tároló létrehozásához.

1. Válassza **Adatkezelő**  >  **Új tároló lehetőséget.**

   A **jobb oldalon** megjelenik a Tároló hozzáadása terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Az Azure Portal Adatkezelője a Tároló hozzáadása panellel":::

2. A Tároló **hozzáadása lapon** adja meg az új tároló beállításait.

   | Beállítás           | Ajánlott érték | Leírás                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Adatbázis-azonosító**   | Feladatok           | Az új adatbázisnak adja a _Feladatok_ nevet. Az adatbázisneveknek 1–255 karakter hosszúságúnak kell lennie, és nem tartalmazhatnak vagy záró `/, \\, #, ?` szóközt. Jelölje be **az Adatbázis átviteli sebességének kiépítése** lehetőséget, amely lehetővé teszi az adatbázis számára kibocsátott átviteli sebesség megosztását az adatbázis összes tárolója között. Ez a lehetőség segít a költségmegtakarításban is. |
   | **Átviteli sebesség**    | 400             | Hagyja az átviteli sebességet másodpercenként 400 kérelemegységen (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.                                                                                                                                                                                                                                                    |
   | **Tároló azonosítója**  | Elemek           | Az _új tárolónak_ adja az Elemek nevet. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.                                                                                                                                                                                                                                                               |
   | **Partíciókulcs** | /kategória       | A cikkben leírt minta a _/category_ partíciókulcsot használja.                                                                                                                                                                                                                                                                                                           |

   A fenti beállításokon kívül egyedi kulcsokat is hozzáadhat **a** tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy tároló létrehozásakor létrehoz egy egyedi kulcs-szabályzatot, azzal biztosítja, hogy partíciókulcsonként egy vagy több érték egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](unique-keys.md) kapcsolatos cikkben talál.

   Kattintson az **OK** gombra. Az Adatkezelő megjeleníti az új adatbázist és tárolót.

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Node.js a GitHubról, beállítjuk a kapcsolati sztringet, és futtatjuk azt.

1. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretne tanulni a kódban létrehozott Azure Cosmos-adatbázis-erőforrásokról, tekintse át az alábbi kódrészleteket. Egyéb esetben ugorhat [A kapcsolati sztring frissítése](#update-your-connection-string) szakaszra.

Ha már ismeri az SQL JavaScript SDK előző verzióját, akkor a rendszer a gyűjtemény és a dokumentum _kifejezéseket_ is _láthatja._ Mivel a Azure Cosmos DB több [API-modellt](introduction.md)is támogat, a [JavaScript SDK 2.0-s](https://www.npmjs.com/package/@azure/cosmos) vagy újabb verziója a  tároló általános kifejezését használja, amely lehet gyűjtemény, gráf vagy tábla, valamint egy elem a tároló tartalmának leírására. 

A Cosmos DB JavaScript SDK neve " , és telepíthető @azure/cosmos az npm...

```bash
npm install @azure/cosmos
```

Az alábbi kódrészletek mind a fájlból _app.js_ vannak.

- A `CosmosClient` importálva van az `@azure/cosmos` npm-csomagból.

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- A rendszer `CosmosClient` inicializál egy új objektumot.

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- Válassza ki a "Feladatok" adatbázist.

  ```javascript
  const database = client.database(databaseId);
  ```

- Válassza az "Elemek" tárolót/gyűjteményt.

  ```javascript
  const container = database.container(containerId);
  ```

- Válassza ki az "Elemek" tároló összes elemét.

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
> Az "update" és a "delete" metódusban is ki kell választani az elemet az adatbázisból a `container.item()` hívásával. A két átadott paraméter az elem azonosítója és az elem partíciókulcsa. Ebben az esetben a parition key a "category" mező értéke.

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Most vissza a Azure Portal az Azure Cosmos-fiók kapcsolati sztringjének részleteiért. Másolja a kapcsolati sztringet az alkalmazásba, hogy az csatlakozni tud az adatbázishoz.

1. A fiók Azure Cosmos DB a Azure Portal [a](https://portal.azure.com/)bal oldali  navigációs sávon válassza a Kulcsok, majd az **Írási/olvasási kulcsok lehetőséget.** A képernyő jobb oldalán található másolási gombokkal másolja az URI-t és az elsődleges kulcsot _app.js_ következő lépésben található fájlba.

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Hozzáférési kulcs megtekintése és másolása az Azure Portal Kulcsok paneljén":::

2. Nyissa meg a _config.js_ fájlt.

3. Másolja ki az URI-értéket a portálról (a másolási gombbal), és azt a végpontkulcs értékeként a _config.js._

   `endpoint: "<Your Azure Cosmos account URI>"`

4. Ezután másolja ki az ELSŐDLEGES KULCS értékét a portálról, és azt a értékeként a `config.key` _fájlbanconfig.js._ Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges.

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Futtassa `npm install` a következőt egy terminálban: " @azure/cosmos " npm-csomag telepítése

2. Futtassa a `node app.js` parancsot egy terminálban a node-alkalmazás elindításához.

3. Megjelenik a rövid útmutatóban korábban létrehozott két elem. A rendszer létrehoz egy új elemet. Az elem "isComplete" jelzője "true" (igaz) értékre frissül, és végül törli az elemet.

Folytathatja a kísérletezést ezzel a mintaalkalmazással, vagy visszamehet a Adatkezelő, módosíthatja és használhatja az adatait.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Cosmos DB-fiókot, hogyan hozhat létre tárolót a Adatkezelő használatával, és hogyan futtathat Node.js alkalmazást. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [adatok importálása az Azure Cosmos DB-be](import-data.md)
