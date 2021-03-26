---
title: Szemantikus keresés
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan használja a Cognitive Search a mélyebb tanulási szemantikai keresési modelleket a Bingből, hogy a keresési eredmények intuitívabbak legyenek.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: b5b33007f71cfc2a29005ce84f1fedba90dd1bf1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561967"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Szemantikai keresés az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresés nyilvános előzetes verzióban érhető el, az előzetes verziójú REST API és a portálon keresztül. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. Ezek a funkciók számlázva vannak. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai keresés olyan lekérdezésekkel kapcsolatos képességek gyűjteménye, amelyek szemantikai relevanciát és nyelvi megértést biztosítanak a keresési eredményekhez. A *szemantikai rangsorolás* a feltételek és a velük kapcsolatos viszonyok között úgy néz ki, hogy a lekérdezés több értelmet biztosít. A Language Understanding olyan *feliratokat* és *válaszokat* talál a tartalomon belül, amelyek összefoglalják a megfelelő dokumentumot, vagy megválaszolnak egy adott kérdést, amely a keresési eredmények oldalon hatékonyabb keresési élményt biztosít.

A korszerű, előre betanított modellek az összegzéshez és rangsoroláshoz használatosak. A felhasználók által a keresésből várt gyors teljesítmény fenntartása érdekében a szemantikai összefoglalást és a rangsorolást csak az első 50 eredményre alkalmazza a rendszer, az [alapértelmezett hasonlósági pontozási algoritmus](index-similarity-and-scoring.md#similarity-ranking-algorithms)alapján. Ha ezeket az eredményeket a dokumentum-corpusként használja, a szemantikai rangsorolás a egyezés szemantikai erőssége alapján újra kiértékeli az eredményeket.

A mögöttes technológia a Bing és a Microsoft Research szolgáltatásból származik, és a Cognitive Search-infrastruktúrába integráltan vehető igénybe. A szemantikai kereséssel kapcsolatos kutatásról és AI-befektetésekről további információt talál a [Bing által az Azure Cognitive Search (Microsoft Research blog) bekapcsolásával](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)foglalkozó témakörben.

A következő videó áttekintést nyújt a képességekről.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Összetevők és munkafolyamat

A szemantikai keresés javítja a pontosságot, és felidézi a következő képességek hozzáadásával:

| Szolgáltatás | Leírás |
|---------|-------------|
| [Helyesírás-ellenőrzés](speller-how-to-add.md) | Kijavítani az elírásokat, mielőtt a lekérdezési kifejezés eléri a keresőmotort. |
| [Szemantikai rangsorolás](semantic-ranking.md) | A környezeti vagy szemantikai jelentést használja az új relevanciás pontszám kiszámításához. |
| [Szemantikai feliratok és kiemelések](semantic-how-to-query-request.md) | Mondatok és kifejezések egy olyan dokumentumból, amely a legjobban összefoglalja a tartalmat, és kiemeli a legfontosabb szakaszokat az egyszerű vizsgálathoz. Az eredményeket összegző feliratok akkor hasznosak, ha az egyes tartalom-mezők túl sűrűk az eredmények lapon. A Kiemelt szöveg megemeli a legfontosabb feltételeket és kifejezéseket, így a felhasználók gyorsan meghatározhatják, hogy a rendszer miért tekintse meg a megfelelőt. |
| [Szemantikai válaszok](semantic-answers.md) | Egy szemantikai lekérdezés által visszaadott opcionális és további alstruktúra. Közvetlen választ biztosít egy olyan lekérdezésre, amely egy adott kérdésre hasonlít. |

### <a name="order-of-operations"></a>Műveletek sorrendje

A szemantikai keresés összetevői mindkét irányban kiterjesztik a meglévő lekérdezés-végrehajtási folyamatot. Ha engedélyezi a helyesírás-javítást, a helyesírás-ellenőrző [kifejti](speller-how-to-add.md) az elírásokat a lekérdezés kialakulásakor, mielőtt a feltételek elérnék a keresőmotort.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Szemantikai összetevők a lekérdezés-végrehajtásban" border="true":::

A lekérdezés végrehajtása a szokásos módon történik, és a kifejezés elemzése, elemzése és vizsgálata a fordított indexeken keresztül történik. A motor a token egyeztetésével kérdezi le a dokumentumokat, és az eredményeket az [alapértelmezett hasonlósági pontozási algoritmus](index-similarity-and-scoring.md#similarity-ranking-algorithms)használatával szerzi be. A pontszámok kiszámítása a lekérdezési kifejezések és az indexhez tartozó feltételek nyelvi hasonlóságának mértéke alapján történik. Ha meghatározta őket, a pontozási profilok is ebben a fázisban lesznek alkalmazva. Ezután a rendszer átadja az eredményeket a szemantikai keresési alrendszernek.

Az előkészítési lépésben a kezdeti eredményhalmaz által visszaadott dokumentumot a rendszer a mondat és a bekezdés szintjén elemzi, hogy megkeresse az egyes dokumentumokat összefoglaló szakaszokat. A kulcsszavas kereséssel ellentétben ez a lépés a gép olvasását és megértését használja a tartalom kiértékeléséhez. A Content Processing ezen szakaszában a szemantikai lekérdezés a [feliratokat](semantic-how-to-query-request.md) és a [válaszokat](semantic-answers.md)adja vissza. A szemantikai keresés a nyelvi ábrázolás használatával kinyeri és kiemeli azokat a kulcsfontosságú részeket, amelyek a legjobban összefoglalják az eredményeket. Ha a keresési lekérdezés egy kérdés-és választ kér – a válasz egy olyan szöveges szövegrészt is tartalmaz, amely a legjobb választ a kérdésre, ahogyan azt a keresési lekérdezés is megadja. 

Mindkét felirat és válasz esetében a rendszer a meglévő szöveget használja az összeállításban. A szemantikai modellek nem állítanak össze új mondatokat vagy kifejezéseket a rendelkezésre álló tartalomból, és nem alkalmaznak logikát új következtetések megérkezésére. Röviden, a rendszer soha nem ad vissza tartalmat, amely még nem létezik.

Az eredményeket a rendszer a lekérdezési feltételek [fogalmi hasonlósága](semantic-ranking.md) alapján újra kiértékeli.

Ha szemantikai képességeket szeretne használni a lekérdezésekben, kisebb módosításokat kell végrehajtania a [keresési kérelemben](semantic-how-to-query-request.md), de nincs szükség további konfigurálásra vagy újraindexelésre.

## <a name="availability-and-pricing"></a>Rendelkezésre állás és díjszabás

A szemantikai képességek a [regisztrációs regisztráción](https://aka.ms/SemanticSearchPreviewSignup)keresztül érhetők el a standard szintű (S1, S2, S3), a következő régiókban található keresési szolgáltatásokban: az USA északi középső régiója, USA nyugati régiója, USA 2. keleti régiója, Észak-Európa, Nyugat-Európa. 

A helyesírás-javítás ugyanabban a régióban érhető el, de nem rendelkezik a rétegek korlátozásával. Ha rendelkezik olyan meglévő szolgáltatással, amely megfelel a rétegek és régiók feltételének, csak a regisztrációra van szükség.

Az előzetes indítás és a 2. március 1-től április 1-én a helyesírás-javítás és a szemantikai rangsorolás díjmentesen vehető igénybe. Április 1. után a funkció futtatásának számítási költségei számlázandó eseménynek számítanak. A várt díj körülbelül 500 USD, a 250 000-es lekérdezésekhez pedig a havi dollár. A részletes költségadatok a [Cognitive Search díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/) , valamint a [költségek becslése és kezelése](search-sku-manage-costs.md)című témakörben találhatók.

## <a name="next-steps"></a>Következő lépések

Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját.

[Hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md) a kezdéshez. Vagy tekintse át a kapcsolódó információkat a következő cikkekben.

+ [Helyesírás-ellenőrzés hozzáadása a lekérdezési feltételekhez](speller-how-to-add.md)
+ [Szemantikai válasz visszaadása](semantic-answers.md)
+ [Szemantikai rangsorolás](semantic-ranking.md)
+ [Szemantikai keresés bemutatása (blogbejegyzés)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Szemantikai képességekkel rendelkező értelmes megállapítások keresése (AI show videó)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)