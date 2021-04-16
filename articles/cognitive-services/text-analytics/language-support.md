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
ms.openlocfilehash: c0d91f803822e018f4363bb78d9138e2efe16f8a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531432"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics API v3 nyelvi támogatása 

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

| Nyelv              | Nyelvkód | v3-támogatás | A v3-as modellverzió indítása: |              Jegyzetek |
|:----------------------|:-------------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |         2019-10-01         | `zh` is elfogadva |
| Chinese-Traditional   |   `zh-hant`   |    ✓      |         2019-10-01         |                    |
| Holland                 |     `nl`      |     ✓      |         2019-10-01        |                    |
| Angol               |     `en`      |     ✓      |         2019-10-01         |                    |
| Francia                |     `fr`      |     ✓      |         2019-10-01         |                    |
| Német                |     `de`      |     ✓      |         2019-10-01         |                    |
| Hindi                 |    `hi`       |     ✓      |         2020-04-01         |                    |
| Olasz               |     `it`      |     ✓      |         2019-10-01         |                    |
| Japán              |     `ja`      |     ✓      |         2019-10-01         |                    |
| Koreai                |     `ko`      |    ✓      |         2019-10-01         |                    |
| Norvég (Bokmål)   |     `no`      |     ✓      |         2020-07-01         |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |         2020-04-01         |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |         2019-10-01         | `pt` is elfogadva |
| Spanyol               |     `es`      |     ✓      |         2019-10-01         |                    |
| Török               |     `tr`      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Véleménybányászat (csak 3.1-es előzetes verzió)

| Nyelv              | Nyelvkód | A v3-as modellverziótól kezdve: |              Jegyzetek |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angol               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[nevesített entitások felismerése (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * Csak a "Személy", "Hely" és "Szervezet" entitásokat ad vissza a *jelű jelölésű nyelvekhez.

| Nyelv               | Nyelvkód | v3-támogatás | A v3-as modellverziótól kezdve: |       Jegyzetek        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Arab                 |     `ar`      |      ✓*    |               2019-10-01        |                    |
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` is elfogadva |
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
| Norvég (Bokmål)   |     `no`      |     ✓*      |               2019-10-01        | `nb` is elfogadva |
| Lengyel                |     `pl`      |     ✓*      |               2019-10-01        |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` is elfogadva |
| Orosz              |     `ru`      |     ✓*       |               2019-10-01        |                    |
| Spanyol               |     `es`      |     ✓       |               2020-04-01        |                    |
| svéd               |     `sv`      |     ✓*      |               2019-10-01        |                    |
| Török               |     `tr`      |     ✓*      |               2019-10-01        |                    |

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/key-phrase-extraction)

| Nyelv              | Nyelvkód |  v3-támogatás | A v3-as modellverziótól kezdődően érhető el: |       Jegyzetek        |
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
| Norvég (Bokmål)   |     `no`      |     ✓      |                2020-07-01                 | `nb` is elfogadva |
| Lengyel                |     `pl`      |    ✓      |                2019-10-01                 |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |                2019-10-01                 |                    |
| Portugál (Portugália) |    `pt-PT`    |    ✓      |                2019-10-01                 | `pt` is elfogadva |
| Orosz               |     `ru`      |     ✓      |                2019-10-01                 |                    |
| Spanyol               |     `es`      |     ✓      |                2019-10-01                 |                    |
| svéd               |     `sv`      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitás-összekapcsolás](#tab/entity-linking)

| Nyelv | Nyelvkód |  v3-támogatás | A v3-as modellverziótól kezdődően érhető el: | Jegyzetek |
|:---------|:-------------:|:----------:|:-----------------------------------------:|:-----:|
| Angol  |     `en`      |     ✓      |                2019-10-01                 |       |
| Spanyol  |     `es`      |    ✓      |                2019-10-01                 |       |

#### <a name="personally-identifiable-information-pii"></a>[Személyazonosításra alkalmas adatok (PII)](#tab/pii)

| Nyelv               | Nyelvkód | v3-támogatás | A v3-as modellverziótól kezdve: |       Jegyzetek        |
|:-----------------------|:-------------:|:----------:|:-------------------------------:|:------------------:|
| Chinese-Simplified     |   `zh-hans`   |     ✓      |               2021-01-15        | `zh` is elfogadva |
| Angol                |     `en`      |     ✓      |               2020-07-01        |                    |
| Francia                 |     `fr`      |     ✓      |               2021-01-15        |                    |
| Német                 |     `de`      |     ✓      |               2021-01-15        |                    |
| Olasz               |     `it`      |     ✓       |               2021-01-15        |                    |
| Japán              |     `ja`      |     ✓       |               2021-01-15        |                    |
| Koreai                |     `ko`      |     ✓       |               2021-01-15        |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓       |               2021-01-15        |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓       |               2021-01-15        | `pt` is elfogadva |
| Spanyol               |     `es`      |     ✓       |               2020-04-01        |                    |

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes észlelni, és az észlelt nyelveket névvel és kóddal tudja visszaadni. Text Analytics Nyelvfelismerés nyelvi kód paraméterei megfelelnek a [BCP-47](https://tools.ietf.org/html/bcp47) szabványnak, és a legtöbbjük megfelel az [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) azonosítóknak. 

Ha ritkábban használt nyelven vannak kifejezve a tartalmak, megpróbálkhat Nyelvfelismerés, hogy visszaad-e egy kódot. A nem észlelhető nyelvekre adott válasz `unknown` a következő: .

| Nyelv | Nyelvkód | v3-támogatás | A v3-as modellverziótól kezdődően érhető el: |
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
|Közép-Khmer|`km`|✓|    |
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
|Hausa|`ha`|✓|2021-01-05|
|héber|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
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
|Kinyarwanda|`rw`|✓|2021-01-05|
|Kirghiz|`ky`|✓|2021-01-05|
|Koreai|`ko`|✓|    |
|Kurd|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latin|`la`|✓|    |
|Lett|`lv`|✓|    |
|Litván|`lt`|✓|    |
|Luxemburgi|`lb`|✓|2021-01-05|
|Macedón|`mk`|✓|    |
|Málgas|`mg`|✓|2020-09-01|
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
|Queretaro Otomi|`otq`|✓|2020-09-01|
|Román|`ro`|✓|    |
|Orosz|`ru`|✓|    |
|Szamoai|`sm`|✓|2020-09-01|
|Szerb|`sr`|✓|    |
|Shona|`sn`|✓|2021-01-05|
|Szindi|`sd`|✓|2021-01-05|
|Singhalese|`si`|✓|    |
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
