---
title: A szövegelemzési API-val kapcsolatos gyakori kérdések
titleSuffix: Azure Cognitive Services
description: A Text Analytics API kapcsolatos gyakori kérdésekre adott válaszok.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: 2d04d93cf11f6c42a6411dc27b6bbeb2a9028773
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226553"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>A Text Analytics Cognitive szolgáltatással kapcsolatos gyakori kérdések (GYIK)

 Kapcsolatos fogalmakat, a kód és a forgatókönyvek a Microsoft Cognitive Services, Azure-ban a szövegelemzési API kapcsolatos gyakori kérdésekre adott válaszok.

## <a name="can-text-analytics-identify-sarcasm"></a>Azonosíthatja a Text Analytics viccelődni?

Elemzés van hangulatának észlelése helyett pozitív negatív véleményeket.

Mindig van bizonyos fokú hangulatelemzés a hiba, de a modell akkor a leghasznosabb, ha nem rejtett jelentése vagy a tartalom alatta szöveggel. Kulturális környezet és normák, szándék átadására alapulnak irony, viccelődni, humorral és hasonlóképpen komplikáltabb tartalmat. Ez a tartalom típus közül a legnagyobb kihívást jelentő elemzéséhez. A legnagyobb eltérést az elemző és a egy szubjektív felmérést egy ember által egy adott pontszám között általában jelentéssel komplikáltabb tartalom.

## <a name="can-i-add-my-own-training-data-or-models"></a>Saját betanítási adatok és modelleket adhat hozzá?

Nem, a modellek imagenet vannak. Az egyetlen elérhető műveleteinek feltöltött adatok pontozásához vannak, kulcsszókeresést és nyelvfelismerést. Egyéni modellek nem mi üzemeltetjük. Ha szeretné-e létre és egyedi gépi tanulási modellek üzemeltetésére, fontolja meg a [gépi tanulási funkciókat a Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kérelem további nyelveken is?

Hangulatelemzés és kulcsszókeresés érhetők el egy [válassza ki azon nyelvek számának](text-analytics-supported-languages.md). Természetes nyelvi feldolgozás összetett, és jelentős tesztelése, mielőtt új funkciókat is kell szabadítani igényel. Ezért azt ne előre bejelentése támogatása, így senki függőséget vesz fel funkciók, amelyek részletes több időre van szüksége. 

Annak érdekében, fontossági sorrendjének megállapításában működéséhez a következő nyelveket, szavazhat a bizonyos nyelveken [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Miért nem adja a kulcsszókeresés egyes szavak, de nem más?

Kulcskifejezések kinyerése kiküszöböli nélkülözhető szavakat és a különálló melléknevek. Melléknév-főnév kombináció, például "látványosan nézetek" vagy "nincs teljesen tisztában időjárási" együtt a rendszer adja vissza.

Általában a kimeneti főneveket és a mondat objektumához áll. Kimenet a legfontosabbak első része, fontossági sorrendben szerepel. Fontos, hogy hányszor említett, egy adott fogalom, vagy az adott elem más elemeket a szövegben való kapcsolat mérjük.

## <a name="why-does-output-vary-given-identical-inputs"></a>Miért kimeneti változik, azonos bemenetei között megadott?

Modelleket és algoritmusokat fejlesztései már bejelentettünk, ha a módosítás nagyobb vagy csendben integrált, a szolgáltatásba, ha a frissítés akkor kisebb. Idővel előfordulhat, hogy ugyanazt a bemeneti szöveg az eredményt egy másik véleménypontszámot vagy kulcskifejezések kimeneti. Ez a felügyelt machine learning-erőforrások használatára a felhőben a normál és szándékos következménye.

## <a name="next-steps"></a>További lépések

Egy hiányzó szolgáltatás vagy funkció a kérdése van? Kérő vagy szavaz, fontolja meg a [UserVoice webhelyen](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Lásd még

 [A StackOverflow: Text Analytics API   
 [A StackOverflow: A cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
