---
title: Konzisztenciaszintek az Azure Cosmos DB-ben
description: Azure Cosmos DB öt konzisztencia-szinttel rendelkezik a végleges konzisztencia, a rendelkezésre állás és a késések kompromisszumának kiegyensúlyozásához.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0a203531e026d00b274ac98784076d33b22666d8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800142"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konzisztenciaszintek az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A magas rendelkezésre állás, kis késés vagy mindkettő replikálására támaszkodó elosztott adatbázisok esetében alapvető kompromisszumot kell biztosítani az [PACLC-tétel](https://en.wikipedia.org/wiki/PACELC_theorem)által meghatározott olvasási konzisztencia, rendelkezésre állás, késés és átviteli sebesség között. Az erős konzisztencia-modell linearizability az adatprogramozási szabvány. Azonban a nagyobb távolságok replikálására és elvégzésére irányuló adatok miatt a magasabb írási késések esetében is meredek árat hoz létre. Az erős konzisztencia a korlátozott rendelkezésre állástól (a hibák során) is romolhat, mivel az adatok nem replikálhatók és nem hajthatók végre minden régióban. A végleges konzisztencia magasabb rendelkezésre állást és jobb teljesítményt nyújt, de az alkalmazások még nehezebbé válik, mert az egyes régiókban az adat nem feltétlenül teljesen konzisztens.

A piacon elérhető legtöbb kereskedelmi forgalomban kapható elosztott NoSQL-adatbázis csak erős és végleges konzisztenciát biztosít. Azure Cosmos DB öt jól definiált szintet kínál. A legerősebbtól a leggyengébbig a szintek a következők:

- *Erős*
- *Kötött elavultság*
- *Munkamenet*
- *Konzisztens előtag*
- *Végleges*

Minden szint rendelkezésre állási és teljesítménybeli kompromisszumokat biztosít. A következő képen a különböző konzisztencia-szintek láthatók spektrumként.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konzisztencia spektrumként" border="false" :::

A konzisztencia szintje régió-agnosztikus, és minden művelet számára garantált, függetlenül attól, hogy az olvasások és írások kiszolgálása melyik régióból történik, az Azure Cosmos-fiókhoz társított régiók száma, illetve hogy a fiókja egyetlen vagy több írási régióval van-e konfigurálva.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

