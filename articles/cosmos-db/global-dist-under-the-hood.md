---
title: Globális terjesztés Azure Cosmos DBokkal – a motorháztető alatt
description: Ez a cikk a Azure Cosmos DB globális elosztásával kapcsolatos technikai részleteket tartalmaz
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486386"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globális Adatterjesztés a Azure Cosmos DB-ben – a motorháztető alatt
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB az Azure alapszintű szolgáltatása, így az összes világszerte elérhető Azure-régióban üzembe helyezhető, beleértve a nyilvános, szuverén, védelmi minisztériumi (DoD) és kormányzati felhőket is. Egy adatközponton belül üzembe helyezjük és felügyeljük a gépek nagy számú, dedikált helyi tárolóval rendelkező Azure Cosmos DBét. Az adatközpontokon belül Azure Cosmos DB több fürtön is üzembe helyezhetők, amelyek mindegyike a hardver több generációját is futtatja. A fürtön belüli gépek általában az 10-20-es tartalék tartományok között oszlanak meg a magas rendelkezésre állás érdekében a régión belül. Az alábbi képen a Cosmos DB globális terjesztési rendszertopológiája látható:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Rendszertopológia" border="false":::

**A Azure Cosmos db globális eloszlása kulcsrakész:** Bármikor, néhány kattintással vagy programozott módon egyetlen API-hívással, hozzáadhat vagy eltávolíthat a Cosmos-adatbázishoz társított földrajzi régiókat. A Cosmos-adatbázisok a Cosmos-tárolók készletét tartalmazzák. Cosmos DB a tárolók a terjesztés és a méretezhetőség logikai egységként szolgálnak. A létrehozott gyűjtemények, táblák és diagramok (belsőleg) csak a Cosmos-tárolók. A tárolók teljes mértékben sémák és agnosztikusok, és hatókört biztosítanak a lekérdezésekhez. A Cosmos-tárolóban tárolt adatmennyiség automatikusan bekerül a betöltés után. Az Automatikus indexelés lehetővé teszi a felhasználók számára az adatlekérdezést a séma vagy az index kezelésének gondja nélkül, különösen egy globálisan elosztott telepítésben.  

- Egy adott régióban a tárolóban lévő adatokat egy olyan partíciós kulcs használatával osztják el, amelyet Ön biztosít, és a mögöttes fizikai partíciók (*helyi terjesztés*) transzparens módon kezelik.  

- Az egyes fizikai partíciók a földrajzi régiók között is replikálódnak (*globális eloszlás*). 

Ha egy Cosmos DB rugalmasan méretezi az átviteli sebességet egy Cosmos-tárolón, vagy több tárhelyet használ fel, Cosmos DB az összes régióban transzparens módon kezeli a particionálási műveleteket (felosztás, klónozás, törlés). A méretezés, a terjesztés vagy a hibáktól függetlenül a Cosmos DB továbbra is egyetlen rendszerképet biztosít a tárolókban lévő adatokról, amelyek globálisan vannak elosztva tetszőleges számú régióban.  

Ahogy az az alábbi ábrán is látható, a tárolóban lévő adatok két dimenzión oszlanak el – egy régión belül és régiókban, a világ minden részén:  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="fizikai partíciók" border="false":::

A fizikai partíciót replikák egy csoportja *hozza létre, amelyet replika-készletnek* neveznek. Mindegyik gép több száz replikát üzemeltet, amelyek a fenti képen látható különböző fizikai partíciókhoz tartoznak. A fizikai partícióknak megfelelő replikák dinamikusan helyezhetők el és töltődnek be a fürtben lévő gépek és a régión belüli adatközpontok között.  

A replika egyedi Azure Cosmos DB bérlőhöz tartozik. Minden replika Cosmos DB [adatbázis-motorjának](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)egy példányát üzemelteti, amely kezeli az erőforrásokat, valamint a hozzájuk tartozó indexeket. A Cosmos-adatbázismotor egy Atom-Record-Sequence (ARS) alapú típusú rendszeren működik. A motor agnosztikus a séma koncepciójában, és elmossa a rekordok szerkezeti és példányi értékei közötti határt. A Cosmos DB a teljes séma-összetételt úgy éri el, hogy a hatékony módon automatikusan indexel mindent, ami lehetővé teszi a felhasználók számára a globálisan elosztott adatlekérdezést anélkül, hogy a sémával vagy az indexeléssel kellene foglalkoznia.

