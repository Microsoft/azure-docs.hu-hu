---
title: Webalkalmazás üzembe helyezése sablonnal – Azure Cosmos DB
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Cosmos-fiókot, Azure App Service Web Appst és egy minta-webalkalmazást egy Azure Resource Manager sablon használatával.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333579"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Cosmos DB és Azure App Service üzembe helyezése a GitHub webalkalmazásával egy Azure Resource Manager sablon használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el az első futtatáshoz kapcsolódó, Azure Cosmos DBhoz csatlakozó webalkalmazások "nincs érintéses" telepítését, anélkül, hogy a `appsettings.json` Azure Portal az Azure app Services az alkalmazásra vonatkozó kapcsolati adatokat kellene kivágnia és beillesztenie a Azure Cosmos db. Ezek a műveletek egyetlen művelettel Azure Resource Manager sablonnal hajthatók végre. Ebben a példában egy [webalkalmazás-oktatóanyagból](sql-api-dotnet-application.md)fogjuk üzembe helyezni a [Azure Cosmos db Todo-mintát](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) .

A Resource Manager-sablonok meglehetősen rugalmasak, és lehetővé teszik, hogy összetett üzembe helyezéseket hozzon létre az Azure bármely szolgáltatásában. Ez olyan speciális feladatokat is magában foglal, mint például alkalmazások üzembe helyezése a GitHubról, és a kapcsolatok adatainak beadása Azure App Service alkalmazási beállításaiba a Azure Portalban. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket egyetlen Resource Manager-sablon használatával.

* Azure Cosmos-fiók üzembe helyezése.
* Azure App Service üzemeltetési csomag üzembe helyezése.
* Azure App Service üzembe helyezése.
* Szúrja be a végpontot és a kulcsokat az Azure Cosmos-fiókból a Azure Portal App Service alkalmazás-beállításaiba.
* Webalkalmazás üzembe helyezése egy GitHub-adattárból a App Serviceba.

Az eredményül kapott üzemelő példány egy teljesen működőképes webalkalmazással rendelkezik, amely a Azure Portal Azure Cosmos DB végpont URL-címének vagy hitelesítési kulcsainak kivágása és beillesztése nélkül tud csatlakozni a Azure Cosmos DBhoz.

## <a name="prerequisites"></a>Előfeltételek

> [!TIP]
> Bár ez az oktatóanyag nem feltételezi az Azure Resource Manager-sablonokkal vagy JSON-vel kapcsolatos korábbi tapasztalatokat, érdemes módosítania a hivatkozott sablonokat vagy telepítési beállításokat, majd az egyes területek ismereteit kötelező megadni.

## <a name="step-1-deploy-the-template"></a>1. lépés: a sablon üzembe helyezése

Először válassza az alábbi **üzembe helyezés az Azure** -ban gombot a Azure Portal egyéni központi telepítés létrehozásához való megnyitásához. Az Azure Resource Management-sablont az [Azure Gyorsindítás sablonok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) katalógusában is megtekintheti.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Egyszer a Azure Portal válassza ki a telepítendő előfizetést, majd válasszon ki vagy hozzon létre egy új erőforráscsoportot. Ezután adja meg a következő értékeket.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Képernyőfelvétel a sablon üzembe helyezésének felhasználói felületéről":::

* **Régió** – ezt a Resource Managernek kell megadnia. Adja meg ugyanazt a régiót, amelyet a Location paraméter használ, ahol az erőforrások találhatók.
* **Alkalmazásnév** – a rendszer ezt a nevet használja a központi telepítéshez tartozó összes erőforráshoz. Ügyeljen arra, hogy egyedi nevet válasszon a meglévő Azure Cosmos DB és App Service fiókokkal való ütközés elkerülése érdekében.
* **Hely** – az a régió, ahol az erőforrások üzembe vannak helyezve.
* **App Service csomag** – app Service csomag díjszabási szintje.
* **App Service Plan instances** – az App Service-csomag feldolgozóinak száma.
* **Adattár URL-címe** – a tárház a webes alkalmazáshoz a githubon.
* **Ág** – a GitHub-adattár ága.
* **Adatbázis neve** – az Azure Cosmos-adatbázis neve.
* **Tároló neve** – az Azure Cosmos-tároló neve.

Az értékek kitöltése után a **Létrehozás** gombra kattintva indíthatja el a telepítést. Ennek a lépésnek a befejezéséhez 5 és 10 perc között kell lennie.

> [!TIP]
> A sablon nem ellenőrzi, hogy a sablonban megadott Azure App Service neve és az Azure Cosmos-fiók neve érvényes és elérhető-e. Erősen ajánlott ellenőrizni, hogy a telepítés elküldése előtt milyen nevekkel kell megadnia a rendelkezésre állást.


## <a name="step-2-explore-the-resources"></a>2. lépés: az erőforrások megismerése

### <a name="view-the-deployed-resources"></a>Az üzembe helyezett erőforrások megtekintése

