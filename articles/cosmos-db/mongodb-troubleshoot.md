---
title: A Mongo adatbázis Azure Cosmos DB API-jával kapcsolatos gyakori hibák elhárítása
description: Ez a dokumentum a Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárításának módszereit tárgyalja.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692232"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A következő cikk a MongoDB-hez készült Azure Cosmos DB API-val történő üzembe helyezések gyakori hibáit és megoldásait ismerteti.

>[!Note]
> Azure Cosmos DB nem üzemelteti a MongoDB motort. Az [4,0](mongodb-feature-support-40.md)-es, [3,6](mongodb-feature-support-36.md)-es és a MongoDB-protokollon keresztüli, az 3,2-es [verzióra](mongodb-feature-support.md)vonatkozó örökölt támogatás implementációját biztosítja. Ezért a hibák némelyike csak Azure Cosmos DB API-MongoDB található.

## <a name="common-errors-and-solutions"></a>Gyakori hibák és megoldások

| Code       | Hiba                | Leírás  | Megoldás  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Ennek az egyik gyakori oka, hogy megadott rendezési elemnek megfelelő index elérési útja ki van zárva, illetve a rendezett lekérdezés nem rendelkezik a végrehajtásához szükséges megfelelő összetett indexszel. A lekérdezés nem indexelt mezőre kér rendezést. | Hozzon létre egy egyeztetési indexet (vagy összetett indexet) a futtatni próbált rendezett lekérdezéshez. |
| 2 | A tranzakció nem aktív | A többdokumentumos tranzakció túllépte a rögzített 5 másodperces korlátot. | Próbálja megismételni a többdokumentumos tranzakciót, vagy korlátozza a műveletek hatókörét a többdokumentumos tranzakción belül, hogy az 5 másodperces korláton belül befejeződjön. |
| 13 | Nem engedélyezett | A kérelem nem rendelkezik a végrehajtáshoz szükséges engedélyekkel. | Ellenőrizze, hogy a megfelelő kulcsokat használja-e.  |
| 26 | NamespaceNotFound | A lekérdezésben hivatkozott adatbázis vagy gyűjtemény nem található. | Győződjön meg arról, hogy az adatbázis vagy a gyűjtemény neve pontosan megegyezik a lekérdezésben szereplő névvel.|
| 50 | ExceededTimeLimit | A kérés túllépte a 60 másodperces végrehajtási időkorlátot. |  Ennek a hibának számos oka lehet. Ennek egyik oka az lehet, hogy az aktuális lefoglalt kérelemegységek kapacitása nem elegendő a kérelem teljesítéséhez. Erre az adott gyűjtemény vagy adatbázis kérelemegységeinek növelése nyújthat megoldást. Egyéb esetekben ez a hiba a nagy méretű kérelmek kisebbekre bontásával kerülhető meg. Az ezt a hibát kiváltó írási művelet újrapróbálása ismételt írást eredményezhet. <br><br>Ha nagy mennyiségű adatot szeretne törölni anélkül, hogy az hatással lenne a kérelemegységek számára: <br>– Fontolja meg az (időbélyeg-alapú) TTL használatát: [Adatok lejárttá tétele a MongoDB-hez készült Azure Cosmos DB API-val](mongodb-time-to-live.md) <br>– A törléshez használja a kurzor-/kötegméret beállítását. Egyesével hívhatja le a dokumentumokat, és egy-egy ciklusban törölheti azokat. Ez segít az adatok lassú törlésének végrehajtásában, az éles alkalmazás befolyásolása nélkül.|
| 61 | ShardKeyNotFound | A kérelemben szereplő dokumentum nem tartalmazza a gyűjtemény szegmenskulcsát (az Azure Cosmos DB-partíciókulcsot). | Gondoskodjon arról, hogy a gyűjtemény szegmenskulcsa szerepeljen a kérelemben.|
| 66 | ImmutableField | A kérelem egy nem módosítható mezőt próbál meg módosítani | a "_id" mezők nem változtathatók meg. Győződjön meg arról, hogy a kérelem nem próbálja meg módosítani ezeket a mezőket vagy a szegmenskulcs mezőjét. |
| 67 | CannotCreateIndex | Az index létrehozására irányuló kérelmet nem lehet végrehajtani. | Legfeljebb 500 egymezős index hozható létre egy tárolóban. Egy összetett indexben legfeljebb nyolc mező szerepelhet (az összetett indexek támogatása a 3.6-os verziótól kezdve érhető el). |
| 112 | WriteConflict | A többdokumentumos tranzakció nem sikerült egy ütköző többdokumentumos tranzakció miatt | Ismételje meg a többdokumentumos tranzakciót egészen addig, amíg a művelet be nem fejeződik. |
| 115 | CommandNotSupported | A megkísérelt kérelem nem támogatott. | A hibaüzenet további részleteket tartalmaz. Ha ez a funkció fontos az üzemelő példányok számára, hozzon létre egy támogatási jegyet a [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és a Azure Cosmos db csapata visszakerül Önnek. |
| 11000 | DuplicateKey | A beszúrt dokumentum szegmenskulcsa (Azure Cosmos DB-partíciókulcsa) már megtalálható a gyűjteményben, vagy meg lett sértve egy indexmezőre vonatkozó egyéni korlátozás. | A meglévő dokumentumokat az update() függvénnyel frissítheti. Ha az indexmezőre vonatkozó egyéni korlátozás meg lett sértve, szúrjon be a dokumentumba egy olyan mezőértéket, vagy frissítse egy olyan mezőértékkel, amely még nem található meg a szegmensben/partícióban. Másik lehetőségként használhat olyan mezőt, amely az azonosító és a szegmenskulcs mező kombinációját tartalmazza. |
| 16500 | TooManyRequests  | A felhasznált kérelemegységek teljes száma nagyobb, mint a gyűjteményhez kiosztott kérelemegységek száma, ezért szabályozva lett. | Érdemes lehet skálázni az adott tárolóhoz vagy tárolókészlethez megadott átviteli sebességet az Azure portálon, vagy újból elvégezni a műveletet. Ha engedélyezi a SSR-t (kiszolgálóoldali újrapróbálkozást), az Azure Cosmos DB automatikusan újrapróbálkozik a hiba miatt meghiúsult kérelmek végrehajtásával. |
| 16501 | ExceededMemoryLimit | Egy több-bérlős szolgáltatás esetében a művelet túllépte az ügyfél által lekötött memóriamennyiséget. Ez csak a MongoDB-hez készült Azure Cosmos DB API 3.2-es verziójára érvényes. | Csökkentse a művelet hatókörét jobban korlátozó lekérdezési feltételekkel, vagy az [Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) lépjen kapcsolatba az ügyfélszolgálattal. Például: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | A folyamatszakasz neve nem ismerhető fel. | Az összesített folyamatkérelemben lévő szakasznév nem ismerhető fel. | Győződjön meg arról, hogy minden összesített folyamatnév érvényes a kérelemben. |
| - | A MongoDB vonalprotokoll-verziójával kapcsolatos problémák | A MongoDB-illesztőprogramok régebbi verziói nem képesek észlelni az Azure Cosmos-fiók nevét a kapcsolati sztringekben. | Fűzze hozzá a `appName=@accountName@` kapcsolatok karakterláncának végéhez, ahol a `accountName` Azure Cosmos db fiók neve. |
| - | A MongoDB-ügyfél hálózati problémái (például szoftvercsatorna- vagy endOfStream-kivételek)| A hálózati kérelem sikertelen volt. Ezt gyakran egy inaktív TCP-kapcsolat okozza, amelyet a MongoDB-ügyfél használni próbál. A MongoDB-illesztők gyakran kapcsolatkészletezést használnak, amelynek eredménye a kérelemhez használt készletből választott, véletlenszerű kapcsolat. Az inaktív kapcsolatok általában négy perc után túllépik az időkorlátot az Azure Cosmos DB oldalán. | Újrapróbálkozhat a sikertelen kérelmekkel az alkalmazás kódjában, módosíthatja a MongoDB-ügyfél (illesztő) beállításait, amellyel a négyperces időtúllépési keret lejárta előtt leállíthatja az inaktív TCP-kapcsolatokat, vagy konfigurálhatja az operációs rendszer `keepalive` beállításait, amellyel aktív állapotban tarthatja a TCP-kapcsolatokat.<br><br>A kapcsolati üzenetek elkerüléséhez érdemes úgy módosítani a kapcsolati sztringet, hogy a `maxConnectionIdleTime` értékét 1–2 percre állítja.<br>– Mongo-illesztő: konfigurálja a `maxIdleTimeMS=120000` értéket <br>– Node.JS: konfigurálja a `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 perc értékeket
| - | A Mongo-felület nem működik az Azure Portalon | Amikor a felhasználó megpróbál megnyitni egy Mongo-felületet, semmi sem történik, és a lap üres marad.  | Ellenőrizze a tűzfalat. Az Azure Portalon a tűzfal nem használható a Mongo-felülettel. <br>– Telepítse a Mongo-felületet a helyi számítógépen a tűzfalszabályok alkalmazásával <br>– Használja az örökölt Mongo-felületet
| - | Nem lehet csatlakozni a kapcsolati sztringgel  | A kapcsolati sztring módosult a 3.2-es verzióról a 3.6-os verzióra történő frissítéskor | Vegye figyelembe, hogy ha a MongoDB fiókokhoz készült Azure Cosmos DB API-t használja, a 3.6-os verziójú fiókok végpontja `*.mongo.cosmos.azure.com` formátumban van, míg a 3.2-es verziójú fiókok végpontja `*.documents.azure.com` formátumban van.  

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
