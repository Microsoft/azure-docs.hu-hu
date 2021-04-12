---
title: Teljesítménymérések
titleSuffix: Azure Cognitive Search
description: Ismerje meg az Azure Cognitive Search teljesítményét a különböző teljesítménnyel kapcsolatos teljesítménytesztek segítségével
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108315"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Az Azure Cognitive Search teljesítményének teljesítménytesztei

Az Azure Cognitive Search teljesítménye [számos tényezőtől](search-performance-tips.md) függ, többek között a keresési szolgáltatás méretétől és a küldendő lekérdezések típusaitól. A munkaterheléshez szükséges keresési szolgáltatás méretének becsléséhez több teljesítménytesztet is futtatunk a különböző keresési szolgáltatások és konfigurációk teljesítményének dokumentálására. Ezek a teljesítménytesztek semmilyen módon nem garantálják a szolgáltatás bizonyos teljesítménybeli teljesítményét, de a várható teljesítményről is hasznos segítséget nyújtanak.

A különböző használati esetek széles körének kihasználása érdekében két fő forgatókönyvre vonatkozó teljesítményteszteket futtatunk:

* **E-kereskedelmi keresés** – ez a teljesítményteszt egy valós e-kereskedelmi forgatókönyvet emulál, és az e-kereskedelmi vállalat északi [CDON](https://cdon.com)alapul.
* **Dokumentum keresése** – ez a forgatókönyv a [szemantikai tudományos](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/)dokumentumokból származó teljes szöveges dokumentumok kulcsszavas keresését foglalja magában. Ez egy tipikus dokumentum-keresési megoldást emulál.

Habár ezek a forgatókönyvek eltérő használati eseteket tükröznek, minden forgatókönyv eltérő, ezért mindig javasoljuk, hogy az egyes számítási feladatok teljesítményét tesztelje. A [JMeter használatával](https://github.com/Azure-Samples/azure-search-performance-testing) közzétettünk egy teljesítményteszt-megoldást, hogy hasonló teszteket futtasson a saját szolgáltatásán keresztül.

## <a name="testing-methodology"></a>Tesztelési módszer

Az Azure Cognitive Search teljesítményének teljesítménytesztét két különböző forgatókönyvhöz, valamint a replika/partíciós kombinációk teszteléséhez futtatjuk.

A referenciaértékek létrehozásához a következő módszert használták:

1. A teszt `X` másodpercenkénti lekérdezések másodpercenkénti számát (QPS) veszi igénybe 180 másodpercig. Ez általában 5 vagy 10 QPS.
2. QPS, majd `X` egy másik 180 másodpercig megnövelt és futott
3. 180 másodpercenként a teszt QPS nőtt, `X` amíg az átlagos késés meghaladja az 1000 MS-ot, vagy kevesebb, mint a lekérdezés 99%-a.

Az alábbi ábrán látható egy példa arra, hogy a teszt lekérdezési terhelése hogyan fog kinézni:

![Példa tesztre](./media/performance-benchmarks/example-test.png)

Az egyes forgatókönyvek legalább 10 000 egyedi lekérdezéssel használták, hogy a tesztek ne legyenek túlságosan elferdítve a gyorsítótárazással.

> [!IMPORTANT]
> Ezek a tesztek csak lekérdezési számítási feladatokat tartalmaznak. Ha várhatóan magas volumne kell lennie, ügyeljen arra, hogy a becslési és a teljesítmény-tesztelési folyamattal rendelkezzen. Ebben az [oktatóanyagban](tutorial-optimize-indexing-push-api.md)az indexelés szimulálása című mintakód is megtalálható.

### <a name="definitions"></a>Definíciók

- **Maximális QPS** – az alábbi maximális QPS-számok a tesztben elért legmagasabb QPS alapulnak, ahol a lekérdezések 99%-a a szabályozást követően sikeresen befejeződött, és az átlagos késés a 1000 MS alatt marad.

- Maximális **QPS százalékos aránya** – egy adott teszthez elért maximális QPS százaléka. Ha például egy adott teszt elérte a maximálisan 100 QPS, a maximális QPS 20%-a 20 QPS lenne.

- **Késés** – a kiszolgáló késése egy lekérdezés esetében; Ezek a számok nem tartalmazzák az oda [-és visszaút késleltetését (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). Az alábbi értékek ezredmásodpercben (MS) találhatók.

### <a name="disclaimer"></a>Disclaimer

Az ezen referenciaértékek futtatásához használt kód [itt](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools)érhető el. Érdemes megjegyezni, hogy a [JMeter teljesítmény-tesztelési megoldásával](https://github.com/Azure-Samples/azure-search-performance-testing) VALAMIVEL alacsonyabb QPS-szintet észlelt, mint az alábbi referenciaértékeknél. A különbségek a tesztek stílusa közötti különbségekre is jellemzőek lehetnek. Ez azt jelenti, hogy a teljesítmény-tesztek a lehető legnagyobb mértékben hasonlóak az éles számítási feladatokhoz.

Ezek a teljesítménytesztek semmilyen módon nem garantálják a szolgáltatás bizonyos teljesítménybeli teljesítményét, de a forgatókönyv alapján várható teljesítményről is hasznos képet kaphatnak.

Ha kérdése vagy problémája van, a következő címen juthat el hozzánk: azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>1. teljesítményteszt: E-kereskedelmi keresés

:::row:::
   :::column span="1":::
      ![CDON embléma](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Ez a viszonyítási alap az e-kereskedelmi vállalattal, a [CDON](https://cdon.com), az északi régió legnagyobb online piactérével, Svédországban, Finnországban, Norvégiában és Dániában végzett műveletekkel jött létre. A CDON a 1 500-es kereskedőkön keresztül széles választékot kínál, amely több mint 8 000 000 terméket tartalmaz. 2020-ben a CDON több mint 120 000 000 látogatót és 2 000 000 aktív ügyfelet vett igénybe. [Ebben a cikkben](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)többet is megtudhat az Azure Cognitive Search CDON használatáról.
   :::column-end:::
:::row-end:::

A tesztek futtatásához pillanatképet készítettünk a CDON éles keresési indexéről és a [webhelyükről](https://cdon.com)származó több ezer egyedi lekérdezésről.

### <a name="scenario-details"></a>Forgatókönyv részletei

- **Dokumentumok száma**: 6 000 000 
- **Index mérete**: 20 GB
- **Index Schema**: széles index 250 mezővel összesen, 25 kereshető mező és 200-es, szűrhető/szűrhető mező
- **Lekérdezési típusok**: teljes szöveges keresési lekérdezések, beleértve az aspektusokat, a szűrőket, a rendezési és pontozási profilokat

### <a name="s1-performance"></a>S1 teljesítmény

#### <a name="queries-per-second"></a>Másodpercenkénti lekérdezések

Az alábbi diagram a legmagasabb lekérdezési terhelést mutatja, amelyet a szolgáltatás a másodpercenkénti lekérdezések (QPS) alapján hosszabb ideig képes kezelni.

![A legmagasabb fenntartható QPS eCommerce S1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Lekérdezés késése

A lekérdezési késés a szolgáltatás és a magasabb terhelés alá eső szolgáltatások terhelése alapján változik, és a lekérdezési késés nagyobb lesz. Az alábbi táblázat a lekérdezés késésének 25, 50, 75th, 90, 95. és esetek 99% százalékát mutatja három különböző használati szinten.

| Maximális QPS százalékos aránya  | Átlagos késés | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50%  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| 80%  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>S2 teljesítmény

#### <a name="queries-per-second"></a>Másodpercenkénti lekérdezések

Az alábbi diagram a legmagasabb lekérdezési terhelést mutatja, amelyet a szolgáltatás a másodpercenkénti lekérdezések (QPS) alapján hosszabb ideig képes kezelni.

![Legmagasabb fenntartható QPS eCommerce S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Lekérdezés késése

A lekérdezési késés a szolgáltatás és a magasabb terhelés alá eső szolgáltatások terhelése alapján változik, és a lekérdezési késés nagyobb lesz. Az alábbi táblázat a lekérdezés késésének 25, 50, 75th, 90, 95. és esetek 99% százalékát mutatja három különböző használati szinten.

| Maximális QPS százalékos aránya  | Átlagos késés | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50%  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| 80%  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>S3 teljesítmény

#### <a name="queries-per-second"></a>Másodpercenkénti lekérdezések

Az alábbi diagram a legmagasabb lekérdezési terhelést mutatja, amelyet a szolgáltatás a másodpercenkénti lekérdezések (QPS) alapján hosszabb ideig képes kezelni.

![Legnagyobb fenntartható QPS eCommerce S3](./media/performance-benchmarks/s3-ecom-qps.png)

Ebben az esetben azt láthatjuk, hogy a második partíció hozzáadása jelentősen növeli a maximális QPS, de a harmadik partíció hozzáadása csökkenti a marginális hozamot. A kisebb javulás valószínűleg azért van, mert az összes adatmennyiség már csak két partícióval van behúzva a S3's aktív memóriába.

#### <a name="query-latency"></a>Lekérdezés késése

A lekérdezési késés a szolgáltatás és a magasabb terhelés alá eső szolgáltatások terhelése alapján változik, és a lekérdezési késés nagyobb lesz. Az alábbi táblázat a lekérdezés késésének 25, 50, 75th, 90, 95. és esetek 99% százalékát mutatja három különböző használati szinten.

| Maximális QPS százalékos aránya  | Átlagos késés | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 MS |
| 50%  | 62 MS  | 24 MS  | 80 MS   | 107 MS | 130 MS | 253 MS |
| 80%  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>2. teljesítményteszt: dokumentumok keresése

### <a name="scenario-details"></a>Forgatókönyv részletei

- **Dokumentumok száma**: 7 500 000
- **Index mérete**: 22 GB
- **Index séma**: 23 mező; 8 kereshető, 10 szűrhető/sokrétű
- **Lekérdezési típusok**: kulcsszavas keresések aspektusokkal és találatok kiemelésével

### <a name="s1-performance"></a>S1 teljesítmény

#### <a name="queries-per-second"></a>Másodpercenkénti lekérdezések

Az alábbi diagram a legmagasabb lekérdezési terhelést mutatja, amelyet a szolgáltatás a másodpercenkénti lekérdezések (QPS) alapján hosszabb ideig képes kezelni.

![Maximálisan karbantartható QPS doc Search S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Lekérdezés késése

A lekérdezési késés a szolgáltatás és a magasabb terhelés alá eső szolgáltatások terhelése alapján változik, és a lekérdezési késés nagyobb lesz. Az alábbi táblázat a lekérdezés késésének 25, 50, 75th, 90, 95. és esetek 99% százalékát mutatja három különböző használati szinten.

| Maximális QPS százalékos aránya  | Átlagos késés | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50%  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| 80%  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>S2 teljesítmény

#### <a name="queries-per-second"></a>Másodpercenkénti lekérdezések

Az alábbi diagram a legmagasabb lekérdezési terhelést mutatja, amelyet a szolgáltatás a másodpercenkénti lekérdezések (QPS) alapján hosszabb ideig képes kezelni.

![Maximálisan karbantartható QPS doc Search S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Lekérdezés késése

A lekérdezési késés a szolgáltatás és a magasabb terhelés alá eső szolgáltatások terhelése alapján változik, és a lekérdezési késés nagyobb lesz. Az alábbi táblázat a lekérdezés késésének 25, 50, 75th, 90, 95. és esetek 99% százalékát mutatja három különböző használati szinten.

| Maximális QPS százalékos aránya  | Átlagos késés | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50%  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| 80%  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Legfontosabb ismeretek

Ezekkel a referenciaértékekkel megismerheti a teljesítmény Azure Cognitive Search ajánlatait. A különböző szintű szolgáltatások közötti különbséget is láthatja.

Néhány kulcsfontosságú módszer a következők közül:

* Az S2 általában legalább négyszer képes a lekérdezési kötet S1-ként való kezelésére.
* Az S2 általában alacsonyabb késéssel fog rendelkezni, mint az S1-hez hasonló lekérdezési kötetek
* A replikák hozzáadásakor a QPS jellemzően lineárisan méretezhető (például ha egy replika 10 QPS tud kezelni, akkor az öt replika általában a 50 QPS-t tudja kezelni)
* Minél nagyobb a szolgáltatás terhelése, annál nagyobb az átlagos késés

Azt is láthatja, hogy a teljesítmény jelentősen változhat a forgatókönyvek között. Ha nem látja el a várt teljesítményt, tekintse meg a [jobb teljesítményre vonatkozó tippeket](search-performance-tips.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a teljesítménnyel kapcsolatos teljesítménymutatókat, többet tudhat meg arról, hogyan elemezheti Cognitive Search teljesítményét és a teljesítményét befolyásoló legfontosabb tényezőket.

> [!div class="nextstepaction"]
> [Teljesítmény elemzése](search-performance-analysis.md) 
>  [Tippek a jobb teljesítmény érdekében](search-performance-tips.md)