A Cosmos adatbázismotor olyan összetevőkből áll, mint például a különböző koordinációs primitívek, nyelvi futtatókörnyezetek, a lekérdezési processzor, valamint a tranzakciós tároláshoz és az adatindexeléshez felelős tárolási és indexelési alrendszerek. A tartósság és a magas rendelkezésre állás biztosítása érdekében az adatbázismotor az SSD-meghajtókon megőrzi az adataikat és az indexeket, és replikálja azt az adatbázismotor-példányok között a másodpéldány-készlet (ek) en belül. A nagyobb bérlők az átviteli sebesség és a tárterület nagyobb méretének felelnek meg, és nagyobb vagy több replikával vagy mindkettővel rendelkeznek. A rendszer minden összetevője teljes mértékben aszinkron – egyetlen szál sincs blokkolva, és minden szál rövid életű munkát végez, anélkül, hogy szükségtelen szál-kapcsolókra lenne szükség. A ráta-korlátozó és a ellennyomás a teljes verembe kerül a beléptetési vezérlőről az összes I/O-útvonalra. A Cosmos adatbázismotor a részletes Egyidejűség kihasználása és a nagy adatátviteli sebesség biztosítására szolgál, és a rendszer erőforrásain belül működik.

Cosmos DB globális eloszlása két kulcsfontosságú absztrakcióra támaszkodik – a *replika-* és a *partíciós készletekre*. A replika-készlet egy moduláris Lego-blokk a koordinációhoz, és egy partíciós készlet egy vagy több földrajzilag elosztott fizikai partíció dinamikus átfedése. A globális terjesztés működésének megismeréséhez ismernie kell ezt a két kulcsfontosságú absztrakciót. 

## <a name="replica-sets"></a>Replika – készletek

A fizikai partíciók önfelügyelt és dinamikusan elosztott replikák, amelyek több tartalék tartományba vannak osztva, és az úgynevezett replika-készletnek nevezzük. Ez a készlet együttesen valósítja meg a replikált állapotú számítógép-protokollt, hogy a fizikai partíción belül elérhetővé tegye a nagyfokú rendelkezésre állást, a tartós és a konzisztens eszközöket. A replika-set tag *N* dinamikus – a hibák, a felügyeleti műveletek és a sikertelen replikák újralétrehozásához/helyreállításához szükséges idő alapján ingadozik a *NMin* és a *nnaponkénti maximum* között. A tagsági változások alapján a replikációs protokoll újrakonfigurálja az olvasási és írási kvórum méretét is. Az adott fizikai partícióhoz rendelt átviteli sebesség egységes elosztásához két ötletet alkalmazzunk: 

- Első lépésként az írási kérelmeknek a vezetőn való feldolgozásának díja magasabb, mint a frissítések a követőn való alkalmazásának díja. Ennek megfelelően a vezető a követőinél több rendszererőforrást is tervezett. 

- Másodszor, amennyire lehetséges, egy adott konzisztencia-szint olvasási kvóruma kizárólag a követő replikából áll. Elkerüljük, hogy csak a szükséges olvasási műveletekért lépjen kapcsolatba a vezetővel. A Cosmos DB által támogatott [öt konzisztencia-modellhez](consistency-levels.md) a kvórumon alapuló rendszerek [terhelésének és kapacitásának](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) összekapcsolásával kapcsolatban számos ötletet alkalmazunk.  

## <a name="partition-sets"></a>Partíciók – készletek

