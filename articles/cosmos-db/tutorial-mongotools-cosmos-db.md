---
title: MongoDB migrálása kapcsolat nélküli üzemmódban Azure Cosmos DB API-hoz az MongoDB-hez, MongoDB natív eszközök használatával
description: Ismerje meg, hogy a MongoDB natív eszközei hogyan telepíthetik át a MongoDB-példányok kis adatkészleteit Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101655751"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Oktatóanyag: a MongoDB migrálása Azure Cosmos DB API-MongoDB offline állapotba MongoDB natív eszközök használatával

A MongoDB natív eszközeivel az adatbázisok offline (egyszeri) áttelepítését végezheti el egy helyszíni vagy Felhőbeli MongoDB, hogy Azure Cosmos DB API-ját a MongoDB-hez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Válassza ki a megfelelő MongoDB natív eszközt a használati esethez
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.
> * Ellenőrizze, hogy az áttelepítés sikeres volt-e.

Ebben az oktatóanyagban áttelepít egy adatkészletet egy Azure-beli virtuális gépen üzemeltetett MongoDB, hogy Azure Cosmos DB API-t MongoDB a MongoDB natív eszközeinek használatával. A MongoDB natív eszközei olyan bináris fájlok összessége, amelyek megkönnyítik az adatkezelést egy meglévő MongoDB-példányon. Mivel a Azure Cosmos DB Mongo API-t tesz elérhetővé, a MongoDB natív eszközei behelyezhetik az adatAzure Cosmos DBba. A dokumentum célja, hogy a *mongoexport/mongoimport* vagy a *mongodump/Mongorestore* használatával egy MongoDB-példányból áttelepíti az adatáthelyezést. Mivel a natív eszközök kapcsolati karakterláncokkal csatlakoznak a MongoDB-hez, az eszközöket bárhol futtathatja, azonban javasoljuk, hogy ezeket az eszközöket ugyanazon a hálózaton belül futtassa, mint a MongoDB-példányt, hogy elkerülje a tűzfalakkal kapcsolatos problémákat. 

