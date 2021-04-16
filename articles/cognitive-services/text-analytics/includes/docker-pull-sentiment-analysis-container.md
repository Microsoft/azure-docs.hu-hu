---
title: Docker pull a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker pull parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564418"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-lekért tároló Hangulatelemzés v3-tárolóhoz

A hangulatelemzési tároló v3 tárolója több nyelven is elérhető. Az angol nyelvű tároló tárolóját az alábbi paranccsal töltheti le. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Ha egy másik nyelvhez is le kell töltenie a tárolót, cserélje le a helyére `en` az alábbi nyelvkódok valamelyikét. 

| Text Analytics Tároló | Nyelvkód |
|--|--|
| Chinese-Simplified    |   `zh-hans`   |
| Chinese-Traditional   |   `zh-hant`   |
| Holland                 |     `nl`      |
| Angol               |     `en`      |
| Francia                |     `fr`      |
| Német                |     `de`      |
| Hindi                 |    `hi`       |
| Olasz               |     `it`      |
| Japán              |     `ja`      |
| Koreai                |     `ko`      |
| Norvég (Bokmål)   |     `no`      |
| Portugál (Brazília)   |    `pt-BR`    |
| Portugál (Portugália) |    `pt-PT`    |
| Spanyol               |     `es`      |
| Török               |     `tr`      |

A tárolókhoz elérhető címkék teljes Text Analytics lásd: [Docker Hub.](https://go.microsoft.com/fwlink/?linkid=2018654)
