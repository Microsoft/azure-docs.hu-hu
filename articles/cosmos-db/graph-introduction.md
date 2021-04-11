---
title: A Azure Cosmos DB Gremlin API bemutatása
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy méretű gráfok tárolására, lekérdezésére és bejárására az Apache TinkerPop gráflekérdezési nyelve, a Gremlin használatával.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 03/22/2021
ms.author: chrande
ms.openlocfilehash: ffe11fafa6ba217572f9d12d40324a5201b62630
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801689"
---
# <a name="introduction-to-gremlin-api-in-azure-cosmos-db"></a>A Gremlin API bemutatása Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

[Azure Cosmos db](introduction.md)   a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása a kritikus fontosságú alkalmazások számára. Ez egy többmodelles adatbázis, amely támogatja a dokumentumok, a kulcs-érték, a gráf és az oszlop-család adatmodelleket. Azure Cosmos DB a Gremlin API-val egy teljes körűen felügyelt, bármilyen méretezésre tervezett adatbázis-szolgáltatáson keresztül biztosít gráf adatbázis-szolgáltatást.  

:::image type="content" source="./media/graph-introduction/cosmosdb-graph-architecture.png" alt-text="Az Azure Cosmos DB gráfarchitektúrája" border="false":::

Ez a cikk áttekintést nyújt a Azure Cosmos DB Gremlin API-ról, és ismerteti, hogyan használhatók a nagy méretű gráfok több milliárd csúcsot és éleket tartalmazó diagramjainak tárolására. A gráfokat ezredmásodperces késéssel kérdezheti le, és könnyedén fejlesztheti a gráf szerkezetét. Azure Cosmos DB Gremlin API-ját az [Apache TinkerPop](https://tinkerpop.apache.org), egy gráf-számítástechnikai keretrendszer alapján építették. A Azure Cosmos DB Gremlin API a Gremlin lekérdezési nyelvet használja.

Azure Cosmos DB Gremlin API-ját nagy mértékben skálázható, felügyelt infrastruktúrával ötvözi a Graph adatbázis-algoritmusok előnyeit, hogy egyedi, rugalmas megoldást nyújtson a rugalmasság és a kapcsolatok hiánya miatti leggyakoribb adatproblémákra.

> [!NOTE]
> Azure Cosmos DB Graph Engine szorosan követi az Apache TinkerPop specifikációját. Van azonban néhány eltérés a Azure Cosmos DB vonatkozó implementációs részletekben. Az Apache TinkerPop által támogatott néhány funkció Azure Cosmos DBban nem érhető el, így többet tudhat meg a nem támogatott funkciókról: az [Apache TinkerPop való kompatibilitást](gremlin-support.md) ismertető cikk.

## <a name="features-of-azure-cosmos-dbs-gremlin-api"></a>A Azure Cosmos DB Gremlin API funkciói

Az Azure Cosmos DB egy teljes körűen felügyelt gráfadatbázis, amely globális elosztási, rugalmas tárhely- és teljesítményméretezési, automatikus indexelési és lekérdezési képességeket biztosít, valamint beállítható konzisztenciaszinteket és a TinkerPop szabvány támogatását.

> [!NOTE]
> A [kiszolgáló nélküli kapacitás mód](serverless.md) mostantól elérhető a Azure Cosmos db Gremlin API-ban.

A Azure Cosmos DB Gremlin API által kínált differenciált funkciók a következők:

* **Rugalmasan méretezhető átviteli sebesség és tárterület**

  A valós életben a gráfokat egyetlen kiszolgáló kapacitásán felül kell tudni méretezni. Azure Cosmos DB támogatja a horizontálisan méretezhető gráf-adatbázisokat, amelyek gyakorlatilag korlátlan méretűek lehetnek a tárolás és a kiosztott átviteli sebesség szempontjából. Ahogy a Graph-adatbázis mérete növekszik, az adatforgalom a [gráf particionálásával](./graph-partitioning.md)automatikusan el lesz terjesztve.

* **Többrégiós replikáció**

  A Azure Cosmos DB automatikusan replikálhatja a Graph-adatait a világ bármely Azure-régiójába. A globális replikáció leegyszerűsíti az olyan alkalmazások fejlesztését, amelyek globális hozzáférést igényelnek az adatkezeléshez. Az olvasási és írási késleltetés a világ bármely pontján belüli minimalizálása mellett Azure Cosmos DB automatikus regionális feladatátvételi mechanizmust biztosít, amely biztosítja az alkalmazás folytonosságát abban a ritka esetben, ha a szolgáltatás megszakad egy adott régióban.

* **Gyors lekérdezések és bejárásokat a legszélesebb körben elfogadott gráf lekérdezési szabvánnyal**

  Heterogén csúcsokat és éleket tárolhat, és egy ismerős Gremlin-szintaxissal kérdezheti le őket. A Gremlin egy rendkívül fontos, funkcionális lekérdezési nyelv, amely sokoldalú felületet biztosít a közös gráf-algoritmusok megvalósításához.
  
  Azure Cosmos DB lehetővé teszi a gazdag valós idejű lekérdezések és bejárásokat használatát anélkül, hogy meg kellene adni a séma-, a másodlagos indexeket vagy a nézeteket. További információk: [Gráfok lekérdezése a Gremlin használatával](gremlin-support.md).

* **Teljes körűen felügyelt Graph-adatbázis**

  Az Azure Cosmos DB használatával nincs szükség az adatbázis és a gép erőforrásainak kezelésére. A legtöbb meglévő Graph-adatbázis platformja az infrastruktúra korlátaihoz van kötve, és gyakran magas fokú karbantartásra van szükség a működésének biztosítása érdekében. 
  
  Teljes körűen felügyelt szolgáltatásként a Cosmos DB eltávolítja a virtuális gépek felügyeletének, a futásidejű szoftverek frissítésének, a horizontális skálázás vagy a replikálás kezelésének, vagy az adatréteg összetett frissítéseinek kezelésére vonatkozó igényt. Minden gráfról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Ez lehetővé teszi a fejlesztők számára, hogy a Graph-adatbázisok működtetése és kezelése helyett az alkalmazás értékének megadására összpontosítsanak. 

* **Automatikus indexelés**

  Alapértelmezés szerint a Azure Cosmos DB automatikusan indexeli a csomópontokon belüli összes tulajdonságot (más néven csúcspontokat) és a diagramon lévő éleket, és nem vár vagy igényel semmilyen sémát, és nem kell másodlagos indexeket létrehoznia. További információ a [Azure Cosmos db indexeléséről](./index-overview.md).

* **Kompatibilitás az Apache TinkerPoppal**

  A Azure Cosmos DB támogatja a [nyílt forráskódú Apache TinkerPop szabványt](https://tinkerpop.apache.org/). A Tinkerpop standard a Azure Cosmos DB Gremlin API-val könnyen integrálható alkalmazások és kódtárak széles körű ökoszisztémáját tartalmazhatja.

* **Beállítható konzisztenciaszintek**

  A Azure Cosmos DB öt jól meghatározott konzisztencia-szintet biztosít az alkalmazás konzisztenciája és teljesítménye közötti megfelelő kompromisszum eléréséhez. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](consistency-levels.md).

## <a name="scenarios-that-use-gremlin-api"></a>Gremlin API-t használó forgatókönyvek

Íme néhány forgatókönyv, ahol a Azure Cosmos DB gráf támogatása hasznos lehet:

* **Közösségi hálózatok/ügyfél 365**

  Az ügyfeleivel és ügyfeleinek más személyekkel való interakcióival kapcsolatos adatok kombinálásával személyre szabott élményeket dolgozhat ki, előre jelezheti az ügyfelek viselkedését, vagy összekötheti a hasonló érdeklődési körű személyeket. Az Azure Cosmos DB használatával felügyelhetők a közösségi hálózatok, és nyomon követhetők az ügyfelek preferenciái és adatai.

* **Javaslati motorok**

  Ezt a forgatókönyvet gyakran alkalmazzák a kiskereskedelemben. A termékekkel, a felhasználókkal és a felhasználók interakcióival (például vásárlások, böngészés vagy az egyes cikkek értékelése) kapcsolatos információk kombinálásával testre szabott javaslatokat állíthat össze. A Azure Cosmos DB kis késése, rugalmas méretezése és a natív gráf támogatása ideális megoldás ezekben a forgatókönyvekben.

* **Térinformatikai**

  Számos távközlési, logisztikai és utazástervezési alkalmazásban van szükség az egyes helyszínek egy területen belül való megkeresésére, vagy a két helyszín közötti legrövidebb/optimális útvonal megállapítására. Az Azure Cosmos DB természetes választás az ilyen problémákhoz.

* **Eszközök internetes hálózata**

  Ha a hálózatot és az IoT-eszközök közötti kapcsolatot gráfként modellezi, jobban megértheti az eszközök és adategységek állapotát. Azt is megértheti, hogy a hálózat egyes részeinek módosítása milyen potenciális hatással lehet a többi részre.

## <a name="introduction-to-graph-databases"></a>A Graph-adatbázisok bemutatása

Az adatok a való világban természetes módon kapcsolódnak egymáshoz. A hagyományos adatmodellezés az entitások külön történő definiálását és a kapcsolatok futtatását határozza meg futásidőben. Habár ennek a modellnek van előnye, a nagy terhelésű adat a megkötések alatt is kihívást jelenthet.  

A Graph-adatbázisok megközelítése az adattárolási rétegben fennálló kapcsolatok megőrzésére támaszkodik, ami nagyon hatékony gráf-lekérési műveletekhez vezet. A Azure Cosmos DB Gremlin API támogatja a [Property Graph modellt](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Tulajdonság Graph-objektumok

A Property [Graph](http://mathworld.wolfram.com/Graph.html) a [csúcspontokból](http://mathworld.wolfram.com/GraphVertex.html) és [élekből](http://mathworld.wolfram.com/GraphEdge.html)álló struktúra. Mindkét objektum tetszőleges számú kulcs-érték párokat tartalmazhat, mint a tulajdonságok. 

* **Csúcspontok/csomópontok** – a csúcspontok különálló entitásokat jelölnek, például egy személyt, egy helyet vagy egy eseményt.

* **Élek/kapcsolatok** – az élek a csúcspontok közötti kapcsolatokat jelölik. Például egy adott személy ismerhet egy másik személyt, részt vehet egy eseményen, vagy nemrégiben meglátogathatott egy helyszínt.

* **Tulajdonságok** – A tulajdonságok a csúcsokkal és élekkel kapcsolatos információkat jelölik. A csúcspontok és élek tetszőleges számú tulajdonsággal rendelkezhetnek, és a lekérdezésben szereplő objektumok leírására és szűrésére használhatók. Például a tulajdonságok közé tartozik egy név és egy kor, vagy egy Edge, amely egy időbélyeggel és/vagy súlyozással is rendelkezhet.

* **Label (címke** ) – a címke a csúcspont vagy a peremhálózat neve vagy azonosítója. A címkék több csúcspontot vagy szegélyt is csoportosíthatjuk, így a csoport összes csúcspontja/szegélye egy bizonyos címkével rendelkezik. Egy gráfhoz például több "person" típusú csúcspont is tartozhat.

A Graph-adatbázisok gyakran beletartoznak a NoSQL vagy a nem kapcsolódó adatbázis kategóriába, mivel a séma vagy a korlátozott adatmodell nem függ egymástól. A séma hiánya lehetővé teszi a csatlakoztatott struktúrák természetes és hatékony modellezését és tárolását.

### <a name="graph-database-by-example"></a>Graph-adatbázis példa alapján

Használjunk egy mintagráfot annak megismeréséhez, hogyan adhatók meg lekérdezések a Gremlinben. Az alábbi ábrán egy üzleti alkalmazás látható, amely felhasználókkal, érdeklődési körökkel és eszközökkel kapcsolatos adatokat kezel egy gráf formájában.  

:::image type="content" source="./media/gremlin-support/sample-graph.png" alt-text="Személyeket, eszközöket és érdeklődési köröket tartalmazó mintaadatbázis" border="false"::: 

Ez a gráf a következő *csúcs* -típusokkal rendelkezik (ezeket a "label" is nevezik a Gremlin-ben):

* **Emberek**: a gráf három személy, Robin, Thomas és ben
* **Érdeklődési** körök: ebben a példában a labdarúgás játéka
* **Eszközök**: a felhasználók által használt eszközök
* **Operációs rendszerek**: az eszközöket futtató operációs rendszerek
* **Hely**: azok a helyek, amelyekről az eszközök hozzáférnek

Az entitások közötti kapcsolatokat a következő *Edge* -típusok szerint jelöljük:

* **Ismerheti** meg például a következőt: "Thomas know Robin"
* **Fontos**: a gráfban lévő személyek érdekeinek kifejezése, például "ben a labdarúgás érdekli"
* **RunsOS**: a laptop a Windows operációs rendszert futtatja
* A következőt **használja**: egy személy által használt eszköz jelölésére. Robin például egy Motorola-telefont használ, amelynek sorozatszáma 77.
* **Helye: annak** a helynek a jelölése, amelyről az eszközök hozzáférnek

A Gremlin-konzol egy interaktív terminál, amelyet az Apache TinkerPop kínál, és ez a terminál a Graph-adatkezeléshez használatos. További információért tekintse meg a rövid útmutató dokumentációját a [Gremlin-konzol használatáról](create-graph-gremlin-console.md). Ezek a műveletek tetszőleges platformon is (Java, Node.js, Python vagy .NET) végrehajthatók a Gremlin-illesztők használatával. Az alábbi példák bemutatják, hogyan futtathat lekérdezéseket a gráf adatain a Gremlin-konzol használatával.

Először is lássuk a CRUD-feladatokat. A következő Gremlin-utasítás beszúrja a „Thomas” csúcspontot a gráfba:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

A következő Gremlin-utasítás beszúr egy „knows” (ismeri) élt Thomas és Robin közé.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Az alábbi lekérdezés visszaadja a „személy” csúcspontokat, az utóneveik fordított ABC-sorrendjében:

```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

A gráfok különösen akkor hasznosak, ha olyan kérdéseket kell megválaszolnunk, mint például: „Milyen operációs rendszereket használnak Thomas barátai?”. Ezt a Gremlin bejárási lehetőséggel lefuttatva lekérheti ezt az információt a gráfból:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Következő lépések

A gráfok Azure Cosmos DB általi támogatásával kapcsolatos további információkért lásd:

* Első lépésként kezdje [az Azure Cosmos DB-gráfokkal kapcsolatos oktatóanyaggal](create-graph-dotnet.md).
* Ismerje meg [az Azure Cosmos DB-ben kezelt gráfok Gremlinnel való lekérdésének](gremlin-support.md) módját.
