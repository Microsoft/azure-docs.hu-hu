---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs Nyelvfelismerés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 1ac035e669ae6d5fcec8dfa8faee12d34039004b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91779288"
---
A *nyelvfelismerés* tároló futtatásához hajtsa végre a következő `docker run` parancsot. Cserélje le az alábbi helyőrzőket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | A Text Analytics erőforrás kulcsa. A Azure Portal az erőforrás **kulcs és végpont** lapján található. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A Text Analytics API elérésére szolgáló végpont. A Azure Portal az erőforrás **kulcs és végpont** lapján található. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* *Nyelvfelismerés* tárolót futtat a tároló rendszerképből
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.