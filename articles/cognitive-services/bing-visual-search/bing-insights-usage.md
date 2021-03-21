---
title: Példák a Bing-bepillantásokra – Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Ez a cikk példákat tartalmaz arra vonatkozóan, hogyan használhatók a Bing Visual Search és hogyan jeleníthetők meg a Bing.com a képelemzések.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493355"
---
# <a name="examples-of-bing-insights-usage"></a>Példák a Bing-adatok használatára

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez a cikk példákat tartalmaz arra vonatkozóan, hogy a Bing hogyan használhatja és képelemzéseket jeleníthet meg a Bing.com.

## <a name="pagesincluding-insight-example"></a>PagesIncluding Insight – példa

Az alábbi hivatkozás az első weblapra mutat, és lehetővé teszi, hogy a felhasználó kibontsa és Összecsukja a képet tartalmazó egyéb weblapok listáját:

![Kibontott lapok, beleértve](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources Insight – példa

Az alábbi ábrán látható, hogyan jelenhetnek meg a Bing a rendszerképben látható termékek vásárlási forrásaiban:

![Vásárlási források](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch Insight – példa

Az alábbi ábrán látható, hogyan jelenhetnek meg a Bing vizuálisan hasonló képek (lásd a példában szereplő **kapcsolódó képeket** ):

![Vizuálisan hasonló képek](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Példák a receptek betekintésére

Az alábbi ábrán látható, hogyan jelenhetnek meg a Bing a képen látható élelmiszerhez kapcsolódó receptek. A példa lehetővé teszi, hogy a felhasználó tudja, hogy vannak-e elérhető receptek:

![Receptek és lapok, beleértve a következőket](./media/recipes-pages-including.PNG)

 És megadja a receptek hivatkozását, amikor a felhasználó kibővíti a listát:

![Kibővített recept lapok, beleértve a következőket is](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches Insight – példa

Az alábbi ábrán látható, hogy a Bing hogyan jelenítheti meg a mások által készített képek kapcsolódó kereséseit. Ha a felhasználó rákattint a képre, a felhasználó a kapcsolódó lekérdezés Bing.com/images keresési eredmények lapjára kerül.

![Kapcsolódó keresések képekhez](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Példa az Entity insightra

Az alábbi ábrán látható, hogyan jelenhet meg a Bing a képen látható entitással (személy, hely vagy dolog) kapcsolatos információkkal. Ha a felhasználó az Entity (entitás) hivatkozásra kattint, a felhasználó az entitás Bing.com keresési eredmények lapjára kerül:

![A képen látható entitás](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>A felhasználó által esetlegesen felderített egyéb bepillantások megjelenítése

Az alábbi ábrán látható, hogyan jelenhet meg a Bing a felhasználó által feltárt rendszerképpel kapcsolatos egyéb információkkal.

![Ismerkedjen meg a rendszerképpel kapcsolatos egyéb elemzésekkel](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>Határolókeret és gyors elérésű pontok

A nem alapértelmezett címkék közé tartozik az a keret, amely a címkére vonatkozó képterületet azonosítja. Ha a határolókeret nem azonosítja a teljes képet, a határolókeret használatával hozzon létre egy gyors helyet a képen. A felhasználó rákattinthat a gyors elérésű helyre a gyors elérésű helyen (vagy négyszögben) található tartalommal kapcsolatos információk beszerzéséhez. Ha például a képen egy magas szintű rendszerkép látható, az eredmények tartalmazhatnak címkéket (és határoló mezőket) a képen látható kiegészítők számára, például erszényes, ékszer, sálak stb. Az alábbi példa egy, a képen látható napszemüveghez tartozó, gyors direktszínű téglalapot mutat be:

![Határolókeret és gyors elérésű hely](./media/click-to-search.PNG)

## <a name="next-steps"></a>Következő lépések

Az első kérésének megkezdéséhez tekintse meg a gyors útmutatót:

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)