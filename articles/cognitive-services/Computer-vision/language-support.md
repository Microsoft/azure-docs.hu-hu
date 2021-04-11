---
title: Nyelvi támogatás – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Computer Vision szolgáltatások által támogatott természetes nyelvek listáját tartalmazza; OCR, képelemzés.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 43804907d68ddd58e55c7ce453e0731d0cb4b9b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560378"
---
# <a name="language-support-for-computer-vision"></a>A Computer Vision nyelvi támogatása

A Computer Vision egyes funkciói több nyelvet támogatnak; az itt nem említett szolgáltatások csak az angol nyelvet támogatják.

## <a name="optical-character-recognition-ocr"></a>Optikai karakterfelismerés (OCR)

Computer Vision OCR API-jai több nyelvet támogatnak. Nem igénylik a nyelvi kód megadását. További információ: [optikai karakterfelismerés (OCR)](concept-recognizing-text.md) .

|Nyelv| Nyelvkód | OCR API | Olvasási 3.0/3.1 | A v 3.2 előzetes verziójának olvasása |
|:-----|:----:|:-----:|:---:|:---:|
|búr|`af`| | |✔ |
|albán |`sq`| | |✔ |
|Arab | `ar`|✔ | | |
|Asztúriai |`ast`| | |✔ |
|Baszk  |`eu`| | |✔ |
|Bislama   |`bi`| | |✔ |
|Breton    |`br`| | |✔ |
|Katalán    |`ca`| | |✔ |
|Cebui    |`ceb`| | |✔ |
|Chamoru  |`ch`| | |✔ |
|Kínai (egyszerűsített) | `zh-Hans`|✔ | |✔ |
|Kínai (hagyományos) | `zh-Hant`|✔ | |✔ |
|Korni     |`kw`| | |✔ |
|Korzikai      |`co`| | |✔ |
|Krími tatár (latin betűs)  |`crh`| | |✔ |
|Cseh | `cs` |✔ | |✔ |
|Dán | `da` |✔ | |✔ |
|Holland | `nl` |✔ |✔ |✔ |
|Angol | `en` |✔ |✔ |✔ |
|Észt  |`crh`| | |✔ |
|Fidzsi |`fj`| | |✔ |
|Filipino  |`fil`| | |✔ |
|finn | `fi` |✔ | |✔ |
|Francia | `fr` |✔ |✔ |✔ |
|Friuli  | `fur` | | |✔ |
|Gallego   | `gl` | | |✔ |
|Német | `de` |✔ |✔ |✔ |
|Gilbertese    | `gil` | | |✔ |
|Görög | `el` |✔ | | |
|Grönlandi   | `kl` | | |✔ |
|Haiti kreol  | `ht` | | |✔ |
|Hani  | `hni` | | |✔ |
|Hmong DAW (latin betűs) | `mww` | | |✔ |
|Magyar | `hu` |✔ | | ✔ |
|Indonéz   | `id` | | |✔ |
|Interlingva  | `ia` | | |✔ |
|Inuktitut (latin betűs)  | `iu` | | |✔ |
|Ír    | `ga` | | |✔ |
|Olasz | `it` |✔ |✔ |✔ |
|Japán | `ja` |✔ | |✔ |
|Jávai | `jv` | | |✔ |
|Quiché  | `quc` | | |✔ |
|I kreol | `kea` | | |✔ |
|Kachin (latin betűs) | `kac` | | |✔ |
|Kara-Kalpak | `kaa` | | |✔ |
|Kasub | `csb` | | |✔ |
|Khasi  | `kha` | | |✔ |
|Koreai | `ko` |✔ | |✔ |
|Kurd (latin betűs) | `kur` | | |✔ |
|Luxemburgi  | `lb` | | |✔ |
|Maláj (latin betűs)  | `ms` | | |✔ |
|Manx  | `gv` | | |✔ |
|Nápolyi   | `nap` | | |✔ |
|Norvég | `nb` |✔ | | |
|Norvég | `no` | | |✔ |
|Okszitán | `oc` | | |✔ |
|Lengyel | `pl` |✔ | |✔ |
|Portugál | `pt` |✔ |✔ |✔ |
|Román | `ro` |✔ | | |
|Réto  | `rm` | | |✔ |
|Orosz | `ru` |✔ | | |
|Skót  | `sco` | | |✔ |
|Skót gael  | `gd` | | |✔ |
|Szerb (cirill betűs) | `sr-Cyrl` |✔ | | |
|Szerb (latin betűs) | `sr-Latn` |✔ | | |
|Szlovák | `sk` |✔ | | |
|Szlovén  | `slv` | | |✔ |
|Spanyol | `es` |✔ |✔ |✔ |
|Szuahéli (latin betűs)  | `sw` | | |✔ |
|svéd | `sv` |✔ | |✔ |
|Tatár (latin betűs)  | `tat` | | |✔ |
|Tetum    | `tet` | | |✔ |
|Török | `tr` |✔ | |✔ |
|Felső szorb  | `hsb` | | |✔ |
|Üzbég (latin betűs)     | `uz` | | |✔ |
|Volapük   | `vo` | | |✔ |
|Wallisi    | `wae` | | |✔ |
|Nyugati fríz | `fy` | | |✔ |
|Yucatec Maya | `yua` | | |✔ |
|Zhuang | `za` | | |✔ |
|zulu  | `zu` | | |✔ |

## <a name="image-analysis"></a>Képelemzés

Az [elemzés-rendszerkép](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API egyes műveletei más nyelveket is visszaadhatnak a `language` lekérdezési paraméterrel megadottak szerint. A többi művelet angol nyelven adja vissza az eredményeket, függetlenül attól, hogy milyen nyelven van megadva, mások pedig kivételt képeznek a nem támogatott nyelvek esetében. A műveletek a és a `visualFeatures` `details` lekérdezési paraméterekkel vannak megadva. az [áttekintést](overview.md) a képelemzéssel elvégezhető műveletek listájáról tekintheti meg.

|Nyelv | Nyelvkód | Kategóriák | Címkék | Leírás | Felnőtt | Márkák | Szín | Arcok | ImageType | Objektumokat | Hírességek | Arcrészek |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Kínai | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angol | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japán | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugál | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spanyol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