A Azure Cosmos DB natív támogatást biztosít a huzal protokollal kompatibilis API-k számára a népszerű adatbázisokhoz. Ilyenek például a MongoDB, az Apache Cassandra, a Gremlin és az Azure Table Storage. A Gremlin API és a Table API használatakor a rendszer az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintet használja. A Cassandra API vagy a MongoDB API és a Azure Cosmos DB konzisztenciáji szintjének összekapcsolásával kapcsolatos részletekért lásd: [Cassandra API konzisztencia](cassandra-consistency.md) -leképezés és [API a MongoDB konzisztencia-leképezéséhez](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Az olvasási konzisztencia hatóköre

Az olvasási konzisztencia a logikai partíción belüli egyetlen olvasási műveletre vonatkozik. Az olvasási műveletet távoli ügyfél vagy tárolt eljárás is kiállíthatja.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Bármikor megadhatja az alapértelmezett konzisztencia-szintet az Azure Cosmos-fiókban. A fiókban konfigurált alapértelmezett konzisztencia-szint az adott fiókhoz tartozó összes Azure Cosmos-adatbázisra és-tárolóra vonatkozik. A tárolók vagy adatbázisok által kiadott összes olvasás és lekérdezés alapértelmezés szerint a megadott konzisztencia-szintet használja. További információ: [az alapértelmezett konzisztencia-szint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level). Felülbírálhatja egy adott kérelem alapértelmezett konzisztencia-szintjét is, ha további információt szeretne megtudni az [alapértelmezett konzisztencia-szint felülbírálásáról](how-to-manage-consistency.md?#override-the-default-consistency-level) szóló cikkben.

> [!IMPORTANT]
> Az alapértelmezett konzisztencia-szint módosítása után újra létre kell hoznia egy SDK-példányt. Ezt az alkalmazás újraindításával teheti meg. Ez biztosítja, hogy az SDK az új alapértelmezett konzisztencia-szintet használja.

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztencia-szintekhez kapcsolódó garanciák

Azure Cosmos DB garantálja, hogy az olvasási kérelmek 100%-ában megfelel a kiválasztott konzisztencia-szint konzisztencia-garanciájának. Az [Azure-Cosmos-tla GitHub-](https://github.com/Azure/azure-cosmos-tla) tárházban megtalálhatók a Azure Cosmos db öt konzisztencia-szintjének pontos definíciói a tla + specifikációs nyelv használatával.

Az öt konzisztencia-szint szemantikai ismertetését a következő szakaszokban találja.

### <a name="strong-consistency"></a>Erős konzisztencia

Az erős konzisztencia garantálja a linearizálhatóságot. A Linearizability a kérelmek egyidejű kiszolgálására hivatkozik. Az olvasások garantáltan egy adott tétel legújabb véglegesített verzióját adják vissza. Az ügyfél nem látja a nem véglegesített vagy részleges írást. A felhasználók mindig garantáltan olvasni a legutóbb véglegesített írást.

  Az alábbi ábrán a hangjegyzetekkel való erős konzisztencia látható. Miután az adatok az "USA nyugati régiója 2" régiójába kerülnek, a többi régióból származó adatok beolvasása után a legfrissebb értéket kapja:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Erős konzisztencia-szint illusztrációja":::

### <a name="bounded-staleness-consistency"></a>Korlátozott frissesség konzisztenciaszint

A kötött elavulás konzisztenciája esetén az olvasások garantáltan tiszteletben tartják a konzisztens előtagú garanciát. Előfordulhat, hogy az olvasások lemaradnak az írások mögött az elemek legfeljebb *"K"* verziójában (azaz "frissítések"), vagy a *"T"* időintervallumban, attól függően, hogy melyik érték van elsőként. Más szóval, ha a kötött elavulás beállítást választja, a "elavulás" két módon konfigurálható:

- Az elemek verzióinak (*K*) száma
- Az írási idő (*T*) az írások mögött is elmaradhat

Egyetlen régióból álló fiók esetén a *K* és a *T* minimális értéke 10 írási művelet vagy 5 másodperc. A többrégiós fiókok esetében a *K* és a *T* minimális értéke 100 000 írási művelet vagy 300 másodperc.

A kötött elavulás az "elévülési időszakon kívüli teljes globális sorrendet kínálja." Ha egy ügyfél olvasási műveleteket hajt végre az írásokat fogadó régióban, a kötött elavulás konzisztenciája által biztosított garanciák megegyeznek az erős konzisztencia által garantált garanciákkal. Mivel az elavultság ablak mindkét időpontra vagy frissítésre közelít, attól függően, hogy melyik a szorosabb, a szolgáltatás az új írásokat fogja szabályozni, hogy lehetővé tegye a replikációt a konzisztencia garantálása érdekében.

Az elavultság ablakon belül a határértékek a következő konzisztencia-garanciákat biztosítják:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = erős
- Különböző régiókban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = konzisztens előtag
- A több írási régióval rendelkező fiókok esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- A különböző régiókba írt ügyfelek konzisztenciája több írási régióval rendelkező fiók esetén = végleges

  A kötött elavulás gyakran olyan globálisan elosztott alkalmazások által van kiválasztva, amelyek alacsony írási késést várnak, de teljes globális rendelési garanciát igényelnek. A kötött elavulás kiválóan használható a csoportos együttműködést és megosztást, a tőzsdei előfizetést, a közzétételt és a várakozási sort is tartalmazó alkalmazások esetében. Az alábbi ábrán látható, hogy a megkötött elavultság konzisztens legyen a zenei megjegyzésekkel. Az adatok az "USA nyugati régiója 2" régiójába való beírása után az "USA 2. keleti régiója" és a "Kelet-Ausztrália" régiók a beállított maximális késési idő vagy a maximális műveletek alapján olvassák el a megírt értéket:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="A kötött elavulás konzisztencia-szintjének illusztrációja":::

### <a name="session-consistency"></a>Munkamenet-konzisztencia

A munkamenet-konzisztencia egyetlen ügyfél-munkameneten belüli olvasási garanciát biztosít a konzisztens előtag, az monoton olvasás, az monoton írás, az olvasás és az írás, valamint az írási és olvasási műveletek elvégzésére. Ez egyetlen "író" munkamenetet feltételez, vagy a munkamenet-tokent több író számára is megosztja.

A munkamenet-végrehajtón kívüli ügyfelek a következő garanciákat fogják látni:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = konzisztens előtag
- Különböző régiókban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = konzisztens előtag
- A több írási régióval rendelkező fiókok esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- Több régióba írt ügyfelek konzisztenciája több írási régióval rendelkező fiók esetén = végleges

  A munkamenet konzisztenciája a legszélesebb körben használt konzisztencia-szint mind az egyetlen régió, mind a globálisan elosztott alkalmazások esetében. Az írási késleltetést, rendelkezésre állást és olvasási sebességet biztosít a végleges konzisztencia szempontjából, de biztosítja a konzisztencia-garanciát is, amely megfelel a felhasználó környezetében való működésre írt alkalmazások igényeinek. A következő ábra a munkamenetek konzisztenciáját mutatja be hangjegyzetekkel. Az "USA nyugati régiója 2 írója" és az "USA nyugati régiója 2 olvasója" ugyanazt a munkamenetet használja (A-munkamenet), hogy egyszerre ugyanazokat az adatfájlokat olvassák. Míg a "Kelet-Ausztrália" régió "B munkamenetet" használ, a rendszer később, de az írásokkal megegyező sorrendben fogadja az adatot.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="A munkamenet konzisztenciáji szintjének ábrája":::

### <a name="consistent-prefix-consistency"></a>Konzisztens előtag konzisztenciaszint

Konzisztens előtag esetén a visszaadott frissítések a frissítések néhány előtagját tartalmazzák, és nincsenek rések. Konzisztens előtag-konzisztenciai szint biztosítja, hogy az olvasások soha ne lássák a megrendelésen kívüli írásokat.

Ha az írások sorrendben lettek elvégezve, akkor az ügyfél a következőt látja:, `A, B, C` `A` `A,B` vagy `A,B,C` `A,C` `B,A,C` Az konzisztens előtag a végleges konzisztencia miatti írási késleltetést, rendelkezésre állást és olvasási átviteli sebességet biztosít, ugyanakkor biztosítja az olyan forgatókönyvek igényeit is, amelyek a sorrend szempontjából fontosak.

Az alábbi konzisztens előtagok konzisztencia-garanciái:

- Azonos régióban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = konzisztens előtag
- Különböző régiókban lévő ügyfelek konzisztenciája egyetlen írási régióval rendelkező fiók esetén = konzisztens előtag
- A több írási régióval rendelkező fiókok esetében egyetlen régióba írt ügyfelek konzisztenciája = konzisztens előtag
- Több régióba írt ügyfelek konzisztenciája több írási régióval rendelkező fiók esetén = végleges

A következő ábra a konzisztencia-előtagot ábrázolja a zenei megjegyzésekkel. Az összes régióban az olvasások soha nem láthatók az írások sorrendjében:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Konzisztens előtag illusztrációja":::

### <a name="eventual-consistency"></a>Végleges konzisztencia

A végleges konzisztencia esetében nem áll rendelkezésre rendezési garancia a beolvasáshoz. Ha nincsenek további írások, a replikák végül konvergálnak.  
A végleges konzisztencia a konzisztencia leggyengébb formája, mivel előfordulhat, hogy az ügyfél elolvashatja azokat az értékeket, amelyek régebbiek, mint a korábban olvasottak. A végleges konzisztencia ideális, ha az alkalmazás nem igényel rendelési garanciát. Ilyenek például a retweets, a Like vagy a nem többszálú megjegyzések száma. Az alábbi ábrán a zenei megjegyzésekkel való végleges konzisztencia látható.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="végleges konzisztencia viIllustration":::

## <a name="consistency-guarantees-in-practice"></a>Konzisztencia-garanciák a gyakorlatban

A gyakorlatban gyakran nagyobb konzisztencia-garanciákat érhet el. Az olvasási művelet konzisztencia-garanciái megfelelnek a kért adatbázis-állapot frissességének és rendezésének. Az olvasás-konzisztencia az írási/frissítési műveletek sorrendjéhez és propagálásához van kötve.  

Ha az adatbázis nem rendelkezik írási művelettel, a rendszer a **végleges**, a **munkamenetet** vagy az **állandó előtagot** tartalmazó olvasási műveletet valószínűleg ugyanazt az eredményt fogja eredményezni, mint egy erős konzisztencia-szintű olvasási művelet.

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a *Probabilistically határos* elavulás (PBS) metrikájának megtekintésével. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="consistency-levels-and-latency"></a>Konzisztencia-szintek és késés

Az összes konzisztencia-érték olvasási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Az átlagos olvasási késés (50%-os százalék) általában 4 ezredmásodperc vagy kevesebb.

Az összes konzisztencia-érték írási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Az átlagos írási késleltetés 50%-os százalékban általában 5 ezredmásodperc vagy kevesebb. A számos régióra kiterjedő Azure Cosmos-fiókok, amelyek erős konzisztencia-konfigurációval rendelkeznek, kivételt jelentenek ez a garancia.

### <a name="write-latency-and-strong-consistency"></a>Írási késés és erős konzisztencia

Az olyan Azure Cosmos-fiókok esetében, amelyeknek több régióval való erős konzisztencia van beállítva, az írási késleltetés egyenlő a két legtávolabbi régió bármelyike között, és 10 ezredmásodpercet a esetek 99% percentilis esetében (RTT). A régiók közötti nagy hálózati RTT a Cosmos DB kérelmeknél nagyobb késéssel jár, mivel az erős konzisztencia csak azt követően végezhető el, hogy a fiókon belül minden régióban elkötelezett a művelet.

A pontos RTT késés a fénysebességi távolság és az Azure hálózati topológia függvénye. Az Azure Networking nem biztosít késési SLA-kat a két Azure-régió közötti RTT, azonban az [Azure-hálózatokra vonatkozó időintervallum-statisztikai adatokat](../networking/azure-network-latency.md)tesz közzé. Az Azure Cosmos-fiók esetében a replikációs késések megjelennek a Azure Portal. Az Azure Cosmos-fiókjához társított különböző régiók közötti replikációs késések figyeléséhez használhatja a Azure Portalt (ugrás a metrikák panelre, válassza a konzisztencia lapot).

> [!IMPORTANT]
> A magas írási késés miatt a több mint 5000 mérföldet (8000 kilométert) felölelő tartományokkal rendelkező fiókok esetében az erős konzisztencia blokkolása alapértelmezés szerint le van tiltva. A funkció engedélyezéséhez forduljon az ügyfélszolgálathoz.

## <a name="consistency-levels-and-throughput"></a>Konzisztencia-szintek és átviteli sebesség

- Az erős és a határos elavulás esetében az olvasások egy négy replikakészlet (kisebbségi kvórum) két replikáján hajthatók végre a konzisztencia-garanciák biztosításához. Munkamenet, konzisztens előtag és végleges művelet egyetlen replika olvasásakor. Ennek az az oka, hogy az adott számú kérelem esetében az erős és a határos elavulás esetében az olvasási teljesítmény az egyéb konzisztencia-szintek felét jelenti.

- Egy adott írási művelet (például INSERT, replace, upsert és DELETE) esetében az írási sebesség a kérelmek egységeinek esetében azonos minden konzisztencia-szinten.

|**Konzisztenciaszint**|**Kvórum olvasási**|**Kvórum írása**|
|--|--|--|
|**Erős**|Helyi kisebbség|Globális többség|
|**Korlátozott frissesség**|Helyi kisebbség|Helyi többség|
|**Munkamenet**|Egyetlen replika (munkamenet-jogkivonat használatával)|Helyi többség|
|**Konzisztens előtag**|Egyetlen replika|Helyi többség|
|**Végleges**|Egyetlen replika|Helyi többség|

> [!NOTE]
> A helyi kisebbségek olvasásának RU/s-díja kétszer szerepel a gyengébb konzisztencia szintjein, mert az olvasások két replikából származnak, és az erős és a határos elavulás érdekében konzisztens garanciákat biztosítanak.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>A konzisztencia szintjei és az adattartósság

Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a **helyreállítási időre vonatkozó célkitűzés** (**RTO**). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. A frissítések elvesztésének időpontját a **helyreállítási pont célkitűzésének** (**RPO**) nevezzük.

Az alábbi táblázat a konzisztencia-modell és az adattartósság közötti kapcsolatot határozza meg egy régióra kiterjedő leállás jelenlétében. Fontos megjegyezni, hogy egy elosztott rendszeren, még erős konzisztencia esetén is lehetséges, hogy a [Cap-tétel](https://en.wikipedia.org/wiki/CAP_theorem)miatt nem lehet RPO és nulla RTO rendelkező elosztott adatbázis.

|**Régió (k)**|**Replikálási mód**|**Konzisztencia szintje**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Egy vagy több írási régió|Bármely konzisztencia-szint|< 240 perc|<1 hét|
|>1|Egyszeri írási régió|Munkamenet, konzisztens előtag, végleges|< 15 perc|< 15 perc|
|>1|Egyszeri írási régió|Korlátozott frissesség|*K*  &  *T*|< 15 perc|
|>1|Egyszeri írási régió|Erős|0|< 15 perc|
|>1|Több írási régió|Munkamenet, konzisztens előtag, végleges|< 15 perc|0|
|>1|Több írási régió|Korlátozott frissesség|*K*  &  *T*|0|

*K* = egy elem *"k"* verziója (azaz frissítései) száma.

*T* = a legutóbbi frissítés óta eltelt idő *"t"* .

Egyetlen régióból álló fiók esetén a *K* és a *T* minimális értéke 10 írási művelet vagy 5 másodperc. A többrégiós fiókok esetében a *K* és a *T* minimális értéke 100 000 írási művelet vagy 300 másodperc. Ez határozza meg az adatmennyiség minimális RPO a kötött elavulás használata esetén.

## <a name="strong-consistency-and-multiple-write-regions"></a>Erős konzisztencia és több írási régió

A több írási régióval konfigurált Cosmos-fiókok nem konfigurálhatók erős konzisztencia elérésére, mert az elosztott rendszer nem tud nulla értékű RPO és nulla RTO megadni. Emellett a több írási régióval való erős konzisztencia használata nem jár írási késéssel, mert bármely régióba való írást replikálni kell, és el kell véglegesíteni a fiókban lévő összes konfigurált régióban. Ez a művelet ugyanazt az írási késést eredményezi, mint egyetlen írási régióbeli fiók.

## <a name="additional-reading"></a>További információ

Ha többet szeretne megtudni a konzisztencia-fogalmakról, olvassa el a következő cikkeket:

- [Magas szintű TLA + specifikációk a Azure Cosmos DB által kínált öt konzisztencia-szinthez](https://github.com/Azure/azure-cosmos-tla)
- [A replikált adatkonzisztencia magyarázata a baseball (videó) által a Doug Terry alapján](https://www.youtube.com/watch?v=gluIh8zd26I)
- [A replikált adatkonzisztencia a baseball (tanulmány) által a Doug Terry alapján magyarázható](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [A gyengén konzisztens replikált adatszolgáltatások munkamenet-garanciái](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában: a CAP csak a történet részét képezi](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Valószínűségi határos elavulás (PBS) a gyakorlati részleges Kvórumokhoz](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – felülvizsgálat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Cosmos DB konzisztenciáji szintjeiről, olvassa el a következő cikkeket:

- [Az alapértelmezett konzisztenciaszint beállítása](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Az alapértelmezett konzisztenciaszint felülírása](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
