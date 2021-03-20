---
title: Többdokumentumos tranzakciók használata a MongoDB Azure Cosmos DB API-ban
description: Megtudhatja, hogyan hozhat létre olyan minta Mongo rendszerhéj-alkalmazást, amely több dokumentumból álló tranzakciót (a teljes vagy semmi szemantikai műveletet) hajt végre Azure Cosmos DB API MongoDB 4,0-es verziójában lévő rögzített gyűjteményen.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692470"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Többdokumentumos tranzakciók használata a MongoDB Azure Cosmos DB API-ban
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ebben a cikkben egy olyan Mongo-rendszerhéj-alkalmazást fog létrehozni, amely egy rögzített gyűjteményben futtat egy Azure Cosmos DB API-ban a 4,0-es verzióval ellátott többdokumentumos tranzakciót a MongoDB-ben.

## <a name="what-are-multi-document-transactions"></a>Mi a többdokumentumos tranzakció?

A MongoDB Azure Cosmos DB API-ban egyetlen dokumentum műveletei az Atomic. A többdokumentumos tranzakciók lehetővé teszik, hogy az alkalmazások több dokumentumon hajtsák végre az atomi műveleteket. "Az összes vagy semmi" szemantikai műveletet a műveletekhez nyújtja. A véglegesítés során a tranzakción belül végrehajtott módosítások megmaradnak, és ha a tranzakció meghiúsul, a tranzakción belüli összes változást elveti a rendszer.

A többdokumentumos tranzakciók a **savas** szemantika követését követik:

* Atomi: az összes kezelt művelet
* Konzisztencia: az adatvéglegesítés érvényes
* Elkülönítés: más művelettől elkülönített
* Tartósság: a tranzakciós adatszolgáltatások megmaradnak, ha az ügyfél ezt mondja

## <a name="requirements"></a>Követelmények

A többdokumentumos tranzakciók támogatottak a 4,0-es API-verzióban található, nem többrétegű gyűjteményekben. A többdokumentumos tranzakciók nem támogatottak a gyűjtemények és a 4,0-es szegmensben lévő gyűjtemények között. A tranzakciók időtúllépése rögzített 5 másodperc.

Az 4,0-es vagy annál nagyobb átviteli protokollt támogató illesztőprogramok támogatják Azure Cosmos DB API-t a többdokumentumos tranzakciók MongoDB.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Több dokumentumból álló tranzakciók futtatása a MongoDB-rendszerhéjban
> [!Note]
> Ez a példa a MongoDB-Iránytűbe ágyazott MongoSH Beta (Shell) esetében nem működik.

1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, ahol a Mongo rendszerhéj 4,0-es vagy újabb verziója telepítve van:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Hozzon létre egy Mongo rendszerhéj-parancsfájlt *connect_friends.js* és adja hozzá a következő tartalmat

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Futtassa a következő parancsot a többdokumentumos tranzakció végrehajtásához. A gazdagép, a port, a felhasználó és a kulcs a Azure Portalban található.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [MongoDB 4,0-es Azure Cosmos db API](mongodb-feature-support-40.md) -val kapcsolatos újdonságokkal
