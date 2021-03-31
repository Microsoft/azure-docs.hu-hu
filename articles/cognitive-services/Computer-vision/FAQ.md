---
title: Gyakori kérdések – Computer Vision
titleSuffix: Azure Cognitive Services
description: Választ kaphat az Azure Cognitive Services Computer Vision APIával kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "68564592"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API gyakori kérdések

> [!TIP]
> Ha nem talál választ a kérdéseire ebben a GYIK-ban, próbálja meg megkérdezni a Computer Vision API Közösséget a [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) -on, vagy forduljon [segítségért és támogatásért a UserVoice](https://cognitive.uservoice.com/)

---

**Kérdés**: *betanítható Computer Vision API egyéni címkék használatára?  Szeretném például, hogy a Cat-ból származó képeket a mesterséges intelligenciára ("Train"), majd egy AI-kérelemre kapják.*

**Válasz**: Ez a függvény jelenleg nem érhető el. Mérnökeink azonban arra dolgozunk, hogy ezt a funkciót Computer Vision.

---

**Kérdés**: *Computer Vision lehet helyileg használni internetkapcsolat nélkül?*

**Válasz**: jelenleg nem biztosítunk helyszíni vagy helyi megoldást.

---

**Kérdés**: *Computer Vision használható a rendszámtábla olvasásához?*

**Válasz**: a jövőkép API jó szöveges észlelést biztosít OCR-sel, de jelenleg nem optimalizálták a licencek lemezeit. Folyamatosan igyekszünk javítani a szolgáltatásainkat, és az Automatikus rendszámtábla-felismeréshez hozzáadott OCR-t is készítettünk a szolgáltatásra vonatkozó kérések listájára.

---

**Kérdés**: *milyen típusú írási felületek támogatottak a kézírás-felismeréshez?*

**Válasz**: a technológia különböző típusú felületekkel működik, beleértve a táblákat, a tanulmányokat és a sárga ragacsos megjegyzéseket.

---

**Kérdés**: *mennyi ideig tart a kézírás-felismerési művelet?*

**Válasz**: a szükséges idő a szöveg hosszától függ. Hosszabb szövegek esetében akár több másodpercig is eltarthat. Ezért, ha a kézzel írt szöveg felismerése művelet befejeződik, előfordulhat, hogy meg kell várnia az eredmények lekérését a kézzel írt szöveg műveleti eredményének beolvasása művelettel.

---

**Kérdés**: *hogyan kezeli a kézírás-felismerési technológia a Sortöréssel beszúrt szöveget egy vonal közepén?*

**Válasz**: ezt a szöveget a kézírás-felismerési művelet külön sorban adja vissza.

---

**Kérdés**: *hogyan kezeli a kézírás-felismerési technológia a határokon átnyúló szavakat vagy sorokat?*

**Válasz**: Ha a szavakat több sorral keresztezik, hogy felismerhetetlenek legyenek, a kézírás-felismerési művelet nem veszi fel őket. Ha azonban a szavakat egyetlen vonallal keresztezik, az átkelést a rendszer zajként kezeli, és a szavakat a kézírás-felismerési művelet továbbra is felveszi.

---

**Kérdés**: *milyen szöveg-tájolás támogatott a kézírás-felismerési technológia esetében?*

**Válasz**: a kézírás-felismerési művelettel akár körülbelül 30 fokos, 40 fokos szögben elhelyezkedő szöveg is megadható.

---
