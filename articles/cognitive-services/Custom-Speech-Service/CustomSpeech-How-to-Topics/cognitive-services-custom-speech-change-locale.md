---
title: Nyelv támogatott a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Támogatott nyelvek a Cognitive Services Custom Speech Service áttekintése.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 8493aeb3c1c2436900ae626ad0f34cbe8b060163
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342168"
---
# <a name="supported-locales-in-custom-speech-service"></a>A Custom Speech Service támogatott nyelv

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

A Custom Speech Service jelenleg támogatja modell testreszabása a következő területi beállításokkal:

| Modell típusa | Nyelvi támogatás |
|----|-----|
| Akusztikai modell | Amerikai angol (en-US) |
| Nyelvi modell | Amerikai angol (en-US), kínai (zh-CN) |

Akusztikai modell testreszabása csak az angol támogatják, testre szabott kínai nyelvi modellek offline tesztelés céljából kínai akusztikai adatok importálása támogatott.

Bármely művelet elvégzése előtt ki kell választani a megfelelő területi beállítást. Az aktuális területi beállítás minden adat-, modell- és üzembehelyezési oldalon megjelenik a táblázat címében. A területi beállítás módosításához kattintson a "Területi beállítás módosítása" gombra, a tábla címe alatt található. Ekkor a területi beállítás jóváhagyást jelző oldal. A táblázathoz való visszatéréshez kattintson az „OK” gombra.

A következő lépésekkel kell követés
* Ismerje meg, [egy importálni akusztikai modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md) pontosságának javítása érdekében
* Ismerje meg, [egyéni nyelvi modell létrehozása](cognitive-services-custom-speech-create-language-model.md) a felismerés sebesség javítása érdekében
* Kövesse a [beszédátírási irányelvek](cognitive-services-custom-speech-transcription-guidelines.md) az adatok előkészítése
