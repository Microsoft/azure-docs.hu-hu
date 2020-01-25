---
title: Felhasználói kifejezés áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Az aktív tanulással a felülvizsgálati végpont hosszúságú kimondott szöveg a megfelelő szándékot és entitást. LUIS kiválasztja a végpont hosszúságú kimondott szöveg, ezért nem biztos benne.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710539"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Az aktív tanulás engedélyezésével kapcsolatos fogalmak a végpontok hosszúságú kimondott szöveg áttekintésével
Az aktív tanulás a három stratégia egyike, amely javítja az előrejelzési pontosságot és a legegyszerűbb megvalósítást. Az aktív tanulással a felülvizsgálati végpont hosszúságú kimondott szöveg a megfelelő szándékot és entitást. LUIS kiválasztja a végpont hosszúságú kimondott szöveg, ezért nem biztos benne.

## <a name="what-is-active-learning"></a>Mi az az aktív tanulás?
Az aktív tanulás egy kétlépéses folyamat. Először LUIS kiválasztja a hosszúságú kimondott szöveg, amely az alkalmazás végpontján fogadja az érvényesítést. A második lépést az alkalmazás tulajdonosa vagy a közreműködő ellenőrzi a kiválasztott hosszúságú kimondott szöveg [ellenőrzéséhez](luis-how-to-review-endpoint-utterances.md), beleértve a megfelelő szándékot és a szándékon belüli entitásokat is. Miután megtekintette a hosszúságú kimondott szöveg, betanítja és közzéteszi az alkalmazást.

## <a name="which-utterances-are-on-the-review-list"></a>Mely hosszúságú kimondott szöveg találhatók a felülvizsgálati listán
LUIS hozzáadja a hosszúságú kimondott szöveg a felülvizsgálati listához, ha a felső égetési szándéknak alacsony pontszáma van, vagy az első két cél pontszáma túl közel van.

## <a name="single-pool-for-utterances-per-app"></a>Önálló készlet hosszúságú kimondott szöveg-alkalmazásokhoz
A **felülvizsgálati végpont hosszúságú kimondott szöveg** listája nem változik a verzió alapján. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton.

## <a name="where-are-the-utterances-from"></a>Honnan származnak a hosszúságú kimondott szöveg
A végpontok hosszúságú kimondott szöveg az alkalmazás HTTP-végpontján lévő végfelhasználói lekérdezésekből származnak. Ha az alkalmazás nincs közzétéve vagy még nem kapott találatokat, nem kell hosszúságú kimondott szöveg. Ha egy adott szándékhoz vagy entitáshoz nem érkezik végpont, akkor nem rendelkezik olyan hosszúságú kimondott szöveg, amely tartalmazza azokat.

## <a name="schedule-review-periodically"></a>Ütemezett áttekintés rendszeres időközönként
A javasolt hosszúságú kimondott szöveg áttekintését nem kell minden nap elvégezni, azonban a LUIS rendszeres karbantartásának részét kell képeznie.

## <a name="delete-review-items-programmatically"></a>A felülvizsgálati elemek programozott törlése
Használja a nem **[címkézett hosszúságú kimondott szöveg API törlését](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . **[A naplófájlok exportálásával a](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** törlés előtt biztonsági mentést készíthet a hosszúságú kimondott szöveg.

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ezt úgy hajthatja végre, hogy meghívja a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

## <a name="next-steps"></a>Következő lépések

* Útmutató az Endpoint hosszúságú kimondott szöveg [áttekintéséhez](luis-how-to-review-endpoint-utterances.md)
