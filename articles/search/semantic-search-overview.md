---
title: Szemantikus keresés
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan használja a Cognitive Search a mélyebb tanulási szemantikai keresési modelleket a Bingből, hogy a keresési eredmények intuitívabbak legyenek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432979"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Szemantikai keresés az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők.

A szemantikai keresés a lekérdezésekkel kapcsolatos olyan funkciók gyűjteménye, amelyek támogatják a magasabb színvonalú, természetesebb lekérdezési élményt. A szolgáltatások közé tartozik a keresési eredmények szemantikai újrarangsorolása, valamint a feliratok és válaszok létrehozása szemantikai kiemeléssel. A [teljes szöveges keresőmotor](search-lucene-query-architecture.md) által visszaadott legfontosabb 50-eredményeket a rendszer a legfontosabb egyezések megkeresése érdekében újrarangsorolja.

A mögöttes technológia a Bing és a Microsoft Research szolgáltatásból származik, és integrálva van a Cognitive Search infrastruktúrába. A szemantikai kereséssel kapcsolatos kutatásról és AI-befektetésekről további információt talál a [Bing által az Azure Cognitive Search (Microsoft Research blog) bekapcsolásával](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)foglalkozó témakörben.

Ha szemantikai keresést szeretne használni a lekérdezésekben, kisebb módosításokat kell végrehajtania a keresési kérelemben, de nincs szükség további konfigurációra vagy újraindexelésre.

A nyilvános előzetes verzió funkciói a következők:

+ Szemantikai rangsorolási modell, amely a kontextust vagy szemantikai jelentést használja a relevancia pontszámának kiszámításához
+ Szemantikai feliratok, amelyek az egyszerű keresés eredményét eredményezik a kulcsfontosságú részek összefoglalásához
+ Szemantikai válaszok a lekérdezésre, ha a lekérdezés kérdése
+ Szemantikai kiemelés, amely a legfontosabb kifejezésekre és kifejezésekre koncentrál
+ Helyesírás-ellenőrzés, amely kijavítani az elírásokat, mielőtt a lekérdezési kifejezés eléri a keresőmotort

## <a name="availability-and-pricing"></a>Rendelkezésre állás és díjszabás

A szemantikai rangsorolás a [regisztrációs regisztráción](https://aka.ms/SemanticSearchPreviewSignup)keresztül érhető el a standard szintű (S1, S2, S3), a következő régiókban található keresési szolgáltatásokban: az USA északi középső régiója, USA nyugati régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa. A helyesírás-javítás ugyanabban a régióban érhető el, de nem rendelkezik a rétegek korlátozásával. Ha rendelkezik olyan meglévő szolgáltatással, amely megfelel a rétegek és régiók feltételének, csak a regisztrációra van szükség.

Az előzetes indítás és a 2. március 1-től április 1-én a helyesírás-javítás és a szemantikai rangsorolás díjmentesen vehető igénybe. Április 1. után a funkció futtatásának számítási költségei számlázandó eseménynek számítanak. A várt díj körülbelül 500 USD, a 250 000-es lekérdezésekhez pedig a havi dollár. A részletes költségadatok a [Cognitive Search díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/) , valamint a [költségek becslése és kezelése](search-sku-manage-costs.md)című témakörben találhatók.

## <a name="semantic-search-architecture"></a>Szemantikai keresési architektúra

A szemantikai keresés összetevői a meglévő lekérdezés-végrehajtási folyamat elejére kerülnek. A helyesírás-javítás (az ábrán nem látható) javítja az elírásokat az egyes lekérdezési feltételekben. Az elemzés és az analízis befejezése után a keresőmotor lekéri a lekérdezésnek megfelelő dokumentumokat, és az [alapértelmezett pontozási algoritmust](index-similarity-and-scoring.md#similarity-ranking-algorithms)(BM25 vagy klasszikus) használja, attól függően, hogy mikor hozták létre a szolgáltatást. Ezen a ponton a pontozási profilokat is alkalmazza a rendszer.

A legfontosabb 50-as egyezések megérkezése után a [szemantikai rangsorolási modell](semantic-how-to-query-response.md) újraértékeli a dokumentumot. Az eredmények több mint 50 egyezést tartalmazhatnak, de csak az első 50 lesz újra rangsorolva. A rangsorban a modell a gépi tanulást és a tanulást is használja, hogy a dokumentumok milyen mértékben felelnek meg a lekérdezés szándékának.

A feliratok és válaszok létrehozásához a szemantikai keresés a nyelvi ábrázolás használatával kinyeri és kiemeli az eredményeket legjobban összefoglaló kulcsfontosságú szakaszokat. Ha a keresési lekérdezés kérdése van, és a rendszer válaszokat kér, a válasz egy olyan szöveges szövegrészt tartalmaz, amely a legjobb választ a kérdésre, ahogy azt a keresési lekérdezés is megadja.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Szemantikai összetevők egy lekérdezési folyamatban" border="true":::

## <a name="next-steps"></a>Következő lépések

Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját.

[Hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md) a kezdéshez. Vagy tekintse át a következő cikkek valamelyikét a kapcsolódó információkhoz.

+ [Helyesírás-ellenőrzés hozzáadása a lekérdezési feltételekhez](speller-how-to-add.md)
+ [Szemantikai rangsorolás és válaszok (válaszok és feliratok)](semantic-how-to-query-response.md)