A fizikai partíciók egy csoportja, amelyek mindegyike a Cosmos adatbázis-régiókkal van konfigurálva, az összes konfigurált régióban replikált azonos kulcsok kezelésére szolgál. Ezt a magasabb szintű koordinációs primitívet *partíciós készletnek* nevezzük – a fizikai partíciók földrajzilag elosztott dinamikus átfedése, amely egy adott kulcsot kezel. Míg egy adott fizikai partíció (egy replika-készlet) egy fürtön belül van, a partíciók a fürtökön, az adatközpontokban és a földrajzi régiókban is kiterjedhetnek, ahogy az alábbi képen is látható:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Particionálási készletek" border="false":::

Úgy gondolja, hogy a partíciók földrajzilag elszórtan szétszórt "Super Replica-set" típusúak, amely több replika-készletből áll, amelyek ugyanazt a kulcsot tartalmazza. Hasonlóan a másodpéldányhoz, a partíciós csoport tagsága is dinamikus – az implicit fizikai particionálási műveletek alapján ingadozik az új partíciók hozzáadására/eltávolítására egy adott partíción (például amikor egy tárolón kibővíti az átviteli sebességet, hozzáadhat/eltávolíthat egy régiót a Cosmos-adatbázishoz, vagy ha hiba történik). Azáltal, hogy az egyes partíciók (partíciók) a saját replikáján belül kezelik a particionálási tagságot, a tagság teljes mértékben decentralizált és magasan elérhető. Egy lemezpartíció újrakonfigurálása során a rendszer a fizikai partíciók közötti átfedés topológiáját is létrehozta. A topológia dinamikusan van kiválasztva a forrás és a cél fizikai partíciói között a konzisztencia, a földrajzi távolság és a rendelkezésre álló hálózati sávszélesség alapján.  

A szolgáltatás lehetővé teszi, hogy a Cosmos-adatbázisokat egyetlen írási vagy több írási régióval konfigurálja, és a választástól függően a particionálási készletek úgy vannak konfigurálva, hogy pontosan egy vagy minden régióban fogadják az írásokat. A rendszer kétszintű, beágyazott konszenzusú protokollt alkalmaz – az egyik szint egy olyan replika replikáján belül működik, amely az írásokat fogadja el, és a másik egy partíció szintjén működik, hogy teljes körű rendelési garanciát biztosítson a partíción belül minden véglegesített íráshoz. Ez a többrétegű, beágyazott konszenzus kritikus fontosságú a szigorú SLA-értékeknek a magas rendelkezésre állás érdekében történő megvalósításához, valamint a konzisztencia-modellek megvalósításához, amely Cosmos DB kínál ügyfeleinek.  

## <a name="conflict-resolution"></a>Ütközések feloldása

A frissítés propagálásának, az ütközések feloldásának és az oksági viszonyok nyomon követésének kialakítását a [járványos algoritmusok](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) és az [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) rendszer korábbi munkája ihlette. Habár az ötletek kernelei megmaradtak, és a Cosmos DB rendszerkialakításával való kommunikációhoz hasznos hivatkozási keretet biztosítanak, az is jelentős átalakításon estek át, ahogy azokat a Cosmos DB rendszerre alkalmazták. Erre azért volt szükség, mert az előző rendszerek nem az erőforrás-szabályozáshoz, sem a Cosmos DB működéséhez szükséges mérethez, sem pedig a képességek biztosításához (például a határos elavulás következetességéhez), valamint a szigorú és átfogó SLA-ra, amelyet a Cosmos DB biztosít ügyfeleinek.  

Ne felejtse el, hogy a partíciók több régióban vannak elosztva, és a Cosmos-adatbázisok (többrégiós írások) replikációs protokollját követve replikálják az adatot az adott partíciós készletet alkotó fizikai partíciók között. Az egyes fizikai partíciók (egy partíciós készlet) elfogadják az írásokat, és általában az adott régióhoz tartozó ügyfelekre mutatnak olvasási műveleteket. Az adott régióban található fizikai partíció által elfogadott írásokat a rendszer tartósan véglegesíti, és a fizikai partíción belül, az ügyfélnek való nyugtázás előtt elvégzi. Ezek a feltételes írások, és a partíción belüli többi fizikai partícióra vannak propagálva egy anti-entrópia Channel használatával. Az ügyfelek feltételes vagy véglegesített írásokat is igényelhetnek a kérelem fejlécének átadásával. Az anti-entrópia-propagálás (beleértve a propagálás gyakoriságát is) dinamikus, a particionálás topológiája, a fizikai partíciók regionális közelsége és a beállított konzisztencia-szint alapján. Egy partíción belül a Cosmos DB egy elsődleges véglegesítő sémát követ egy dinamikusan kiválasztott döntőbíró-partícióval. A döntőbíró kiválasztása dinamikus, és az átfedések topológiája alapján szerves részét képezi a partíció újrakonfigurálásának. A véglegesített írások (beleértve a többsoros vagy kötegelt frissítéseket) garantáltan megrendelhetők. 

