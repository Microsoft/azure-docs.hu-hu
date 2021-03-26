---
title: Szemantikai rangsorolás
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan működik a szemantikai rangsorolási algoritmus az Azure Cognitive Searchban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562035"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Szemantikai rangsorolás az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, az előzetes verziójú REST API és a portálon keresztül. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai rangsor a lekérdezés végrehajtási folyamatának kiterjesztése, amely javítja a pontosságot a kezdeti eredményhalmaz felső egyezésének újbóli kihelyezésével. A szemantikai rangsor a lekérdezési kifejezések szemantikai jelentésének begyűjtésére szolgáló nagyméretű transzformátor-alapú hálózatok által támogatott, a kulcsszavak nyelvi megfeleltetése helyett. Az [alapértelmezett hasonlósági rangsorolási algoritmussal](index-ranking-similarity.md)ellentétben a szemantikai rangsor a szavak kontextusát és jelentését használja a relevancia megállapításához.

A szemantikai rangsorolás erőforrás-és időigényes. A lekérdezési művelet várható késésén belüli feldolgozás befejezéséhez a szemantikai rangsorba való bemenetek konszolidálva és csökkentve lesznek, így a mögöttes összefoglaló és a visszahelyezési lépések a lehető leggyorsabban végezhetők el.

## <a name="pre-processing"></a>Előzetes feldolgozás

A relevancia megállapítása előtt a tartalmat olyan felügyelhető számú bemenetre kell csökkenteni, amely hatékonyan kezelhető a szemantikai rangsorban.

1. Először a tartalom csökkentése a kulcsszavas kereséshez használt alapértelmezett [hasonlósági rangsorolási algoritmus](index-ranking-similarity.md) által visszaadott kezdeti eredményekkel kezdődik. Bármely lekérdezés esetében az eredmény lehet egy maroknyi dokumentum, amely legfeljebb 1 000. Mivel a nagy számú egyezés feldolgozása túl hosszú időt vesz igénybe, csak a legfontosabb 50-as folyamat lesz a szemantikai rangsorolás.

   A dokumentumok számától függetlenül, hogy egy vagy 50, a kezdeti eredményhalmaz létrehozza-e a dokumentum első iterációját a szemantikai rangsoroláshoz.

1. Ezt követően a corpusban a "searchFields" egyes mezőinek tartalma kinyerve, és egy hosszú sztringbe van összevonva.

1. A karakterlánc-konszolidáció után a túlságosan hosszú karakterláncok kivágása megtörténik annak biztosítására, hogy a teljes hossz megfelel az összegzési lépés bemeneti követelményeinek.

   Ennek a kivágási gyakorlatnak az az oka, hogy fontos a tömör mezők elhelyezése a "searchFields" elemben, hogy a karakterlánc szerepeljen bennük. Ha nagyon nagy méretű, szöveggel ellátott mezőket tartalmazó dokumentumokkal rendelkezik, a rendszer figyelmen kívül hagyja a maximális korlátot.

Minden dokumentumot egyetlen hosszú sztring képvisel.

> [!NOTE]
> A karakterlánc jogkivonatokból, nem karakterekből vagy szavakból áll. A jogkivonatok létrehozása a kereshető mezőkön az analizátor-hozzárendelés részben határozható meg. Ha speciális elemzőt (például nGram vagy EdgeNGram) használ, érdemes lehet kizárni a mezőt a searchFields. A sztringek jogkivonatának vizsgálatához tekintse át az analizátor jogkivonat-kimenetét a [test analyzer REST API](/rest/api/searchservice/test-analyzer)használatával.

## <a name="extraction"></a>Kigyűjtés

A karakterláncok csökkentése után már lehetséges a csökkentett bemenetek átadása a gépi olvasási és nyelvi ábrázolási modelleken keresztül annak megállapításához, hogy mely mondatok és kifejezések a legjobban összefoglalják a dokumentumot, a lekérdezéshez képest. Ebben a fázisban a sztringből származó tartalmat kell kibontani, amely továbbítva lesz a szemantikai sorba.

Az összegzésbe való bemenetek az előkészítési fázisban az egyes dokumentumokhoz kapott hosszú karakterláncok. Az összefoglaló modell minden sztringből megtalál egy, a leginkább reprezentatív átjárót. Ez a szakasz a dokumentum [szemantikai feliratát](semantic-how-to-query-request.md) is jelenti. Mindegyik felirat egy egyszerű szöveges verzióban és egy kiemelési verzióban érhető el, és a dokumentumban gyakran kevesebb mint 200 szó van.

A "válaszok" paraméter megadásakor a rendszer a [szemantikai választ](semantic-answers.md) is visszaadja, ha a lekérdezést feltették kérdésként, és ha egy átjáró olyan hosszú karakterláncban található, amely valószínűleg választ ad a kérdésre.

## <a name="semantic-ranking"></a>Szemantikai rangsorolás

1. A feliratokat a rendszer a megadott lekérdezéshez képest elméleti és szemantikai jelentőséggel értékeli ki.

   A következő ábra a "szemantikai relevancia" fogalmát mutatja be. Vegye figyelembe a "Capital" kifejezést, amely pénzügyi, jogi, földrajzi vagy nyelvtani kontextusban használható. Ha egy lekérdezés azonos vektoros területből származó kifejezéseket tartalmaz (például "Capital" és "Investment"), akkor az ugyanabban a fürtben lévő jogkivonatokat is tartalmazó dokumentumok nagyobb pontszámot kapnak, mint az egyik, ami nem.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="A környezet vektoros ábrázolása" border="true":::

1. Az az @search.rerankerScore egyes dokumentumokhoz a felirat szemantikai relevanciája alapján van hozzárendelve.

1. Az összes dokumentum kiértékelése után a rendszer csökkenő sorrendben sorolja fel őket a lekérdezési válasz hasznos adatai között. A hasznos adatok között szerepelnek a válaszok, az egyszerű szöveg és a Kiemelt feliratok, valamint a beolvasható vagy a Select záradékban megadott mezők.

## <a name="next-steps"></a>Következő lépések

A szemantikai rangsorolást a standard szinteken, adott régiókban kínáljuk. A rendelkezésre állással és a regisztrációval kapcsolatos további információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing). Az új lekérdezési típus lehetővé teszi a szemantikai keresés rangsorolási és reagálási struktúráját. Első lépésként [hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md).

Másik megoldásként tekintse át az alapértelmezett rangsorolással kapcsolatos alábbi cikkeket. A szemantikai rangsorolás a hasonlósági sorrendtől függ a kezdeti eredmények visszaküldéséhez. A lekérdezés-végrehajtás és a rangsorolás megismerése a teljes folyamat működésének széles körű megismerését teszi lehetővé.

+ [Teljes szöveges keresés az Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Hasonlóság és pontozás az Azure Cognitive Search](index-similarity-and-scoring.md)
+ [Az Azure Cognitive Searchban való szövegszerkesztés elemzői](search-analyzers.md)