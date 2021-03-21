---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: A szöveg-beszéd Helm diagram konfigurációs beállításainak részletei.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002278"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Szöveg-beszéd (Aldiagram: diagramok/textToSpeech)

Az "esernyő" diagram felülbírálásához adja hozzá az előtagot `textToSpeech.` bármelyik paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert például: felülbírálások `textToSpeech.numberOfConcurrentRequest` `numberOfConcurrentRequest` .

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt jelzi, hogy engedélyezve van-e a **szöveg-beszéd** szolgáltatás. | `false` |
| `numberOfConcurrentRequest` | A **szöveg-beszéd** típusú szolgáltatás egyidejű kérelmeinek száma. Ez a diagram automatikusan kiszámítja a processzor-és memória-erőforrásokat ezen érték alapján. | `2` |
| `optimizeForTurboMode`| Azt határozza meg, hogy a szolgáltatásnak szöveges fájlok használatával kell-e optimalizálnia a szövegbevitelt. Ha `true` Ez a diagram több CPU-erőforrást fog kiosztani a szolgáltatás számára. | `false` |
| `image.registry`| A **szöveg-beszéd** Docker-rendszerkép beállításjegyzéke. | `containerpreview.azurecr.io` |
| `image.repository` | A **szöveg-beszéd** Docker-rendszerkép tárháza. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A **szöveg és a beszéd** Docker-rendszerkép címkéje. | `latest` |
| `image.pullSecrets` | A **szöveg-beszéd** Docker-rendszerkép kihúzásának képtitka. | |
| `image.pullByHash`| Azt jelzi, hogy a Docker-rendszerképet kivonattal húzta-e. Ha `true` `image.hash` kötelező megadni. | `false` |
| `image.hash`| A **szöveg-beszéd** Docker-rendszerkép kivonata. Csak akkor használatos, ha `image.pullByHash: true` .  | |
| `image.args.eula` szükséges | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték a `accept` | |
| `image.args.billing` szükséges | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el. | |
| `image.args.apikey` szükséges | A számlázási információk nyomon követésére szolgál. ||
| `service.type` | A **szöveg-beszéd** típusú szolgáltatás Kubernetes. További részletekért tekintse meg a [Kubernetes-szolgáltatások típusaival kapcsolatos útmutatót](https://kubernetes.io/docs/concepts/services-networking/service/) , és ellenőrizze a felhőalapú szolgáltatók támogatását. | `LoadBalancer` |
| `service.port`|  A **szöveg-beszéd** típusú szolgáltatás portja. | `80` |
| `service.annotations` | A szolgáltatás metaadatainak **szöveg-beszéd** típusú megjegyzései A jegyzetek a kulcs érték párok. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Azt jelzi, hogy engedélyezve van-e a [horizontális Pod automéretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha `true` a, a a `text-to-speech-autoscaler` Kubernetes-fürtben lesz telepítve. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy engedélyezve van-e a [Pod-megszakadási költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha `true` a, a a `text-to-speech-poddisruptionbudget` Kubernetes-fürtben lesz telepítve. | `true` |