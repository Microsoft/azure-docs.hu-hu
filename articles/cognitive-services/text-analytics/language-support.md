---
title: Nyelvi támogatás – Text Analytics API
titleSuffix: Azure Cognitive Services
description: A Text Analytics API által támogatott természetes nyelvek listája. Ez a cikk az egyes műveletek által támogatott nyelveket ismerteti.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: aahi
ms.openlocfilehash: 9e257209060396fbf45a4a24c38bc6950acb5168
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387026"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics API v3 nyelvi támogatás 

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

| Nyelv              | Nyelvkód | v3 támogatás | A v3 modell verziójának elindítása: |              Jegyzetek |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` szintén elfogadva |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Angol               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francia                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Német                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Olasz               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japán              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreai                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` szintén elfogadva |
| Spanyol               |     `es`      |     ✓      |         2019-10-01         |                    |
| Török               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Közvélemény-bányászat (csak előzetes verzió)

| Nyelv              | Nyelvkód | A v3 modell verziójától kezdve: |              Jegyzetek |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angol               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Elnevezett entitások felismerése](#tab/named-entity-recognition)

> [!NOTE]
> * Csak a "person", a "location" és a "Organization" entitások lesznek visszaadva a *-val jelölt nyelvekhez.

| Nyelv               | Nyelvkód | v3 támogatás | A v3 modell verziójától kezdve: |       Jegyzetek        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arab                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` szintén elfogadva |
| Chinese-Traditional   |   `zh-hant`   |     ✓*      |               2019-10-01        |                    |
| Cseh                 |     `cs`      |     ✓*      |               2019-10-01        |                    |
| Dán                |     `da`      |     ✓*      |               2019-10-01        |                    |
| Holland                 |     `nl`      |     ✓*      |               2019-10-01        |                    |
| Angol                |     `en`      |     ✓      |               2019-10-01        |                    |
| finn               |     `fi`      |     ✓*      |               2019-10-01        |                    |
| Francia                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Német                 |     `de`      |     ✓      |               2021-01-15        |                    |
| héber                |     `he`      |     ✓*      |               2019-10-01        |                    |
| Magyar             |     `hu`      |     ✓*      |               2019-10-01        |                    |
| Olasz               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japán              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreai                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Norvég (nyelven)   |     `no`      |     ✓*      |               2019-10-01        | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` szintén elfogadva |
| Orosz              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spanyol               |     `es`      |     ✓       |               2020-04-01        |                    |
| svéd               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Török               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Fő kifejezés kibontása](#tab/key-phrase-extraction)

| Nyelv              | Nyelvkód |  v3 támogatás | A v3 modell verziójától kezdődően érhető el: |       Jegyzetek        |
|:----------------------|:-------------:|:----------:|:-----------------------------------------:|:------------------:|
| Dán                |     `da`      |     ✓     |                2019-10-01                 |                    |
| Holland                 |     `nl`      |     ✓      |                2019-10-01                 |                    |
| Angol               |     `en`      |     ✓      |                2019-10-01                 |                    |
| finn               |     `fi`      |     ✓      |                2019-10-01                 |                    |
| Francia                |     `fr`      |     ✓      |                2019-10-01                 |                    |
| Német                |     `de`      |     ✓      |                2019-10-01                 |                    |
| Olasz               |     `it`      |     ✓      |                2019-10-01                 |                    |
| Japán              |     `ja`      |     ✓      |                2019-10-01                 |                    |
| Koreai                |     `ko`      |     ✓      |                2019-10-01                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |                2020-07-01                 | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugál (Portugália) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` szintén elfogadva |
| Orosz               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Spanyol               |     `es`      |     ✓      |                2019-10-01                 |                    |
| svéd               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitás összekapcsolása](#tab/entity-linking)

| Nyelv | Nyelvkód |  v3 támogatás | A v3 modell verziójától kezdődően érhető el: | Jegyzetek |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Angol  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spanyol  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Személyazonosításra alkalmas adatok](#tab/pii)

| Nyelv               | Nyelvkód | v3 támogatás | A v3 modell verziójától kezdve: |       Jegyzetek        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` szintén elfogadva |
| Angol                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francia                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Német                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Olasz               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japán              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreai                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` szintén elfogadva |
| Spanyol               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes észlelni, és az észlelt nyelveket a nevükkel és kóddal kell visszaadnia. Text Analytics Nyelvfelismerés nyelvkód-paraméterek a [BCP-47](https://tools.ietf.org/html/bcp47) szabványnak felelnek meg, és a legtöbbjük megfelel az [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) azonosítóknak. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

| Nyelv | Nyelvkód | v3 támogatás | A v3 modell verziójától kezdődően érhető el: |
|:-|:-:|:-:|:-:|
|búr|`af`|✓|    |
|albán|`sq`|✓|    |
|Amhara|`am`|✓|2021-01-05|
|Arab|`ar`|✓|    |
|örmény|`hy`|✓|    |
|Asszámi|`as`|✓|2021-01-05|
|Azerbajdzsáni|`az`|✓|2021-01-05|
|Baszk|`eu`|✓|    |
|belorusz|`be`|✓|    |
|bengáli|`bn`|✓|    |
|boszniai|`bs`|✓|2020-09-01|
|Bolgár|`bg`|✓|    |
|Burmai|`my`|✓|    |
|Katalán|`ca`|✓|    |
|Közép-khmer|`km`|✓|    |
|Kínai|`zh`|✓|    |
|kínai (egyszerűsített)|`zh_chs`|✓|    |
|kínai (hagyományos)|`zh_cht`|✓|    |
|Korzikai|`co`|✓|2021-01-05|
|Horvát|`hr`|✓|    |
|Cseh|`cs`|✓|    |
|Dán|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi|`dv`|✓|    |
|Holland|`nl`|✓|    |
|Angol|`en`|✓|    |
|Eszperantó|`eo`|✓|    |
|Észt|`et`|✓|    |
|Fidzsi|`fj`|✓|2020-09-01|
|finn|`fi`|✓|    |
|Francia|`fr`|✓|    |
|Gallego|`gl`|✓|    |
|grúz|`ka`|✓|    |
|Német|`de`|✓|    |
|Görög|`el`|✓|    |
|gudzsaráti|`gu`|✓|    |
|Haiti|`ht`|✓|    |
|Hausza|`ha`|✓|2021-01-05|
|héber|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong DAW|`mww`|✓|2020-09-01|
|Magyar|`hu`|✓|    |
|Izlandi|`is`|✓|    |
|Igbo|`ig`|✓|2021-01-05|
|Indonéz|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Ír|`ga`|✓|    |
|Olasz|`it`|✓|    |
|Japán|`ja`|✓|    |
|Jávai|`jv`|✓|2021-01-05|
|kannada|`kn`|✓|    |
|Kazak|`kk`|✓|2020-09-01|
|Kinyarvanda|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Koreai|`ko`|✓|    |
|Kurd|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latin betűs|`la`|✓|    |
|Lett|`lv`|✓|    |
|Litván|`lt`|✓|    |
|Luxemburgi|`lb`|✓|2021-01-05|
|Macedón|`mk`|✓|    |
|Malgas|`mg`|✓|2020-09-01|
|Maláj|`ms`|✓|    |
|malajálam|`ml`|✓|    |
|Máltai|`mt`|✓|    |
|maori|`mi`|✓|2020-09-01|
|marathi|`mr`|✓|2020-09-01|
|mongol|`mn`|✓|2021-01-05|
|Nepáli|`ne`|✓|2021-01-05|
|Norvég|`no`|✓|    |
|Norvég nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pasht|`ps`|✓|    |
|perzsa|`fa`|✓|    |
|Lengyel|`pl`|✓|    |
|Portugál|`pt`|✓|    |
|pandzsábi|`pa`|✓|    |
|Queretaro otomi|`otq`|✓|2020-09-01|
|Román|`ro`|✓|    |
|Orosz|`ru`|✓|    |
|Szamoai|`sm`|✓|2020-09-01|
|Szerb|`sr`|✓|    |
|Sona|`sn`|✓|2021-01-05|
|Szindhi|`sd`|✓|2021-01-05|
|Szingaléz|`si`|✓|    |
|Szlovák|`sk`|✓|    |
|Szlovén|`sl`|✓|    |
|Szomáli|`so`|✓|    |
|Spanyol|`es`|✓|    |
|Sundanese|`su`|✓|2021-01-05|
|szuahéli|`sw`|✓|    |
|svéd|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitian|`ty`|✓|2020-09-01|
|Tádzsik|`tg`|✓|2021-01-05|
|tamil|`ta`|✓|    |
|tatár|`tt`|✓|2021-01-05|
|telugu|`te`|✓|    |
|Thai|`th`|✓|    |
|Tibeti|`bo`|✓|2021-01-05|
|Tigrinya|`ti`|✓|2021-01-05|
|Tongai|`to`|✓|2020-09-01|
|Török|`tr`|✓|2021-01-05|
|Türkmén|`tk`|✓|2021-01-05|
|xhosza|`xh`|✓|2021-01-05|
|Joruba|`yo`|✓|2021-01-05|
|zulu|`zu`|✓|2021-01-05|

---

## <a name="see-also"></a>Lásd még

* [Mi a Text Analytics API?](overview.md)   
