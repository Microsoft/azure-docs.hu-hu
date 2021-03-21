---
title: Gyakori kérdések – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services egyéni fordítóval kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 001314817b0c18a8023258d01bcfb02eaaffe79b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895832"
---
# <a name="custom-translator-frequently-asked-questions"></a>Egyéni Translator – gyakori kérdések

Ez a cikk az [Egyéni fordítóval](https://portal.customtranslator.azure.ai)kapcsolatos gyakran feltett kérdésekre adott válaszokat tartalmazza.

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Mik a jelenlegi korlátozások az egyéni fordítóban?

A fájlméretre, a modell betanítására és a modell üzembe helyezésére vonatkozó korlátozások és korlátok vonatkoznak. Tartsa szem előtt ezeket a korlátozásokat, amikor beállít egy modellt az egyéni fordítóban.

- Az elküldött fájlok mérete nem haladhatja meg a 100 MB-ot.
- Az egynyelvű adathalmazok nem támogatottak.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Mikor kell kérelmet benyújtani a betanított fordítási rendszerhez?

Több tanítást is igénybe vehet a projekt optimális fordítási rendszerének létrehozásához. Érdemes lehet több betanítási vagy alaposabban szűrt adatmennyiséget használni, ha a BLEU pontszám és/vagy a teszt eredménye nem kielégítő. Szigorú és körültekintően kell megtervezni a hangolási készletet és a tesztelési készletet, hogy teljes mértékben reprezentatív legyen a lefordítani kívánt anyagok terminológiája és stílusa. A betanítási adatait több liberálisban is megtekintheti, és különböző lehetőségekkel kísérletezheti. A rendszer központi telepítésének kérése, ha elégedett a Rendszerteszt eredményeivel, nem kell több olyan adattal bővíteni a képzést, amely fejleszti a betanított rendszerét, és API-kon keresztül szeretné elérni a betanított modellt.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hány betanítható rendszer helyezhető üzembe egy projektben?

Projektenként csak egy képzett rendszer helyezhető üzembe. Több tanítást is igénybe vehet, hogy létrehoz egy megfelelő fordítási rendszer a projekthez, és javasoljuk, hogy a lehető legjobb eredményt biztosító képzés üzembe helyezését kérje. Megtekintheti a BLEU pontszám (nagyobb jobb) és a véleményezők által folytatott képzés minőségét, mielőtt azt döntene, hogy a fordítások minősége megfelelő az üzembe helyezéshez.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Mikor várhatom el a Betanítások üzembe helyezését?

Az üzemelő példány általában kevesebb mint egy órát vesz igénybe.

## <a name="how-do-you-access-a-deployed-system"></a>Hogyan férhet hozzá egy telepített rendszerhez?

A központilag telepített rendszerek a Kategóriakód megadásával érhetők el a Microsoft Translator Text API v3 segítségével. A Translator Text APIról további információt az [API-referenciák](../reference/v3-0-reference.md) weblapján találhat.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hogyan kihagyhatja az igazítást és a mondatot, ha az adataim már a mondattal összhangban vannak?

Az egyéni fordító kihagyja a mondatok igazítását és a mondatot a TMX-fájlokra és a `.align` kiterjesztésű szövegfájlokra. `.align` a fájlok lehetővé teszi a felhasználók számára, hogy kihagyják az egyéni fordító mondatának feltörési és igazítási folyamatát, és nem igényelnek további feldolgozást. Javasoljuk, `.align` hogy csak a tökéletesen igazított fájlokhoz használjon bővítményt.

Ha a kinyert mondatok száma nem egyezik meg az azonos alapnévvel rendelkező két fájllal, az egyéni fordító továbbra is a mondatok igazítását fogja futtatni a `.align` fájlokon.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Megpróbáltam feltölteni a TMX, de azt mondja, hogy a "dokumentum feldolgozása sikertelen"


Győződjön meg arról, hogy a TMX megfelel a TMX 1.4 b specifikációjának <https://www.gala-global.org/tmx-14b> .