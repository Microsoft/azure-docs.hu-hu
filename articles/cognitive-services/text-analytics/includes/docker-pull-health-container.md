---
title: Docker-lekérés az állapotfigyelő tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs az Text Analytics for Health tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089710"
---
Töltse ki és küldje el a [Cognitive Services kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést kérjen a Text Analytics for Health nyilvános előzetes verziójához.  Ez az alkalmazás a tárolóra és az üzemeltetett webes API nyilvános előzetes verziójára is érvényes.
Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Miután elküldte az űrlapot, az Azure Cognitive Services csapata ellenőrzi, hogy megfelelnek-e a Private Container registryhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> * Az űrlapon az Azure-előfizetési AZONOSÍTÓhoz társított e-mail-címet kell használnia.
> * A tároló futtatásához használt Text Analytics erőforrást (számlázási végpontot és apikey) a jóváhagyott Azure-előfizetés azonosítójával kell létrehozni. 
> * A Microsofttól származó alkalmazás állapotával kapcsolatos frissítésekért keresse meg az e-mail-címét (a beérkezett fájlok és a levélszemét mappáját is).
> * A tároló URL-címe megváltozott, lásd az alábbi példákat. A tároló nem lesz letölthető a `containerpreview.azurecr.io` 2021. április 26-ától kezdődően.


A jóváhagyást követően a [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltheti le ezt a tároló-rendszerképet a Microsoft nyilvános tároló-beállításjegyzékből.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
