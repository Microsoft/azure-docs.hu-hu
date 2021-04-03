---
title: Docker-lekérés a Kulcsszókeresés tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs Kulcsszókeresés tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906016"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-lekérés a Kulcsszókeresés tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal tölthet le egy tároló rendszerképet a Microsoft Container Registryból.

A Text Analytics tárolók számára elérhető címkék teljes leírását a Docker hub [kulcsszókeresés](https://go.microsoft.com/fwlink/?linkid=2018757) tárolójában találja.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
