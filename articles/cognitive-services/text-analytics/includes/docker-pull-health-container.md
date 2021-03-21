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
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965161"
---
Töltse ki és küldje el a [Cognitive Services kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést kérjen a Text Analytics for Health nyilvános előzetes verziójához.  Ez az alkalmazás a tárolóra és az üzemeltetett webes API nyilvános előzetes verziójára is érvényes.
Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Miután elküldte az űrlapot, az Azure Cognitive Services csapata ellenőrzi, hogy megfelelnek-e a Private Container registryhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> * Az űrlapon az Azure-előfizetési AZONOSÍTÓhoz társított e-mail-címet kell használnia.
> * A tároló futtatásához használt Azure-erőforrást a jóváhagyott Azure-előfizetés azonosítójával kell létrehozni. 
> * A Microsofttól származó alkalmazás állapotával kapcsolatos frissítésekért keresse meg az e-mail-címét (a beérkezett fájlok és a levélszemét mappáját is).

A jóváhagyást követően a rendszer e-mailt küld a hitelesítő adatokkal a privát tároló beállításjegyzékének eléréséhez.  A bevezetési e-mailben megadott hitelesítő adatokkal a Docker login paranccsal csatlakozhat a Cognitive Services tárolók saját tároló-beállításjegyzékéhez.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Használja a [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancsot a tároló rendszerképének letöltéséhez a saját tároló beállításjegyzékből.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
