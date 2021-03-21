---
title: Gyakori kérdések a kiosztott átviteli sebességről Azure Cosmos DB
description: Gyakori kérdések az Azure Cosmos DB-adatbázisok és-tárolók kiosztott átviteli sebességéről
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2020
ms.openlocfilehash: 31b96f03a8519b068eaa816443be0a0f374a4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247029"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Gyakori kérdések a kiosztott átviteli sebességről Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az automatikus méretezés kiépített átviteli sebessége révén a Azure Cosmos DB automatikusan kezeli és méretezi az adatbázis vagy tároló RU/s-adatait a használat alapján. Ez a cikk az autoskálázással kapcsolatos gyakori kérdésekre ad választ.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Mi a különbség az "Autopilot" és az "autoscale" között a Azure Cosmos DBban?
Az "autoscale" vagy az "autoscale kiépített átviteli sebesség" a szolgáltatás frissített neve, korábbi nevén "Autopilot". Az autoscale jelenlegi kiadásával új funkciókkal bővült, beleértve az egyéni maximális RU/s és programozott támogatás beállításának lehetőségét is. 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>Mi történik az előző Autopilot-modellben létrehozott adatbázisokkal vagy tárolókkal?
Az előző rétegű modellel létrehozott erőforrásokat a rendszer automatikusan támogatja az új automatikus méretezési egyéni RU/s modell esetében. A rétegek felső határa lesz az új maximális RU/s, amely ugyanazt a méretezési tartományt eredményezi. 

Ha például korábban a 400 és 4000 RU/s közötti skálázási szintet választotta, az adatbázis vagy a tároló mostantól a 4000 RU/s értékkel rendelkező maximális RU/s-ként jelenik meg, amely a 400 és a 4000 RU/s közötti skálán látható. Itt módosíthatja a maximális RU/s értéket egy egyéni értékre, hogy megfeleljen a munkaterhelésnek. 

