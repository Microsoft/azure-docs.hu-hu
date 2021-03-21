---
title: Környezetfüggő szűrés metaadatok használatával
titleSuffix: Azure Cognitive Services
description: A QnA Maker a QnA-párokat metaadatok alapján szűri.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022101"
---
# <a name="filter-responses-with-metadata"></a>Válaszok szűrése metaadatokkal

QnA Maker lehetővé teszi metaadatok hozzáadását kulcs-érték párok formájában a kérdésekre és a válaszokra. Ezt az információt használhatja az eredmények felhasználói lekérdezésekre való szűrésére, valamint a követési beszélgetések során használható további információk tárolására.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Kérdések és válaszok tárolása QnA-entitással

Fontos tisztában lenni azzal, hogy a QnA Maker hogyan tárolja a kérdés-és adatválaszait. Az alábbi ábrán egy QnA entitás látható:

![QnA entitás ábrája](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Minden QnA entitás egyedi és állandó AZONOSÍTÓval rendelkezik. Az AZONOSÍTÓval egy adott QnA-entitás frissítését végezheti el.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Metaadatok használata egyéni metaadat-címkék alapján történő szűréshez

A metaadatok hozzáadása lehetővé teszi a válaszok szűrését a metaadatok címkéi alapján. Adja hozzá a metaadatok oszlopot a **nézet beállításai** menüből. A metaadatokat a metaadatok ikonra kattintva adhat hozzá a tudásbázishoz **+** . Ez a pár egy kulcsból és egy értékből áll.

![A metaadatok hozzáadásának képernyőképe](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Eredmények szűrése a strictFilters a metaadatok címkéi számára

Vegye figyelembe, hogy "Ha ez a Hotel be van zárva?" kérdésre a "Paradise" (paradicsom) nevű étterem esetében a szándék vonatkozik.

Mivel az eredmények csak a "Paradise" étterem esetében szükségesek, beállíthat egy szűrőt a GenerateAnswer hívásában az "étterem neve" metaadatokban. A következő példa ezt mutatja be:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logikai és alapértelmezés szerint

Ha több metaadat-szűrőt szeretne egyesíteni a lekérdezésben, adja hozzá a további metaadat-szűrőket a tulajdonság tömbhöz `strictFilters` . Alapértelmezés szerint az értékek logikailag kombinálhatók (és). A logikai kombináció megköveteli, hogy az összes szűrő megfeleljen a QnA pároknak ahhoz, hogy a válaszban vissza lehessen adni a párokat.

Ez egyenértékű a `strictFiltersCompoundOperationType` tulajdonság értékkel való használatával `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logikai vagy strictFiltersCompoundOperationType tulajdonság használata

Több metaadat-szűrő kombinálásával, ha csak egy vagy több szűrővel érintett, használja a `strictFiltersCompoundOperationType` tulajdonságot a értékkel `OR` .

Ez lehetővé teszi a Tudásbázis számára, hogy válaszokat adjanak vissza, ha bármely szűrő egyezik, de nem ad vissza olyan válaszokat, amelyek nem rendelkeznek metaadatokkal.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Metaadatok – példák a gyors útmutatókban

További információ a metaadatokat a QnA Maker-portálon a metaadatokkal kapcsolatban:
* [Szerzői metaadatok hozzáadása a QnA-párokhoz](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Lekérdezések előrejelzése – válaszok szűrése metaadatok alapján](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Kérdések és válaszok eredményeinek használata a beszélgetési környezet megőrzése érdekében

A GenerateAnswer válasza tartalmazza az egyeztetett kérdés és válasz pároknak megfelelő metaadat-információkat. Ezt az információt használhatja az ügyfélalkalmazás számára, hogy az előző beszélgetés kontextusát tárolja a későbbi beszélgetések során való használatra.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Tudásbázis elemzése](../How-to/get-analytics-knowledge-base.md)
