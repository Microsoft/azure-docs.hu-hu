---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: A beszéd – szöveg Helm diagram konfigurációs beállításainak részletei.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002277"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Beszéd – szöveg (Aldiagram: diagramok/speechToText)

Az "esernyő" diagram felülbírálásához adja hozzá az előtagot `speechToText.` bármelyik paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert például: felülbírálások `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest` .

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt határozza meg, hogy a **beszédfelismerési** szolgáltatás engedélyezve van-e. | `false` |
| `numberOfConcurrentRequest` | A **beszédfelismerési** szolgáltatáshoz való egyidejű kérelmek száma. Ez a diagram automatikusan kiszámítja a processzor-és memória-erőforrásokat ezen érték alapján. | `2` |
| `optimizeForAudioFile`| Azt határozza meg, hogy a szolgáltatásnak hangfájlokon keresztül kell-e optimalizálnia a hangbemenetet. Ha `true` Ez a diagram több CPU-erőforrást fog kiosztani a szolgáltatás számára. | `false` |
| `image.registry`| A **beszéd-szöveg** Docker-rendszerkép beállításjegyzéke. | `containerpreview.azurecr.io` |
| `image.repository` | A **beszéd-szöveg** Docker-rendszerkép tárháza. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A **beszéd – szöveg** Docker-rendszerkép címkéje. | `latest` |
| `image.pullSecrets` | A **hang-szöveg** Docker-rendszerkép kihúzásának képtitka. | |
| `image.pullByHash`| Azt jelzi, hogy a Docker-rendszerképet kivonattal húzta-e. Ha `true` `image.hash` kötelező megadni. | `false` |
| `image.hash`| A **beszéd – szöveg** Docker-rendszerkép kivonata. Csak akkor használatos, ha `image.pullByHash: true` .  | |
| `image.args.eula` szükséges | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték a `accept` | |
| `image.args.billing` szükséges | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el. | |
| `image.args.apikey` szükséges | A számlázási információk nyomon követésére szolgál. ||
| `service.type` | A **beszéd-szöveg** típusú szolgáltatás Kubernetes. További részletekért tekintse meg a [Kubernetes-szolgáltatások típusaival kapcsolatos útmutatót](https://kubernetes.io/docs/concepts/services-networking/service/) , és ellenőrizze a felhőalapú szolgáltatók támogatását. | `LoadBalancer` |
| `service.port`|  A **beszédfelismerési** szolgáltatás portja. | `80` |
| `service.annotations` | A szolgáltatás metaadatainak **beszéd-szöveg** típusú megjegyzései. A jegyzetek a kulcs érték párok. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Azt jelzi, hogy engedélyezve van-e a [horizontális Pod automéretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha `true` a, a a `speech-to-text-autoscaler` Kubernetes-fürtben lesz telepítve. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy engedélyezve van-e a [Pod-megszakadási költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha `true` a, a a `speech-to-text-poddisruptionbudget` Kubernetes-fürtben lesz telepítve. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Érzelmek elemzése (Aldiagram: diagramok/speechToText)

A Helm diagram beszéd-szöveg tárolója és v 0.2.0-től kezdődően a következő paraméterek használhatók a Text Analytics API használatával való érzelmek elemzéséhez.

|Paraméter|Leírás|Értékek|Alapértelmezett|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Azt jelzi, hogy engedélyezve van-e a **text-Analytics** szolgáltatás| Igaz/hamis| `false`|
|`textanalytics.image.registry`| A **text-Analytics** Docker-rendszerkép beállításjegyzéke| érvényes Docker-rendszerkép beállításjegyzéke| |
|`textanalytics.image.repository`| A **text-Analytics** Docker-rendszerkép tárháza| érvényes Docker-rendszerkép tárháza| |
|`textanalytics.image.tag`| A **text-Analytics** Docker-rendszerkép címkéje| érvényes Docker-rendszerkép címkéje| |
|`textanalytics.image.pullSecrets`| A **szöveg-Analytics** Docker-rendszerkép kihúzásának képtitka| érvényes titkok neve| |
|`textanalytics.image.pullByHash`| Megadja, hogy a Docker-rendszerkép kihúzása kivonattal történjen-e.  Ha `yes` szükséges, akkor is `image.hash` . Ha a `no` beállítás értéke "false" (hamis). Az alapértelmezett szint a `false`.| Igaz/hamis| `false`|
|`textanalytics.image.hash`| A **text-Analytics** Docker-rendszerkép kivonata. Csak a használatával `image.pullByHash:true` .| érvényes Docker-rendszerkép kivonata | |
|`textanalytics.image.args.eula`| A **text-Analytics** tároló egyik kötelező argumentuma, amely azt jelzi, hogy elfogadta a licencet. A beállítás értékének a következőnek kell lennie: `accept` .| `accept`, ha a tárolót szeretné használni | |
|`textanalytics.image.args.billing`| A **text-Analytics** tároló egyik kötelező argumentuma, amely a számlázási végpont URI azonosítóját adja meg. A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el.|érvényes számlázási végpont URI-ja||
|`textanalytics.image.args.apikey`| Az egyik kötelező argumentum a **text-Analytics-** tárolóban, amely a számlázási információk nyomon követésére szolgál.| érvényes apikey||
|`textanalytics.cpuRequest`| A kért CPU a **text-Analytics-** tárolóhoz| int| `3000m`|
|`textanalytics.cpuLimit`| A korlátozott CPU a **text-Analytics-** tárolóhoz| | `8000m`|
|`textanalytics.memoryRequest`| A **text-Analytics-** tárolóhoz szükséges memória| | `3Gi`|
|`textanalytics.memoryLimit`| A **text-Analytics-** tároló korlátozott memóriája| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| A "http:// `<service>` : `<port>` / `<sentimentURISuffix>` " formátum a teljes URI formátumú | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| A **text-Analytics** szolgáltatás típusa a Kubernetes-ben. Lásd: [Kubernetes-szolgáltatások típusai](https://kubernetes.io/docs/concepts/services-networking/service/) | érvényes Kubernetes-szolgáltatás típusa | `LoadBalancer` |
|`textanalytics.service.port`| A **text-Analytics** szolgáltatás portja| int| `50085`|
|`textanalytics.service.annotations`| A jegyzetek felhasználói hozzáadhatók a **text-Analytics** szolgáltatás metaadataihoz. Ilyenek például a következők:<br/> **Széljegyzetek**<br/>`   `**néhány/annotation1: érték1**<br/>`  `**néhány/annotation2: érték2** | jegyzetek, egy soronként| |
|`textanalytics.serivce.autoScaler.enabled`| Azt jelzi, hogy engedélyezve van-e a [horizontális Pod autoskálázás](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha engedélyezve van, `text-analytics-autoscaler` a rendszer a Kubernetes-fürtön fogja telepíteni. | Igaz/hamis| `true`|
|`textanalytics.service.podDisruption.enabled`| Azt jelzi, hogy engedélyezve van-e a [Pod megszakadási költségkeret](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha engedélyezve van, `text-analytics-poddisruptionbudget` a rendszer a Kubernetes-fürtön fogja telepíteni.| Igaz/hamis| `true`|