### <a name="how-quickly-will-autoscale-scale-up-based-on-spikes-in-traffic"></a>Milyen gyorsan méretezhető a vertikális felskálázás a forgalomban lévő tüskék alapján?
Az autoskálázással a rendszer a `T` `0.1 * Tmax` Bejövő forgalom alapján a és a tartományon belüli átviteli SEBESSÉGET (ru/s) méretezi `Tmax` . Mivel a skálázás automatikus és azonnali, bármikor felhasználható a késleltetés nélküli kiépítés `Tmax` . 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>Hogyan meghatározni, hogy a rendszer milyen RU/s-ra van méretezve?
[Azure monitor metrikák](how-to-choose-offer.md#measure-and-monitor-your-usage) használatával figyelheti a kiépített autoscale Max ru/s-t és az aktuális átviteli SEBESSÉGET (ru/s) a rendszerre. 

### <a name="what-is-the-pricing-for-autoscale"></a>Mi az az autoscale díjszabása?
Minden órában a legmagasabb átviteli sebességért kell fizetni, amelyet a `T` rendszer az órán belül felskálázással végez. Ha az erőforrás nem kapott kérelmeket az óra során, vagy nem lépték túl a skálázást `0.1 * Tmax` , akkor a minimumért kell fizetnie `0.1 * Tmax` . A részletekért tekintse meg a Azure Cosmos DB [díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>Hogyan jelenik meg az autoscale a számlán?
Az egyszeri írási régió fiókjaiban az automatikus skálázási sebesség 100 RU/s esetén 1,5 x a standard (manuális) kiosztott átviteli sebesség. A számlán látni fogja a meglévő standard kiosztott átviteli sebességet. A mérő mennyiségét a 1,5-es számmal megszorozza. Ha például a legmagasabb RU/s a rendszer egy órán belül, 6000 RU/s értékre van méretezve, akkor az adott órára a mérőszámban 60 * 1,5 = 90 egység lesz.

A több írási régióval rendelkező fiókok esetében az automatikus skálázási sebesség 100 RU/s-nál ugyanaz, mint a standard (manuális) kiosztott több írási régió átviteli sebessége. A számlán a meglévő több írási régió mérőszáma jelenik meg. Mivel a díjszabások azonosak, ha az autoscale-t használja, ugyanazt a mennyiséget fogja látni, mint a standard átviteli sebességgel.

### <a name="does-autoscale-work-with-reserved-capacity"></a>Működik az autoskálázás a fenntartott kapacitással?
Igen. Ha a fenntartott kapacitást egyszeri írási régióval rendelkező fiókok számára vásárolja meg, az autoskálázási erőforrásokra vonatkozó foglalási kedvezményt a rendszer 1,5 * arányban alkalmazza az [adott régió aránya](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)alapján. 

A többszörös írási régió számára fenntartott kapacitás ugyanúgy működik, mint az automatikus skálázás és a standard (manuális) kiosztott átviteli sebesség. [Azure Cosmos db fenntartott kapacitás](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>Működik az autoscale ingyenes szintje?
Igen. Az ingyenes szinten az adatátviteli sebességet használhatja egy tárolón. A megosztott átviteli sebességű adatbázisok egyéni maximális RU/s-vel való támogatása még nem érhető el. Megtudhatja [, hogy az ingyenes szintű számlázás hogyan működik az autoscale szolgáltatással](understand-your-bill.md#azure-free-tier).

### <a name="is-autoscale-supported-for-all-apis"></a>Támogatott-e az összes API-t használó autoskálázás?
Igen, az autoscale minden API esetében támogatott: Core (SQL), Gremlin, Table, Cassandra és API a MongoDB-hez.

### <a name="is-autoscale-supported-for-multi-region-write-accounts"></a>Támogatott-e a többrégiós írási fiókok esetében a méretezési funkció?
Igen. A maximális RU/s a Azure Cosmos DB-fiókhoz hozzáadott minden régióban elérhető. 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>Hogyan engedélyezi az autoskálázást az új adatbázisokon vagy tárolókban?
Tekintse meg ezt a cikket az [autoskálázás engedélyezéséhez](how-to-provision-autoscale-throughput.md).

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>Engedélyezhető az autoskálázás egy meglévő adatbázison vagy tárolón?
Igen. Igény szerint válthat az automatikus méretezés és a normál (manuális) kiosztott átviteli sebesség között is. Jelenleg az összes API esetében csak a [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) használhatja ezeket a műveleteket.

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>Hogyan működik az áttelepítés az automatikus méretezés és a standard (manuális) kiépített átviteli sebesség között?
Elméletileg az átviteli sebesség típusának módosítása kétfázisú folyamat. Először küld egy kérést, amely megváltoztatja az átviteli sebesség beállításait az automatikus méretezés vagy a manuális kiosztott átviteli sebesség használatára. A rendszer mindkét esetben automatikusan meghatározza és beállítja a kezdeti RU/s értéket az aktuális átviteli sebesség és a tárterület alapján. Ebben a lépésben a rendszer nem fogadja el a felhasználó által megadott RU/s értéket. Ezután a frissítés befejeződése után [módosíthatja az ru/s](#can-i-change-the-max-rus-on-the-database-or-container) -t, hogy megfeleljen a számítási feladatnak. 

**Áttelepítés standard (manuális) kiépített átviteli sebességről automatikus méretezésre**

Tároló esetén a következő képlettel becsülje meg a kezdeti autoskálázási Max RU/s: ``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)`` , a legközelebbi 1000 ru/s értékre kerekítve. A tényleges kezdeti automatikus méretezési Max RU/s a fiók konfigurációjától függően változhat.

Példa #1: tegyük fel, hogy rendelkezik egy, a 10 000 RU/s manuális kiosztott átviteli sebességgel és 25 GB tárhellyel rendelkező tárolóval. Ha engedélyezi az autoskálázást, a kezdeti autoskálázási Max RU/s a következő lesz: 10 000 RU/s, amely a 1000-10 000 RU/s-ben lesz méretezhető. 

Példa #2: tegyük fel, hogy rendelkezik egy, a 50 000 RU/s manuális kiosztott átviteli sebességgel és 2500 GB tárhellyel rendelkező tárolóval. Ha engedélyezi az autoskálázást, a kezdeti autoskálázási Max RU/s a következő lesz: 250 000 RU/s, amely a 25 000-250 000 RU/s-ben lesz méretezhető. 

**Áttelepítés automatikus méretezésről standard (manuális) kiépített átviteli sebességre**

A kezdeti manuális kiépített átviteli sebesség az aktuális automatikus méretezési maximális RU/s értékkel egyenlő lesz. 

Példa: tegyük fel, hogy rendelkezik egy, a 20 000 RU/s értékkel rendelkező, max. számú (2000 – 20 000 Ha a manuális kiosztott átviteli sebesség használatát frissíti, a kezdeti átviteli sebesség 20 000 RU/s lesz. 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>Létezik Azure CLI vagy PowerShell-támogatás az adatbázisok vagy tárolók autoskálázással való kezeléséhez?
Jelenleg csak az Azure Portal, a .NET v3 SDK, a Java v4 SDK és a Azure Resource Manager használatával hozhat létre és kezelhet erőforrásokat az autoscale paranccsal. Az Azure CLI, a PowerShell és más SDK-k támogatása még nem érhető el.

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Támogatott-e a megosztott átviteli sebességű adatbázisok esetében a méretezés?
Igen, az autoskálázás támogatott a megosztott átviteli sebességű adatbázisok esetében. A szolgáltatás engedélyezéséhez válassza az autoskálázás lehetőséget, és az adatbázis létrehozásakor a **kapacitás kiépítése** beállítást. 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>Mennyibe kerül az engedélyezett tárolók száma egy megosztott átviteli sebességű adatbázisban, ha engedélyezve van az autoskálázás?
Azure Cosmos DB legfeljebb 25 tárolót kényszerít ki egy megosztott átviteli sebességű adatbázisban, amely az Automatikus méretezéssel vagy a standard (manuális) átviteli sebességgel rendelkező adatbázisokra vonatkozik. 

### <a name="what-is-the-impact-of-autoscale-on-database-consistency-level"></a>Milyen hatással van az autoskálázás az adatbázis-konzisztencia szintjén?
Az adatbázis konzisztencia-szintjének nem befolyásolja az autoskálázást.
A rendelkezésre álló konzisztencia-szintekkel kapcsolatos további információkért tekintse meg a [konzisztencia szintjeivel](consistency-levels.md) foglalkozó cikket.

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Mekkora a maximális RU/s beállításhoz tartozó tárolási korlát?  
Az egyes maximális RU/s-k tárolási korlátja GB-ban: az adatbázis vagy a tároló/100 maximális száma. Ha például a maximális RU/s értéke 20 000 RU/s, az erőforrás képes támogatni a 200 GB tárterületet. A rendelkezésre álló maximális RU/s-és tárolási lehetőségekért lásd az [autoskálázási korlátokat](provision-throughput-autoscale.md#autoscale-limits) ismertető cikket. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Mi történik, ha túllépem a maximális átviteli sebességhez kapcsolódó tárolási korlátot?
Ha túllépte az adatbázis vagy tároló maximális átviteli sebességéhez tartozó tárolási korlátot, Azure Cosmos DB automatikusan növeli a maximális átviteli sebességet a következő legmagasabb RU/s értékre, amely képes támogatni az adott szintű tárterületet.

Ha például a 50 000 RU/s max. RU/s értékkel kezdődik (a 5000-50 000 RU/s-ig terjedő skála), akár 500 GB-nyi adat tárolására is képes. Ha túllépi az 500 GB-ot (például a tárterület 600 GB), az új maximális RU/s érték 60 000 lesz (a skálázás 6000 és 60 000 RU/s között történik).

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>Módosíthatom a maximális RU/mp-t az adatbázison vagy a tárolón? 
Igen. Tekintse meg ezt a [cikket](how-to-provision-autoscale-throughput.md) a maximális ru/mp módosításához. Ha a maximális RU/s értéket módosítja a kért értéktől függően, ez lehet egy aszinkron művelet, amely eltarthat egy ideig (akár 4-6 óra is lehet, attól függően, hogy milyen RU/s van kiválasztva)

#### <a name="increasing-the-max-rus"></a>A maximális RU/s növelése
Ha a maximális ru/mp növelésére vonatkozó kérést küld, attól függően, hogy a maximális ru/s `Tmax` érték van kiválasztva, a szolgáltatás több erőforrást is kiépít, hogy támogassa a magasabb maximális ru/mp-t. Habár ez történik, a meglévő számítási feladatokat és műveleteket nem érinti a rendszer. A rendszer továbbra is átméretezi az adatbázist vagy a tárolót az előzőtől `0.1*Tmax` `Tmax` egészen addig, amíg az új méretezési tartomány fel `0.1*Tmax_new` `Tmax_new` nem áll.

#### <a name="lowering-the-max-rus"></a>A maximális RU/mp csökkentése
Ha csökkenti a maximális RU/s értéket, a minimális érték a következőre állítható:, a `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` legközelebbi 1000 ru/s értékre kerekítve. 

Példa #1: tegyük fel, hogy rendelkezik egy, a 20 000 RU/s maximális RU/s értékkel rendelkező, autoskálázási tárolóval (2000-20 000 RU/s) és 50 GB tárhellyel. A legalacsonyabb, minimális értékre állíthatja a maximális RU/s értéket: MAX (4000, 20 000/10, **50 * 100**) = 5000 ru/s (skála a 500-5000 ru/s között).

Példa #2: tegyük fel, hogy rendelkezik egy, a 100 000 RU/s és 100 GB méretű maximális RU/s értékkel rendelkező autoskálázási tárolóval. Mostantól legfeljebb 150 000 RU/s méretig méretezheti a maximális RU/s-t (a 15 000-150 000 RU/s közötti skálán). A legalacsonyabb, minimális érték, amellyel most már beállíthatja a maximális RU/s értéket: MAX (4000, **150 000/10**, 100 * 100) = 15 000 ru/s (skála a 1500-15 000 ru/s között). 

Megosztott átviteli sebességű adatbázis esetén, ha csökkenti a maximális RU/s értéket, a minimális érték a következőre állítható be: `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))` , a legközelebbi 1000 ru/s értékre kerekítve.  

A fenti képletek és példák a minimális automatikus méretezési (RU/s) értékre vonatkoznak, és nem különböznek a `0.1 * Tmax` rendszer által automatikusan átadott `Tmax` tartománytól. Függetlenül attól, hogy mi a maximális RU/s, a rendszer mindig a és a közötti méretet fogja méretezni `0.1 * Tmax` `Tmax` . 

### <a name="how-does-ttl-work-with-autoscale"></a>Hogyan működik az élettartam az autoscaletel?
Az automatikus skálázással a TTL-műveletek nincsenek hatással az RU/s méretezésére. A TTL miatt felhasznált összes RUs nem része az autoscale tároló számlázott RU/s-nek. 

Tegyük fel például, hogy rendelkezik egy, a 400 – 4000 RU/s méretű autoscale tárolóval:
- 1. óra: T = 0: a tároló nem rendelkezik használattal (TTL-vagy munkaterhelés-kérelmek nélkül). A számlázható RU/s értéke 400 RU/s.
- 1. óra: T = 1: a TTL engedélyezve van.
- 1. óra: T = 2: a tároló elindítja a kérelmeket, amelyek 1 másodperc alatt használják a 1000 RU-T. Emellett a 200-es számú, TTL értékű TTL-értéket is meg kell történnie. A számlázható RU/s még mindig 1000 RU/s. Függetlenül attól, hogy mikor történik a TTLs, a rendszer nem befolyásolja az automatikus skálázási méretezési logikát.

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Mi a különbség a maximális RU/s és a fizikai partíciók között?
Amikor először kiválasztja a maximális RU/s-t, Azure Cosmos DB a következőt fogja kiépíteni: Max RU/s/10 000 RU/s = # fizikai partíciók. Minden [fizikai partíció](partitioning-overview.md#physical-partitions) legfeljebb 10 000 ru/s és 50 GB tárterületet tud támogatni. Mivel a tárterület mérete növekszik, Azure Cosmos DB automatikusan felosztja a partíciókat, hogy további fizikai partíciókat adjon hozzá a tárterület növeléséhez, vagy növelje a maximális RU/s értéket, ha [a tárterület meghaladja a társított korlátot](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Az adatbázis vagy tároló maximális RU/s értéke egyenletesen oszlik el az összes fizikai partíció között. Tehát a teljes átviteli sebesség egyetlen fizikai partíción is méretezhető a következőre: az adatbázis vagy tároló/# fizikai partíciók maximális száma (RU/s). 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Mi történik, ha a bejövő kérelmek túllépik az adatbázis vagy a tároló maximális RU/s értékeit?
Ha a teljes felhasznált RU/s érték meghaladja az adatbázis vagy tároló maximális RU/mp-számát, a rendszer lekéri a maximális RU/s értéket, és egy 429 állapotkódot ad vissza. A 100%-os normalizált kihasználtságot eredményező kérések is szabályozva lesznek. A normalizált kihasználtság az összes fizikai partíción az RU/s kihasználtság maximális értékeként van definiálva. Tegyük fel például, hogy a maximális átviteli sebesség 20 000 RU/s, és két fizikai partíció, P_1 és P_2 van, amelyek mindegyike 10 000 RU/s-ra méretezhető. Egy adott másodpercben, ha P_1 6000 RUs-t használ, és P_2 8000 RUs, a normalizált kihasználtság MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Az Azure Cosmos DB ügyféloldali SDK-k és adatimportálási eszközök (Azure Data Factory, tömeges végrehajtó kódtár) automatikusan újrapróbálkoznak a 429s, így az alkalmi 429s is rendben vannak. A tartósan nagy mennyiségű 429s arra utalhat, hogy emelnie kell a maximális RU/mp-t, vagy a particionálási stratégiát egy [gyors partícióra](#autoscale-rate-limiting)vonatkozóan át kell tekintenie.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> Továbbra is látható a 429s (szabályozás/arány korlátozása), ha engedélyezve van az autoskálázás? 
Igen. 429-es hiba két esetben jelentkezhet. Először is, ha a teljes felhasznált RU/s meghaladja az adatbázis vagy tároló maximális RU/s értékeit, a szolgáltatás ennek megfelelően szabályozza a kérelmeket. 

Másodszor, ha van egy gyakori partíció, azaz egy logikai partíciós kulcs értéke, amely aránytalanul nagyobb mennyiségű kérést tartalmaz a többi partíciós kulcs értékeihez képest, lehetséges, hogy a mögöttes fizikai partíció túllépi az RU/s költségvetést. A gyakori hozzáférésű partíciók elkerülésének ajánlott eljárása [egy megfelelő partíciókulcs kiválasztása](partitioning-overview.md#choose-partitionkey), amellyel a tárterület és az átviteli sebesség egyenlően osztható ki. 

Ha például az 20 000 RU/s maximális átviteli sebesség lehetőséget választja, és 200 GB tárhellyel rendelkezik, és négy fizikai partícióval rendelkezik, az egyes fizikai partíciók akár 5000 RU/s-ra is felméretezhetők. Ha egy adott logikai partíción egy gyors partíció található, akkor a 429s jelenik meg, ha a mögöttes fizikai partíció mérete meghaladja az 5000 RU/s-t, azaz meghaladja az 100%-os normalizált kihasználtságot.


## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [engedélyezhető az Azure Cosmos db-adatbázis vagy-tárolók autoskálázása](how-to-provision-autoscale-throughput.md).
* Ismerje meg, hogy milyen [előnyökkel jár a kiépített átviteli sebesség az autoskálázással](provision-throughput-autoscale.md#benefits-of-autoscale).
* További információ a [logikai és a fizikai partíciókhoz](partitioning-overview.md).
                        
