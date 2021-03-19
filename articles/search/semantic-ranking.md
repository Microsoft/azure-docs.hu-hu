---
title: Szemantikai rangsorolás
titleSuffix: Azure Cognitive Search
description: A Cognitive Search szemantikai rangsorolási algoritmusát ismerteti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604287"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Szemantikai rangsorolás az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai rangsor a lekérdezés végrehajtási folyamatának kiterjesztése, amely javítja a pontosságot, és visszahívja azt a kezdeti eredményhalmaz felső egyezésének átállításával. A szemantikai rangsorolást a legkorszerűbb mély gépi olvasási felolvasási modellek jelentik, amelyek természetes nyelven kifejezett lekérdezésekre vannak kialakítva, ellentétben a kulcsszavak nyelvi megfeleltetésével. Az [alapértelmezett hasonlósági rangsorolási algoritmussal](index-ranking-similarity.md)ellentétben a szemantikai rangsor a szavak kontextusát és jelentését használja a relevancia megállapításához.

## <a name="how-semantic-ranking-works"></a>A szemantikai rangsorolás működése

A szemantikai rangsorolás erőforrás-és időigényes is. A lekérdezési művelet várható késésén belüli feldolgozás befejezéséhez a modell csak az alapértelmezett [hasonlósági rangsorolási algoritmus](index-ranking-similarity.md)által visszaadott első 50-dokumentumot veszi át. A kezdeti rangsor eredményei több mint 50 egyezést tartalmazhatnak, de csak az első 50 lesz a szemantikai sorrendben. 

A szemantikai rangsoroláshoz a modell a gépi olvasást és a tanulást is használja a dokumentumok újbóli kiértékeléséhez, attól függően, hogy milyen jól illeszkedik a lekérdezés szándéka.

### <a name="preparation-passage-extraction-phase"></a>Előkészítési (átjáró-kinyerési) fázis

A kezdeti eredményekben található minden dokumentumhoz van egy, a fő részek azonosítására szolgáló kibontási gyakorlat. Ez egy leépítési gyakorlat, amely csökkenti a tartalmat egy gyorsan feldolgozható mennyiségre.

1. Minden 50-dokumentum esetében a searchFields paraméter minden mezője egymást követő sorrendben lesz kiértékelve. Az egyes mezők tartalma egyetlen hosszú sztringbe van összevonva. 

1. A hosszú karakterláncot ezután a rendszer kivágja, hogy a teljes hossz ne legyen több, mint 8 000 token. Ezért javasoljuk, hogy először a tömör mezőket helyezze el, hogy azok szerepeljenek a karakterláncban. Ha nagyon nagy méretű, szöveggel ellátott mezőket tartalmazó dokumentumokkal rendelkezik, a rendszer figyelmen kívül hagyja a jogkivonat-korlátot.

1. Minden dokumentumot egy olyan hosszú karakterlánc képvisel, amely legfeljebb 8 000 tokenből áll. Ezeket a karakterláncokat az összefoglaló modellbe küldi a rendszer, amely tovább csökkenti a karakterláncot. Az összefoglaló modell kiértékeli a hosszú karakterláncot a dokumentum legjobban összefoglaló mondatok vagy szövegrészek esetében, vagy válaszol a kérdésre.

1. A fázis kimenete egy felirat (és opcionálisan egy válasz). A felirat legfeljebb 128 tokent tartalmazó dokumentum, és a dokumentum legalkalmasabbnak számít.

### <a name="scoring-and-ranking-phases"></a>Pontozási és rangsorolási fázisok

Ebben a fázisban a rendszer az összes 50 feliratot kiértékeli a relevancia értékeléséhez.

1. A pontozás meghatározása az egyes feliratok elméleti és szemantikai relevanciára való kiértékelésével történik, a megadott lekérdezéshez képest.

   A következő ábra a "szemantikai relevancia" fogalmát mutatja be. Vegye figyelembe a "Capital" kifejezést, amely pénzügyi, jogi, földrajzi vagy nyelvtani kontextusban használható. Ha egy lekérdezés azonos vektoros területből származó kifejezéseket tartalmaz (például "Capital" és "Investment"), akkor az ugyanabban a fürtben lévő jogkivonatokat is tartalmazó dokumentumok nagyobb pontszámot kapnak, mint az egyik, ami nem.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="A környezet vektoros ábrázolása" border="true":::

1. A fázis kimenete az @search.rerankerScore egyes dokumentumokhoz van rendelve. Az összes dokumentum kiértékelése után a rendszer csökkenő sorrendben sorolja fel őket, és tartalmazza a lekérdezési válasz hasznos adatait.

## <a name="next-steps"></a>Következő lépések

A szemantikai rangsorolást a standard szinteken, adott régiókban kínáljuk. További információért és a regisztrációhoz tekintse meg a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing). Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját. Első lépésként [hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md).

Másik megoldásként tekintse át a következő cikkek valamelyikét a kapcsolódó információkhoz.

+ [Szemantikai keresés – áttekintés](semantic-search-overview.md)
+ [Szemantikai válasz visszaadása](semantic-answers.md)