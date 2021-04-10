---
title: A lekérdezési és indexelési feladatok becsült kapacitása
titleSuffix: Azure Cognitive Search
description: A partíciós és a replika számítógép erőforrásainak módosítása az Azure Cognitive Searchban, ahol az egyes erőforrások számlázható keresési egységekben vannak.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: b1f742c1de259f6c1c06d9b31a8788699f0b8426
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580026"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás kapacitásának becslése és kezelése

A [keresési szolgáltatás üzembe helyezése](search-create-service-portal.md) és egy adott díjszabási szinten való zárolása előtt szánjon néhány percet, hogy megértse, hogyan működik a kapacitás, és hogyan módosíthatja a replikákat és a partíciókat a munkaterhelés ingadozásának kielégítése érdekében.

A kapacitás a [szolgáltatási szintek](search-sku-tier.md)egyik funkciója, a szolgáltatások maximális tárterületének létrehozása, a partíciók száma, valamint a létrehozható objektumok maximális korlátja. Az alapszintű csomag olyan alkalmazások számára készült, amelyek szerény tárolási követelményekkel rendelkeznek (csak egy partíció), de a magas rendelkezésre állású konfigurációban (3 replika) futtathatók. A többi szintet meghatározott számítási feladatokhoz vagy mintákhoz (például bérlős) tervezték. Belsőleg az ezeken a platformokon létrehozott szolgáltatások kihasználják azokat a hardvereket, amelyek segítik ezeket a forgatókönyveket.

Az Azure Cognitive Search skálázhatósági architektúrája a replikák és partíciók rugalmas kombinációján alapul, így a kapacitás attól függően változhat, hogy több lekérdezésre vagy az indexelési teljesítményre van szüksége. A szolgáltatás létrehozása után a replikák és a partíciók száma egymástól függetlenül növelhető vagy csökkenthető. A költségek felmerülnek minden további fizikai erőforrással, de ha a nagyméretű munkaterhelések befejeződtek, csökkentheti a méretezést a számla csökkentése érdekében. A szintjétől és a beállítás méretétől függően a kapacitás hozzáadása vagy csökkentése akár 15 perctől akár több óráig is eltarthat.

A replikák és partíciók kiosztásának módosításakor javasoljuk, hogy használja a Azure Portal. A portál kényszeríti az olyan megengedett kombinációk korlátozásait, amelyek az adott szinten maximális korláton belül maradnak. Ha azonban parancsfájl-vagy programkód-alapú létesítési megközelítésre van szüksége, akkor a [Azure PowerShell](search-manage-powershell.md) vagy a [felügyeleti REST API](/rest/api/searchmanagement/services) alternatív megoldás.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Fogalmak: keresési egységek, replikák, partíciók, szegmensek

A kapacitás olyan *keresési egységben* van kifejezve, amely *partíciók* és *replikák* kombinációi között foglalható le, a rugalmas konfigurációk támogatásához *egy mögöttes* horizontális Felskálázási mechanizmus használatával:

| Fogalom  | Definíció|
|----------|-----------|
|*Keresési egység* | A teljes rendelkezésre álló kapacitás (36 egység) egyetlen növekménye. Az Azure Cognitive Search szolgáltatás számlázási egysége is. A szolgáltatás futtatásához legalább egy egység szükséges.|
|*Replika* | A keresési szolgáltatás azon példányai, amelyek elsődlegesen a lekérdezési műveletek terheléselosztására szolgálnak. Minden replika egy adott index egy példányát tárolja. Ha három replikát oszt ki, akkor a lekérdezési kérések kiszolgálásához három példánya lesz elérhető.|
|*Partíció* | Fizikai tárterület és I/O írási/olvasási műveletekhez (például indexek újraépítésekor vagy frissítésekor). Minden partíció tartalmaz egy szeletet a teljes indexből. Ha három partíciót foglal le, az index a harmadikra van osztva. |
|*Szegmens* | Egy indexből álló tömb. Az Azure Cognitive Search felosztja az egyes indexeket a szegmensekre, így gyorsabbá teszi a partíciók hozzáadását (a szegmensek új keresési egységekre való áthelyezésével).|

