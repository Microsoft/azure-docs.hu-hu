---
title: A kognitív szolgáltatások SKU-és díjszabása
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 8cc4bc6907f83ce062fed82dde17815fc4debd67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719955"
---
Tekintse meg az alábbi lista az SKU-ket és a díjszabási információkat. 

#### <a name="multi-service"></a>Több szolgáltatás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/) oldalt.            | `CognitiveServices`     |


#### <a name="vision"></a>Látás

| Szolgáltatás                    | Altípus                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision – előrejelzés | `CustomVision.Prediction` |
| Custom Vision – képzés   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |

#### <a name="search"></a>Keresés

| Szolgáltatás            | Altípus                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Szolgáltatás            | Altípus                 |
|--------------------|----------------------|
| Beszédfelismerési szolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

#### <a name="language"></a>Nyelv

| Szolgáltatás            | Altípus                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

#### <a name="decision"></a>Döntés

| Szolgáltatás           | Altípus               |
|-------------------|--------------------|
| Anomáliadetektor  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* előre meghatározott számú tranzakció díja. Ennek a számnak a meghaladása esetén a szolgáltatás [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) alapján külön díjat számítunk fel.

> [!NOTE]
> Számos Cognitive Services rendelkezik egy ingyenes szintű lehetőséggel, amellyel kipróbálhatja a szolgáltatást. Az ingyenes szintet az `F0` erőforráshoz tartozó SKU-ként használhatja.