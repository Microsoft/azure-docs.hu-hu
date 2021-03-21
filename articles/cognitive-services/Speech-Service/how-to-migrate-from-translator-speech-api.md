---
title: Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan migrálhatja alkalmazásait a Translator Speech APIról a Speech Service-be.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95013638"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba

Ebből a cikkből megtudhatja, hogyan telepíthet alkalmazásokat a Microsoft Translator Speech APIból a [Speech szolgáltatásba](index.yml). Ez az útmutató ismerteti a Translator Speech API és a beszédfelismerési szolgáltatás közötti különbségeket, és javaslatot tesz az alkalmazások áttelepítésére vonatkozó stratégiákra.

> [!NOTE]
> A beszédfelismerési szolgáltatás nem fogadja el a Translator Speech API előfizetési kulcsát. Létre kell hoznia egy új Speech Service-előfizetést.

## <a name="comparison-of-features"></a>Funkciók összehasonlítása

| Szolgáltatás                                           | Translator Speech API                                  | Speech szolgáltatás | Részletek                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fordítás szöveggé                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Fordítás a beszédbe                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globális végpont                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A beszédfelismerési szolgáltatás nem biztosít globális végpontot. A globális végpontok automatikusan irányítják a forgalmat a legközelebbi regionális végpontra, és csökkentik a késést az alkalmazásban.                                                    |
| Regionális végpontok                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Kapcsolatok időbeli korlátja                             | 90 perc                                               | Korlátlan az SDK-val. 10 perc WebSockets-kapcsolattal.                                                                                                                                                                                                                                                                                   |
| Hitelesítési kulcs a fejlécben                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Több nyelv lett lefordítva egyetlen kérelemben | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Elérhető SDK-k                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Tekintse meg az elérhető SDK-k [Speech Service-dokumentációját](index.yml) .                                                                                                                                                    |
| WebSocket-kapcsolatok                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Nyelvek API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | A beszédfelismerési szolgáltatás a [Translator languages Reference]() című cikkben leírt nyelvek széles körét támogatja. |
| Trágár szűrő és jelölő                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM bemenetként                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Egyéb fájltípusok bemenetként                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Részleges eredmények                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Időzítési információ                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Korrelációs azonosító                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Egyéni beszédfelismerési modellek                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | A beszédfelismerési szolgáltatás olyan egyéni beszédfelismerési modelleket kínál, amelyek segítségével testre szabhatja a beszédfelismerést a szervezet egyedi szókincse számára.                                                                                                                                           |
| Egyéni fordítási modellek                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | A Microsoft Text Translation API-ra való feliratkozás lehetővé teszi, hogy [Egyéni Translator](https://www.microsoft.com/translator/business/customization/) használatával saját adatait használja a pontosabb fordításhoz.                                                 |

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezete rendelkezik olyan alkalmazásokkal, amelyeket a Translator Speech API használó fejlesztési vagy éles környezetben használ, frissítse őket a beszédfelismerési szolgáltatás használatára. Tekintse meg a [beszédfelismerési szolgáltatás](index.yml) dokumentációját, amely tartalmazza az elérhető SDK-kat, a kódot és az oktatóanyagokat. A Migrálás során vegye figyelembe a következőket:

* A beszédfelismerési szolgáltatás nem biztosít globális végpontot. Állapítsa meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes adatforgalmához. Ha nem, a leghatékonyabb végpont meghatározásához használja a térinformatikat.

* Ha az alkalmazás hosszú élettartamú kapcsolatokat használ, és nem tudja használni a rendelkezésre álló SDK-kat, használhat WebSockets-kapcsolatot. A 10 perces időtúllépési korlátot a megfelelő időpontokban történő újrakapcsolattal kezelheti.

* Ha az alkalmazás a Translator Service-t használja, és Translator Speech API az egyéni fordítási modellek engedélyezéséhez, a beszédfelismerési szolgáltatás használatával közvetlenül is hozzáadhat kategória-azonosítókat.

* A Translator Speech APItól eltérően a beszédfelismerési szolgáltatás több nyelven is elvégezheti a fordítást egyetlen kérelemben.

## <a name="next-steps"></a>Következő lépések

* [Próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free)
* [Gyors útmutató: beszédfelismerés felismerése UWP-alkalmazásban a Speech SDK használatával](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Lásd még

* [Mi a beszédfelismerési szolgáltatás?](overview.md)
* [A Speech Service és a Speech SDK dokumentációja](./speech-devices-sdk-quickstart.md?pivots=platform-android)