Az alábbi ábrán a replikák, a partíciók, a szegmensek és a keresési egységek közötti kapcsolat látható. Ebből a példából kiderül, hogy az egyes indexek hogyan terjednek át egy szolgáltatás négy keresési egységére két replikával és két partícióval. A négy keresési egység az indexnek csak a felét tárolja. A bal oldali oszlopban található keresési egységek a szegmensek első felét tárolják, amely az első partíciót tartalmazza, míg a jobb oldali oszlopban lévők a második partíciót tartalmazó szegmensek második felét tárolják. Mivel két replika létezik, az egyes indexek szegmensének két példánya van. A felső sorban található keresési egységek egyetlen másolatból állnak, amelyek az első replikát alkotják, míg az alsó sorban lévők egy másik példányt tárolnak, amely a második replikát tartalmazza.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="A keresési indexek felosztva vannak a partíciók között.":::

A fenti diagram csak egy példát mutat be. A partíciók és a replikák számos kombinációja lehetséges, legfeljebb 36 teljes keresési egység.

Cognitive Search a szilánkok kezelése egy implementációs adat és nem konfigurálható, de az indexek horizontális felskálázása segít megérteni a rangsorolási és az automatikus kiegészítési viselkedésben előforduló esetenkénti rendellenességeket:

+ Rangsorolási rendellenességek: a keresési pontszámok először a szegmens szintjén vannak kiszámítva, majd egyetlen eredményhalmaz szerint összesítve. A szegmens tartalmának jellemzőitől függően előfordulhat, hogy az egyik szegmensből származó egyezések egy másiknál magasabbra vannak sorolva. Ha a keresési eredmények között a számláló intuitív rangsora látható, akkor valószínűleg a horizontális skálázás hatása okozza, különösen, ha az indexek kicsik. A rangsorolási rendellenességek elkerülhetők, ha [a teljes indexben globálisan szeretné kiszámítani a pontszámokat](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), de ez a teljesítményre vonatkozó szankciót is eredményez.

+ Automatikus kiegészítési rendellenességek: automatikus lekérdezések, ahol a egyezések egy részben megadott kifejezés első több karakterén történnek, fogadjon egy olyan fuzzy paramétert, amely a helyesírási kis eltéréseket megbocsátja. Az automatikus kiegészítés esetében a zavaros egyezés az aktuális szegmensen belüli kifejezésekre van korlátozva. Ha például egy szegmens "Microsoft" kifejezést tartalmaz, és a "micor" részleges kifejezése van megadva, akkor a keresőmotor a "Microsoft" résznek felel meg a szegmensben, de nem az index fennmaradó részeit tároló más szegmensekben.

## <a name="approaching-estimation"></a>A becslés közeledik

A szolgáltatás üzemeltetésének kapacitása és költségei. A rétegek két szintre korlátozzák a korlátozásokat: a tartalmak (például egy szolgáltatás indexei száma) és a tárterület. Fontos figyelembe venni mindkettőt, mivel a határértékek közül az első a hatályos korlát.

Az indexek és más objektumok számát általában az üzleti és mérnöki követelmények írják elő. Előfordulhat például, hogy az Active Development, a Testing és a Production esetében ugyanaz az index több verziója van.

A tárolási igényeket a felépíteni kívánt indexek méretétől függően kell meghatározni. Nincsenek olyan szilárd heurisztikus vagy általános értékek, amelyek segítenek a becslésekben. Az indexek méretének [meghatározása egyetlen módszer.](search-what-is-an-index.md) A mérete az importált adatmennyiség, a szöveg elemzése és az index konfigurációja alapján történik, például az, hogy engedélyezi-e a javaslatokat, a szűrést és a rendezést.

