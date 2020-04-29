---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: Részletek a Speech Umbrella Helm diagram konfigurációs lehetőségeiről.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80874345"
---
### <a name="speech-umbrella-chart"></a>Beszéd (esernyő diagram)

A legfelső szintű "esernyő" diagram értékei felülbírálják a megfelelő Aldiagram-értékeket. Ezért itt fel kell venni az összes helyszíni testreszabott értéket.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- | -- |
| `speechToText.enabled` | Azt határozza meg, hogy a **beszédfelismerési** szolgáltatás engedélyezve van-e. | `true` |
| `speechToText.verification.enabled` | Azt jelzi `helm test` , hogy engedélyezve van-e a **beszédfelismerési** szolgáltatás funkciójának engedélyezése. | `true` |
| `speechToText.verification.image.registry` | A beszédfelismerési szolgáltatás teszteléséhez `helm test` használt Docker **-** rendszerkép tárháza. A Helm külön hüvelyt hoz létre a fürtön belül a teszteléshez, és lekéri a *tesztelési célú* rendszerképet ebből a beállításjegyzékből. | `docker.io` |
| `speechToText.verification.image.repository` | A beszédfelismerési szolgáltatás teszteléséhez `helm test` használt Docker **-** rendszerkép tárháza. A Helm test Pod ezt a tárházat használja a *tesztelési célú* rendszerképek lekéréséhez. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A `helm test` **beszédfelismerési** szolgáltatáshoz használt Docker-rendszerkép címkéje. A Helm test Pod ezt a címkét használja a *tesztelési célú* rendszerképek lekéréséhez. | `latest` |
| `speechToText.verification.image.pullByHash` | Azt határozza meg, hogy a rendszer *használja-e a tesztet* a Docker-rendszerképpel. `speechToText.verification.image.hash` Ha `true`a értéket hozzá kell adni, érvényes képkivonat-értékkel. | `false` |
| `speechToText.verification.image.arguments` | A *tesztelési célú* Docker-rendszerkép végrehajtásához használt argumentumok. A Helm test Pod a futtatáskor `helm test`átadja ezeket az argumentumokat a tárolónak. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Azt jelzi, hogy engedélyezve van-e a **szöveg-beszéd** szolgáltatás. | `true` |
| `textToSpeech.verification.enabled` | Azt jelzi `helm test` , hogy engedélyezve van-e a **beszédfelismerési** szolgáltatás funkciójának engedélyezése. | `true` |
| `textToSpeech.verification.image.registry` | A beszédfelismerési szolgáltatás teszteléséhez `helm test` használt Docker **-** rendszerkép tárháza. A Helm külön hüvelyt hoz létre a fürtön belül a teszteléshez, és lekéri a *tesztelési célú* rendszerképet ebből a beállításjegyzékből. | `docker.io` |
| `textToSpeech.verification.image.repository` | A beszédfelismerési szolgáltatás teszteléséhez `helm test` használt Docker **-** rendszerkép tárháza. A Helm test Pod ezt a tárházat használja a *tesztelési célú* rendszerképek lekéréséhez. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A `helm test` **beszédfelismerési** szolgáltatáshoz használt Docker-rendszerkép címkéje. A Helm test Pod ezt a címkét használja a *tesztelési célú* rendszerképek lekéréséhez. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Azt határozza meg, hogy a rendszer *használja-e a tesztet* a Docker-rendszerképpel. `textToSpeech.verification.image.hash` Ha `true`a értéket hozzá kell adni, érvényes képkivonat-értékkel. | `false` |
| `textToSpeech.verification.image.arguments` | A *tesztelési célú* Docker-képpel végrehajtandó argumentumok. A Helm test Pod a futtatáskor `helm test`átadja ezeket az argumentumokat a tárolónak. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |