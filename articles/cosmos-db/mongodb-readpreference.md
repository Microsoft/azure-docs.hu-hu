---
title: Olvasási beállítások használata a Azure Cosmos DB API-MongoDB
description: Ismerje meg, hogyan használhatja a MongoDB olvasási előnyt a Azure Cosmos DB API-MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96349044"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Olvasási műveletek globális elosztása a Azure Cosmos DB API-MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a cikk bemutatja, hogyan terjeszthetők globálisan az olvasási műveletek a [MongoDB olvasási](https://docs.mongodb.com/manual/core/read-preference/) beállításokkal a Azure Cosmos db API-MongoDB használatával.

## <a name="prerequisites"></a>Előfeltételek 
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

[Ebben a](tutorial-global-distribution-mongodb.md) rövid útmutatóban megtudhatja, hogyan állíthatja be a Azure Portal egy globális eloszlású Cosmos-fiók beállításához, majd hogyan csatlakozhat hozzá.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

Az alábbi parancsok futtatásával klónozza a mintatárházat. A saját platformja alapján a következő minta-adattárak egyikét használja:

1. [.NET-minta alkalmazása](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS-minta alkalmazása]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongúz-minta alkalmazása](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java-minta alkalmazás](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot-minta alkalmazása](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A használt platformtól függően telepítse a szükséges csomagokat, és indítsa el az alkalmazást. A függőségek telepítéséhez kövesse a minta alkalmazás-tárházban található README fájlt. A NodeJS minta alkalmazásban például a következő parancsokkal telepítheti a szükséges csomagokat, és elindíthatja az alkalmazást.

```bash
cd mean
npm install
node index.js
```
Az alkalmazás megpróbál csatlakozni egy MongoDB-forráshoz, és sikertelen, mert a kapcsolati sztring érvénytelen. A kapcsolódási karakterlánc frissítéséhez kövesse a README lépéseit `url` . Frissítse a t a `readFromRegion` Cosmos-fiókjában lévő olvasási régióra is. Az alábbi utasítások a NodeJS mintából származnak:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

A következő lépések elvégzése után a minta alkalmazás fut, és a következő kimenetet hozza létre:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Olvasás a preferencia mód használatával

A MongoDB protokoll a következő olvasási beállításokat biztosítja az ügyfelek számára:

1. ELSŐDLEGES
2. PRIMARY_PREFERRED
3. MÁSODLAGOS
4. SECONDARY_PREFERRED
5. LEGKÖZELEBBI

Tekintse át a részletes [MongoDB olvasási beállításokra](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) vonatkozó dokumentációját, amely részletesen ismerteti ezen olvasási beállítások viselkedését. Cosmos DB az elsődleges leképezések a régiók és a másodlagos leképezések ÍRÁSÁRA az OLVASÁSI régióba.

A gyakori forgatókönyvek alapján a következő beállítások használatát javasoljuk:

1. Ha **kis késleltetésű olvasásra** van szükség, használja a **legközelebbi** olvasási preferencia módot. Ez a beállítás a legközelebbi elérhető régióra irányítja az olvasási műveleteket. Vegye figyelembe, hogy ha a legközelebbi régió az írási régió, akkor ezeket a műveleteket az adott régióra irányítja a rendszer.
2. Ha a **magas rendelkezésre állás és az olvasások földrajzi eloszlása** kötelező (a késés nem korlátozás), akkor használja az **elsődleges előnyben részesített** vagy **másodlagos előnyben részesített** olvasási preferencia módot. Ezzel a beállítással az olvasási műveleteket egy elérhető írási vagy OLVASÁSI régióra irányítja. Ha a régió nem érhető el, a rendszer a következő elérhető régióra irányítja át a kérelmeket, az olvasási preferencia viselkedése szerint.

A minta alkalmazás következő kódrészlete azt mutatja be, hogyan konfigurálható a legközelebbi olvasási preferencia a NodeJS-ben:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Hasonlóképpen, az alábbi kódrészlet azt mutatja be, hogyan konfigurálható a SECONDARY_PREFERRED olvasási preferencia a NodeJS-ben:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Az olvasási beállítások a következő módon adhatók meg `readPreference` paraméterként a kapcsolatok karakterlánc URI-beállításaiban:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Tekintse át a megfelelő példákat a más platformokra, például a [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) -re és a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)-ra.

## <a name="read-using-tags"></a>Olvasás címkék használatával

Az olvasási preferencia mód mellett a MongoDB protokoll lehetővé teszi a címkék közvetlen olvasási műveletekhez való használatát. A MongoDB Cosmos DB API-ban `region` alapértelmezés szerint a címke szerepel a válasz részeként `isMaster` :

```json
"tags": {
         "region": "West US"
      }
```

Ezért a MongoClient használhatja a `region` címkét és a régió nevét, hogy az adott régióba irányítsa az olvasási műveleteket. Cosmos-fiókok esetén a régió neve a **Beállítások – >replika-adat globális** részén, a bal oldali Azure Portal található. Ez a beállítás akkor hasznos, ha **olvasási elkülönítést** szeretne elérni – olyan esetekben, amelyekben az ügyfélalkalmazás csak egy adott régióra szeretné irányítani az olvasási műveleteket. Ez a beállítás ideális olyan nem üzemi/analitikai típusú forgatókönyvekhez, amelyek a háttérben futnak, és nem a kritikus fontosságú szolgáltatások.

A minta alkalmazás következő kódrészlete azt mutatja be, hogyan konfigurálható az olvasási beállítások a címkékkel a NodeJS-ben:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Tekintse át a megfelelő példákat a más platformokra, például a [.net](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) -re és a [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)-ra.

Ebből a cikkből megtudhatta, hogyan oszthat meg globálisan olvasási műveleteket az olvasási beállításokkal az Azure Cosmos DB API-MongoDB való használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a jelen cikk által létrehozott összes erőforrást a Azure Portal a következő lépésekkel:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

* [MongoDB adatok importálása az Azure Cosmos DB-be](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Globálisan elosztott adatbázis beállítása Azure Cosmos DB API-MongoDB](tutorial-global-distribution-mongodb.md)
* [Helyi fejlesztés a Azure Cosmos DB emulátorral](local-emulator.md)