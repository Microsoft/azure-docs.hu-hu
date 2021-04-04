---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs Hangulatelemzés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 96b41c27b282bdc05dafa8c69398a11e8dbf3b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91779083"
---
A *Hangulatelemzés v3* tároló futtatásához hajtsa végre a következő `docker run` parancsot. Cserélje le az alábbi helyőrzőket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | A Text Analytics erőforrás kulcsa. A Azure Portal az erőforrás **kulcs és végpont** lapján található. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A Text Analytics API elérésére szolgáló végpont. A Azure Portal az erőforrás **kulcs és végpont** lapján található. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Hangulatelemzés* tárolót futtat a tároló rendszerképből
* Egy CPU mag és 8 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.