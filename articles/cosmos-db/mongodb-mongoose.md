---
title: Node.js mongúz-alkalmazás összekötése Azure Cosmos DB
description: Megtudhatja, hogyan tárolhatja és kezelheti az Azure Cosmos DBban tárolt adattárakat a mongúz keretrendszer használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 8958699ae279d2613f8dbadca802ee2137407e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442396"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Node.js mongúz-alkalmazás összekötése Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez az oktatóanyag azt mutatja be, hogyan használható a [mongúz keretrendszer](https://mongoosejs.com/) a Cosmos DBban tárolt adattároláshoz. Ehhez a bemutatóhoz a Azure Cosmos DB API-ját használjuk a MongoDB-hez. Ha nem ismerné, a Mongoose egy objektummodellezési keretrendszer a MongoDB-hez Node.js-ben, és egyszerű, sémaalapú megoldást biztosít az alkalmazásadatok modellezéséhez.

Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérdezheti a dokumentum-, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/)-verzió: 0.10.29-s vagy újabb.

## <a name="create-a-cosmos-account"></a>Cosmos-fiók létrehozása

Hozzunk létre egy Cosmos-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa A Node.js-alkalmazás beállítása című lépéssel. Ha a Azure Cosmos DB emulátort használja, kövesse az Emulator [Azure Cosmos db](local-emulator.md) című témakör lépéseit az emulátor beállításához, majd ugorjon előre a Node.js alkalmazás beállításához.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Adatbázis létrehozása 
Ebben az alkalmazásban két módszert fogunk létrehozni a Azure Cosmos DB gyűjtemények létrehozásához: 
- **Az egyes objektummodell tárolása különálló gyűjteményben**: javasoljuk, hogy [hozzon létre egy adatbázist dedikált átviteli](set-throughput.md#set-throughput-on-a-database)sebességgel. A kapacitás modell használata jobb költséghatékonyságot nyújt.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js oktatóanyag – a Azure Portal képernyőképe, amely bemutatja, hogyan hozhat létre egy adatbázist a Adatkezelő egy Azure Cosmos DB-fiókhoz, a mongúz csomópont-modullal való használathoz":::

- Az **összes objektummodell tárolása egyetlen Cosmos db gyűjteményben**: Ha az összes modellt egyetlen gyűjteményben szeretné tárolni, egyszerűen létrehozhat egy új adatbázist anélkül, hogy kijelöli az átviteli sebesség lehetőséget. Ha ezt a kapacitási modellt használja, minden egyes gyűjtemény saját átviteli kapacitással fog létrejönni minden objektummodell esetében.

Az adatbázis létrehozása után a nevet az `COSMOSDB_DBNAME` alábbi környezeti változóban fogja használni.

## <a name="set-up-your-nodejs-application"></a>A Node.js-alkalmazás beállítása

>[!Note]
> Ha nem szeretné beállítani magát az alkalmazást, csak a mintakódon szeretne végighaladni, klónozza az oktatóanyaghoz használt [mintát](https://github.com/Azure-Samples/Mongoose_CosmosDB), és hozza létre a Node.js Mongoose alkalmazást az Azure Cosmos DB-ben.

1. Node.js-alkalmazás létrehozásához a kiválasztott mappában futtassa az alábbi parancsot egy csomópont parancssorban.

    ```npm init```

    Válaszoljon a kérdésekre, és a projekt indulásra kész.

2. Vegyen fel a mappába egy új fájlt, és adja neki az ```index.js``` nevet.
3. Telepítse a szükséges csomagokat az egyik ```npm install``` lehetőséggel:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Az alábbi mongúz-példa az 5. és a korábbi verziók óta megváltoztatott mongúz-alapú.
    
   * Dotenv (ha a titkos kulcsokat egy .env fájlból szeretné betölteni): ```npm install dotenv --save```

     >[!Note]
     > A ```--save``` jelző hozzáadja a függőséget a package.json fájlhoz.

4. Importálja a függőségeket az index.js fájljába.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Vegye fel a Cosmos DB kapcsolati sztringjét és a Cosmos DB-nevet a ```.env``` fájlba. Cserélje le a {Cosmos-Account-Name} és a {dbname} helyőrzőket a saját Cosmos-fiókja nevére és az adatbázis nevére a kapcsos zárójelek nélküli szimbólumok nélkül.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMOSDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Kapcsolódjon Cosmos DB a mongúz-keretrendszer használatával, és adja hozzá a következő kódot a index.js végéhez.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMOSDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Itt a környezeti változók a process.env{variableName} segítségével, a „dotenv” npm-csomaggal tölthetők be.

    Ha már csatlakozott az Azure Cosmos DB-hez, elkezdheti az objektummodellek beállítását a Mongoose-ban.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Ajánlott eljárások a mongúz és a Cosmos DB használatával

Minden létrehozott modellhez a mongúz létrehoz egy új gyűjteményt. Ez az [adatbázis-szintű átviteli sebesség lehetőséggel](set-throughput.md#set-throughput-on-a-database)van megcélozva, amelyet korábban már tárgyaltak. Egyetlen gyűjtemény használatához el kell végeznie [a mongúz-](https://mongoosejs.com/docs/discriminators.html)elválasztók használatát. A diszkriminátorok sémaöröklési mechanizmusok. Lehetővé teszik, hogy egy alapul szolgáló MongoDB-gyűjteményre több modell épüljön átfedő sémákkal.

A különböző adatmodelleket tárolhatja ugyanabban a gyűjteményben, majd a lekérdezés időpontjában alkalmazhat egy szűrőfeltételt, hogy csak a szükséges adatokat kérje le. Lássuk végig a modelleket.

### <a name="one-collection-per-object-model"></a>Objektummodellenként egy gyűjtemény

Ez a szakasz ismerteti, hogyan érheti el ezt Azure Cosmos DB API-MongoDB. Ez a módszer a javasolt megközelítés, mivel lehetővé teszi a költségeket és a kapacitás szabályozását. Ennek eredményeképpen az adatbázishoz tartozó kérelmek mennyisége nem függ az objektummodell számától. Ez a mongúz alapértelmezett működési modellje, így ismerős lehet.

1. Nyissa meg újra az ```index.js``` fájlt.

1. Hozzon létre egy sémadefiníciót a „Family” (Család) elemhez.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Hozzon létre egy objektumot a „Family” (Család) elemhez.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Végül mentse az objektumot Cosmos DBba. Ezzel létrehoz egy gyűjteményt a háttérben.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Most hozzon létre egy másik sémát és objektumot. Ez alkalommal hozzon létre egy „Vacation Destinations” (Nyaralási úticélok) elemet a családokat esetlegesen érdeklő célpontokhoz.
   1. Hozza létre a sémát, akárcsak a legutóbb
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Hozzon létre egy mintaobjektumot (ehhez a sémához több objektumot is hozzáadhat), majd mentse azt.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Most, hogy bekerül a Azure Portalba, két, Cosmos DBban létrehozott gyűjteményt láthat.

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text="Node.js oktatóanyag – képernyőkép a Azure Portalről, amely egy Azure Cosmos DB fiókot jelenít meg, és több gyűjtemény neve van kiemelve – csomópont-adatbázis":::

1. Végül olvassa el Cosmos DB adatait. Mivel az alapértelmezett Mongoose működési modellt használjuk, az olvasások megegyeznek bármely más Mongoose olvasással.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Adatok tárolása egyetlen gyűjteményben Mongoose diszkriminátorok használatával

Ebben a módszerben az egyes gyűjtemények költségeinek optimalizálásához a [mongúz](https://mongoosejs.com/docs/discriminators.html) -felhasználók használata segít. A diszkriminátorok lehetővé teszik egy megkülönböztető „kulcs” meghatározását, amely segítségével tárolhatja, megkülönböztetheti és szűrheti a különböző objektummodelleket.

Itt létrehozunk egy alapszintű objektummodellt, meghatározunk egy megkülönböztető kulcsot, majd felvesszük az alapszintű modell kiterjesztéseként a „Family” (Család) és a „VacationDestinations” (Nyaralási úti célok) sémát.

1. Állítsuk be az alapszintű konfigurációt, és határozzuk meg a diszkriminátorkulcsot.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Ezután határozzuk meg a közös objektummodellt

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Most meghatározzuk a „Family” (Család) modellt. Vegye észre, hogy a ```mongoose.model``` helyett a ```commonModel.discriminator``` elemet használjuk. Ezen felül hozzáadjuk az alapszintű konfigurációt is a Mongoose sémához. Itt a discriminatorKey (diszkriminátorkulcs) tehát ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Vegyük fel ugyanígy a másik sémát, ez alkalommal a „VacationDestinations” (Nyaralási úti célok) nevűt. A discriminatorKey (diszkriminátorkulcs) itt ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Végezetül hozzunk létre objektumokat a modellhez, majd mentsük azt.
   1. Vegyünk fel objektumo(ka)t a „Family” (Család) modellhez.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Buddy" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Ezután vegyünk fel objektumo(ka)t a „VacationDestinations” (Nyaralási úti cél) modellhez, majd mentsük azt.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Ha most visszatér az Azure Portalra, láthatja, hogy egyetlen gyűjteménye van ```alldata``` néven, amelyben a „Family” (Család) és a „VacationDestinations” (Nyaralási úti célok) adatai is szerepelnek.

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text="Node.js oktatóanyag – képernyőkép a Azure Portalról, amely egy Azure Cosmos DB fiókot jelenít meg a kiválasztott gyűjtemény neve – csomópont-adatbázis":::

1. Azt is észreveheti, hogy minden objektum rendelkezik egy másik, ```__type``` nevű attribútummal, amely segít megkülönböztetni a két különböző objektummodellt.

1. Végezetül most olvassuk be az Azure Cosmos DB-ben tárolt adatokat. A Mongoose gondoskodik a modellen alapuló adatok szűréséről. Így semmit sem kell másképp csinálnia az adatok olvasásakor. Csak adja meg a modellt (ebben az esetben: ```Family_common```), és a Mongoose kezeli a szűrést a „DiscriminatorKey” alapján.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Amint láthatja, a Mongoose diszkriminátorok könnyen használhatók. Tehát ha olyan alkalmazással rendelkezik, amely a mongúz keretrendszert használja, ez az oktatóanyag lehetővé teszi, hogy az Azure Cosmos API-ját MongoDB, anélkül, hogy túl sok módosításra lenne szükség.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
