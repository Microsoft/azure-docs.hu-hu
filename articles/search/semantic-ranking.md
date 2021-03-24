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
ms.openlocfilehash: c3a0a8bd5805757b92e3f5b046335c8883b4ba72
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888923"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Szemantikai rangsorolás az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, az előzetes verziójú REST API és a portálon keresztül. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai rangsor a lekérdezés végrehajtási folyamatának kiterjesztése, amely javítja a pontosságot a kezdeti eredményhalmaz felső egyezésének újbóli kihelyezésével. A szemantikai rangsor a lekérdezési kifejezések szemantikai jelentésének begyűjtésére szolgáló nagyméretű transzformátor-alapú hálózatok által támogatott, a kulcsszavak nyelvi megfeleltetése helyett. Az [alapértelmezett hasonlósági rangsorolási algoritmussal](index-ranking-similarity.md)ellentétben a szemantikai rangsor a szavak kontextusát és jelentését használja a relevancia megállapításához.

A szemantikai rangsorolás erőforrás-és időigényes. A lekérdezési művelet várható késésén belüli feldolgozás befejezéséhez a szemantikai rangsorba való bemenetek konszolidálva és csökkentve lesznek, így a mögöttes összefoglaló és a visszahelyezési lépések a lehető leggyorsabban végezhetők el.

## <a name="preparation-for-semantic-ranking"></a>Szemantikai rangsorolás előkészítése

A relevancia megállapítása előtt a tartalmat több olyan bemenetre kell csökkenteni, amelyek hatékonyan kezelhetők a szemantikai rangsorban. A tartalom csökkentése a következő lépések sorát tartalmazza.

1. A tartalom csökkentése a kulcsszavas kereséshez használt alapértelmezett [hasonlósági rangsorolási algoritmus](index-ranking-similarity.md) által visszaadott kezdeti eredményhalmaz használatával kezdődik. A keresési eredmények akár 1 000 egyezést is tartalmazhatnak, de a szemantikai rangsorolás csak az első 50 feldolgozza. 

   A lekérdezés miatt a kezdeti eredmények sokkal kevesebbek, mint 50, attól függően, hogy hány egyezés található. A dokumentumok száma nélkül a kezdeti eredményhalmaz a dokumentum-Corpus a szemantikai rangsoroláshoz.

1. A Document corpusban a "searchFields" egyes mezőinek tartalma kinyerve, és egy hosszú karakterláncba van összevonva.

1. A túlságosan hosszú karakterláncok úgy vannak kimetszve, hogy a teljes hossz megfeleljen az összefoglalás lépésének bemeneti követelményeinek. Ennek a kivágási gyakorlatnak az az oka, hogy fontos a tömör mezők elhelyezése a "searchFields" elemben, hogy a karakterlánc szerepeljen bennük. Ha nagyon nagy méretű, szöveggel ellátott mezőket tartalmazó dokumentumokkal rendelkezik, a rendszer figyelmen kívül hagyja a maximális korlátot.

Minden dokumentumot egyetlen hosszú sztring képvisel.

> [!NOTE]
> A modellekhez tartozó paraméterek a tokenek, nem karakterek vagy szavak. A jogkivonatok létrehozása a kereshető mezőkön az analizátor-hozzárendelés részben határozható meg. A sztringek jogkivonatának vizsgálatához tekintse át az analizátor jogkivonat-kimenetét a [test analyzer REST API](/rest/api/searchservice/test-analyzer)használatával.

## <a name="summarization"></a>Összegzés

A karakterláncok csökkentése után már lehetséges a csökkentett bemenetek átadása a gépi olvasási és nyelvi ábrázolási modelleken keresztül annak megállapításához, hogy mely mondatok és kifejezések a legjobban összefoglalják a dokumentumot, a lekérdezéshez képest.

Az összegzésbe való bemenetek az előkészítési fázisban az egyes dokumentumokhoz beszerzett hosszú karakterláncok. Egy adott bemenetből az összefoglaló modell megkeres egy, a megfelelő dokumentumot legjobban jelképező áthaladási modellt. Ez a szakasz a dokumentum [szemantikai feliratát](semantic-how-to-query-request.md) is jelenti. Az egyes feliratok egyszerű szövegben érhetők el, és a kiemelések és a dokumentumok száma kevesebb, mint 200 szó.

A "válaszok" paraméter megadásakor a rendszer a [szemantikai választ](semantic-answers.md) is visszaadja, ha a lekérdezést feltették kérdésként, és ha egy átjáró olyan hosszú karakterláncban található, amely valószínűleg választ ad a kérdésre.

## <a name="scoring-and-ranking"></a>Pontozás és rangsorolás

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