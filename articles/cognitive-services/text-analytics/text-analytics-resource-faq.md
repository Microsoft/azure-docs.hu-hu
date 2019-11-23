---
title: Gyakran ismételt kérdések a Text Analytics API
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics API kapcsolatos fogalmakkal, kóddal és forgatókönyvekkel kapcsolatos gyakori kérdésekre adott válaszokat talál.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837126"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Gyakori kérdések (GYIK) a Text Analytics kognitív szolgáltatásról

 Válaszok az Azure-beli Microsoft Cognitive Services Text Analytics API kapcsolatos fogalmakkal, kódokkal és forgatókönyvekkel kapcsolatos gyakori kérdésekre.

## <a name="can-text-analytics-identify-sarcasm"></a>Képes Text Analytics azonosítani a szarkazmust?

Az elemzés a hangulat észlelése helyett a pozitív negatív hangulathoz hasonlít.

Az érzelmek elemzése mindig bizonyos fokú pontatlanságot jelent, de a modell akkor is hasznos, ha nincs rejtett szó vagy alszöveg a tartalomhoz. Az irónia, a szarkazmus, a humor és a hasonló árnyalt tartalom a kulturális környezet és a normák alapján közvetíti a szándékot. Az ilyen típusú tartalom az elemzéshez leginkább felépülő kihívás. Általában az analizátor által előállított egy adott pontszám és az emberi tartalommal való szubjektív értékelés közötti legnagyobb eltérés az árnyalt jelentéssel rendelkező tartalmak esetében.

## <a name="can-i-add-my-own-training-data-or-models"></a>Hozzáadhatom a saját betanítási adataikat vagy modelljeiket?

Nem, a modellek előképzés alatt állnak. A feltöltött adatokon csak a következő műveletek érhetők el: pontozás, fő kifejezés kinyerése és nyelvfelismerés. Nem üzemeltetünk egyéni modelleket. Ha egyéni gépi tanulási modellt szeretne létrehozni és üzemeltetni, vegye figyelembe a [gépi tanulási képességeit Microsoft R Serverban](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kérhetek további nyelveket is?

Az érzelmek elemzése és a kulcsfontosságú kifejezés kinyerése a [kiválasztott számú nyelven](text-analytics-supported-languages.md)érhető el. A természetes nyelvi feldolgozás összetett, és jelentős tesztelést igényel az új funkciók felszabadítása előtt. Ezért elkerüljük a támogatás előzetes bejelentését, hogy senki ne vegyen igénybe olyan funkciót, amelynek több időre van szüksége. 

Annak érdekében, hogy a következő munkahelyeken Milyen nyelveket kell megkeresni, szavazzon a [felhasználói hangon](https://cognitive.uservoice.com/forums/555922-text-analytics)megadott nyelvekre. 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Miért ad vissza a Key kifejezés kinyerése néhány szót, de másokat nem?

A fő kifejezés kinyerése kiküszöböli a nem alapvető szavakat és az önálló jelzőket. A melléknév-főnévi kombinációk, például a "látványos nézetek" vagy a "ködös időjárási viszonyok" együttesen lesznek visszaadva.

A kimenet általában a mondatok és a mondat objektumaiból áll. A kimenet fontossági sorrendben jelenik meg, az első mondat pedig a legfontosabb. A fontosságot a rendszer egy adott fogalom megemlítése, illetve az elemnek a szöveg más elemeihez viszonyított aránya alapján méri.

## <a name="why-does-output-vary-given-identical-inputs"></a>Miért változik a kimenet, mivel azonos bemenetek vannak?

A modellek és algoritmusok fejlesztése akkor jelent meg, ha a módosítás jelentős, vagy ha a frissítés kisebb, akkor a szolgáltatás csendesen slipstreamed. Az idő múlásával előfordulhat, hogy ugyanaz a szöveges bevitel egy másik hangulati pontszám vagy a kulcs kifejezésének kimenetét eredményezi. Ez a felügyelt gépi tanulási erőforrások Felhőbeli használatának normális és szándékos következménye.

## <a name="next-steps"></a>Következő lépések

Egy hiányzó funkcióval vagy funkcióval kapcsolatos kérdése van? Javasoljuk, hogy a [UserVoice webhelyén](https://cognitive.uservoice.com/forums/555922-text-analytics)kérjen vagy szavazzon.

## <a name="see-also"></a>Lásd még

 [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
