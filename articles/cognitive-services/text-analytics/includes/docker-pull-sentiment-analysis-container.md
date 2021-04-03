---
title: Docker-lekérés a Hangulatelemzés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906005"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-lekérés a Hangulatelemzés v3 tárolóhoz

Az érzelmek elemzése tároló v3 tárolója több nyelven is elérhető. Az angol tárolóhoz tartozó tároló letöltéséhez használja az alábbi parancsot. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Egy másik nyelvhez tartozó tároló letöltéséhez cserélje le az `en` alábbi nyelvi kódok egyikét. 

| Text Analytics tároló | Nyelvkód |
|--|--|
| Angol | `en` |
| Spanyol | `es` |
| Francia | `fr` |
| Olasz | `it` |
| Német | `de` |
| Egyszerűsített kínai | `zh` |
| Kínai – hagyományos | `zht` |
| Japán | `ja` |
| Portugál | `pt` |
| Holland | `nl` |

A Text Analytics tárolók számára elérhető címkék teljes leírását lásd: [Docker hub](https://go.microsoft.com/fwlink/?linkid=2018654).