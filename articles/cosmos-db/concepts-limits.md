---
title: Azure Cosmos DB szolgáltatási kvóták
description: Azure Cosmos DB a szolgáltatási kvótákat és az alapértelmezett korlátokat a különböző erőforrástípusok esetében.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: f33490e273104105502ecc8f4f255c354a2d2f3b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800023"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB szolgáltatási kvóták

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk áttekintést nyújt az Azure Cosmos DB különböző erőforrásaihoz ajánlott alapértelmezett kvótákról.

## <a name="storage-and-database-operations"></a>Tárolási és adatbázis-műveletek

Miután létrehozta az előfizetéséhez tartozó Azure Cosmos-fiókot, a fiókban lévő adatokat [adatbázisok, tárolók és elemek létrehozásával](account-databases-containers-items.md)kezelheti.

### <a name="provisioned-throughput"></a>Kiosztott átviteli sebesség

Az átviteli sebességet tároló szinten vagy adatbázis-szinten is kiépítheti a [kérési egységek (ru/s vagy RUs)](request-units.md)szempontjából. A következő táblázat felsorolja a tárolók és az adatbázisok tárolási és átviteli korlátait.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális RUs/tároló ([dedikált teljesítményű kiépített mód](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](create-support-request-quota-increase.md) |
| Maximális RUs/adatbázis ([megosztott teljesítményű kiépített mód](account-databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](create-support-request-quota-increase.md) |
| Maximális RUs/(logikai) partíció | 10,000 |
| Maximális tárterület az összes elem/(logikai) partíció között | 20 GB |
| Eltérő (logikai) partíciós kulcsok maximális száma | Korlátlan |
| Tárolók maximális száma | Korlátlan |
| Tárolók maximális száma adatbázis szerint | Korlátlan |
| A mellékletek maximális mérete (a melléklet funkció elavult) | 2 GB |
| 1 GB-os minimálisan szükséges RU/s | 10 RU/s<br>**Megjegyzés:** ez a minimum akkor csökkenthető, ha a fiókja jogosult a ["nagy tárterület/alacsony átviteli sebesség" program](set-throughput.md#high-storage-low-throughput-program) használatára |

> [!NOTE]
> Ha többet szeretne megtudni a tárolási vagy átviteli sebességre vonatkozó magasabb korlátot igénylő munkaterhelések kezelésével kapcsolatos ajánlott eljárásokról, olvassa el [a szintetikus partíciós kulcs létrehozása](synthetic-partition-keys.md)című témakört.

### <a name="minimum-throughput-limits"></a>Minimális átviteli sebesség korlátai

A Cosmos-tárolónak (vagy a megosztott átviteli sebességű adatbázisnak) legalább 400 RU/s-nek kell lennie. Ahogy a tároló növekszik, Cosmos DB minimális átviteli sebességre van szükség annak biztosításához, hogy az adatbázis vagy a tároló elegendő erőforrással rendelkezik a műveleteihez.

A tárolók vagy adatbázisok aktuális és minimális átviteli sebessége a Azure Portal vagy az SDK-k alapján kérhető le. További információkért lásd: [átviteli sebesség tárolók és adatbázisok](set-throughput.md)számára. 

A tényleges RU/mp a fiók konfigurációjától függően változhat. [Azure monitor metrikák](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) használatával megtekintheti az erőforráson kiépített átviteli sebesség (ru/s) és tárterület előzményeit. 

#### <a name="minimum-throughput-on-container"></a>Minimális átviteli sebesség a tárolón 

A manuális átviteli sebességű tárolók minimális átviteli sebességének becsléséhez keresse meg a maximális értéket:

* 400 RU/s 
* Aktuális tárterület (GB * 10 RU/s)
* A tárolón/100-ben kiépített legmagasabb RU/s

Példa: tegyük fel, hogy rendelkezik egy 400 RU/s és 0 GB tárhelytel kiépített tárolóval. Növelje az átviteli sebességet 50 000 RU/s értékre, és 20 GB adat importálását. A minimális RU/s most `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 ru/s. Az idő múlásával a tárterület 200 GB-ra nő. A minimális RU/s most `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 ru/s. 

**Megjegyzés:** ha a fiókja jogosult a ["nagy tárterület/alacsony átviteli sebesség" programra](set-throughput.md#high-storage-low-throughput-program), a minimális átviteli sebesség (GB/s) is csökkenthető.

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Minimális átviteli sebesség megosztott átviteli sebességgel rendelkező adatbázis esetén 
A megosztott átviteli sebességgel rendelkező adatbázishoz szükséges minimális átviteli sebesség manuális átviteli sebességgel való megbecsléséhez keresse meg a következő maximális értékét:

* 400 RU/s 
* Aktuális tárterület (GB * 10 RU/s)
* Az adatbázison/100-ben kiépített legmagasabb RU/s
* 400 + MAX (tárolók száma-25, 0) * 100 RU/s

Példa: Tegyük fel, hogy egy 400 RU/s sebességgel, 15 GB tárhellyel és 10 tárolóval kiépített adatbázisa van. A minimális RU/s érték `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 ru/s. Ha az adatbázisban 30 tároló található, a minimális RU/s érték `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 ru/s. 

**Megjegyzés:** ha a fiókja jogosult a ["nagy tárterület/alacsony átviteli sebesség" programra](set-throughput.md#high-storage-low-throughput-program), a minimális átviteli sebesség (GB/s) is csökkenthető.

Összefoglalva: itt vannak a minimálisan kiépített RU-korlátok. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Minimális RUs/tároló ([dedikált teljesítményű kiépített mód](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimális RUs/adatbázis ([megosztott teljesítményű kiépített mód](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s az első 25 tárolóhoz. További 100 RU/s az egyes tárolók esetében. |

A Cosmos DB az SDK-k vagy a portál használatával támogatja az átviteli sebesség (RU/s) programozott méretezését tároló vagy adatbázis alapján.    

Az aktuális RU/s kiépített és erőforrás-beállításoktól függően az egyes erőforrások szinkron módon és azonnal méretezhetők a minimális RU/s között, és legfeljebb 100x a minimum RU/s értékig. Ha a kért átviteli sebesség kívül esik a tartományon, a skálázás aszinkron módon történik. Az aszinkron skálázás perctől akár órákig is eltarthat, a tárolóban kért átviteli sebességtől és az adattároló méretétől függően.  

### <a name="serverless"></a>Kiszolgáló nélküli

A [kiszolgáló](serverless.md) nélküli használata lehetővé teszi a Azure Cosmos db-erőforrások felhasználáson alapuló módon történő használatát. A következő táblázat felsorolja a tárolók és az adatbázisok tárolási és átviteli sebességének korlátozásait.

| Erőforrás | Korlát |
| --- | --- |
| Maximális RU/s/(logikai) partíció | 5000 |
| Maximális tárterület az összes elem/(logikai) partíció között | 20 GB |
| Eltérő (logikai) partíciós kulcsok maximális száma | Korlátlan |
| Tárolók maximális száma | 50 GB |

## <a name="control-plane-operations"></a>Vezérlési sík műveletei

Azure Cosmos-fiókját az Azure Portal, az Azure PowerShell, az Azure CLI és a Azure Resource Manager sablonok használatával is [kiépítheti és kezelheti](how-to-manage-database-account.md) . A következő táblázat felsorolja az előfizetések, a fiókok és a műveletek számát.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázis-fiókok maximális száma (előfizetés) | 50 alapértelmezés szerint. Megnövelheti [egy Azure-támogatási jegy bejelentésével](create-support-request-quota-increase.md)|
| Regionális feladatátvételek maximális száma | Alapértelmezés szerint 1/óra. Megnövelheti [egy Azure-támogatási jegy bejelentésével](create-support-request-quota-increase.md)|

> [!NOTE]
> A regionális feladatátvétel csak az egyetlen régióba írja a fiókokat. A többrégiós írási fiókok nem igénylik vagy nem korlátozzák az írási régió módosítását.

A Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. A biztonsági másolatok megőrzési időközökről és a Windowsról a következő témakörben tájékozódhat: [online biztonsági mentés és igény szerinti adatok visszaállítása Azure Cosmos DBban](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Felhasználónkénti korlátok

### <a name="provisioned-throughput"></a>Kiosztott átviteli sebesség

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Adatbázisok maximális száma | Korlátlan |
| Tárolók maximális száma a megosztott átviteli sebességgel rendelkező adatbázisokban |25 |
| Tárolók maximális száma adatbázis vagy fiók számára dedikált átviteli sebességgel  |korlátlan |
| Régiók maximális száma | Nincs korlát (az összes Azure-régió) |

### <a name="serverless"></a>Kiszolgáló nélküli

| Erőforrás | Korlát |
| --- | --- |
| Adatbázisok maximális száma | Korlátlan |
| Tárolók maximális száma egy fiókban  | 100 |
| Régiók maximális száma | 1 (bármely Azure-régió) |

## <a name="per-container-limits"></a>/Tároló korlátok

Attól függően, hogy melyik API-t használja, az Azure Cosmos-tárolók gyűjteményeket, táblákat vagy diagramokat is jelenthetnek. A tárolók támogatják az [egyedi kulcsokra vonatkozó megkötések](unique-keys.md), [tárolt eljárások, eseményindítók és UDF](stored-procedures-triggers-udfs.md)konfigurációját, valamint az [indexelési szabályzatot](how-to-manage-indexing-policy.md). A következő táblázat felsorolja a tárolón belüli konfigurációkra vonatkozó korlátozásokat. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az adatbázis vagy a tároló nevének maximális hossza | 255 |
| Tárolt eljárások maximális száma tárolóban | 100 <sup>*</sup>|
| UDF maximális száma | 50 <sup>*</sup>|
| Elérési utak maximális száma az indexelési házirendben| 100 <sup>*</sup>|
| Egyedi kulcsok maximális száma egy tárolóban|10 <sup>*</sup>|
| Elérési utak maximális száma egyedi kulcs megkötése esetén|16 <sup>*</sup>|
| Maximális TTL-érték |2147483647|

<sup>*</sup> Az [Azure-support Request](create-support-request-quota-increase.md)létrehozásával növelheti az egyes tárolók korlátainak bármelyikét.

## <a name="per-item-limits"></a>Cikkenként érvényes korlátok

Attól függően, hogy melyik API-t használja, egy Azure Cosmos-elem a gyűjteményben szereplő dokumentumot, egy tábla egy sorát, vagy egy gráf egyik csomópontját vagy szegélyét jelöli. A következő táblázat a Cosmos DBban található határértékeket mutatja. 

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Elemek maximális mérete | 2 MB (UTF-8 a JSON-ábrázolás hossza) |
| Partíciós kulcs értékének maximális hossza | 2048 bájt |
| AZONOSÍTÓ érték maximális hossza | 1023 bájt |
| Tulajdonságok maximális száma cikkenként | Nincs gyakorlati korlát |
| Tulajdonságnév maximális hossza | Nincs gyakorlati korlát |
| Tulajdonságérték maximális hossza | Nincs gyakorlati korlát |
| Karakterlánc-tulajdonságérték maximális hossza | Nincs gyakorlati korlát |
| Numerikus tulajdonság értékének maximális hossza | IEEE754 dupla pontosságú, 64 bites |
| Beágyazott objektumok/tömbök beágyazásának maximális szintje | 128 |
| Maximális TTL-érték |2147483647|

Az elemekre nem vonatkoznak korlátozások (például a tulajdonságok száma és a beágyazás mélysége), kivéve a partíciós kulcs és az azonosító értékének korlátozásait, valamint a 2 MB-os teljes méret korlátozását. Előfordulhat, hogy a RU-felhasználás csökkentése érdekében az indexelési házirendet nagy vagy összetett elem-struktúrákkal rendelkező tárolók számára kell konfigurálnia. Tekintse meg a valós példák [modellezési elemeit Cosmos DBban](how-to-model-partition-example.md) , valamint a nagyméretű elemek kezelésére szolgáló mintákat.

## <a name="per-request-limits"></a>Kérelmekre vonatkozó korlátozások

Azure Cosmos DB támogatja a [szifilisz-és lekérdezési műveleteket](/rest/api/cosmos-db/) olyan erőforrásokon, mint például a tárolók, elemek és adatbázisok. Emellett támogatja a [tranzakciós batch-kérelmeket](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) több olyan elem esetében is, amelyek egy tárolóban ugyanazzal a partíciós kulccsal rendelkeznek.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Egy művelet maximális végrehajtási ideje (például egy tárolt eljárás végrehajtása vagy egy lekérdezési lap beolvasása)| 5 MP |
| Kérelmek maximális mérete (például tárolt eljárás, szifilisz)| 2 MB |
| Válasz maximális mérete (például többoldalas lekérdezés) | 4 MB |
| Műveletek maximális száma tranzakciós kötegben | 100 |

Ha egy művelet, például a lekérdezés eléri a végrehajtás időtúllépését vagy a válasz méretkorlát értéket, a rendszer visszaadja az eredmények egy oldalát, valamint egy folytatási tokent az ügyfélnek a végrehajtás folytatásához. Az időtartamra vonatkozóan nincs gyakorlati korlát az egyes oldalakra vagy folytatásokra vonatkozó egyetlen lekérdezés futtatásához.

Cosmos DB a HMAC használja az engedélyezéshez. A részletes hozzáférés-vezérléshez használhatja az elsődleges kulcsot vagy az [erőforrás-jogkivonatokat](secure-access-to-data.md) , például a tárolókat, a partíciós kulcsokat vagy az elemeket. Az alábbi táblázat a Cosmos DB engedélyezési jogkivonatának korlátait sorolja fel.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Elsődleges jogkivonat maximális lejárati ideje | 15 perc  |
| Erőforrás-jogkivonat minimális lejárati ideje | 10 perc  |
| Erőforrás-jogkivonat maximális lejárati ideje | Alapértelmezés szerint 24 óra. Megnövelheti [egy Azure-támogatási jegy bejelentésével](create-support-request-quota-increase.md)|
| Token-engedélyezés maximális órajele| 15 perc |

Cosmos DB támogatja az eseményindítók végrehajtását az írás során. A szolgáltatás legfeljebb egy trigger előtti és egy trigger utáni műveletet támogat.

## <a name="metadata-request-limits"></a>Metaadat-kérelmek korlátai

Azure Cosmos DB az egyes fiókok rendszermetaadatainak karbantartását. Ez a metaadatok lehetővé teszik gyűjtemények, adatbázisok, egyéb Azure Cosmos DB erőforrások és a hozzájuk tartozó konfigurációk díjmentes számbavételét.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
|Gyűjtemény maximális létrehozási sebessége percenként|    100|
|Adatbázis maximális létrehozási sebessége percenként|    100|
|Maximális kiosztott átviteli sebesség percenkénti frissítése|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Az autoscale kiépített átviteli sebességre vonatkozó korlátok

Az adatátviteli és a tárolási korlátokkal kapcsolatos részletesebb magyarázatért tekintse meg az [autoskálázási](provision-throughput-autoscale.md#autoscale-limits) cikket és a [gyakori kérdéseket](autoscale-faq.md#lowering-the-max-rus) .

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális RU/s a (z) rendszeren át lehet méretezni |  `Tmax`, a felhasználó által beállított autoscale Max RU/s|
| A (z) rendszeren minimálisan méretezhető RU/s | `0.1 * Tmax`|
| Aktuális RU/s a rendszer méretezése  |  `0.1*Tmax <= T <= Tmax`, használat alapján|
| Minimális számlázható RU/s óránként| `0.1 * Tmax` <br></br>A számlázás óránként történik, ahol a legmagasabb RU/s számlázása a rendszer által az órában, vagy akár `0.1*Tmax` magasabb is. |
| A tárolóhoz tartozó minimum RU/s-k minimális méretezése  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` a legközelebbi 1000 RU/s értékre kerekítve |
| Az adatbázis minimálisan megengedett maximális száma (RU/s)  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, a legközelebbi 1000 RU/s értékre kerekítve. <br></br>Vegye figyelembe, hogy ha az adatbázisa 25-nél több tárolóval rendelkezik, a (z) rendszeren további tárolóként 1000 RU/s értékkel növekszik a minimálisan megengedett maximális érték (RU/s). Ha például 30 tárolóval rendelkezik, a legfeljebb 9000 RU/s értékkel állítható be a legalacsonyabb méretezési sebesség (900-9000 RU/s).

## <a name="sql-query-limits"></a>SQL-lekérdezés korlátai

A Cosmos DB az [SQL](./sql-query-getting-started.md)használatával támogatja az elemek lekérdezését. Az alábbi táblázat a lekérdezési utasítások korlátozásait mutatja be, például a záradékok vagy a lekérdezés hosszának a számát.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| SQL-lekérdezés maximális hossza| 256 KB |
| Illesztések maximális száma lekérdezés szerint| 5 <sup>*</sup>|
| UDF maximális száma| 10 <sup>*</sup>|
| Maximális pont/sokszög| 4096 |
| Tárolók maximális száma| 500 |
| A kizárt elérési utak maximális száma tárolóban| 500 |
| Maximális tulajdonságok egy összetett indexben| 8 |

<sup>*</sup> Az SQL-lekérdezési korlátok bármelyikét megnövelheti egy [Azure-support Request](create-support-request-quota-increase.md)létrehozásával.

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifikus korlátok

A Cosmos DB a MongoDB-on írt alkalmazások esetében támogatja a MongoDB átviteli protokollt. A támogatott parancsokat és protokoll-verziókat a [támogatott MongoDB-funkciók és-szintaxisban](mongodb-feature-support.md)találja.

A következő táblázat felsorolja a MongoDB funkcióinak támogatására vonatkozó korlátozásokat. Az SQL (Core) API számára említett egyéb szolgáltatási korlátok a MongoDB API-ra is érvényesek.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Maximális MongoDB-lekérdezési memória mérete (ez a korlátozás csak a 3,2-es verziójú kiszolgáló esetében érvényes) | 40 MB |
|MongoDB műveletek maximális végrehajtási ideje (3,2-es kiszolgáló verziója esetén)| 15 másodperc|
|MongoDB műveletek maximális végrehajtási ideje (3,6-es kiszolgáló verziója esetén)| 60 másodperc|
| Tétlen kapcsolat időtúllépése a kiszolgálóoldali kapcsolat bezárásakor * | 30 perc |

\* Azt javasoljuk, hogy az ügyfélalkalmazások a tétlen kapcsolat időkorlátját 2-3 percre állítsa be, mert az [Azure terheléselosztó alapértelmezett időtúllépése 4 perc](../load-balancer/load-balancer-tcp-idle-timeout.md).  Ez az időkorlát biztosítja, hogy az üresjárati kapcsolatok ne legyenek lezárva egy köztes terheléselosztó között az ügyfélszámítógép és a Azure Cosmos DB között.

## <a name="try-cosmos-db-free-limits"></a>Próbálja ki Cosmos DB szabad korlátokat

A következő táblázat felsorolja az ingyenes próbaverzióra vonatkozó [kipróbálási Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) korlátozásait.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| A próbaverzió időtartama | 30 nap (a lejáratot követően új próbaverzió igényelhető) <br> A lejárat után a tárolt adatok törlődnek. |
| Tárolók maximális száma/előfizetés (SQL, Gremlin, Table API) | 1 |
| Tárolók maximális száma (MongoDB API) | 3 |
| Maximális átviteli sebesség/tároló | 5000 |
| Maximális átviteli sebesség egy megosztott átviteli sebességű adatbázisban | 20000 |
| Összes tárterület maximális száma | 10 GB |

Próbálja ki, Cosmos DB támogatja a globális terjesztést csak az USA középső, Észak-és Délkelet-ázsiai régiójában. Nem hozhatók létre Azure-támogatási jegyek az Azure Cosmos DB-fiókok kipróbálásához. Azonban támogatást biztosítanak a meglévő támogatási csomagokkal rendelkező előfizetőknek.

## <a name="azure-cosmos-db-free-tier-account-limits"></a>Az ingyenes szintű fiókokra vonatkozó korlátozások Azure Cosmos DB

A következő táblázat felsorolja az [ingyenes szintű fiókok Azure Cosmos db](optimize-dev-test.md#azure-cosmos-db-free-tier) korlátozásait.

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Az Azure-előfizetések által felszámított ingyenes szintű fiókok száma | 1 |
| Az ingyenes rétegbeli kedvezmény időtartama | A fiók élettartama. A fiók létrehozása során kötelező bejelentkezni. |
| Maximális RU/mp ingyenesen | 400 RU/s |
| Maximális tárterület ingyenesen | 5 GB |
| Megosztott átviteli sebességű adatbázisok maximális száma | 5 |
| Tárolók maximális száma egy megosztott átviteli sebességű adatbázisban | 25 <br>Az ingyenes szintű fiókok esetében a közös átviteli sebességű adatbázishoz legfeljebb 25 tárolóval rendelkező minimális RU/s 400 RU/s. |

A fentiek mellett a [fiókra vonatkozó korlátok](#per-account-limits) az ingyenes szintű fiókok esetében is érvényesek.

> [!NOTE]
> A Azure Cosmos DB ingyenes szintje eltér az ingyenes Azure-fióktól. Az ingyenes Azure-fiók korlátozott ideig ingyenesen kínál Azure-krediteket és-erőforrásokat. Ha az ingyenes fiók részeként Azure Cosmos DB használ, a kiosztott átviteli sebesség 12 hónapig 25 GB tárhelyet és 400 RU/s-t kap.

## <a name="next-steps"></a>Következő lépések

További információ a Cosmos DB alapvető fogalmak [globális eloszlásáról](distribute-data-globally.md) , [particionálásáról](partitioning-overview.md) és kiosztott [átviteli sebességéről](request-units.md).

Az alábbi rövid útmutatókkal könnyedén elkezdheti az Azure Cosmos DB használatát:

* [Bevezetés az Azure Cosmos DB SQL API használatába](create-sql-api-dotnet.md)
* [Ismerkedés a Azure Cosmos DB API-MongoDB](create-mongodb-nodejs.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](create-cassandra-dotnet.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](create-graph-dotnet.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/)
