---
title: Teljes tulajdonlási költség (TCO) Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB IaaS és helyszíni adatbázisokkal való teljes tulajdonlásának költségeit hasonlítja össze
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079291"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Teljes tulajdonlási költség (TCO) Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB részletes, több-bérlős működésre és erőforrás-szabályozásra lett tervezve. Ez lehetővé teszi, hogy az Azure Cosmos DB jelentősen alacsonyabb költségekkel működjön, és így a felhasználók pénzt takaríthassanak meg. Jelenleg az Azure Cosmos DB több mint 280 ügyfél általi számítási feladatot támogat gépenként, miközben ennek sűrűsége folyamatosan nő, valamint több ezret egy fürtön belül. Elosztja az ügyfél általi számítási feladatok replikáit egy adott fürt különböző gépei, valamint egy adatközpont több fürtje között a terheléskiegyenlítés érdekében. További információt [a Azure Cosmos db: globálisan elosztott adatbázisok határának](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/)leküldésével foglalkozó témakörben talál. Az erőforrás-szabályozás, a több-bérlős és az Azure-infrastruktúrával való natív integráció miatt a Azure Cosmos DB átlagosan 4 – 6-szor olcsóbb, mint a MongoDB, Cassandra vagy más, IaaS-on futó OSS-NoSQL, és akár 10-szer olcsóbb, mint a helyszínen futó adatbázismotor. Tekintse meg a [NoSQL-adatbázis felhőalapú szolgáltatásának teljes költsége (nem) tulajdonjogát](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Az OSS NoSQL adatbázis-megoldásai, például az Apache Cassandra, a MongoDB, a HBase, a motorok helyszíni használatra készültek. Felügyelt szolgáltatásként való felkínálás esetén a kiépített fürtök kezeléséhez és a figyelés támogatásához a bérlői adatbázissal egyenértékű Resource Manager-sablon felel meg. Az OSS NoSQL architektúrák jelentős működési terhelést igényelnek, és a szakértelmet nehéz és költséges lehet megtalálni. Másfelől Azure Cosmos DB egy teljes körűen felügyelt felhőalapú szolgáltatás, amely lehetővé teszi a fejlesztők számára, hogy az adatbázis-infrastruktúra kezelése és karbantartása helyett az üzleti innovációra összpontosítsanak.

A natív adatbázis-szolgáltatás Azure Cosmos DBával ellentétben az OSS NoSQL adatbázis-motorokat nem az erőforrás-szabályozással vagy a részletes, az alapvető építészeti alapelveknek megfelelően építettük. Az OSS NoSQL-adatbázis-kezelők, például a Cassandra és a MongoDB, alapvető feltételezést tesznek, hogy a virtuális gép azon erőforrásai, amelyen futnak, elérhetők. Az adatbázis-hajtóművek sok esetben nem működhetnek, ha az erőforrások mennyisége egy bizonyos küszöbérték alá esik. Kisméretű virtuálisgép-példányok esetében például a gyártó által javasolt konfigurációk is elérhetők, amelyek általában nagyobb méretű virtuális gépeket javasolnak. Így nem üzemeltethető OSS-NoSQL vagy bármely más helyszíni adatbázismotor, és elérhetővé teheti azt egy olyan fogyasztáson alapuló díjszabási modell használatával, mint a kérelmek másodpercenkénti száma vagy a felhasznált tárterület.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Azure Cosmos DB teljes tulajdonlási költsége

A Azure Cosmos DB kiszolgáló nélküli kiépítési modellje kiküszöböli az adatbázis-infrastruktúra túlzott kiépítésének szükségességét. A Azure Cosmos DB erőforrások speciális konfigurációk vagy licencelési igények nélkül érhetők el. Ennek eredményeképpen a Azure Cosmos DB által támogatott alkalmazások az OSS NoSQL-adatbázisokhoz képest akár 70%-os teljes tulajdonlási díjat is futtathatnak. A valós idejű példákért tekintse meg az [ügyfelek használati eseteit](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). A Azure Cosmos DB díjszabási modell egyéb előnyei a következők:

