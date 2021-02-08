---
title: A Tudásbázis lekérdezése – QnA Maker
description: A tudásbázist közzé kell tenni. A közzététel után a tudásbázist a generateAnswer API használatával kérdezi le a futásidejű előrejelzési végponton.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: deefc53fdc94851da0e9e255962fbf85692d1393
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820381"
---
# <a name="query-the-knowledge-base-for-answers"></a>A válaszok tudásbázisának lekérdezése

A tudásbázist közzé kell tenni. A közzététel után a tudásbázist a generateAnswer API használatával kérdezi le a futásidejű előrejelzési végponton. A lekérdezés tartalmazza a kérdés szövegét és az egyéb beállításokat, hogy QnA Maker válassza ki a lehető legjobb egyezést a válaszhoz.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Hogyan dolgozza fel QnA Maker a felhasználói lekérdezéseket a legjobb válasz kiválasztásához

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A betanított és [közzétett](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker Tudásbázis felhasználói lekérdezést kap egy robottól vagy más ügyfélalkalmazástól a [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)-ban. A következő ábra a felhasználói lekérdezés fogadásának folyamatát szemlélteti.

![A felhasználói lekérdezés rangsorolási modelljének folyamata](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Rangsorolási folyamat

A folyamatot az alábbi táblázat ismerteti.

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md)-nak.|
|2|QnA Maker elődolgozza a felhasználói lekérdezést a nyelvfelismerés, a helyesírás-ellenőrző és a Word-megszakítók használatával.|
|3|Ez az előfeldolgozás a legjobb keresési eredmények felhasználói lekérdezésének megváltoztatásához szükséges.|
|4|Ezt a módosított lekérdezést egy Azure Cognitive Search indexbe küldik, amely megkapja az `top` eredmények számát. Ha a helyes válasz nem szerepel ezekben az eredményekben, növelje a kis-és nagymértékű értéket `top` . Általában a 10-es érték a `top` lekérdezések 90%-ában működik. Az Azure Search-szűrők [leállítják a szavakat](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) ebben a lépésben.|
|5|A QnA Maker szintaktikai és szemantikai alapú featurization használatával határozza meg a felhasználói lekérdezés és a beolvasott QnA eredményei közötti hasonlóságot.|
|6|A géppel megtanult Ranger-modell az 5. lépés különböző funkcióit használja a megbízhatósági pontszámok és az új rangsorolási sorrend meghatározásához.|
|7|Az új eredményeket rangsorolt sorrendben adja vissza az ügyfélalkalmazás.|
|||

A használatban lévő funkciók közé tartozik például a Word-szintű szemantika, a kifejezés szintű fontosság a corpusban, és a mélyebben megtanult szemantikai modellek határozzák meg a hasonlóságot és a megfelelést két szöveges karakterlánc között.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A betanított és [közzétett](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker Tudásbázis felhasználói lekérdezést kap egy robottól vagy más ügyfélalkalmazástól a [GenerateAnswer API](../how-to/metadata-generateanswer-usage.md)-ban. A következő ábra a felhasználói lekérdezés fogadásának folyamatát szemlélteti.

![A felhasználói lekérdezés előzetes verziójához tartozó rangsorolási modell folyamata](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Rangsorolási folyamat

A folyamatot az alábbi táblázat ismerteti.

|Lépés|Cél|
|--|--|
|1|Az ügyfélalkalmazás elküldi a felhasználói lekérdezést a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md)-nak.|
|2|QnA Maker elődolgozza a felhasználói lekérdezést a nyelvfelismerés, a helyesírás-ellenőrző és a Word-megszakítók használatával.|
|3|Ez az előfeldolgozás a legjobb keresési eredmények felhasználói lekérdezésének megváltoztatásához szükséges.|
|4|Ezt a módosított lekérdezést egy Azure Cognitive Search indexbe küldik, amely megkapja az `top` eredmények számát. Ha a helyes válasz nem szerepel ezekben az eredményekben, növelje a kis-és nagymértékű értéket `top` . Általában a 10-es érték a `top` lekérdezések 90%-ában működik. Az Azure Search-szűrők [leállítják a szavakat](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/STOPWORDS.md) ebben a lépésben.|
|5|A QnA Maker a legkorszerűbb transzformátor-alapú modellt használja a felhasználói lekérdezés és az Azure Cognitive Searchból beolvasott QnA eredmények közötti hasonlóság meghatározásához. A transzformátor-alapú modell egy mély tanulási többnyelvű modell, amely horizontálisan működik az összes nyelven a megbízhatósági pontszámok és az új rangsorolási sorrend meghatározásához.|
|6|Az új eredményeket rangsorolt sorrendben adja vissza az ügyfélalkalmazás.|
|||

A ranker az összes alternatív kérdésre és válaszra támaszkodik, és megkeresi a legjobban illeszkedő QnA párokat a felhasználói lekérdezéshez. A felhasználók rugalmasan konfigurálhatják a Ranger-t a kérdéses rangsorban. 

---

## <a name="http-request-and-response-with-endpoint"></a>HTTP-kérelem és-válasz végponttal
A Tudásbázis közzétételekor a szolgáltatás egy REST-alapú HTTP-végpontot hoz létre, amely integrálható az alkalmazásba, és általában egy csevegési robot.

### <a name="the-user-query-request-to-generate-an-answer"></a>A felhasználó lekérdezési kérelme a válasz létrehozásához

A felhasználó lekérdezése az a kérdés, hogy a végfelhasználó kéri a tudásbázist, például: `How do I add a collaborator to my app?` . A lekérdezés gyakran természetes nyelvi formátumban van, vagy néhány kulcsszó, amely a kérdést jelképezi, például: `help with collaborators` . A rendszer elküldi a lekérdezést az ügyfélalkalmazás HTTP-kérelme alapján.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

A választ a tulajdonságok, például a [scoreThreshold](./confidence-score.md#choose-a-score-threshold), a [Top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers)és a [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags)tulajdonságainak beállításával szabályozhatja.

A beszélgetési [kontextusban](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) [többfordulatos funkciókkal](../how-to/multiturn-conversation.md) megtarthatja a beszélgetést a kérdések és válaszok pontosításával, hogy megtalálja a megfelelő és a végső választ.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Válasz meghívása a válasz létrehozásához

A HTTP-válasz a Tudásbázisból beolvasott válasz, amely egy adott felhasználói lekérdezés legjobb egyezése alapján történik. A válasz tartalmazza a választ és az előrejelzési pontszámot. Ha a tulajdonsággal egynél több legfelső szintű választ kér `top` , egynél több legjobb választ kap, amelyek mindegyike egy pontszámmal rendelkezik.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)
