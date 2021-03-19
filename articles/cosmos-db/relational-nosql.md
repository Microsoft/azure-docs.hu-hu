---
title: Azure Cosmos DB NoSQL és a kapcsolati adatbázisok közötti különbségek ismertetése
description: Ez a cikk a NoSQL és a kapcsolati adatbázisok közötti különbségeket sorolja fel.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: d986106337eb1ede2f6d61303d8a4c487bbed276
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93088471"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>A NoSQL és a kapcsolati adatbázisok közötti különbségek megértése
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk a NoSQL-adatbázisok a kapcsolati adatbázisokkal kapcsolatos főbb előnyeit sorolja fel. A NoSQL használatának néhány kihívását is tárgyaljuk. Ha részletesen meg kívánja nézni a különböző adattárakat, tekintse meg a [megfelelő adattár kiválasztásával](/azure/architecture/guide/technology-choices/data-store-overview)foglalkozó cikket.

## <a name="high-throughput"></a>Nagy átviteli sebesség

A viszonyítási adatbázisrendszer fenntartásának egyik legnyilvánvalóbb kihívása, hogy a legtöbb összekapcsolási motor zárolásokat és zárakat alkalmaz a szigorú [sav-szemantika](https://en.wikipedia.org/wiki/ACID)kikényszerített állapotának biztosítására. Ennek a megközelítésnek az az előnye, hogy az adatbázison belül konzisztens adatállapotot biztosítanak. A párhuzamosságok, a késések és a rendelkezésre állás tekintetében azonban jelentős kompromisszumok állnak fenn. Az alapvető építészeti korlátozások miatt a nagy tranzakciós kötetek esetében szükség lehet az adatszegmensek manuális szétosztására. A manuális horizontális bevezetése időigényes és fájdalmas gyakorlat lehet.

Ezekben az esetekben az [elosztott adatbázisok](https://en.wikipedia.org/wiki/Distributed_database) méretezhető megoldást nyújthatnak. A karbantartás azonban továbbra is költséges és időigényes feladat lehet. Előfordulhat, hogy a rendszergazdáknak további munkát kell végezniük annak biztosítása érdekében, hogy a rendszer elosztott jellege transzparens legyen. Előfordulhat, hogy az adatbázis "leválasztott" természetét is fel kell venni.

[Azure Cosmos db](./introduction.md) leegyszerűsíti ezeket a kihívásokat azáltal, hogy világszerte az összes Azure-régióban üzembe helyezi őket. A particionálási tartományok képesek dinamikusan osztani az adatbázist az alkalmazással összhangban, és egyidejűleg a magas rendelkezésre állás fenntartásával. A részletes, több-és szigorúan szabályozott, Felhőbeli natív erőforrás-szabályozás lehetővé teszi az [elképesztő késési garanciákat](./consistency-levels.md#consistency-levels-and-latency) és kiszámítható teljesítményt. A particionálás teljes mértékben felügyelt, ezért a rendszergazdáknak nem kell programkódot írniuk vagy partíciókat kezelnie.

Ha a tranzakciós kötetek extrém szintekhez kapcsolódnak, például több ezer tranzakció másodpercenként, érdemes fontolóra venni egy elosztott NoSQL-adatbázist. Vegye figyelembe Azure Cosmos DB a maximális hatékonyságot, a könnyű karbantartást és a korlátozott teljes tulajdonlási költségeket.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Backend" border="false":::

## <a name="hierarchical-data"></a>Hierarchikus adatok

Jelentős számú felhasználási eset van, ahol az adatbázisban lévő tranzakciók sok szülő-gyermek kapcsolatot tartalmazhatnak. Ezek a kapcsolatok jelentősen növekednek az idő múlásával, és nehezen kezelhetők. A [hierarchikus adatbázisok](https://en.wikipedia.org/wiki/Hierarchical_database_model) formája az 1980-as időszakban jelent meg, de a tárolás hatékonysága miatt nem volt népszerű. A [Ted Codd kapcsolati modellje](https://en.wikipedia.org/wiki/Relational_model) is elvesztette a vontatást, mivel a gyakorlatilag az összes mainstream adatbázis-kezelő rendszer által használt de facto szabványt használta.

Napjainkban azonban a dokumentum-stílusú adatbázisok népszerűsége jelentős mértékben megnőtt. Előfordulhat, hogy ezek az adatbázisok a hierarchikus adatbázis paradigmájának újraelemzését jelentik, ami mostantól nem jelent akadályt az adatlemezen tárolt adattárolási költségeket illetően. Ennek eredményeképpen a relációs adatbázisban számos összetett szülő-gyermek entitás kapcsolata mostantól a modern dokumentum-orientált megközelítésekhez képest egy anti-mintázatnak tekinthető.

Az [objektum-orientált kialakítás](https://en.wikipedia.org/wiki/Object-oriented_design)megjelenése, valamint az olyan [impedancia-eltérések](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) , amelyek a viszonyítási modellekkel való összevonásakor felmerülnek, a kapcsolódó adatbázisokban bizonyos felhasználási esetekben is kiemelnek egy anti-mintát. Rejtett, de gyakran jelentős karbantartási költségek is származhatnak. Bár az [ORM-megközelítések](https://en.wikipedia.org/wiki/Object-relational_mapping) részben csökkentik ezt a megoldást, a dokumentum-orientált adatbázisok mégis sokkal jobban összefonódnak az objektumorientált megközelítésekkel. Ezzel a megközelítéssel a fejlesztők nem kénytelenek elkötelezettek lenni az ORM-illesztőprogramok vagy az adott nyelvre szabott [OO adatbázis-hajtóművek](https://en.wikipedia.org/wiki/Object_database)számára. Ha az adatai sok szülő-gyermek kapcsolatot és mély hierarchiát tartalmaznak, érdemes fontolóra venni egy NoSQL-dokumentum-adatbázis használatát, például a [Azure Cosmos db SQL API](./introduction.md)-t.

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Összetett hálózatok és kapcsolatok

Ironikus módon a relációs adatbázisok nem optimális megoldást jelentenek a mély és összetett kapcsolatok modellezésére. Ennek az az oka, hogy az entitások közötti kapcsolatok valójában nem léteznek relációs adatbázisban. A kérelmeket futásidőben kell kiszámítani, és az összetett kapcsolatokhoz Descartes-as illesztést igénylő kapcsolatok szükségesek, hogy lehetővé váljon a leképezés a lekérdezésekkel. Ennek eredményeképpen a műveletek exponenciálisan növekednek a számítások tekintetében, ahogy a kapcsolatok növekedése növekszik. Bizonyos esetekben előfordulhat, hogy egy, az ilyen entitások kezeléséhez megkísérelt viszonyítási adatbázis használhatatlan lesz.

A "hálózati" adatbázisok különböző formái a viszonyítási adatbázisok megjelenése idején merültek fel, a hierarchikus adatbázisok esetében azonban a rendszerek népszerűsége is küzdött. A lassú bevezetést a használati esetek hiányában, a tárolási elégtelenségek okozta. Napjainkban a Graph adatbázis-kezelők a hálózati adatbázis paradigmájának újbóli felbukkanását tekintették. Ezeknek a rendszereknek a legfőbb előnye, hogy a kapcsolatok "első osztályú polgárai" tárolódnak az adatbázison belül. Így az átjárási kapcsolatok állandó időben is elvégezhető, és nem kell megnövelni az egyes új csatlakoztatások és a termékek összekapcsolásának időbeli összetettségét.

Ha egy összetett hálózati kapcsolatot tart fenn az adatbázisban, érdemes lehet egy gráf-adatbázist, például az [Azure Cosmos db GREMLIN API](./graph-introduction.md) -t használni az adatkezeléshez.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Az adatbázisábrán számos alkalmazott és részleg látható egymáshoz.":::

Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely API-kivetítést biztosít az összes főbb NoSQL-modellhez; Oszlop – család, dokumentum, gráf és kulcs-érték. A [Gremlin (gráf)](./gremlin-support.md) és az SQL (Core) Document API-rétegek teljes mértékben interoperábilisak. Ez előnyökkel jár a különböző modellek közötti váltáshoz a programozhatóság szintjén. A Graph-áruházak a komplex hálózati bejárásokat, valamint az ugyanazon tárolóban található dokumentum-rekordként modellezhető tranzakciók esetében is lekérdezhető.

## <a name="fluid-schema"></a>Fluid-séma

A viszonyítási adatbázisok egy másik sajátossága, hogy a sémákat a tervezéskor kell meghatározni. Ez a hivatkozási integritás és az adatok megfelelősége tekintetében előnyökkel jár. Ugyanakkor az alkalmazás növekedésével is korlátozó lehet. A sémában történt változásokra reagálva a logikailag különálló, ugyanazon tábla vagy adatbázis-definíciót használó modellek között összetett lehet az idő múlásával. Az ilyen használati esetek gyakran előnyt jelentenek ahhoz, hogy az alkalmazásnak a séma alapján kell kezelnie az alkalmazást. Ehhez az adatbázisnak "agnosztikus séma" értékűnek kell lennie, és lehetővé kell tennie, hogy a rekordok "önleíró" legyenek a bennük található adatok alapján.

Ha olyan adatkezelést végez, amelynek struktúrái folyamatosan változnak nagy arányban, különösen akkor, ha a tranzakciók olyan külső forrásokból származnak, ahol nehéz kikényszeríteni az adatbázison belüli megfelelést, érdemes lehet több sémát is megfontolni a felügyelt NoSQL adatbázis-szolgáltatás, például a Azure Cosmos DB használatával.

## <a name="microservices"></a>Mikroszolgáltatások

A rendszer az elmúlt években jelentős mértékben megnőtt a [szolgáltatásokra](https://en.wikipedia.org/wiki/Microservices) vonatkozó minta. Ez a minta a [szolgáltatás-orientált architektúrában](https://en.wikipedia.org/wiki/Service-oriented_architecture)található. Ezekben a modern Service-architektúrákban a de-facto szabvány a [JSON](https://en.wikipedia.org/wiki/JSON), amely a dokumentum-orientált NoSQL adatbázisok túlnyomó többségének tárolási médiuma is. Ez lehetővé teszi, hogy a NoSQL-dokumentumok sokkal több zökkenőmentesen illeszkedjenek az adatmegőrzéshez és a szinkronizáláshoz (az [esemény-beszerzési minták](https://en.wikipedia.org/wiki/Event-driven_architecture)használatával) az összetett webszolgáltatás-implementációk között. Az ilyen architektúrákban sokkal összetettebbek a hagyományos, összehasonlítható adatbázisok is. Ennek az az oka, hogy a transzformáció nagyobb mennyiségű, és az API-k közötti állapothoz és szinkronizáláshoz szükséges. Azure Cosmos DB különösen számos olyan funkcióval rendelkezik, amelyek még zökkenőmentesen illeszkednek a JSON-alapú NoSQL-adatbázisokhoz, például:

* a tiszta JSON-adattípusok kiválasztása
* az adatbázisba beépített JavaScript motor és [lekérdezési API](./javascript-query-api.md) .
* egy legkorszerűbb [változási hírcsatorna](./change-feed.md) , amelyet az ügyfelek előfizethetnek, hogy értesítést kapjanak a tároló módosításairól.

## <a name="some-challenges-with-nosql-databases"></a>NoSQL-adatbázisokkal kapcsolatos problémák

Bár a NoSQL-adatbázisok megvalósítása során egyértelmű előnyökkel jár, bizonyos kihívásokat is érdemes figyelembe venni. Előfordulhat, hogy ezek a kapcsolati modell használatakor nem jelennek meg ugyanolyan mértékben:

* a sok kapcsolattal rendelkező tranzakciók ugyanarra az entitásra mutatnak.
* a teljes adatkészleten belül erős konzisztenciát igénylő tranzakciók.

Az első kihívás az, hogy a NoSQL-adatbázisok szabálya általában a denormalizálás, amely a korábban megfogalmazott módon hatékonyabb beolvasást eredményez egy elosztott rendszeren. Vannak azonban olyan tervezési kihívások is, amelyek a jelen megközelítéssel együtt játszanak. Vegyük például egy olyan terméket, amely egy kategóriához és több címkéhez kapcsolódik:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Összekapcsolások":::

A NoSQL-dokumentumok adatbázisának ajánlott módszere, ha a kategória neve és a címke neveit közvetlenül egy "termék dokumentumában" szeretné denormalizálni. Ahhoz azonban, hogy a kategóriák, címkék és termékek szinkronban maradjanak, a tervezési beállítások lehetővé teszik a karbantartási bonyolultság hozzáadását, mivel az adatok duplikálva vannak a termék több rekordjához, nem pedig egy "egy a többhöz" kapcsolathoz, és egy JOIN az adatok lekéréséhez. 

A kikapcsolás azt eredményezi, hogy az olvasások hatékonyabbak a denormalizált rekordban, és egyre hatékonyabbá válnak, mivel a koncepcionálisan összekapcsolt entitások száma növekszik. Ugyanakkor ahogy az olvasási hatékonyság növekszik a denormalizáló rekordban lévő összekapcsolt entitások számának növekedésével, úgy is működik, hogy a karbantartás bonyolultsága szinkronban tartja az entitásokat. A kompromisszumok enyhítésének egyik módja a [hibrid adatmodell](./modeling-data.md#hybrid-data-models)létrehozása.

Habár a NoSQL-adatbázisok nagyobb rugalmasságot biztosítanak ezen kompromisszumok kezelésére, a nagyobb rugalmasság további tervezési döntéseket is hozhat. Tekintse meg a cikket, [Hogyan modellezheti és particionálhatja Azure Cosmos db a valós példa használatával](./how-to-model-partition-example.md), amely a [denormalizált felhasználói adatelemek szinkronizálásának](./how-to-model-partition-example.md#denormalizing-usernames) módszerét tartalmazza, ahol a felhasználók nem csak a különböző partíciókban, hanem különböző tárolókban találhatók.

Az erős konzisztencia szempontjából ritkán fordul elő, hogy ez a teljes adathalmazban szükséges. Ha azonban erre szükség van, akkor az elosztott adatbázisok esetében is kihívást jelenthet. Az erős konzisztencia biztosítása érdekében az adatokat az összes replikán és régióban szinkronizálni kell, mielőtt az ügyfelek beolvassák azt. Ez növelheti az olvasások késését.

A Azure Cosmos DB ismét nagyobb rugalmasságot biztosít, mint a különböző, itt releváns kompromisszumok, de a kis léptékű implementációk esetében ez a megközelítés további tervezési szempontokat is felvehet. Ennek a témakörnek a részletes ismertetését a [konzisztencia, a rendelkezésre állás és a teljesítménybeli kompromisszumok](./consistency-levels.md) című cikkben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti Azure Cosmos-fiókját és egyéb fogalmait:

* [Útmutató az Azure Cosmos-fiók kezeléséhez](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszintek](consistency-levels.md)
* [Azure Cosmos-tárolók és-elemek használata](account-databases-containers-items.md)
* [VNET szolgáltatási végpont az Azure Cosmos-fiókhoz](how-to-configure-vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)
* [Útmutató Azure-régiók Azure Cosmos-fiókhoz való hozzáadásához és eltávolításához](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA-kat](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)