A MongoDB natív eszközei csak olyan gyorsan helyezhetik át az adatátvitelt, amennyit a gazdagép hardvere engedélyez; a natív eszközök lehet a legegyszerűbb megoldás olyan kis adatkészletek esetében, amelyeknél a teljes áttelepítési idő nem jelent problémát. A [MongoDB Spark-összekötő](https://docs.mongodb.com/spark-connector/current/), az [Azure adatáttelepítési szolgáltatás (DMS)](../dms/tutorial-mongodb-cosmos-db.md)vagy a [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) jobb alternatíva lehet, ha méretezhető áttelepítési folyamatra van szüksége.

Ha még nincs beállítva MongoDB-forrás, tekintse [meg a MongoDB telepítése és konfigurálása Windowsos virtuális gépen az Azure-ban](/previous-versions/azure/virtual-machines/windows/install-mongodb)című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például az átviteli sebesség becslését, a partíciós kulcs kiválasztását és az indexelési házirendet.
* [Hozzon létre egy Azure Cosmos db API-t a MongoDB-fiókhoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Jelentkezzen be a MongoDB-példányba
    * [Töltse le és telepítse a MongoDB natív eszközeit erről a hivatkozásról](https://www.mongodb.com/try/download/database-tools).
        * **Győződjön meg arról, hogy a MongoDB natív eszközeinek verziója megegyezik a meglévő MongoDB-példánnyal.**
        * Ha a MongoDB-példánya Azure Cosmos DB Mongo API-val eltérő verziójú, akkor **telepítse mindkét MongoDB natív eszköz verzióját, és használja a MongoDB és Azure Cosmos db MONGO API megfelelő eszköz-verzióját.**
    * Adjon hozzá `readWrite` engedélyekkel rendelkező felhasználót, ha már létezik ilyen. Az oktatóanyag későbbi részében adja meg ezt a felhasználónevet és jelszót a *mongoexport* és a *mongodump* eszközökhöz.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Azure Cosmos DB kiszolgáló oldali újrapróbálkozások konfigurálása

A MongoDB-ből áttelepített ügyfelek az erőforrás-irányítási képességektől Azure Cosmos DB előnyt élveznek, ami garantálja a kiépített RU/s teljesítmény teljes kihasználását. Azure Cosmos DB egy adott kérést az áttelepítés során szabályozhat, ha a kérelem túllépi a kiosztott RU/mp-t; ezt követően a kérést újra kell próbálkozni. Az áttelepítési eszköz közötti, a hálózati ugrásban érintett idő, valamint a Azure Cosmos DB hatással van a kérelem teljes válaszideje; Emellett előfordulhat, hogy a MongoDB natív eszközei nem kezelik az újrapróbálkozásokat. A Azure Cosmos DB *kiszolgálóoldali újrapróbálkozási* funkciója lehetővé teszi, hogy a szolgáltatás lehallgassa a sávszélesség-szabályozási kódokat, és sokkal alacsonyabb időpontra próbálkozzon újra, ami jelentősen javítja a kérelmek válaszideje. Az MongoDB natív eszközei szempontjából az újrapróbálkozások kezelésének szükségessége a lehető legkisebbre csökkenthető, ami pozitív hatással lesz az áttelepítés során tapasztalható élményre.

A kiszolgálóoldali újrapróbálkozás funkció a Azure Cosmos DB portál *szolgáltatások* paneljén található.

![Képernyőkép a MongoDB SSR szolgáltatásról.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Ha pedig le van *tiltva*, akkor azt javasoljuk, hogy az alább látható módon engedélyezze azt.

![Képernyőkép a MongoDB SSR engedélyezéséről.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>A megfelelő MongoDB natív eszköz kiválasztása

![A legjobb MongoDB natív eszköz kiválasztásának ábrája.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* a *mongoexport/mongoimport* az áttelepítési eszközök legjobb párja a MongoDB-adatbázis egy részhalmazának áttelepítéséhez.
    * a *mongoexport* a meglévő adatait egy ember által olvasható JSON-vagy CSV-fájlba exportálja. a *mongoexport* egy argumentummal adja meg az exportálandó meglévő adatkészletet. 
    * a *mongoimport* egy JSON-vagy CSV-fájlt nyit meg, és beszúrja a tartalmat a célként megadott adatbázis-példányba (ebben az esetben Azure Cosmos db.). 
    * Vegye figyelembe, hogy a JSON és a CSV nem tömöríthető formátum; Előfordulhat, hogy felesleges hálózati költségek merülnek fel, mivel a *mongoimport* adatokat küld Azure Cosmos db.
* a *mongodump/mongorestore* a legjobb áttelepítési eszközök a teljes MongoDB-adatbázis áttelepítéséhez. A Compact BSON formátum hatékonyabban fogja használni a hálózati erőforrásokat, mivel a rendszer az adatAzure Cosmos DBba szúrja be az adatmennyiséget.
    * a *mongodump* exportálja a meglévő adatait BSON-fájlként.
    * a *mongorestore* Azure Cosmos DBba importálja a BSON-fájl tartalmát.
* Félretéve – ha egyszerűen van egy kis JSON-fájl, amelyet importálni szeretne Azure Cosmos DB Mongo API-ba, a *mongoimport* eszköz gyors megoldást kínál az adatfeldolgozásra.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>A Azure Cosmos DB Mongo API hitelesítő adatainak összegyűjtése

Azure Cosmos DB Mongo API kompatibilis hozzáférési hitelesítő adatokat biztosít, amelyeket a natív eszközök MongoDB. Ezeket a hitelesítő adatokat kézzel kell elvégeznie ahhoz, hogy áttelepítse az adatokat Azure Cosmos DB Mongo API-ba. A hitelesítő adatok megkeresése:

1. Nyissa meg az Azure Portalt
1. Navigáljon a Azure Cosmos DB Mongo API-fiókjához
1. A bal oldali navigációs panelen válassza ki a *kapcsolódási karakterlánc* panelt, és az alábbihoz hasonló megjelenítést kell látnia:

    ![Képernyőkép a Azure Cosmos DB hitelesítő adatokról.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Gazdagép* – a Azure Cosmos db végpont MongoDB állomásnévként működik
    * *Port* – ha a MongoDB Native tools csatlakozik Azure Cosmos DBhoz, explicit módon meg kell adnia ezt a portot
    * *Username (Felhasználónév* ) – az Azure Cosmos db végponti tartománynév előtagja MongoDB felhasználónévként működik
    * *Password (jelszó* ) – a Azure Cosmos db főkulcs MongoDB-jelszóként működik
    * Emellett jegyezze fel az *SSL* -mezőt is `true` – a MongoDB natív eszköznek ENGEDÉLYEZNIE **kell** az ssl-t az adatAzure Cosmos DBba való íráskor.

## <a name="perform-the-migration"></a>Az áttelepítés végrehajtása

1. Válassza ki, hogy mely adatbázis (ok) és gyűjtemény (ek) et szeretné áttelepíteni. Ebben a példában a *lekérdezési* gyűjteményt áttelepíti a *EDX* -adatbázisból a MongoDB-ről a Azure Cosmos DBra.

A szakasz további része végigvezeti Önt az előző szakaszban kiválasztott eszközök párosításán.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Az adatok forrás MongoDB-példányból való exportálásához nyisson meg egy terminált a MongoDB-példány számítógépén. Ha Linux rendszerű gép, írja be a következőt

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    Windows rendszeren a végrehajtható fájl lesz `mongoexport.exe` . A *gazdagépet*, a *portot*, a *felhasználónevet* és a *jelszót* a meglévő MongoDB-adatbázis-példány tulajdonságai alapján kell kitölteni. 
    
    Dönthet úgy is, hogy csak a MongoDB adatkészlet egy részhalmazát exportálja. Ennek egyik módja egy további szűrési argumentum hozzáadásával:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Csak a szűrőnek megfelelő dokumentumok lesznek `{"field1":"value1"}` exportálva.

    A hívás végrehajtása után látnia kell, hogy egy `edx.json` fájl készül:

    ![Képernyőkép a mongoexport-hívásról.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Az Azure Cosmos DBba való importáláshoz ugyanazt a terminált használhatja `edx.json` . Ha Linux rendszerű `mongoimport` gépen fut, írja be a következőt

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    Windows rendszeren a végrehajtható fájl lesz `mongoimport.exe` . A *gazdagépet*, a *portot*, a *felhasználónevet* és a *jelszót* a korábban összegyűjtött Azure Cosmos db hitelesítő adatok alapján kell kitölteni. 
1. A terminál kimenetének **figyelése** a *mongoimport*. Látnia kell, hogy a szöveg sorait kinyomtatja az áttelepítési állapot frissítéseit tartalmazó terminálra:

    ![Képernyőkép a mongoimport-hívásról.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Végezetül vizsgálja meg Azure Cosmos DB az áttelepítés sikerességének **ellenőrzéséhez** . Nyissa meg a Azure Cosmos DB portált, és navigáljon a Adatkezelő. Meg kell jelennie (1) arról, hogy egy *EDX* -adatbázis *importedQuery* -gyűjteménysel lett létrehozva, és (2) Ha csak az adathalmazt exportálta, a *importedQuery* *csak* azokat a dokumentumokat tartalmazza, amelyek megfelelnek az adott adathalmaznak. Az alábbi példában csak egy dokumentum felel meg a szűrőnek `{"field1":"value1"}` :

    ![Képernyőkép Cosmos DB adatellenőrzésről.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. A MongoDB-példány BSON-adatainak létrehozásához nyisson meg egy terminált a MongoDB-példány számítógépén. Ha Linux rendszerű gép, írja be a következőt

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    A *gazdagépet*, a *portot*, a *felhasználónevet* és a *jelszót* a meglévő MongoDB-adatbázis-példány tulajdonságai alapján kell kitölteni. Látnia kell, hogy a rendszer létrehoz egy `edx-dump` könyvtárat, és azt, hogy a címtár szerkezete a `edx-dump` forrás MongoDB-példány erőforrás-hierarchiáját (adatbázis és gyűjtemény struktúráját) hozza-e létre. Minden gyűjteményt egy BSON-fájl képvisel:

    ![Képernyőkép a mongodump-hívásról.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Ugyanazt a terminált használhatja a Azure Cosmos DB tartalmának visszaállításához `edx-dump` . Ha Linux rendszerű `mongorestore` gépen fut, írja be a következőt

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    Windows rendszeren a végrehajtható fájl lesz `mongorestore.exe` . A *gazdagépet*, a *portot*, a *felhasználónevet* és a *jelszót* a korábban összegyűjtött Azure Cosmos db hitelesítő adatok alapján kell kitölteni. 
1. A terminál kimenetének **figyelése** a *mongorestore*. Látnia kell, hogy a rendszer kinyomtatja a sorokat a terminál frissítésére az áttelepítés állapotában:

    ![Képernyőkép a mongorestore-hívásról.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Végezetül vizsgálja meg Azure Cosmos DB az áttelepítés sikerességének **ellenőrzéséhez** . Nyissa meg a Azure Cosmos DB portált, és navigáljon a Adatkezelő. Meg kell jelennie (1) arról, hogy a *EDX* -adatbázis *importedQuery* -gyűjteménysel lett létrehozva, és (2) a *importedQuery* tartalmaznia kell a *teljes* adatkészletet a forrás-gyűjteményből:

    ![Képernyőkép Cosmos DB mongorestore-adatmennyiség ellenőrzéséről.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálás

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-ját a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti az adatkezelési lehetőséget. Más áttelepítés utáni optimalizálási lépéseket is végrehajthat, például optimalizálhatja az indexelési házirendet, frissítheti az alapértelmezett konzisztencia-szintet, vagy konfigurálhatja a Azure Cosmos DB-fiók globális eloszlását. További információ: [áttelepítés utáni optimalizálási](../cosmos-db/mongodb-post-migration.md) cikk.

## <a name="additional-resources"></a>További források

* [Cosmos DB szolgáltatás adatai](https://azure.microsoft.com/services/cosmos-db/)
* [A MongoDB Database Tools dokumentációja](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Következő lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatóban](https://datamigration.microsoft.com/)található további forgatókönyvekhez.