Miután a sablon üzembe helyezte az erőforrásokat, láthatja mindegyiket az erőforráscsoporthoz.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Erőforráscsoport":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB végpont és kulcsok megtekintése

Ezután nyissa meg az Azure Cosmos-fiókot a portálon. Az alábbi képernyőfelvételen egy Azure Cosmos-fiók végpontja és kulcsa látható.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos-kulcsok":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Az Alkalmazásbeállítások Azure Cosmos DB kulcsainak megtekintése

Ezután navigáljon az erőforráscsoport Azure App Service. Kattintson a Configuration (konfiguráció) lapra a App Service alkalmazás beállításainak megtekintéséhez. Az Alkalmazásbeállítások tartalmazzák a Cosmos DB fiókot és az elsődleges kulcs értékeit, amelyek szükségesek a Cosmos DBhoz való kapcsolódáshoz, valamint a sablon üzembe helyezése során átadott adatbázis-és tároló-nevekhez.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Alkalmazásbeállítások":::

### <a name="view-web-app-in-deployment-center"></a>Webalkalmazás megtekintése a központi telepítési központban

Ezután nyissa meg a App Service központi telepítési központját. Itt láthatja, hogy a rendszer a sablonba átadott GitHub-tárházat fogja látni a tárházban. Az alábbi állapot azt is jelzi, hogy sikeres (aktív), ami azt jelenti, hogy az alkalmazás telepítése és elindítása sikeres volt.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Üzembe helyezési központ":::

### <a name="run-the-web-application"></a>A webalkalmazás futtatása

A webalkalmazás megnyitásához kattintson a központi telepítési központ tetején található **Tallózás** gombra. A webalkalmazás megnyílik a kezdőképernyőn. Kattintson a **Create New (új létrehozása** ) elemre, és adjon meg néhány adat mezőt, majd kattintson a Mentés gombra. Az eredményül kapott képernyő a Cosmos DBba mentett adatmegjelenítést jeleníti meg.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Kezdőképernyő":::

## <a name="step-3-how-does-it-work"></a>3. lépés: Hogyan működik?

A működéséhez három elem szükséges.

### <a name="reading-app-settings-at-runtime"></a>Alkalmazás beállításainak olvasása futásidőben

Először az alkalmazásnak meg kell kérnie az Cosmos DB-végpontot és a kulcsot a `Startup` ASP.net MVC webalkalmazás osztályában. A [mintaként szolgáló Cosmos db](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) helyileg futtatható, ahol megadhatja a kapcsolódási adatokat a appsettings.js. A telepítésekor azonban ez a fájl az alkalmazással együtt települ. Ha a piros vonal nem fér hozzá a beállításokhoz a appsettings.json-ben, akkor a Azure App Service alkalmazás beállításai közül próbálkozik.

:::image type="content" source="./media/create-website/startup.png" alt-text="A képernyőfelvétel egy olyan metódust mutat be, amely több, vörös színnel jelölt karakterlánc-változóval rendelkezik, beleértve a databaseName, a containerName, a fiókot és a kulcsot":::

### <a name="using-special-azure-resource-management-functions"></a>Speciális Azure Resource Management-függvények használata

Ahhoz, hogy ezek az értékek elérhetők legyenek az alkalmazás számára az üzembe helyezés során, a Azure Resource Manager sablon a speciális Azure Resource Management functions használatával kérheti ezeket az értékeket a Cosmos DB-fiókból, beleértve a [hivatkozásokat](../azure-resource-manager/templates/template-functions-resource.md#reference) és a [listkeys műveletének beolvasása](../azure-resource-manager/templates/template-functions-resource.md#listkeys) , amelyek megkeresik a Cosmos db-fiók értékeit, és beszúrják azokat az Alkalmazásbeállítások értékeibe, amelyek megfelelnek a fenti alkalmazásban a {szakasz: Key Például: `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Sablon kulcsai":::

### <a name="deploying-web-apps-from-github"></a>Webalkalmazások üzembe helyezése a GitHubról

Végül telepíteni kell a webalkalmazást a GitHubról a App Serviceba. Ezt az alábbi JSON használatával végezheti el. Az erőforrás típusa és neve két dolgot kell, hogy legyen óvatos. A `"type": "sourcecontrols"` és a `"name": "web"` tulajdonság értéke rögzített, és nem módosítható.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Üzembe helyezés a GitHubról":::

## <a name="next-steps"></a>Következő lépések

Gratulálunk! Telepítette Azure Cosmos DB, Azure App Service és egy minta webalkalmazást, amely automatikusan rendelkezik a Cosmos DBhoz való kapcsolódáshoz szükséges kapcsolódási adatokkal, egyetlen művelettel, és a bizalmas adatok kivágása és beillesztése nélkül. A sablon kiindulási pontként való használata lehetővé teszi, hogy a saját webalkalmazásait ugyanúgy telepítse.

* Az ehhez a mintához tartozó Azure Resource Manager sablonhoz nyissa meg az [Azure Gyorsindítás sablonok](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) katalógusát
* A minta alkalmazás forráskódja Ugrás a következőre: [Cosmos db to do app on GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
