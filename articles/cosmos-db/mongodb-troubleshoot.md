---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661273"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A következő cikk a MongoDB-hez készült Azure Cosmos DB API-val történő üzembe helyezések gyakori hibáit és megoldásait ismerteti.

>[!Note]
> Azure Cosmos DB nem üzemelteti a MongoDB motort. Az [4,0](mongodb-feature-support-40.md)-es, [3,6](mongodb-feature-support-36.md)-es és a MongoDB-protokollon keresztüli, az 3,2-es [verzióra](mongodb-feature-support.md)vonatkozó örökölt támogatás implementációját biztosítja. Ezért a hibák némelyike csak Azure Cosmos DB API-MongoDB található.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Code       | Hiba                | Leírás  | Megoldás  |
|------------|----------------------|--------------|-----------|
| 2 | A megadott Order-by elemnek megfelelő index elérési útja ki van zárva, vagy a lekérdezési sorrend nem rendelkezik a megfelelő összetett indexszel, amelyből kiszolgálható. | A lekérdezés nem indexelt mezőre kér rendezést. | Hozzon létre egy egyeztetési indexet (vagy összetett indexet) a futtatni próbált rendezett lekérdezéshez. |
| 13 | Nem engedélyezett | A kérelem nem rendelkezik a végrehajtáshoz szükséges engedélyekkel. | Ügyeljen arra, hogy megfelelő engedélyeket állítson be az adatbázishoz és a gyűjteményhez.  |
| 16 | InvalidLength | A megadott kérelem hossza érvénytelen. | Ha a magyarázat () függvényt használja, ügyeljen arra, hogy csak egy műveletet adjon meg. |
| 26 | NamespaceNotFound | A lekérdezésben hivatkozott adatbázis vagy gyűjtemény nem található. | Győződjön meg arról, hogy az adatbázis vagy a gyűjtemény neve pontosan megegyezik a lekérdezésben szereplő névvel.|
| 50 | ExceededTimeLimit | A kérés túllépte a 60 másodperces végrehajtási időkorlátot. |  Ennek a hibának számos oka lehet. Ennek egyik oka az lehet, hogy az aktuális lefoglalt kérelemegységek kapacitása nem elegendő a kérelem teljesítéséhez. Erre az adott gyűjtemény vagy adatbázis kérelemegységeinek növelése nyújthat megoldást. Egyéb esetekben ez a hiba a nagy méretű kérelmek kisebbekre bontásával kerülhető meg. Az ezt a hibát kiváltó írási művelet újrapróbálása ismételt írást eredményezhet. <br><br>Ha nagy mennyiségű adatot szeretne törölni anélkül, hogy az hatással lenne a kérelemegységek számára: <br>-Érdemes használni az ÉLETTARTAMot (timestamp alapján): az [MongoDB Azure Cosmos db API-val való lejárata](./mongodb-time-to-live.md) <br>– A törlés végrehajtásához használja a kurzor/batch méretet. Egyesével hívhatja le a dokumentumokat, és egy-egy ciklusban törölheti azokat. Ez segít az adatok lassú törlésének végrehajtásában, az éles alkalmazás befolyásolása nélkül.|
| 61 | ShardKeyNotFound | A kérelemben szereplő dokumentum nem tartalmazza a gyűjtemény szegmenskulcsát (az Azure Cosmos DB-partíciókulcsot). | Gondoskodjon arról, hogy a gyűjtemény szegmenskulcsa szerepeljen a kérelemben.|
| 66 | ImmutableField | A kérelem egy nem módosítható mezőt próbál meg módosítani | az "id" mezők nem változtathatók meg. Győződjön meg arról, hogy a kérés nem kísérli meg a mező frissítését. |
| 67 | CannotCreateIndex | Az index létrehozására irányuló kérelmet nem lehet végrehajtani. | Legfeljebb 500 egymezős index hozható létre egy tárolóban. Egy összetett indexben legfeljebb nyolc mező szerepelhet (az összetett indexek támogatása a 3.6-os verziótól kezdve érhető el). |
| 115 | CommandNotSupported | A megkísérelt kérelem nem támogatott. | A hibaüzenet további részleteket tartalmaz. Ha az üzemelő példányok esetében ez a funkció fontos, kérjük, tudassa velünk, ha létrehoz egy támogatási jegyet a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | A beszúrt dokumentum szegmenskulcsa (Azure Cosmos DB-partíciókulcsa) már megtalálható a gyűjteményben, vagy meg lett sértve egy indexmezőre vonatkozó egyéni korlátozás. | A meglévő dokumentumokat az update() függvénnyel frissítheti. Ha az indexmezőre vonatkozó egyéni korlátozás meg lett sértve, szúrjon be a dokumentumba egy olyan mezőértéket, vagy frissítse egy olyan mezőértékkel, amely még nem található meg a szegmensben/partícióban. |
| 16500 | TooManyRequests  | A felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményhez kiosztott kérelemegységek száma, ezért szabályozva lett. | Érdemes lehet skálázni az adott tárolóhoz vagy tárolókészlethez megadott átviteli sebességet az Azure portálon, vagy újból elvégezni a műveletet. Ha [engedélyezi a SSR](prevent-rate-limiting-errors.md) (kiszolgálóoldali újrapróbálkozás) lehetőséget, a Azure Cosmos db automatikusan újrapróbálkozik a hiba miatt meghiúsult kérelmekkel. |
| 16501 | ExceededMemoryLimit | Egy több-bérlős szolgáltatás esetében a művelet túllépte az ügyfél által lekötött memóriamennyiséget. | Csökkentse a művelet hatókörét jobban korlátozó lekérdezési feltételekkel, vagy az [Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lépjen kapcsolatba az ügyfélszolgálattal. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | A folyamatszakasz neve nem ismerhető fel. | Az összesített folyamatkérelemben lévő szakasznév nem ismerhető fel. | Győződjön meg arról, hogy minden összesített folyamatnév érvényes a kérelemben. |
| - | A MongoDB vonalprotokoll-verziójával kapcsolatos problémák | A MongoDB-illesztőprogramok régebbi verziói nem képesek észlelni az Azure Cosmos-fiók nevét a kapcsolati sztringekben. | Fűzze hozzá a *appName = @**accountName** @* a Cosmos db API-ját a MongoDB-kapcsolatok karakterláncához, ahol a ***accountName*** a Cosmos db fiók neve. |
| - | A MongoDB-ügyfél hálózati problémái (például szoftvercsatorna- vagy endOfStream-kivételek)| A hálózati kérelem sikertelen volt. Ezt gyakran egy inaktív TCP-kapcsolat okozza, amelyet a MongoDB-ügyfél használni próbál. A MongoDB-illesztők gyakran kapcsolatkészletezést használnak, amelynek eredménye a kérelemhez használt készletből választott, véletlenszerű kapcsolat. Az inaktív kapcsolatok általában négy perc után túllépik az időkorlátot az Azure Cosmos DB oldalán. | Próbálja megismételni a sikertelen kérelmeket az alkalmazás kódjában, módosítsa a MongoDB-ügyfél (illesztőprogram) beállításait úgy, hogy az inaktív TCP-kapcsolatokat Teardown a négy perces időtúllépési időszak előtt, vagy konfigurálja az operációs rendszer életben tartási beállításait a TCP-kapcsolatok aktív állapotban való fenntartásához.<br><br>A kapcsolati üzenetek elkerüléséhez érdemes úgy módosítani a kapcsolati sztringet, hogy a maxConnectionIdleTime értékét 1–2 percre állítja.<br>-Mongo-illesztőprogram: configure *maxIdleTimeMS = 120000* <br>-Node.JS: configure *socketTimeoutMS = 120000*, *autoReconnect* = true, *életben* = true, *keepAliveInitialDelay* = 3 perc

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.