A teljes szöveges kereséshez az elsődleges adatstruktúra egy [fordított index](https://en.wikipedia.org/wiki/Inverted_index) -struktúra, amely különböző tulajdonságokkal rendelkezik, mint a forrásadatok. A fordított indexek esetében a méretet és az összetettséget a tartalom határozza meg, nem feltétlenül a betáplált adatmennyiség. Egy nagy redundanciával rendelkező nagyméretű adatforrás kisebb indexet eredményezhet, mint egy nagy mértékben változó tartalmat tartalmazó kisebb adathalmaz. Így ritkán lehet az index méretét az eredeti adatkészlet méretétől függően kikövetkeztetni.

Az index attribútumai, például a szűrők és a rendezés engedélyezése hatással lesznek a tárolási követelményekre. A javaslatok használatának a tárolási következményei is vannak. További információ: [attribútumok és index mérete](search-what-is-an-index.md#index-size).

> [!NOTE]
> Annak ellenére, hogy az indexek és a tárolás jövőbeli igényeit is megbecsülik, érdemes meggondolni. Ha a rétegek kapacitása túl alacsonyra vált, egy új szolgáltatást kell kiépíteni egy magasabb szintű szinten, majd [újra kell töltenie az indexeket](search-howto-reindex.md). A szolgáltatás egyik rétegből a másikba való helyben történő frissítése nem történik meg.
>

### <a name="estimate-with-the-free-tier"></a>Becslés az ingyenes szintjével

A kapacitás becslésének egyik megközelítése az ingyenes szinten való kezdés. Ne feledje, hogy az ingyenes szolgáltatás legfeljebb három indexet kínál, 50 MB tárterületet és 2 perc indexelési időt. A megszorítások alapján megbecsülheti a tervezett indexek méretét, de ezek a lépések a következők:

+ [Hozzon létre egy ingyenes szolgáltatást](search-create-service-portal.md).
+ Készítsen elő egy kis, reprezentatív adatkészletet.
+ Hozzon létre egy indexet, és töltse be az adatait. Ha az adatkészlet egy indexelő által támogatott Azure-adatforrásban is üzemeltethető, a [portálon az adatimportálás varázsló](search-get-started-portal.md) segítségével is létrehozhatja és betöltheti az indexet. Ellenkező esetben a REST és a [Poster](search-get-started-rest.md) vagy a [Visual Studio Code](search-get-started-vs-code.md) használatával hozza létre az indexet, és küldje el az adatok küldését. A leküldéses modellnek JSON-dokumentumok formájában kell lennie, ahol a dokumentum mezői az index mezőinek felelnek meg.
+ Gyűjtsön adatokat az indexről, például a méretet. A funkciók és attribútumok hatással vannak a tárterületre. Például a javaslatok hozzáadásával (keresési típusú lekérdezések) növelheti a tárolási követelményeket. 

  Ha ugyanazt az adatkészletet használja, előfordulhat, hogy egy index több verzióját is létrehozhatja az egyes mezők különböző attribútumaival, hogy megtekintse, hogyan változnak a tárolási követelmények. További információkért lásd: [alapszintű index létrehozása "tárolási vonzatok"](search-what-is-an-index.md#index-size).

A durva becsléssel megduplázhatja ezt a költségvetést két index (fejlesztés és éles környezet) esetében, majd a szintet ennek megfelelően kiválaszthatja.

### <a name="estimate-with-a-billable-tier"></a>Becslés számlázható szintjével

A dedikált erőforrások nagyobb mintavételezési és feldolgozási időt biztosítanak a fejlesztés során az index mennyiségének, méretének és lekérdezési köteteinek reális becsléséhez. Néhány ügyfél azonnal egy számlázható réteggel ugrik, majd újraértékeli a fejlesztési projekt futamideje alatt.

1. [Tekintse át a szolgáltatási korlátokat az egyes szinteknél](./search-limits-quotas-capacity.md#index-limits) annak megállapításához, hogy az alsóbb szintű csomagok támogatni tudják-e a szükséges indexek számát. Az alapszintű, S1 és S2 szinten az index korlátai 15, 50 és 200. A tárolási optimalizált csomagok korlátja 10 index, mert a rendszer úgy van kialakítva, hogy támogassa a nagyon nagy méretű indexek alacsony számát.

1. [Szolgáltatás létrehozása számlázandó szinten](search-create-service-portal.md):

    + Ha nem biztos benne, hogy a tervezett terhelésről van szó, az alapszintű vagy S1.
    + Ha a tesztelés nagy léptékű indexelést és lekérdezési terhelést foglal magában, a magas szintű, S2-es vagy akár S3 értékű.
    + Ha nagy mennyiségű adatmennyiséget indexel, és a lekérdezés terhelése viszonylag alacsony, mint egy belső üzleti alkalmazás esetében, az optimalizált tárolóval kezdődik az L1 vagy az L2.

1. [Hozzon létre egy kezdeti indexet](search-what-is-an-index.md) annak meghatározására, hogy a forrásadatok hogyan fordíthatók le egy indexre. Ez az egyetlen módszer az index méretének becslésére. 

1. A portálon [megfigyelheti a tárterületet, a szolgáltatási korlátokat, a lekérdezési kötetet és a késést](search-monitor-usage.md) . A portálon másodpercenként megjelennek a lekérdezések, a szabályozott lekérdezések és a keresési késések. Ezeknek az értékeknek a segítségével eldöntheti, hogy a megfelelő szintet választotta-e.

1. Ha magas rendelkezésre állásra van szüksége, vagy lassú lekérdezési teljesítményt tapasztal, adjon hozzá replikákat.

   Nincsenek irányelvek arra vonatkozóan, hogy hány replikára van szükség a lekérdezési terhelések fogadásához. A lekérdezési teljesítmény a lekérdezés és a versengő számítási feladatok összetettsége alapján változhat. Bár a replikák hozzáadása egyértelműen jobb teljesítményt eredményez, az eredmény nem feltétlenül lineáris: három replika hozzáadása nem garantálja a háromszoros átviteli sebességet. A megoldás QPS becslésével kapcsolatos útmutatásért lásd: [Méretezés a teljesítmény és a](search-performance-optimization.md) [figyelési lekérdezések](search-monitor-queries.md)számára.

> [!NOTE]
> A tárolási követelmények akkor tölthetők fel, ha olyan adathalmazt tartalmaz, amelyet soha nem fog keresni. Ideális esetben a dokumentumok csak azokat az adattartalmakat tartalmazzák, amelyekre szüksége van a keresési élményhez. A bináris adatkeresés nem kereshető, és külön kell tárolni (lehet, hogy egy Azure Table vagy blob Storage-tárolóban). Ezután fel kell venni egy mezőt az indexbe, hogy a külső értékekre mutató URL-hivatkozást lehessen tárolni. Az egyéni keresési dokumentumok maximális mérete 16 MB (vagy kevesebb, ha egy kérelemben több dokumentumot tölt fel tömeges feltöltéssel). További információ: [szolgáltatási korlátozások az Azure Cognitive Searchban](search-limits-quotas-capacity.md).
>

**Mennyiségi megfontolások lekérdezése**

A másodpercenkénti lekérdezések (QPS-k) fontos mérőszámot jelentenek a teljesítmény finomhangolása során, de ez általában csak a szintet veszi figyelembe, ha kezdettől fogva nagy lekérdezési kötetre számíthat.

A standard szintű csomagok replikákat és partíciókat is biztosíthatnak. A lekérdezési fordulat növeléséhez replikákat adhat hozzá a terheléselosztáshoz, vagy hozzáadhat partíciókat a párhuzamos feldolgozáshoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

Ha kezdettől fogva magas fenntartható lekérdezési köteteket vár, érdemes megfontolnia a magasabb színvonalú, nagyobb teljesítményű hardverek által támogatott szintet. Ezután offline állapotba helyezheti a partíciókat és a replikákat, vagy átválthat egy alacsonyabb szintű szolgáltatásra, ha ezek a lekérdezési kötetek nem történnek. A lekérdezési teljesítmény kiszámításával kapcsolatos további információkért lásd: [Azure Cognitive Search teljesítmény és optimalizálás](search-performance-optimization.md).

A tárolásra optimalizált csomagok nagy mennyiségű adatszámítási feladatokhoz hasznosak, és a lekérdezési késésre vonatkozó követelmények kevésbé fontosak lesznek. Továbbra is használjon további replikákat a terheléselosztáshoz és a párhuzamos feldolgozáshoz szükséges további partíciókhoz. Ezt követően a szolgáltatás üzembe helyezése után beállíthatja a teljesítményt.

**Szolgáltatási szintű szerződések**

Az ingyenes szint és az előzetes verzió funkcióit a [szolgáltatói szerződések (SLA-EK)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)nem fedik le. Minden számlázható szinten a SLA-kat akkor kell végrehajtani, ha elegendő redundancia van kiépítve a szolgáltatáshoz. Legalább két replikát kell megadnia a Query (olvasási) SLA-hoz. Három vagy több replikával kell rendelkeznie a lekérdezéshez és az indexeléshez (írható-olvasható) SLA-hoz. A partíciók száma nem befolyásolja a SLA-kat.

## <a name="tips-for-capacity-planning"></a>Tippek a kapacitás megtervezéséhez

+ Lekérdezések használatának engedélyezése a mérőszámok számára, valamint adatok gyűjtése használati minták köré (a lekérdezések munkaidőn kívüli, az indexelés a munkaidőn kívüli órákban). Ezekkel az információkkal tájékoztathatja a szolgáltatás kiépítési döntéseit. Bár az óránkénti vagy napi lépésszám nem praktikus, dinamikusan módosíthatja a partíciókat és az erőforrásokat a lekérdezési kötetek tervezett változásainak megfelelően. A nem tervezett, de tartós módosításokat is elvégezheti, ha a szintek elég sokáig tartanak a beavatkozáshoz.

+ Ne feledje, hogy a kiépítés alatti egyetlen hátránya, hogy előfordulhat, hogy le kell bontania egy szolgáltatást, ha a tényleges követelmények nagyobbak az előrejelzéseknél. A szolgáltatás megszakadásának elkerülése érdekében hozzon létre egy új szolgáltatást egy magasabb szintű szinten, és futtassa azt egymás mellett, amíg az összes alkalmazás és kérelem meg nem célozza az új végpontot.

## <a name="when-to-add-capacity"></a>Mikor lehet kapacitást felvenni

A szolgáltatás eredetileg egy partíciót és egy replikát tartalmazó minimális szintű erőforrásokat foglal le. A [kiválasztott](search-sku-tier.md) keret határozza meg a partíció méretét és sebességét, az egyes szintek pedig a különböző forgatókönyvekhez illeszkedő jellemzők körén vannak optimalizálva. Ha magasabb szintű szintet választ, előfordulhat, hogy kevesebb partícióra van szüksége, mint ha S1-et használ. Az egyik kérdés, amelyet saját irányított teszteléssel kell megválaszolnia, hogy egy nagyobb és drágább partíció jobb teljesítményt biztosít-e, mint két olcsóbb partíció az alacsonyabb szinten kiépített szolgáltatásokban.

Egyetlen szolgáltatásnak elegendő erőforrással kell rendelkeznie az összes számítási feladat (indexelés és lekérdezések) kezeléséhez. Egyik munkaterhelés sem fut a háttérben. Az indexelést ütemezhet olyan időpontokra, amikor a lekérdezési kérések természetesen ritkábbak, de a szolgáltatás más módon nem rangsorolja az egyik feladatot. Emellett egy bizonyos mennyiségű redundancia is kisimítja a lekérdezési teljesítményt, ha a szolgáltatások vagy a csomópontok belsőleg frissülnek.

Néhány útmutató a kapacitás hozzáadásának meghatározásához:

+ A szolgáltatói szerződés magas rendelkezésre állási feltételeinek teljesítése
+ A HTTP 503-hibák gyakorisága növekszik
+ Nagyméretű lekérdezési kötetek várhatók

Általános szabályként a keresési alkalmazások általában több replikát igényelnek, mint a partíciók, különösen akkor, ha a szolgáltatási műveletek a lekérdezési munkaterhelések szempontjából elfogultak. Minden replika az index egy példánya, amely lehetővé teszi, hogy a szolgáltatás a kérelmeket több példányon is terheléselosztással ossza meg. Az Azure Cognitive Search felügyeli az összes terheléselosztást és replikációt, és a szolgáltatáshoz lefoglalt replikák számát bármikor módosíthatja. Egy standard keresési szolgáltatásban legfeljebb 12 replikát, egy alapszintű keresési szolgáltatásban pedig 3 replikát foglalhat le. A replika kiosztása a [Azure Portal](search-create-service-portal.md) vagy egy programozott lehetőség közül lehet.

A közel valós idejű adatfrissítést igénylő alkalmazások kereséséhez a replikák több partícióra van szükségük. A partíciók hozzáadásával az írási/olvasási műveletek nagyobb számú számítási erőforráson keresztül terjednek ki. Emellett további lemezterületet biztosít a további indexek és dokumentumok tárolásához.

Végezetül a nagyobb indexek lekérése hosszabb időt vesz igénybe. Ezért előfordulhat, hogy a partíciók növekményes növekedésének a replikák kisebb, de arányos növekedése szükséges. A lekérdezések és a lekérdezési kötetek összetettsége azt eredményezi, hogy milyen gyorsan történik a lekérdezés végrehajtása.

> [!NOTE]
> További replikák vagy partíciók hozzáadásával növelheti a szolgáltatás futtatásának költségeit, és az eredmények rendezésének módjában enyhe eltéréseket vezethet be. Ügyeljen arra, hogy ellenőrizze a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/) , és Ismerje meg a további csomópontok hozzáadásának számlázási következményeit. Az [alábbi diagram](#chart) segítséget nyújt az adott konfigurációhoz szükséges keresési egységek számának kereszthivatkozásához. További információ arról, hogyan befolyásolják a további replikák a lekérdezés feldolgozását: [megrendelés eredményei](search-pagination-page-layout.md#ordering-results).

<a name="adjust-capacity"></a>

## <a name="add-or-reduce-replicas-and-partitions"></a>Replikák és partíciók hozzáadása vagy csökkentése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és válassza ki a keresési szolgáltatást.

1. A **Beállítások** területen nyissa meg a **skálázás** lapot a replikák és partíciók módosításához. 

   Az alábbi képernyőfelvételen egy replikával és partícióval kiépített alapszintű standard látható. A lenti képlet azt jelzi, hogy hány keresési egység van használatban (1). Ha az egység ára $100 (nem valós díj), akkor a szolgáltatás futtatásának havi költsége átlagosan $100.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Az aktuális értékeket mutató méretezési oldal" border="true":::

1. A csúszka használatával növelheti vagy csökkentheti a partíciók számát. A lenti képlet azt jelzi, hogy hány keresési egységet használ a rendszer. Kattintson a **Mentés** gombra.

   Ez a példa egy második replikát és partíciót helyez el. Figyelje meg a keresési egységek darabszámát; most négy, mert a számlázási képlet a replikák szorozza a partíciókkal (2 x 2). A kapacitás megkettőzése több, mint megduplázza a szolgáltatás futtatásának költségeit. Ha a keresési egység díja $100 volt, az új havi számla mostantól $400 lesz.

   Az egyes szintek aktuális egységenkénti költségeiért látogasson el a [díjszabási oldalra](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Replikák és partíciók hozzáadása" border="true":::

1. A mentést követően ellenőrizheti az értesítéseket a művelet sikerességének megerősítéséhez.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Módosítások mentése" border="true":::

   A kapacitás változása akár 15 perctől akár több óráig is eltarthat. A folyamat elindítása után nem lehet megszakítani a műveletet, és nincs valós idejű figyelés a replika és a partíciók beállításaihoz. A következő üzenet azonban továbbra is látható, amíg megváltozik.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Állapotjelző üzenet a portálon" border="true":::

> [!NOTE]
> A szolgáltatás üzembe helyezését követően nem lehet magasabb szintre frissíteni. Létre kell hoznia egy keresési szolgáltatást az új szinten, és újra kell töltenie az indexeket. A szolgáltatás kiépítési támogatásához tekintse meg az [Azure Cognitive Search szolgáltatás létrehozása a portálon](search-create-service-portal.md) című témakört.
>
> Emellett a partíciókat és a replikákat a szolgáltatás kizárólag és belsőleg kezeli. A processzor affinitása nem áll rendelkezésre, vagy a számítási feladatok egy adott csomóponthoz rendelhetők hozzá.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Partíció-és replika-kombinációk

Az alapszintű szolgáltatásoknak pontosan egy partíciójuk és legfeljebb három replikájuk lehet, legfeljebb három SUs esetén. Az egyetlen állítható erőforrás replika. A lekérdezésekben a magas rendelkezésre állás érdekében legalább két replikára van szükség.

A standard és a Storage-alapú optimalizált keresési szolgáltatások a következő típusú replikákat és partíciókat vehetik igénybe, amelyek a 36-SU határértékre érvényesek. 

|   | **1 partíció** | **2 partíció** | **3 partíció** | **4 partíció** | **6 partíció** | **12 partíció** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replika** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 replika** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 replika** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 replika** |4 SU |8 SU |12 SU |16 SU |24 SU |N/A |
| **5 replika** |5 SU |10 SU |15 SU |20 SU |30 SU |N/A |
| **6 replika** |6 SU |12 SU |18 SU |24 SU |36 SU |N/A |
| **12 replika** |12 SU |24 SU |36 SU |N.A. |N.A. |N.A. |

Az SUs, a díjszabás és a kapacitás részletes ismertetését az Azure webhelyén találja. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> A replikák és partíciók száma egyenletesen oszlik el a 12 (pontosabban, 1, 2, 3, 4, 6, 12). Ennek az az oka, hogy az Azure Cognitive Search az egyes indexeket 12 szegmensre osztja, így a partíciók között egyenlő arányban terjedhet. Ha például a szolgáltatás három partícióval rendelkezik, és létrehoz egy indexet, minden partíció az index négy szegmensét fogja tartalmazni. Az Azure Cognitive Search az indexek egy implementációs részletességgel rendelkeznek, amely a jövőbeli kiadásokban változhat. Bár a szám 12 ma, nem várható, hogy ez a szám mindig 12 lesz a jövőben.
>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Költségek kezelése](search-sku-manage-costs.md)