---
title: Támogatott nyelvek – Translator Speech API
titlesuffix: Azure Cognitive Services
description: Tekintse meg a Translator Speech API által támogatott nyelveket.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ed8f693e4dc0344a0117ae9d6992b925992ef0c4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446913"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>A Translator Speech API által támogatott nyelvek

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

A következő nyelvek támogatottak beszédalapú fordítási. Ha mindkét nyelveket támogatja a tolmácsolás, beszédfelismerés, beszédfelismerési vagy Diktálás érhető el. Ha a célként megadott nyelv nem támogatott beszédalapú fordítási, szövegfordítás csak beszédfelismerés érhető el.

| Beszédfelismerési nyelvet    |
|:----------- |
| Arab (Modern Standard)      |
| Brazíliai portugál nyelveken     |
| Kínai (Mandarin)      |
| Angol      |
| francia      |
| német      |
| olasz      |
| japán      |
| orosz      |
| spanyol      |

A Translator Speech API a következő nyelveket támogatja mint szövegfordítás beszédfelismerés cél nyelvét.

| Szöveg nyelvét    | Nyelvkód |
|:----------- |:-------------:|
| afrikaans      | `af`          |
| arab       | `ar`          |
| Bengáli      | `bn`          |
| bosnyák (latin betűs)      | `bs`          |
| bolgár      | `bg`          |
| Kantoni (hagyományos)      | `yue`          |
| katalán      | `ca`          |
| kínai (egyszerűsített)      | `zh-Hans`          |
| kínai (hagyományos)      | `zh-Hant`          |
| horvát      | `hr`          |
| cseh      | `cs`          |
| dán      | `da`          |
| holland      | `nl`          |
| Angol      | `en`          |
| észt      | `et`          |
| Fijian      | `fj`          |
| filippínó      | `fil`          |
| finn      | `fi`          |
| francia      | `fr`          |
| német      | `de`          |
| görög      | `el`          |
| Haitian Creole      | `ht`          |
| héber      | `he`          |
| hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| magyar      | `hu`          |
|izlandi|`is`          |
| indonéz      | `id`          |
| olasz      | `it`          |
| japán      | `ja`          |
| szuahéli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| koreai      | `ko`          |
| lett      | `lv`          |
| litván      | `lt`          |
| Madagaszkári      | `mg`          |
| maláj      | `ms`          |
| máltai      | `mt`          |
| norvég      | `nb`          |
| perzsa      | `fa`          |
| lengyel      | `pl`          |
| portugál      | `pt`          |
| Queretaro Otomi      | `otq`          |
| román      | `ro`          |
| orosz      | `ru`          |
| Samoa      | `sm`          |
| szerb (cirill betűs)      | `sr-Cyrl`          |
| szerb (latin betűs)      | `sr-Latn`          |
| szlovák     | `sk`          |
| szlovén      | `sl`          |
| spanyol      | `es`          |
| svéd      | `sv`          |
| Tahitian      | `ty`          |
| tamil      | `ta`          |
| thai      | `th`          |
| Tongan      | `to`          |
| török      | `tr`          |
| ukrán      | `uk`          |
| urdu      | `ur`          |
| vietnami      | `vi`          |
| walesi      | `cy`          |
| A Maya alkalmazáshoz Yucatec      | `yua`          |

## <a name="access-the-list-programmatically"></a>A lista programozott elérése

Programozott módon használja a nyelvek erőforrás támogatott nyelvek listáját is elérheti. A lista ismerteti a nyelvkódot, valamint a nyelv nevének az angol, és minden más támogatott nyelven. Ez a lista automatikusan frissül a Translator Speech szolgáltatással, amint elérhetővé válnak az új nyelvek.

A nyelvek erőforrás beszéd, a szöveg és a szöveg-beszéd átalakítás, támogatott nyelvek listáját adja vissza. A nyelvek erőforrás nem igényel hitelesítést.

[Látogasson el az API-referencia, és próbálja ki a nyelvet metódus](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Hozzáférés a listában, a Microsoft Translator webhelyen

A Microsoft Translator webhely a nyelvek egy gyors pillantást mutatja be, a Translator Text és a Speech API-k által támogatott összes nyelv. Ez a lista nem tartalmaz fejlesztői jellemző információkat, például nyelvi kódot.

[Nyelvek listáját lásd:](https://www.microsoft.com/translator/languages.aspx)