* **Az ár nagy értéke:** A piaci elemzők, az ügyfelek és a partnerek megerősítették az összes olyan funkciót, amelyet a Azure Cosmos DB sokkal alacsonyabb áron kínál, mint az ügyfelek számára a megoldások saját vagy más gyártók általi megvalósítása során. Az adatbázis olyan funkciókkal rendelkezik, mint a globális terjesztés, a többrégiós írások, valamint a jól definiált és intuitív konzisztencia-modellek, az Automatikus indexelés nagy mértékben egyszerűsíthető Azure Cosmos DB összetettség, terhelés vagy állásidő nélkül.

* **Nincs szükség NoSQL DevOps-felügyeletre:** Azure Cosmos DB az egyiknek nem kell DevOps használnia a központi telepítések kezeléséhez, karbantartás, méretezés vagy javítás végrehajtásához. Végrehajthatja az összes olyan munkaterhelést, amelyet a helyszíni vagy a felhőalapú infrastruktúrán üzemeltetett OSS NoSQL-fürtökkel tenne.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Azure Cosmos DB tulajdonlási díja" border="false":::

* **Rugalmas méretezési képesség:** A Azure Cosmos DB átviteli sebesség fel-és leskálázással növelhető, így csökkentheti a tulajdonosi költségeket a nem maximális időpontokban. A felhőalapú infrastruktúrán üzembe helyezett OSS NoSQL-fürtök korlátozott rugalmasságot biztosítanak, és a helyszíni üzemelő példányok nem rugalmasak a definíciók alapján. Ha Azure Cosmos DB több átviteli sebességet is kiépít, az átviteli sebesség garantált a lineáris skálázás érdekében. Ezt a garanciát a pénzügyi SLA-kat és a esetek 99% percentilis-t bármilyen méretben biztonsági mentéssel készítjük.

* **Méretgazdaságosság:** Egy felügyelt szolgáltatás, például a Azure Cosmos DB nagy számú csomóponttal működik, és natív módon integrálódik a hálózatkezelés, a tárolás és a számítások során. Azure Cosmos DB nagyméretű skálázása miatt a költségek is megmenthetők.

* **Felhőre optimalizált:** Azure Cosmos DB az alapoktól kezdve a részletes, több-bérlős és teljesítmény-elkülönítéssel lett kialakítva. Ez lehetővé teszi a bérlők és az adatközpontok közötti, több ezer bérlő és számítási feladatok optimális elhelyezését, végrehajtását és kiegyensúlyozását. Ezzel szemben az OSS-NoSQL adatbázisok jelenlegi generációja a helyszínen működik együtt a teljes virtuális géppel, amely egyetlen bérlő munkaterhelésének futtatását feltételezi. Ezek az adatbázisok nem úgy vannak kialakítva, hogy teljes mértékben kihasználják a felhőalapú szolgáltató infrastruktúráját és hardverét. Egy OSS NoSQL-adatbázismotor például nem ismeri a virtuális gép és a rutinos lemezkép-frissítés közötti különbséget, vagy azt, hogy a prémium szintű lemez már háromutas módon replikálódik. Nem tudja kihasználni ezeket az előnyöket, és az ügyfeleknek nyújtott előnyökkel és megtakarítással jár.

* **Az óra után fizet:** A nagy méretű számítási feladatokhoz, amelyeknek bármely időpontban kell méreteznie, csak az óra után kell fizetni. Az alkalmazáson belüli munkaterhelések általában az év minden időpontjában változnak, a lekérdezett adatokkal. A Azure Cosmos DB segítségével igény szerint méretezheti, és csak azért kell fizetnie, amire szüksége van. A helyszíni vagy IaaS üzemeltetett rendszerekkel nem lehet megfelelni ennek a modellnek, mert a hardvert nem lehet óránként leszerelni. Ilyen esetekben előfordulhat, hogy a Azure Cosmos DB-vel átlagosan 10 – 14 alkalommal lehet megtakarítani.

* **Számos funkciót kap ingyenesen:** Azure Cosmos DB az írási munkaterhelések lényegesen olcsóbbak az alternatív adatbázis-szolgáltatásokhoz képest. Emellett a Azure Cosmos DB olyan funkciókat is kínál, mint például az [Automatikus indexelés](index-policy.md), az élettartam [(TTL)](time-to-live.md), a [hírcsatornák módosítása](change-feed.md) és egyéb költségek nélkül, ami más adatbázis-szolgáltatások esetében általában díjköteles.

