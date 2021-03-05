---
title: Szemantikus keresés
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan használja a Cognitive Search a mélyebb tanulási szemantikai keresési modelleket a Bingből, hogy a keresési eredmények intuitívabbak legyenek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203232"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Szemantikai keresés az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)mellett is elérhetők.

A szemantikai keresés a lekérdezésekkel kapcsolatos olyan funkciók gyűjteménye, amelyek támogatják a magasabb színvonalú, természetesebb lekérdezési élményt. A szolgáltatások közé tartozik a keresési eredmények szemantikai újrarangsorolása, valamint a feliratok és válaszok létrehozása szemantikai kiemeléssel. A [teljes szöveges keresőmotor](search-lucene-query-architecture.md) által visszaadott legfontosabb 50-eredményeket a rendszer a legfontosabb egyezések megkeresése érdekében újrarangsorolja.

Az alapul szolgáló technológia a Bing és a Microsoft Research által nyújtott beruházásokat használja, és integrálva van a Cognitive Search infrastruktúrába. A használatához kis módosításokra van szükség a keresési kérelemben, de nincs szükség további konfigurációra vagy újraindexelésre.

A nyilvános előzetes verzió funkciói a következők:

+ Szemantikai rangsorolási modell, amely az eredményeket a keresési lekérdezési feltételek kontextusa vagy szemantikai jelentése alapján szerzi be
+ A kapcsolódó szakaszokat kiemelő szemantikai feliratok
+ Szemantikai válaszok a lekérdezésre, az eredményekből is kialakítva
+ Helyesírás-ellenőrzés, amely kijavítani az elírásokat, mielőtt a lekérdezési kifejezés eléri a keresőmotort

## <a name="semantic-search-architecture"></a>Szemantikai keresési architektúra

A szemantikai keresés összetevői a meglévő lekérdezés-végrehajtási folyamat elejére kerülnek. A helyesírás-javítás (az ábrán nem látható) javítja az elírásokat az egyes lekérdezési feltételekben. Az összes elemzés és elemzés befejezése után a keresőmotor lekéri a lekérdezésnek megfelelő dokumentumokat, és az [alapértelmezett pontozási algoritmust](index-similarity-and-scoring.md#similarity-ranking-algorithms)(BM25 vagy klasszikus) használja, attól függően, hogy a szolgáltatás mikor lett létrehozva. Ezen a ponton a pontozási profilokat is alkalmazza a rendszer. 

A legfontosabb 50-as egyezések megérkezése után a [szemantikai rangsorolási algoritmus](semantic-how-to-query-response.md) újraértékeli a dokumentumot. Az eredmények több mint 50 egyezést tartalmazhatnak, de csak az első 50 lesz újra rangsorolva. A rangsoroláshoz az algoritmus a gépi tanulást és a tanulást is használja a dokumentumok újraértékeléséhez, attól függően, hogy milyen jól illeszkedik a lekérdezés szándéka.

A feliratok és válaszok létrehozásához használja a Language reprezentációs modelleket. A feliraton belül az algoritmus a Bing által fejlesztett nyelvi modellt használ a feliratok kinyeréséhez, és kiemeli a lekérdezés eredményeinek legjobb összegzését. Ha a keresési lekérdezés kérdése van, és a rendszer válaszokat kér, egy hasonló nyelvi modell azonosítja azokat a szöveges részeket, amelyek a keresési lekérdezés által kifejezett kérdésre leginkább választanak.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Szemantikai összetevők egy lekérdezési folyamatban" border="true":::

## <a name="availability-and-pricing"></a>Rendelkezésre állás és díjszabás

A szemantikai rangsorolás a [regisztrációs regisztráción](https://aka.ms/SemanticSearchPreviewSignup)keresztül érhető el a standard szintű (S1, S2, S3), a következő régiókban található keresési szolgáltatásokban: az USA északi középső régiója, USA nyugati régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa. A megadott régiókban az S1 vagy a magasabb szintű keresési szolgáltatás jogosult az előzetes verzióra (új szolgáltatás létrehozása nem szükséges).

A helyesírás-javítás ugyanabban a régióban érhető el, de nem rendelkezik a rétegek korlátozásával, és nincs szükség regisztrációra. 

Az előzetes indítás és a 2. március 1-től április 1-én a helyesírás-javítás és a szemantikai rangsorolás díjmentesen vehető igénybe. Április 1. után a funkció futtatásának számítási költségei számlázandó eseménynek számítanak. A várt díj körülbelül 500 USD, a 250 000-es lekérdezésekhez pedig a havi dollár. A részletes költségadatok a [Cognitive Search díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/) , valamint a [költségek becslése és kezelése](search-sku-manage-costs.md)című témakörben találhatók.

## <a name="next-steps"></a>Következő lépések

Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját. [Hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md) a kezdéshez. Vagy tekintse át a következő cikkek valamelyikét a kapcsolódó információkhoz.

+ [Helyesírás-ellenőrzés hozzáadása a lekérdezési feltételekhez](speller-how-to-add.md)
+ [Szemantikai rangsorolás és válaszok](semantic-how-to-query-response.md)