---
title: Szemantikai rangsorolás
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan működik a szemantikai rangsorolási algoritmus az Azure Cognitive Searchban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720508"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Szemantikai rangsorolás az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai rangsor a lekérdezés végrehajtási folyamatának kiterjesztése, amely javítja a pontosságot, és visszahívja azt a kezdeti eredményhalmaz felső egyezésének átállításával. A szemantikai rangsorolást a legkorszerűbb gépi olvasási felolvasási modellek jelentik, amelyek természetes nyelven kifejezett lekérdezésekre vannak kialakítva, a kulcsszavak nyelvi megfeleltetése helyett. Az [alapértelmezett hasonlósági rangsorolási algoritmussal](index-ranking-similarity.md)ellentétben a szemantikai rangsor a szavak kontextusát és jelentését használja a relevancia megállapításához.

A szemantikai rangsorolás erőforrás-és időigényes. A lekérdezési művelet várható késésén belüli feldolgozás befejezéséhez a rendszer konszolidálja és egyszerűsíti a bemeneti adatokat, hogy az Összegzés és az elemzés a lehető leggyorsabban befejeződjön.

## <a name="preparation-for-semantic-ranking"></a>Szemantikai rangsorolás előkészítése

A relevancia megállapítása előtt a tartalmat olyan mennyiségű paraméterre kell csökkenteni, amelyet a szemantikai rangsor hatékonyan tud kezelni. A tartalom csökkentése a következő lépések sorát tartalmazza.

1. A tartalom csökkentése a kulcsszavas kereséshez használt alapértelmezett [hasonlósági rangsorolási algoritmus](index-ranking-similarity.md) által visszaadott kezdeti eredmények használatával kezdődik. A keresési eredmények akár 1 000 egyezést is tartalmazhatnak, de a szemantikai rangsorolás csak az első 50 feldolgozza. 

   A lekérdezés miatt a kezdeti eredmények sokkal kevesebbek, mint 50, attól függően, hogy hány egyezés található. A dokumentumok száma nélkül a kezdeti eredményhalmaz a dokumentum-Corpus a szemantikai rangsoroláshoz.

1. A Document corpusban a "searchFields" egyes mezőinek tartalma kinyerve, és egy hosszú karakterláncba van összevonva.

1. A túlságosan hosszú karakterláncok úgy vannak kimetszve, hogy a teljes hossz megfeleljen az összefoglaló modell bemeneti követelményeinek. Ennek a kivágási gyakorlatnak az az oka, hogy fontos a tömör mezők elhelyezése a "searchFields" elemben, hogy a karakterlánc szerepeljen bennük. Ha nagyon nagy méretű, szöveggel ellátott mezőket tartalmazó dokumentumokkal rendelkezik, a rendszer figyelmen kívül hagyja a maximális korlátot.

Minden dokumentumot egyetlen hosszú sztring képvisel.

> [!NOTE]
> A modellbe való bemenetek a tokenek nem karaktereket vagy szavakat tartalmazhatnak. A jogkivonatok létrehozása a kereshető mezőkön az analizátor-hozzárendelés részben határozható meg. A sztringek jogkivonatának vizsgálatához tekintse át az analizátor jogkivonat-kimenetét a [test analyzer REST API](/rest/api/searchservice/test-analyzer)használatával.
>
> Jelenleg ebben az előzetes verzióban a hosszú karakterláncok maximális száma 8 000 token lehet. Ha a keresés nem tud kiadni egy várt választ a dokumentum mélyén belül, a tartalom kivágásának ismerete segít megérteni, hogy miért. 

## <a name="summarization"></a>Összegzés

A karakterláncok csökkentése után már lehetséges a paraméterek átadása a gépi olvasással és a nyelvi ábrázolással annak meghatározására, hogy mely mondatok és kifejezések a legjobban összefoglalják a modellt, a lekérdezéshez képest.

Az összegzésbe való bemenetek az előkészítési fázisból származó hosszú karakterláncok. Ebből a bemenetből az összefoglaló modell kiértékeli a tartalmat, hogy megtalálja a leginkább reprezentatív szakaszokat.

A kimenet egy [szemantikai felirat](semantic-how-to-query-request.md), egyszerű szövegben és kiemeli. A felirat kisebb, mint a hosszú karakterlánc, amely általában kevesebb mint 200 szót tartalmaz a dokumentumban, és a dokumentum leginkább reprezentálja. 

A "válaszok" paraméter megadásakor a rendszer a [szemantikai választ](semantic-answers.md) is visszaadja, ha a lekérdezést feltették a kérdéses lekérdezésre, és ha a hosszú karakterláncban egy olyan szakasz található, amely kézenfekvő választ ad a kérdésre.

## <a name="scoring-and-ranking"></a>Pontozás és rangsorolás

Ezen a ponton már minden dokumentumhoz feliratok tartoznak. A rendszer kiértékeli a feliratokat a lekérdezés szempontjából.

1. A pontozás meghatározása az egyes feliratok elméleti és szemantikai relevanciára való kiértékelésével történik, a megadott lekérdezéshez képest.

   A következő ábra a "szemantikai relevancia" fogalmát mutatja be. Vegye figyelembe a "Capital" kifejezést, amely pénzügyi, jogi, földrajzi vagy nyelvtani kontextusban használható. Ha egy lekérdezés azonos vektoros területből származó kifejezéseket tartalmaz (például "Capital" és "Investment"), akkor az ugyanabban a fürtben lévő jogkivonatokat is tartalmazó dokumentumok nagyobb pontszámot kapnak, mint az egyik, ami nem.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="A környezet vektoros ábrázolása" border="true":::

1. A fázis kimenete az @search.rerankerScore egyes dokumentumokhoz van rendelve. Az összes dokumentum kiértékelése után a rendszer csökkenő sorrendben sorolja fel őket, és tartalmazza a lekérdezési válasz hasznos adatait. A hasznos adatok között szerepelnek a válaszok, az egyszerű szöveg és a Kiemelt feliratok, valamint a beolvasható vagy a Select záradékban megadott mezők.

## <a name="next-steps"></a>Következő lépések

A szemantikai rangsorolást a standard szinteken, adott régiókban kínáljuk. Az elérhető és a regisztrációval kapcsolatos további információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing). Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját. Első lépésként [hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md).

Másik megoldásként tekintse át az alapértelmezett rangsorolással kapcsolatos alábbi cikkeket. A szemantikai rangsorolás a hasonlósági sorrendtől függ a kezdeti eredmények visszaküldéséhez. A lekérdezés-végrehajtás és a rangsorolás megismerése a teljes folyamat működésének széles körű megismerését teszi lehetővé.

+ [Teljes szöveges keresés az Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Hasonlóság és pontozás az Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Az Azure Cognitive Searchban való szövegszerkesztés elemzői](search-analyzers.md)