A rendszer kódolt vektoros órákat alkalmaz (amelyek a régió-azonosítót és a logikai órákat tartalmazzák az egyes konszenzusi szintekhez a replika-set és a Partition-set esetében) a frissítési ütközések észlelése és megoldása érdekében. A topológia és a társ-kiválasztási algoritmus úgy lett kialakítva, hogy biztosítsa a rögzített és minimális tárterületet, valamint a verziók vektoros minimális hálózati terhelését. Az algoritmus garantálja a szigorú konvergencia-tulajdonságot.  

A több írási tartománnyal konfigurált Cosmos-adatbázisok esetében a rendszer számos rugalmas automatikus ütközés-feloldási szabályzatot biztosít a fejlesztők számára, többek között a következők közül: 

- A **Last-Write-WINS (LWW)**, amely alapértelmezés szerint a rendszer által definiált időbélyeg-tulajdonságot használja (amely az időszinkronizálási órajel protokollon alapul). A Cosmos DB az ütközés feloldásához használt egyéni numerikus tulajdonságok megadását is lehetővé teszi.  
- Az **alkalmazás által definiált (egyéni) ütközés-feloldási szabályzat** (egyesítési eljárásokkal kifejezve), amely az alkalmazás által definiált szemantikai egyeztetésekhez készült. Ezek az eljárások a írási-olvasási ütközések észlelése után kerülnek meghívásra egy adatbázis-tranzakció védnöksége alatt a kiszolgálói oldalon. A rendszer pontosan egyszer garantálja az egyesítési eljárás végrehajtását a kötelezettségvállalási protokoll részeként. [Több ütközési megoldási minta](how-to-manage-conflicts.md) is elérhető a következővel:.  

## <a name="consistency-models"></a>Konzisztencia-modellek

Akár egy vagy több írási régióval konfigurálja a Cosmos-adatbázist, az öt jól meghatározott konzisztencia-modell közül választhat. Több írási régió esetében a konzisztencia szintjeinek néhány jelentős aspektusa a következő:  

A határos állandósági konzisztencia garantálja, hogy az összes olvasás *K* előtagon vagy *T* másodpercen belül maradjon a legújabb írásokból bármelyik régióban. Emellett a korlátozott elavulás konzisztenciájával való olvasás garantáltan monoton és konzisztens előtag-garanciákat biztosít. Az anti-entrópia protokoll korlátozott módon működik, és biztosítja, hogy az előtagok ne legyenek felhalmozva, és az írási ellennyomás ne kelljen alkalmazni. A munkamenet konzisztenciája garantálja az monoton olvasást, az monoton írást, a saját írásait, az írást, az olvasást és az konzisztens előtag-garanciákat világszerte. Az erős konzisztencia-konfigurációval konfigurált adatbázisok esetében a több írási régió előnyei (alacsony írási késés, magas írási rendelkezésre állás) nem érvényesek a különböző régiók közötti szinkron replikáció miatt.

Az Cosmos DB öt konzisztencia-modelljének szemantikai leírását [itt](consistency-levels.md)találja, és matematikai módon ismertetjük a magas szintű tla [+ specifikációk](https://github.com/Azure/azure-cosmos-tla)segítségével.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan konfigurálhatja a globális eloszlást a következő cikkek használatával:

* [Régiók hozzáadása és eltávolítása az adatbázisfiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egyéni ütközés-feloldási szabályzat létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