* **Az egységes pénznemet használ a különböző munkaterhelésekhez:** Az alternatív ajánlatoktól eltérően a Azure Cosmos DBban nem szükséges a számítási feladatok (például olvasások és írások) szegmentálása. Vagy az adatátviteli kapacitás kiépítése az olvasási sebesség és az írási átviteli sebesség alapján. Azure Cosmos DB a kiépített átviteli sebesség egy egységes és normalizált pénznem használatával van lefoglalva a kérési egységekben vagy az RU/mp-ben. a Azure Cosmos DB nem kényszeríti, hogy prioritást rendeljen a számítási feladatokhoz, hajtsa végre a kapacitás megtervezését, vagy a kapacitást külön kell fizetnie. Ez a módszer lehetővé teszi, hogy könnyedén átmásolja ugyanazokat az RU/s-ket a különböző műveletek és munkaterhelés-típusok között.

* **Nem szükséges kiépíteni a virtuális gépeket a skálázáshoz:** A legtöbb operatív adatbázishoz szükség van nagyméretű virtuális gépekre, hogy elkerülje a zajos szomszédokat és a kihasználatlan erőforrás-szabályozást, ha méretezést szeretne. Ez a terheket és az ügyfelekre háruló költségek iránti elkötelezettségét teszi lehetővé. A Azure Cosmos DB segítségével zökkenőmentesen elkezdheti a nagy méretű számítási feladatok méretének növelését, és az adatok rendelkezésre állásának leállása nélkül is.

* **A kiépített átviteli sebességet a maximális korlátra használhatja:** A Azure Cosmos DB-ben az alcore Multiplexing alapján a kiépített átviteli sebesség nagyobb mértékben telített, mint az üzemeltetett IaaS vagy harmadik féltől származó ajánlatok. Ez a módszer sokkal többet takarít meg az alternatív megoldásoknál.

* **Azure Cosmos DB a többi Azure-szolgáltatással való szoros integrációja.** Azure Cosmos DB rendelkezik a hálózatkezelés, a számítási, a Azure Functions (kiszolgáló nélküli), az Azure IoT és más Azure-szolgáltatások natív integrációs szolgáltatásával. Ezzel az integrációval a legjobb teljesítményt, az adatreplikáció sebességét biztosítja a világ minden pontján robusztus garanciákkal. A harmadik féltől származó megoldások nem tudnak megegyezni, vagy általában prémium díjat számítanak fel az ilyen funkciók felkínálása érdekében.

* A **magas rendelkezésre állást automatikusan elérheti legalább 10-20 tartalék tartománnyal, alapértelmezés szerint:** Azure Cosmos DB támogatja a terhelések elosztását a tartalék tartományok között, amely a magas rendelkezésre állás szempontjából kritikus fontosságú szolgáltatás. 99,999 magas rendelkezésre állást kínál a esetek 99% percentilis olvasására és írására a világ bármely pontján belül. A saját vagy harmadik féltől származó megoldáson alapuló megvalósítási díj magas lenne.

* **Az összes nagyvállalati funkciót automatikusan, díjmentesen veheti igénybe.** A Azure Cosmos DB a megfelelőségi tanúsítványok, a biztonság és a titkosítás legátfogóbb készletét kínálja a nyugalmi állapotban és a mozgásban (a versenyhez képest) külön díj nélkül. A világ bármely pontján automatikusan elérhető a regionális elérhetőség. Az adatbázist tetszőleges számú Azure-régióban áthelyezheti, és bármikor hozzáadhat vagy eltávolíthat régiókat.

* A **fenntartott kapacitással akár 65%-ot is megtakaríthat:** Azure Cosmos DB [fenntartott kapacitással](cosmos-db-reserved-capacity.md) pénzt takaríthat meg, ha egy vagy három évig Azure Cosmos db erőforrásokra előre fizet. Jelentős mértékben csökkentheti a költségeket egy-vagy hároméves előzetes kötelezettségvállalásokkal, és a normál díjszabáshoz képest 20-65%-os kedvezményt takaríthat meg. A kritikus fontosságú számítási feladatokban jobb SLA-kat érhet el a kiépítési kapacitás szempontjából.

## <a name="next-steps"></a>Következő lépések

* További információ arról [, hogy a Azure Cosmos db díjszabási modellje mennyire költséghatékony az ügyfelek számára](total-cost-ownership.md)
* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)
* További információ [a több régióból álló Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
* További információ [a NoSQL-adatbázis felhőalapú szolgáltatásának teljes (nem) tulajdonosi díjszabásáról](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
