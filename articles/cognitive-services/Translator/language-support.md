---
title: Nyelvi támogatás – Translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator a következő nyelveket támogatja szöveg-szöveg fordításához neurális gépi fordítás (NMT) használatával.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: lajanuar
ms.openlocfilehash: 935a9e92de88c2519dc1a1042315d204e8f60099
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919918"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Nyelvi és területi támogatás szöveg-és beszédfelismerési fordításhoz

A fordítóval fordítást használhat a 70-es és a szöveges fordítási nyelvek bármelyikére. A neurális gépi fordítás (NMT) az új szabvány a kiváló minőségű, mesterséges intelligenciát használó gépi fordításokhoz, és az alapszintű Translator v3 használatával érhető el, amikor egy neurális rendszer elérhető.

A fordítót az egyéni fordítóval együtt is használhatja olyan neurális fordítási rendszerek kiépítéséhez, amelyek megértik a saját üzleti és iparági terminológiáját, valamint a Microsoft Speech Service segítségével beszédfelismerési fordítást adhat az alkalmazásához.

[További információ a gépi fordítás működéséről](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Szövegfordítás
A fordítás a fordítási művelettel érhető el a fordítóban elérhető bármely nyelvről, illetve azokról. Az API az észlelési művelettel, a Átbetűzés művelettel és a szótári keresési és szótári példákat használó kétnyelvű szótárakkal is kínál nyelvi észlelést. Az egyes műveletekhez elérhető nyelvek alább láthatók. 

### <a name="translate"></a>Translate

A fordító a következő nyelveket támogatja szöveg szöveggé fordításához. 

[A fordítási műveletekre vonatkozó dokumentáció megtekintése](reference/v3-0-translate.md)

| Nyelv | Nyelvkód |
|:-|:-:|
| búr | `af` |
| Arab | `ar` |
| Asszámi | `as` |
| Bangla | `bn` |
| Bosnyák (latin betűs) | `bs` |
| Bolgár | `bg` |
| Kantoni (hagyományos) | `yue` |
| Katalán | `ca` |
| kínai (egyszerűsített) | `zh-Hans` |
| kínai (hagyományos) | `zh-Hant` |
| Horvát | `hr` |
| cseh | `cs` |
| Dari | `prs` |
| Dán | `da` |
| Holland | `nl` |
| Angol | `en` |
| Észt | `et` |
| Fidzsi | `fj` |
| Filipino | `fil` |
| Finn | `fi` |
| Francia | `fr` |
| Francia (Kanada) | `fr-ca` |
| Német | `de` |
| Görög | `el` |
| gudzsaráti | `gu` |
| Haiti kreol | `ht` |
| héber | `he` |
| Hindi | `hi` |
| Hmong DAW | `mww` |
| Magyar | `hu` |
| Izlandi | `is` |
| Indonéz | `id` |
| Inuktitut | `iu` |
| Ír | `ga` |
| Olasz | `it` |
| Japán | `ja` |
| kannada | `kn` |
| Kazak | `kk` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Koreai | `ko` |
| Kurd (Közép) | `ku` |
| Kurd (északi) | `kmr` |
| Lett | `lv` |
| Litván | `lt` |
| Malgas | `mg` |
| Maláj | `ms` |
| malajálam | `ml` |
| Máltai | `mt` |
| maori | `mi` |
| marathi | `mr` |
| Norvég | `nb` |
| Odia | `or` |
| Pasto | `ps` |
| perzsa | `fa` |
| Lengyel | `pl` |
| Portugál (Brazília) | `pt` |
| Portugál (Portugália) | `pt-pt` |
| pandzsábi | `pa` |
| Queretaro otomi | `otq` |
| Román | `ro` |
| Orosz | `ru` |
| Szamoai | `sm` |
| Szerb (cirill betűs) | `sr-Cyrl` |
| Szerb (latin betűs) | `sr-Latn` |
| Szlovák | `sk` |
| Szlovén | `sl` |
| Spanyol | `es` |
| szuahéli | `sw` |
| svéd | `sv` |
| Tahitian | `ty` |
| tamil | `ta` |
| telugu | `te` |
| Thai | `th` |
| Tongai | `to` |
| Török | `tr` |
| Ukrán | `uk` |
| urdu | `ur` |
| Vietnámi | `vi` |
| walesi | `cy` |
| Yucatec Maya | `yua` |

> [!NOTE]
> A Nyelvkód `pt` alapértelmezett értéke `pt-br` , Portugál (Brazília).

### <a name="detect"></a>Észlelés

A Translator a következő nyelveket észleli fordításhoz és íráshoz.

[Észlelési műveletre vonatkozó dokumentáció megtekintése](reference/v3-0-detect.md)

| Nyelv | Nyelvkód |
|:-|:-:|
| búr | `af` |
| Arab | `ar` |
| Bolgár | `bg` |
| Katalán | `ca` |
| kínai (egyszerűsített) | `zh-Hans` |
| kínai (hagyományos) | `zh-Hant` |
| Horvát | `hr` |
| Cseh | `cs` |
| Dán | `da` |
| Holland | `nl` |
| Angol | `en` |
| Észt | `et` |
| Finn | `fi` |
| Francia | `fr` |
| Német | `de` |
| Görög | `el` |
| gudzsaráti | `gu` |
| Haiti kreol | `ht` |
| héber | `he` |
| Hindi | `hi` |
| Magyar | `hu` |
| Izlandi | `is` |
| Indonéz | `id` |
| Ír | `ga` |
| Olasz | `it` |
| Japán | `ja` |
| Klingon | `tlh-Latn` |
| Koreai | `ko` |
| Kurd (Közép) | `ku-Arab` |
| Lett | `lv` |
| Litván | `lt` |
| Maláj | `ms` |
| Máltai | `mt` |
| Norvég | `nb` |
| Pasto | `ps` |
| perzsa | `fa` |
| Lengyel | `pl` |
| Portugál | `pt` |
| Román | `ro` |
| Orosz | `ru` |
| Szerb (cirill betűs) | `sr-Cyrl` |
| Szerb (latin betűs) | `sr-Latn` |
| Szlovák | `sk` |
| Szlovén | `sl` |
| Spanyol | `es` |
| szuahéli | `sw` |
| svéd | `sv` |
| Tahitian | `ty` |
| Thai | `th` |
| Török | `tr` |
| Ukrán | `uk` |
| urdu | `ur` |
| Vietnámi | `vi` |
| walesi | `cy` |
| Yucatec Maya | `yua` |

### <a name="transliterate"></a>Átírás

A Átbetűzés metódus a következő nyelveket támogatja. A "to/from", "<->" kifejezés azt jelzi, hogy a nyelv a felsorolt parancsfájlokból vagy azok egyikére is átadható. A "-->" érték azt jelzi, hogy a nyelv csak az egyik parancsfájlból a másikba való átírására használható.

[A Átbetűzés-művelet dokumentációjának megtekintése](reference/v3-0-translate.md)


| Nyelv    | Nyelvkód | Script | Hová/honnan | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arab | `ar` | arab `Arab` | <--> | Latin betűs `Latn` |
| Asszámi | `as` | bengáli `Beng` | <--> | Latin betűs `Latn` |
| Bangla  | `bn` | bengáli `Beng` | <--> | Latin betűs `Latn` |
|belorusz| `be` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
|Bolgár| `bg` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | Latin betűs `Latn` |
| Kínai (egyszerűsített) | `zh-Hans` | kínai (egyszerűsített) `Hans`| <--> | kínai (hagyományos) `Hant`|
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | Latin betűs `Latn` |
| Kínai (hagyományos) | `zh-Hant` | kínai (hagyományos) `Hant`| <--> | kínai (egyszerűsített) `Hans` |
|Görög| `el` | görög `Grek`  | <--> | Latin betűs `Latn` |
| gudzsaráti | `gu`  | gudzsaráti `Gujr` | <--> | Latin betűs `Latn` |
| héber | `he` | héber `Hebr` | <--> | Latin betűs `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latin betűs `Latn` |
| Japán | `ja` | japán `Jpan` | <--> | Latin betűs `Latn` |
| kannada | `kn` | kannada `Knda` | <--> | Latin betűs `Latn` |
|Kazak| `kk` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
|Koreai| `ko` | koreai `Kore`  | <--> | Latin betűs `Latn` |
|kirgiz| `ky` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
|Macedón| `mk` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| malajálam | `ml` | malajálam `Mlym` | <--> | Latin betűs `Latn` |
| marathi | `mr` | Devanagari `Deva` | <--> | Latin betűs `Latn` |
|mongol| `mn` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| Odia | `or` | Oriya `Orya` | <--> | Latin betűs `Latn` |
|perzsa| `fa` | arab `Arab`  | <--> | Latin betűs `Latn` |
| pandzsábi | `pa` | Gurmukhi `Guru`  | <--> | Latin betűs `Latn`  |
|Orosz| `ru` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| Szerb (cirill betűs) | `sr-Cyrl` | Cirill betűs `Cyrl`  | --> | Latin betűs `Latn` |
| Szerb (latin betűs) | `sr-Latn` | Latin betűs `Latn` | --> | Cirill betűs `Cyrl`|
|Szindhi| `sd` | arab `Arab`  | <--> | Latin betűs `Latn` |
|Szingaléz| `si` | Szingaléz `Sinh`  | <--> | Latin betűs `Latn` |
|Tádzsik| `tg` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| tamil | `ta` | tamil `Taml` | <--> | Latin betűs `Latn` |
|tatár| `tt` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
| telugu | `te` | telugu `Telu` | <--> | Latin betűs `Latn` |
| Thai | `th` | thai `Thai` | --> | Latin betűs `Latn` |
|Ukrán| `uk` | Cirill betűs `Cyrl`  | <--> | Latin betűs `Latn` |
|urdu| `ur` | arab `Arab`  | <--> | Latin betűs `Latn` |

### <a name="dictionary"></a>Szótár

A szótár a következő nyelveket támogatja angol nyelven vagy angol nyelven a keresési és példákat használó módszerek használatával.

Tekintse meg a [szótári keresési](reference/v3-0-dictionary-lookup.md) és a [szótári példákhoz](reference/v3-0-dictionary-examples.md) kapcsolódó dokumentációt.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
| búr      | `af`          |
| Arab       | `ar`          |
| Bangla      | `bn`          |
| Bosnyák (latin betűs)      | `bs`          |
| Bolgár      | `bg`          |
| Katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| Horvát      | `hr`          |
| Cseh      | `cs`          |
| Dán      | `da`          |
| Holland      | `nl`          |
| Észt      | `et`          |
| Finn      | `fi`          |
| Francia      | `fr`          |
| Német      | `de`          |
| Görög      | `el`          |
| Haiti kreol      | `ht`          |
| héber      | `he`          |
| Hindi      | `hi`          |
| Hmong DAW      | `mww`          |
| Magyar      | `hu`          |
| Izlandi    | `is`  |
| Indonéz      | `id`          |
| Olasz      | `it`          |
| Japán      | `ja`          |
| Klingon      | `tlh`          |
| Koreai      | `ko`          |
| Lett      | `lv`          |
| Litván      | `lt`          |
| Maláj      | `ms`          |
| Máltai      | `mt`          |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília)     | `pt`          |
| Román      | `ro`          |
| Orosz      | `ru`          |
| Szerb (latin betűs)      | `sr-Latn`          |
| Szlovák     | `sk`          |
| Szlovén      | `sl`          |
| Spanyol      | `es`          |
| szuahéli      | `sw`          |
| svéd      | `sv`          |
| tamil      | `ta`          |
| Thai      | `th`          |
| Török      | `tr`          |
| Ukrán      | `uk`          |
| urdu      | `ur`          |
| Vietnámi      | `vi`          |
| walesi      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Programozott módon férhet hozzá a Translator Language listához

A fordító által támogatott nyelvek listáját a languages metódus használatával kérheti le. A listát a szolgáltatás, a Nyelvkód, valamint az angol nyelvű és más támogatott nyelven is megtekintheti. Ezt a listát a Microsoft Translator Service automatikusan frissíti, mivel a rendszer új nyelveket tesz elérhetővé.

[Nyelvek megtekintése – útmutató – dokumentáció](reference/v3-0-languages.md)

## <a name="customization"></a>Testreszabás

Az [Egyéni Translator](https://aka.ms/CustomTranslator)használatával a következő nyelvek érhetők el az angol nyelvre és a testreszabásra.

| Nyelv    | Nyelvkód |
|:----------- |:-------------:|
|búr| `af`|
| Arab       | `ar`          |
| Bangla      | `bn`          |
| Bosnyák (latin betűs)      | `bs`          |
| Bolgár      | `bg`          |
|Katalán|   `ca`    |
| kínai (egyszerűsített)      | `zh-Hans`          |
|kínai (hagyományos)|   `zh-Hant`   |
| Horvát      | `hr`          |
| Cseh      | `cs`          |
| Dán      | `da`          |
| Holland      | `nl`          |
| Angol    | `en`     |
| Észt      | `et`          |
|Fidzsi|    `fj`    |
|Filipino|  `fil`   |
| Finn      | `fi`          |
| Francia      | `fr`          |
| Német      | `de`          |
| Görög      | `el`          |
| gudzsaráti| `gu`    |
| héber      | `he`          |
| Hindi      | `hi`          |
| Magyar      | `hu`          |
| Izlandi | `is` |
| Indonéz|   `id`    |
| Ír | `ga`  |
| Olasz      | `it`          |
| Japán      | `ja`          |
|kannada|`kn`|
| Koreai      | `ko`          |
| Lett      | `lv`          |
| Litván      | `lt`          |
| Malgas| `mg`    |
| Maláj|    `ms` |
|Máltai|   `mt`    |
| maori| `mi`  |
| marathi| `mr`  |
| Norvég      | `nb`          |
| perzsa      | `fa`          |
| Lengyel      | `pl`          |
| Portugál (Brazília) | `pt` |
| pandzsábi|`pa`|
| Román      | `ro`          |
| Orosz      | `ru`          |
| Szamoai|   `sm`    |
| Szerb (latin betűs)      | `sr-Latn`          |
| Szlovák     | `sk`          |
| Szlovén      | `sl`          |
| Spanyol      | `es`          |
| szuahéli|  `sw`    |
| svéd      | `sv`          |
|Tahitian|  `ty`    |
| Thai      | `th`          |
|Tongai|    `to`    |
| Török      | `tr`          |
| Ukrán      | `uk`          |
| urdu| `ur`    |
| Vietnámi      | `vi`          |
| walesi | `cy` |

## <a name="speech-translation"></a>Speech Translation
A fordítás a fordító és a Cognitive Services Speech Service használatával érhető el. A [beszédfelismerési szolgáltatás dokumentációjában](../speech-service/index.yml) tájékozódhat a beszédfelismerés használatáról és az összes [elérhető nyelvi lehetőségről](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Diktálás
A beszédet szöveggé alakíthatja, hogy a választott szöveg nyelvét lefordítsa. A beszédfelismerési szöveg fordítására, illetve beszéd-beszéd fordításra szolgál, ha a beszédfelismeréssel együtt használja.

| Nyelv    |
|:----------- |
|Arab|
|Kantoni (hagyományos)|
|Katalán|
|kínai (egyszerűsített)|
|kínai (hagyományos)|
|Dán|
|Holland|
|Angol|
|Finn|
|Francia|
|Francia (Kanada)|
|Német|
|gudzsaráti|
|Hindi|
|Olasz|
|Japán|
|Koreai|
|marathi|
|Norvég|
|Lengyel|
|Portugál (Brazília)|
|Portugál (Portugália)|
|Orosz|
|Spanyol|
|Svéd|
|tamil|
|telugu|
|Thai|
|Török|

### <a name="text-to-speech"></a>Szövegfelolvasás
Szöveg konvertálása beszédre A szöveg és a beszéd használatával a fordítási eredmények hallható kimenetét, illetve beszéd – beszéd fordítást lehet használni a beszédfelismerési szöveggel történő használathoz. 

| Nyelv |
|:-|
| Arab |
| Bolgár |
| Kantoni (hagyományos) |
| Katalán |
| kínai (egyszerűsített) |
| kínai (hagyományos) |
| Horvát |
| Cseh |
| Dán |
| Holland |
| Angol |
| Finn |
| Francia |
| Francia (Kanada) |
| Német |
| Görög |
| héber |
| Hindi |
| Magyar |
| Indonéz |
| Olasz |
| Japán |
| Koreai |
| Maláj |
| Norvég |
| Lengyel |
| Portugál (Brazília) |
| Portugál (Portugália) |
| Román |
| Orosz |
| Szlovák |
| Szlovén |
| Spanyol |
| Svéd |
| tamil |
| telugu |
| Thai |
| Török |
| Vietnámi |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>A Microsoft Translator webhelyén megtekintheti a nyelvi listát

A nyelvek gyors áttekintéséhez a Microsoft Translator webhelye a fordító által támogatott összes nyelvet megjeleníti a fordítási és beszédfelismerési szolgáltatásokhoz. Ez a lista nem tartalmazza a fejlesztői adatokat, például a nyelvi kódokat.

[A nyelvek listája](https://www.microsoft.com/translator/languages.aspx)
