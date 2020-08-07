---
title: Kérelmek korlátai – Translator
titleSuffix: Azure Cognitive Services
description: Ez a cikk a fordítóra vonatkozó kérelmek korlátozásait sorolja fel. A díjak a karakterek száma alapján merülnek fel, a kérelmek gyakorisága pedig a 5 000 karakteres korlátot kéri. A karakterkészletek az előfizetés-alapúak, a F0 pedig óránként 2 000 000 karakterre van korlátozva.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 9ce2ea7da562372e5c1c25d4c0fe9ab410b02a1d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903407"
---
# <a name="request-limits-for-translator"></a>Fordítói kérelmek korlátai

Ez a cikk a fordító számára biztosít szabályozási korlátozásokat. A szolgáltatások közé tartoznak a fordítás, az írás, a mondatok hosszának észlelése, a nyelvfelismerés és az alternatív fordítások.

## <a name="character-and-array-limits-per-request"></a>Karakter és tömb korlátja kérelmek szerint

Az összes fordítási kérelem 10 000 karakterből áll, az összes megcélzott nyelven. Ha például egy 3 000 karakterből álló fordítási kérelem küldése 3 különböző nyelvre, a kérelem mérete 3000x3 = 9 000 karakter, amely megfelel a kérelem korlátjának. A díjat nem a kérelmek száma alapján számítjuk fel. A rövidebb kérelmek küldését javasoljuk.

A következő táblázat a fordító egyes műveleteinek tömb elemeit és karakteres korlátait sorolja fel.

| Művelet | Tömb elemének maximális mérete |    Tömb elemeinek maximális száma |    Kérelmek maximális mérete (karakter) |
|:----|:----|:----|:----|
| Fordítás | 10,000    | 100   | 10,000 |
| Átírás | 5000 | 10    | 5000 |
| Észlelés | 10,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| Keresés a szótárban| 100 |  10  | 1,000 |
| Szótár – példák | 100 szöveg és 100 fordításhoz (200 összesen)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Karakteres korlátok óránként

A karakteres korlát/óra a fordítói előfizetés szintjétől függ. 

Az óránkénti kvótát egyenletesen kell használni az egész órában. Például a F0 küszöbértéke óránként 2 000 000 karakter, a karaktereket nem lehet kevesebb, mint durván 33 300 karakter/perc csúszó ablak (2 000 000 karakter, amely 60 perc).

Ha eléri vagy meghaladja ezeket a korlátokat, vagy a kvóta egy részének rövid időn belül túl nagy részét küldi el, valószínű, hogy a kvóta kiválasztva lesz. Az egyidejű kérések esetében nincsenek korlátok.

| Szint | Karakteres korlát |
|------|-----------------|
| F0 | 2 000 000 karakter/óra |
| S1 | 40 000 000 karakter/óra |
| S2/C2 | 40 000 000 karakter/óra |
| S3/C3 | 120 000 000 karakter/óra |
| S4/C4 | 200 000 000 karakter/óra |

A [több szolgáltatásra vonatkozó előfizetések](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) korlátai megegyeznek az S1 szinttel.

Ezek a korlátok a Microsoft szabványos fordítási modelljeire korlátozódnak. Az egyéni fordítót használó egyéni fordítási modellek másodpercenként 1 800 karakterre vannak korlátozva.

## <a name="latency"></a>Késés

A fordító a standard modellekkel és az 120 másodperces késleltetéssel 15 másodperces késést használ egyéni modellek használata esetén. A *100 karakternél hosszabb szövegre* adott válaszokat a rendszer a 150 ezredmásodpercben, 300 ezredmásodpercben adja vissza. Az egyéni fordítói modellek hasonló késési jellemzőkkel rendelkeznek a tartós kérelmek arányában, és nagyobb késéssel járhatnak, ha a kérések sebessége időszakos. A válaszidő a kérelem és a nyelvi pár méretétől függően változhat. Ha nem kap fordítást vagy [hibaüzenetet](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) az adott időkereten belül, ellenőrizze a kódot, a hálózati kapcsolatokat, és próbálkozzon újra. 

## <a name="sentence-length-limits"></a>Mondat hosszának korlátai

A [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) függvény használatakor a mondat hossza 275 karakterre van korlátozva. Ezekhez a nyelvekhez kivételek tartoznak:

| Nyelv | Kód | Karakteres korlát |
|----------|------|-----------------|
| Kínai | zh | 166 |
| Német | de | 800 |
| Olasz | Ez | 800 |
| Japán | Japán | 166 |
| Portugál | pt | 800 |
| Spanyol | Igen | 800 |
| Thai | adik | 180 |

> [!NOTE]
> Ez a korlát nem vonatkozik a fordításokra.

## <a name="next-steps"></a>Következő lépések

* [Díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Régiónkénti rendelkezésre állás](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [3-as verziójú Translator-referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
