---
title: Docker-lekérés a Nyelvfelismerés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Nyelvfelismerés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906099"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Docker-lekérés a Nyelvfelismerés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
