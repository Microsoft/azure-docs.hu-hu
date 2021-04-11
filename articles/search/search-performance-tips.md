---
title: Teljesítménnyel kapcsolatos tippek
titleSuffix: Azure Cognitive Search
description: Ismerkedjen meg a keresési szolgáltatás teljesítményének maximalizálásához szükséges tippekkel és ajánlott eljárásokkal.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582265"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Tippek az Azure-Cognitive Search jobb teljesítményéhez

Ez a cikk tippek és ajánlott eljárások gyűjteménye, amelyeket gyakran ajánlunk a teljesítmény növelése érdekében. Annak ismerete, hogy mely tényezők várhatóan befolyásolják a keresési teljesítményt, segít elkerülni a hatékonyságot, és a legtöbbet kihasználhatja a keresési szolgáltatásból. A legfontosabb tényezők a következők:

+ Index összetétele (séma és méret)
+ Lekérdezéstípusok
+ A szolgáltatás kapacitása (a szintet, a replikák és a partíciók számát)

## <a name="index-size-and-schema"></a>Index mérete és sémája

A lekérdezések gyorsabban futnak kisebb indexeken. Ez részben annak a funkciója, hogy kevesebb mezőt kell beolvasni, de azt is, hogy a rendszer hogyan gyorsítótárazza a tartalmat a jövőbeli lekérdezésekhez. Az első lekérdezés után egyes tartalmak a memóriában maradnak, ahol a keresés hatékonyabban történik. Mivel az index mérete általában az idő múlásával nő, az egyik legjobb megoldás az index-összeállítás, a séma és a dokumentumok rendszeres újralátogatása a tartalom-csökkentési lehetőségek kereséséhez. Ha azonban az index megfelelő méretű, az egyetlen további kalibrálás, amely a kapacitás növelését teszi elérhetővé: vagy [replikák hozzáadásával](search-capacity-planning.md#adjust-capacity) vagy a szolgáltatási réteg frissítésével. A (z) ["Tipp: standard S2 szintre való frissítés"]] (#tip-upgrade-to-a-standard-S2-réteg) című szakasz bemutatja, hogyan értékelheti ki a vertikális felskálázást és a vertikális Felskálázási döntést.

A séma összetettsége hátrányosan befolyásolhatja az indexelést és a lekérdezési teljesítményt is. A túlzott mező-hozzárendelés korlátozásokat és feldolgozási követelményeket eredményez. Az [összetett típusok](search-howto-complex-data-types.md) az index és a lekérdezés számára hosszabb időt vesznek igénybe. A következő néhány szakaszban megismerheti az egyes feltételeket.

### <a name="tip-be-selective-in-field-attribution"></a>Tipp: szelektívnek kell lennie a mező-hozzárendelésben

Gyakori hiba, hogy a rendszergazdák és a fejlesztők a keresési index létrehozásakor kijelölik a mezők összes elérhető tulajdonságát, és nem csak a szükséges tulajdonságokat választják. Ha például egy mezőnek nem kell teljes szöveges kereshetőnek lennie, hagyja ki ezt a mezőt a kereshető attribútum beállításakor.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Szelektív hozzárendelés" border="true":::

A szűrők, a dimenziók és a rendezés támogatása négyágyas tárolási követelményeket is igénybe vehet. Ha javaslatokat ad hozzá, a tárolási követelmények még többet is felvehetnek. Az attribútumok tárolásra gyakorolt hatásával kapcsolatos illusztráció: [attribútumok és index mérete](search-what-is-an-index.md#index-size).

Összefoglalva, a túlterhelés következményei a következők:

+ Az indexelési teljesítmény romlása a mező tartalmának feldolgozásához szükséges további munka miatt, majd a keresés fordított indexben való tárolása (a "kereshető" attribútum csak kereshető tartalmat tartalmazó mezőkön állítható be).

+ Egy nagyobb felületet hoz létre, amelyet minden lekérdezésnek ki kell terjednie. A kereshetőként megjelölt mezők teljes szöveges kereséssel lesznek beolvasva.

+ A további tárhely miatt növeli a működési költségeket. A szűrés és a rendezés további helyet igényel az eredeti (nem elemzett) karakterláncok tárolásához. Kerülje a szűrhető vagy rendezhető beállítást olyan mezőknél, amelyeknek nincs rá szüksége.

+ Sok esetben a több mint jóváírás korlátozza a mező képességeit. Ha például egy mező látható, szűrhető és kereshető, akkor csak 16 KB-nyi szöveget tárolhat egy mezőn belül, míg egy kereshető mező legfeljebb 16 MB szöveget tud tárolni.

> [!NOTE]
> Csak a szükségtelen hozzárendelést kell elkerülni. A szűrők és az aspektusok gyakran elengedhetetlenek a keresési élményhez, és a szűrők használatakor gyakran szükség van a rendezésre, hogy az eredményeket (a szűrők önmagukban egy rendezetlen készletben is visszatérhetnek).

### <a name="tip-consider-alternatives-to-complex-types"></a>Tipp: az összetett típusok alternatíváinak figyelembevétele

Az összetett adattípusok akkor hasznosak, ha az adat bonyolult beágyazott szerkezettel rendelkezik, például a JSON-dokumentumokban található szülő-gyermek elemek. Az összetett típusok hátránya a további tárolási követelmények és a tartalom indexeléséhez szükséges további erőforrások, a nem összetett adattípusokkal összehasonlítva. 

Bizonyos esetekben elkerülheti ezeket a kompromisszumokat azáltal, hogy összetett adatstruktúrát rendel egy egyszerűbb mezőtípus-típushoz, például egy gyűjteményhez. Azt is megteheti, hogy egy mező-hierarchia összeolvasztását választja az egyes gyökérszintű mezőkben.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="lapított mező szerkezete" border="true":::

## <a name="types-of-queries"></a>A lekérdezések típusai

A küldött lekérdezések típusai az egyik legfontosabb tényező a teljesítmény szempontjából, és a lekérdezés optimalizálása jelentősen növelheti a teljesítményt. A lekérdezések tervezésekor a következő szempontokat érdemes meggondolni:

+ **Kereshető mezők száma** Minden további kereshető mezőhöz a keresési szolgáltatás további munkát igényel. A lekérdezési időszakban a "searchFields" paraméter használatával korlátozhatja a keresett mezőket. A legjobb megoldás, ha csak azokat a mezőket kell megadnia, amelyeket érdekel a teljesítmény javítása érdekében.

+ **A visszaadott adatmennyiség.** Sok tartalom lekérése lassabban végezheti a lekérdezéseket. Egy lekérdezés strukturálásakor csak azokat a mezőket adja vissza, amelyekre szüksége van az eredmények oldal megjelenítéséhez, majd a [Keresés API](/rest/api/searchservice/lookup-document) -val lekéri a fennmaradó mezőket, ha a felhasználó egy egyezést választ ki.

+ **A részleges kifejezéses keresések használata.** A [részleges](search-query-partial-matching.md)keresések, például az előtag-keresés, a fuzzy keresés és a reguláris kifejezéses keresés a leggyakoribb kulcsszavas keresések esetében több számítási feltételt eredményeznek, mivel a teljes index-ellenőrzéseket igényelnek.

+ **A dimenziók száma.** A lekérdezésekhez tartozó dimenziók hozzáadásához az egyes lekérdezések összesítései szükségesek. Általánosságban elmondható, hogy csak az alkalmazásban megjeleníteni kívánt aspektusokat adja hozzá.

+ **Korlátozza a magas kardinális mezőket.**  A *magas kardinális mező* egy olyan, jól látható vagy szűrhető mezőre hivatkozik, amely jelentős számú egyedi értékkel rendelkezik, és ennek eredményeképpen jelentős erőforrásokat használ a számítási eredmények alapján. Például ha a termék-azonosító vagy a Leírás mezőt felhasználhatóként szeretné beállítani, és a szűrésre használható, akkor a dokumentum és a dokumentum közötti értékek többsége egyedinek számít.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Tipp: keresési függvények használata a szűrési feltételek túlterhelése helyett

Mivel a lekérdezés egyre [összetettebb szűrési feltételeket](search-query-odata-filter.md#filter-size-limitations)használ, a keresési lekérdezés teljesítménye csökken. Vegye figyelembe a következő példát, amely bemutatja a szűrők használatát az eredmények felhasználói identitás alapján történő körülvágásához:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

Ebben az esetben a szűrési kifejezések segítségével ellenőrizhető, hogy az egyes dokumentumokban lévő egyetlen mező egyenlő-e a felhasználói identitás számos lehetséges értékével. Ennek a mintának a legvalószínűbb a [biztonsági körülvágást](search-security-trimming-for-azure-search.md) megvalósító alkalmazásokban való megkeresése (egy vagy több egyszerű azonosítót tartalmazó mező ellenőrzése a lekérdezést kiállító felhasználót jelölő egyszerű azonosítók listáján).

A nagy mennyiségű értéket tartalmazó szűrők végrehajtásának hatékonyabb módja a [ `search.in` függvény](search-query-odata-search-in-function.md)használata, ahogy az ebben a példában is látható:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Tipp: partíciók hozzáadása lassú egyéni lekérdezésekhez

Ha a lekérdezés teljesítménye általában lelassul, a további replikák hozzáadása gyakran megoldja a problémát. De mi történik, ha a probléma egy olyan lekérdezés, amely túl sokáig tart? Ebben a forgatókönyvben a replikák hozzáadása nem segít, de előfordulhat, hogy további partíciók is lehetnek. A partíciók feldarabolják az adategységeket A további számítástechnikai erőforrások között. A két partíció az adatmegosztást fél, a harmadik partíció pedig háromra osztja szét, és így tovább. 

A partíciók hozzáadásának egyik pozitív mellékhatása, hogy a lassabb lekérdezések időnként gyorsabban futnak párhuzamos számítástechnika miatt. Megjegyezték, hogy az alacsony szelektivitású lekérdezések, például a sok dokumentumnak megfelelő lekérdezések, illetve a nagy számú dokumentumra vonatkozó párhuzamos. Mivel a dokumentumok relevanciájának kiszámításához, illetve a dokumentumok számának megszámlálásához jelentős számítási követelmény szükséges, a további partíciók hozzáadásával a lekérdezések gyorsabban elvégezhető.  

Partíciók hozzáadásához használja a [Azure Portal](search-create-service-portal.md), a [PowerShell](search-manage-powershell.md), az [Azure CLI](search-manage-azure-cli.md)vagy egy felügyeleti SDK-t.

## <a name="service-capacity"></a>Szolgáltatás kapacitása

A szolgáltatás túlterhelt, ha a lekérdezések túl sokáig tartanak, vagy amikor a szolgáltatás elkezdi a kérelmek eldobását. Ha ez történik, a probléma megoldásához frissítse a szolgáltatást, vagy bővítse a kapacitást.

A keresési szolgáltatás szintje és a replikák/partíciók száma is nagy hatással van a teljesítményre. Minden magasabb szinten gyorsabb processzorok és több memória áll rendelkezésre, amelyek közül mindkettő pozitív hatással van a teljesítményre.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Tipp: verziófrissítés standard S2 szintre

A standard S1 szintű keresési szint gyakran az ügyfelek elindítására szolgál. Az S1-szolgáltatások általános mintája, hogy az indexek az idő múlásával növekednek, és több partíciót igényelnek. A további partíciók lassabban reagálnak, így több replikát adnak hozzá a lekérdezési terhelés kezeléséhez. Az S1 szolgáltatás futtatásának költségeit mostantól a kezdeti konfiguráción túli szinteken haladhatja meg.

Ebben az esetben fontos feltenni a kérdést, hogy érdemes-e magasabb szintűre költözni, szemben az aktuális szolgáltatás partícióinak vagy replikáinak fokozatos növelésével. 

Vegye figyelembe a következő topológiát példaként egy olyan szolgáltatásra, amely a kapacitás növelésére tett kísérletet:

+ Standard S1 szint
+ Index mérete: 190 GB
+ Partíciók száma: 8 (S1 esetén a partíció mérete 25 GB/partíció)
+ Replika száma: 2
+ Összes keresési egység: 16 (8 partíció x 2 replika)
+ Feltételezett kiskereskedelmi díj: ~ $4 000 USD/hó (feltételezhető, $250 USD x 16 keresési egység)

Tegyük fel, hogy a szolgáltatás rendszergazdája továbbra is magasabb késési arányt lát, és egy újabb replika hozzáadását fontolgatja. Ez megváltoztatja a replika 2 és 3 közötti számát, és ennek eredményeképpen a keresési egység számát 24-re változtatja, és az eredményül kapott $6 000 USD/hó értékű.

Ha azonban a rendszergazda úgy döntött, hogy szabványos S2-szintet helyez át, a topológia a következőképpen fog kinézni:

+ Standard S2 szint
+ Index mérete: 190 GB
+ Partíciók száma: 2 (S2-ben, partíció mérete 100 GB/partíció)
+ Replika száma: 2
+ Összes keresési egység: 4 (2 partíció x 2 replika)
+ Feltételezett kiskereskedelmi ár: ~ $4 000 USD/hónap ($1000 USD x 4 keresési egység)

Mivel ez a feltételezett forgatókönyv szemlélteti, az alacsonyabb szinteken olyan konfigurációk is lehetnek, amelyek hasonló költségeket eredményeznek, mintha az első helyen magasabb szintet választott volna. A magasabb szintű csomag azonban a Premium Storage szolgáltatással érhető el, ami gyorsabb indexelést tesz lehetővé. A magasabb szintű kapacitás sokkal nagyobb számítási teljesítményt és extra memóriát is tartalmaz. Ugyanezen költségek mellett nagyobb teljesítményű infrastruktúrával is rendelkezhet, mint az index.

A hozzáadott memória jelentős előnye, hogy az index több gyorsítótárban is elérhető, ami alacsonyabb keresési késést és másodpercenként több lekérdezést eredményez. Ennél az extra teljesítménynél előfordulhat, hogy a rendszergazdának nem kell még a replikák számának növeléséhez, és az S1 szolgáltatásban maradva kevesebbet kell fizetnie.

## <a name="next-steps"></a>Következő lépések

Tekintse át a szolgáltatás teljesítményével kapcsolatos további cikkeket.

+ [Teljesítményelemzés](search-performance-analysis.md)
+ [Szolgáltatási szintek kiválasztása](search-sku-tier.md)
+ [Kapacitás (replikák és partíciók) hozzáadása](search-capacity-planning.md#adjust-capacity)
