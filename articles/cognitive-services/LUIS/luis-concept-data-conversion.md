---
title: Adatkonverzió – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan változtathatók meg a hosszúságú kimondott szöveg a Language Understanding (LUIS) előrejelzések előtt
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 42a9caff0433808734ee853cbad90a2088bf4e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95019245"
---
# <a name="convert-data-format-of-utterances"></a>Hosszúságú kimondott szöveg adatformátumának konvertálása
A LUIS a következő konverziókat biztosítja a felhasználók teljes leírásához az előrejelzés előtt: "

* Beszéd a szövegben [Cognitive Services Speech](../Speech-Service/overview.md) Service használatával.

## <a name="speech-to-text"></a>Diktálás

A beszéd szövege a LUIS-nal való integrációként van megadva.

### <a name="intent-conversion-concepts"></a>Szándék-átalakítási fogalmak
Ha a LUIS szövegre konvertálja a szöveget, lehetővé teszi a szóbeli hosszúságú kimondott szöveg küldését egy végpontra, és megkapta a LUIS-előrejelzési választ. A folyamat a [Speech](/azure/cognitive-services/Speech) Service és a Luis közötti integráció. További információ a beszédfelismerési szándékról az [oktatóanyaghoz](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Fő követelmények
Ehhez az integrációhoz nem kell **Bing Speech API** kulcsot létrehoznia. Az integrációhoz a Azure Portalban létrehozott **Language Understanding** kulcs működik. Ne használja a LUIS Starter-kulcsot.

### <a name="pricing-tier"></a>Tarifacsomag
Ez az integráció eltérő [díjszabási](luis-limits.md#key-limits) modellt használ, mint a szokásos Language Understanding árképzési szintek.

### <a name="quota-usage"></a>Kvóta használata
További információ: [kulcs korlátai](luis-limits.md#key-limits) .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése](luis-concept-data-extraction.md)