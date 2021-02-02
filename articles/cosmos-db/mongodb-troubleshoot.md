---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: f4ed242dced4798f5f416dae90ef2d6b6bde0e06
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258184"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A következő cikk a MongoDB-hez készült Azure Cosmos DB API-val történő üzembe helyezések gyakori hibáit és megoldásait ismerteti.

>[!Note]
> Azure Cosmos DB nem üzemelteti a MongoDB motort. A MongoDB átviteli protokoll implementációját biztosítja. Ezért a hibák némelyike csak Azure Cosmos DB API-MongoDB található. 

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Code       | Hiba                | Leírás  | Megoldás  |
|------------|----------------------|--------------|-----------|
| 2 | A megadott Order-by elemnek megfelelő index elérési útja ki van zárva, vagy a lekérdezési sorrend nem rendelkezik a megfelelő összetett indexszel, amelyből kiszolgálható. | A lekérdezés nem indexelt mezőre kér rendezést. | Hozzon létre egy egyező indexet (vagy kompozit indexet) a megkísérelt rendezési lekérdezéshez. |
| 13 | Nem engedélyezett | A kérelem nem rendelkezik a befejezéshez szükséges engedélyekkel. | Ügyeljen arra, hogy megfelelő engedélyeket állítson be az adatbázishoz és a gyűjteményhez.  |
| 16 | InvalidLength | A megadott kérelem hossza érvénytelen. | Ha a magyarázat () függvényt használja, ügyeljen arra, hogy csak egy műveletet adjon meg. |
| 26 | NamespaceNotFound | A lekérdezésben hivatkozott adatbázis vagy gyűjtemény nem található. | Győződjön meg arról, hogy az adatbázis/gyűjtemény neve pontosan megegyezik a lekérdezésben szereplő névvel.|
| 50 | ExceededTimeLimit | A kérés túllépte a 60 másodperces végrehajtási időkorlátot. |  Ennek a hibának számos oka lehet. Az egyik oka az, hogy ha a jelenleg lefoglalt kérési egység kapacitása nem elegendő a kérelem teljesítéséhez. Erre az adott gyűjtemény vagy adatbázis kérelemegységeinek növelése nyújthat megoldást. Más esetekben ez a hiba a nagyméretű kérések kisebbekre bontásával is feldolgozható. A hibát megkapott írási művelet újrapróbálása ismétlődő írást eredményezhet. <br><br>Ha nagy mennyiségű adatmennyiséget próbál törölni az RUs hatása nélkül: <br>-Érdemes használni az ÉLETTARTAMot (timestamp alapján): az [MongoDB Azure Cosmos db API-val való lejárata](https://docs.microsoft.com/azure/cosmos-db/mongodb-time-to-live) <br>– A törlés végrehajtásához használja a kurzor/batch méretet. Egyesével hívhatja le a dokumentumokat, és egy-egy ciklusban törölheti azokat. Ezzel a művelettel lassan törölheti az adatait anélkül, hogy az hatással lenne az éles alkalmazásra.|
| 61 | ShardKeyNotFound | A kérelemben szereplő dokumentum nem tartalmazta a gyűjteményhez tartozó szilánk-kulcsot (Azure Cosmos DB partíciós kulcsot). | Győződjön meg arról, hogy a kérelemben a gyűjteményhez tartozó szilánk-kulcs használatban van.|
| 66 | ImmutableField | A kérelem nem módosítható mezőt próbál módosítani | az "id" mezők nem változtathatók meg. Győződjön meg arról, hogy a kérés nem kísérli meg a mező frissítését. |
| 67 | CannotCreateIndex | Az index létrehozásának kérelme nem hajtható végre. | Akár 500 egymezős index hozható létre egy tárolóban. Egy összetett indexben legfeljebb nyolc mező szerepelhet (az összetett indexek a 3.6 + verzióban támogatottak). |
| 115 | CommandNotSupported | A megkísérelt kérés nem támogatott. | A hibaüzenetben további részleteket is meg kell adni. Ha az üzemelő példányok esetében ez a funkció fontos, kérjük, tudassa velünk, ha létrehoz egy támogatási jegyet a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | A beszúrt dokumentum (Azure Cosmos DB partíciós kulcsa) már létezik a gyűjteményben, vagy egy egyedi index mező megkötése meg lett sértve. | Egy meglévő dokumentum frissítéséhez használja a Update () függvényt. Ha az egyedi index mező megkötését megsértették, szúrja be vagy frissítse a dokumentumot olyan értékkel, amely még nem szerepel a szegmensben/partícióban. |
| 16500 | TooManyRequests  | A felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményhez kiosztott kérelemegységek száma, ezért szabályozva lett. | Érdemes lehet skálázni az adott tárolóhoz vagy tárolókészlethez megadott átviteli sebességet az Azure portálon, vagy újból elvégezni a műveletet. Ha [engedélyezi a SSR](prevent-rate-limiting-errors.md) (kiszolgálóoldali újrapróbálkozás) lehetőséget, a Azure Cosmos db automatikusan újrapróbálkozik a hiba miatt meghiúsult kérelmekkel. |
| 16501 | ExceededMemoryLimit | Több-bérlős szolgáltatásként a művelet túllépte az ügyfél memóriájának kiosztását. | Csökkentse a művelet hatókörét szigorúbb lekérdezési feltételekkel, vagy forduljon a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)támogatási szolgálatához. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | Ismeretlen a folyamat fázisának neve. | Nem ismerhető fel a szakasz neve az összesítési folyamat kérelmében. | Győződjön meg arról, hogy az összes aggregációs folyamat neve érvényes a kérelemben. |
| - | A MongoDB vonalprotokoll-verziójával kapcsolatos problémák | A MongoDB-illesztőprogramok régebbi verziói nem tudják felderíteni az Azure Cosmos-fiók nevét a kapcsolódási karakterláncokban. | Fűzze hozzá a *appName = @**accountName** @* a Cosmos db API-ját a MongoDB-kapcsolatok karakterláncához, ahol a ***accountName** _ a Cosmos db fiók neve. |
| - | MongoDB-ügyfél hálózati problémái (például szoftvercsatorna-vagy endOfStream-kivételek)| A hálózati kérelem sikertelen volt. Ezt gyakran a MongoDB-ügyfél által megkísérelt inaktív TCP-kapcsolatok okozzák. A MongoDB-illesztőprogramok gyakran használják a kapcsolatok készletezését, ami véletlenszerű kapcsolatokat eredményez a kérelemhez használt készletből. Az inaktív kapcsolatok jellemzően időtúllépést okoznak a Azure Cosmos DB négy perc után. | Próbálja megismételni a sikertelen kérelmeket az alkalmazás kódjában, módosítsa a MongoDB-ügyfél (illesztőprogram) beállításait úgy, hogy az inaktív TCP-kapcsolatokat Teardown a négy perces időtúllépési időszak előtt, vagy konfigurálja az operációs rendszer életben tartási beállításait a TCP-kapcsolatok aktív állapotban való fenntartásához.<br><br>A kapcsolati üzenetek elkerüléséhez érdemes úgy módosítani a kapcsolati sztringet, hogy a maxConnectionIdleTime értékét 1–2 percre állítja.<br>-Mongo-illesztőprogram: configure _maxIdleTimeMS = 120000 * <br>-Node.JS: configure *socketTimeoutMS = 120000*, *autoReconnect* = true, *életben* = true, *keepAliveInitialDelay* = 3 perc

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
