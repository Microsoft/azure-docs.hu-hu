---
title: Tárolók korlátai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló nyelvei támogatottak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002339"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUIS) tároló korlátozásai

A LUIS-tárolók néhány jelentős korlátozással rendelkeznek. A nem támogatott függőségek közül a jelen cikk ezeket a korlátozásokat részletezi.

## <a name="supported-dependencies-for-latest-container"></a>A tároló által támogatott függőségek `latest`

A legújabb LUIS-tároló a következőket támogatja:

* [Új előre összeépített tartományok](luis-reference-prebuilt-domains.md): ezek a nagyvállalati szintű tartományok többek között entitásokat, például hosszúságú kimondott szöveg és mintákat tartalmaznak. Kiterjesztheti ezeket a tartományokat saját használatra.

## <a name="unsupported-dependencies-for-latest-container"></a>A tárolóhoz tartozó függőségek nem támogatottak `latest`

A [tárolóba való exportáláshoz](luis-container-howto.md#export-packaged-app-from-luis)el kell távolítania a nem támogatott függőségeket a Luis-alkalmazásból. Ha tárolóra próbál exportálni, a LUIS-portál ezeket a nem támogatott szolgáltatásokat jelenti, amelyeket el kell távolítania.

LUIS-alkalmazást használhat, ha az **nem tartalmazza** a következő függőségek egyikét sem:

Nem támogatott alkalmazások konfigurációi|Részletek|
|--|--|
|A tároló kulturális környezetei nem támogatottak| A holland ( `nl-NL` ), a japán () `ja-JP` és a német () `de-DE` nyelveket csak a [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions)támogatja.|
|Nem támogatott entitások minden kultúrához|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) előre összeépített entitás minden kultúrához|
|Nem támogatott entitások az angol ( `en-US` ) kulturális környezethez|[GeographyV2](luis-reference-prebuilt-geographyV2.md) előre elkészített entitások|
|Beszéd alapozó|A tárolóban a külső függőségek nem támogatottak.|
|Hangulatelemzés|A tárolóban a külső függőségek nem támogatottak.|
|Bing – helyesírás-ellenőrzés|A tárolóban a külső függőségek nem támogatottak.|

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS-tárolók támogatják a LUIS megfelelő által [támogatott nyelvek](luis-language-support.md#languages-supported) egy részhalmazát. A LUIS-tárolók képesek megismerni a hosszúságú kimondott szöveg a következő nyelveken:

| Nyelv | Területi beállítás | Előre elkészített tartomány | Előre elkészített entitás | Kifejezések listája – javaslatok | **[Szöveges elemzés](../text-analytics/language-support.md)<br>(Hangulat és<br>Kulcsszavak|
|--|--|:--:|:--:|:--:|:--:|
| angol (Egyesült Államok) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Arab (előzetes verzió – modern standard arab) |`ar-AR`|❌|❌|❌|❌|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francia (Franciaország) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francia (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Német |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Olasz |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreai |`ko-KR` | ✔️ | ❌ | ❌ | Csak a *kulcs kifejezése* |
| marathi | `mr-IN`|❌|❌|❌|❌|
| Portugál (Brazília) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem minden alkultúra |
| Spanyol (Spanyolország) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanyol (Mexikó)|`es-MX` | ❌ | ❌ |✔️|✔️|
| tamil | `ta-IN`|❌|❌|❌|❌|
| telugu | `te-IN`|❌|❌|❌|❌|
| Török | `tr-TR` |✔️| ❌ | ❌ | Csak *hangulat* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
