---
title: Szuverén felhők – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A szuverén felhők használatának megismerése
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061752"
---
# <a name="speech-services-in-sovereign-clouds"></a>A Speech Services szuverén felhőkben

## <a name="azure-government-united-states"></a>Azure Government (Egyesült Államok)

Csak az USA kormányzati szervei és azok partnerei számára érhető el. További információt a Azure Government [itt](../../azure-government/documentation-government-welcome.md) és [itt talál.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Régiók**
  - USA-beli államigazgatás – Arizona
  - USA-beli államigazgatás – Virginia
- **Elérhető díjszabási szintek:**
  - Ingyenes (F0) és standard (S0). További részletek [itt](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) találhatók
- **Támogatott funkciók:**
  - Diktálás
    - Egyéni beszéd (akusztikus modell (AM) és Language Model (LM) adaptáció)
      - [Speech Studio](https://speech.azure.us/)
  - Szövegfelolvasás
    - Normál hang
    - Neurális hang
  - Speech Translator
- **Nem támogatott funkciók:**
  - Egyéni hang
- **Támogatott nyelvek:**
  - A támogatott nyelvek listáját [itt](language-support.md) tekintheti meg

### <a name="endpoint-information"></a>Végpont adatai

Ez a szakasz a Speech [SDK](speech-sdk.md), a [beszéd és a szöveg közötti REST API](rest-speech-to-text.md)és a [szöveg-beszéd REST API](rest-text-to-speech.md)használatával kapcsolatos beszédfelismerési szolgáltatások végponti információit tartalmazza.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

A Speech Services REST API-végpontok Azure Government formátuma a következő:

|  REST API típus/művelet | Végpont formátuma |
|--|--|
| Hozzáférési jogkivonat | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Beszéd – szöveg REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Beszéd – szöveg REST API rövid hanghoz](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Szövegfelolvasás REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Cserélje le az `<REGION_IDENTIFIER>` elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

|                     | Régió azonosítója |
|--|--|
| **USA-beli államigazgatás – Arizona**  | `usgovarizona` |
| **USA-beli államigazgatás – Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Beszéd SDK

A szuverén felhőkben található Speech SDK esetén a "from host" parancsot kell használnia a `SpeechConfig` `--host` [Speech CLI](spx-overview.md)osztályának vagy lehetőségének létrehozásához. (Használhatja a "végpont" példányát is, és `--endpoint` Speech CLI-beállítás).

`SpeechConfig` az osztályt a következőhöz hasonló módon kell létrehozni:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
A Speech CLI-t ehhez hasonló módon kell használni (a beállítás megjegyzése `--host` ):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Cserélje le a szöveget `subscriptionKey` a beszédfelismerési erőforrás-kulcsára. Cserélje le a `usGovHost` kifejezést a szükséges szolgáltatási ajánlatnak megfelelő kifejezésre, és az előfizetés régióját ebből a táblából:

|  Régió/szolgáltatás ajánlat | Gazdagép kifejezése |
|--|--|
| **USA-beli államigazgatás – Arizona** | |
| Diktálás | `wss://usgovarizona.stt.speech.azure.us` |
| Text-to-Speech | `https://usgovarizona.tts.speech.azure.us` |
| **USA-beli államigazgatás – Virginia** | |
| Diktálás | `wss://usgovvirginia.stt.speech.azure.us` |
| Text-to-Speech | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure China

Elérhető a Kínában üzleti jelenléttel rendelkező szervezetek számára. További információt az Azure China-ról [itt talál.](/azure/china/overview-operations) 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Régiók**
  - Kelet-Kína 2
- **Elérhető díjszabási szintek:**
  - Ingyenes (F0) és standard (S0). További részletek [itt](https://www.azure.cn/pricing/details/cognitive-services/index.html) találhatók
- **Támogatott funkciók:**
  - Diktálás
    - Egyéni beszéd (akusztikus modell (AM) és Language Model (LM) adaptáció)
      - [Speech Studio](https://speech.azure.cn/)
  - Szövegfelolvasás
    - Normál hang
    - Neurális hang
  - Speech Translator
- **Nem támogatott funkciók:**
  - Egyéni hang
- **Támogatott nyelvek:**
  - A támogatott nyelvek listáját [itt](language-support.md) tekintheti meg

### <a name="endpoint-information"></a>Végpont adatai

Ez a szakasz a Speech [SDK](speech-sdk.md), a [beszéd és a szöveg közötti REST API](rest-speech-to-text.md)és a [szöveg-beszéd REST API](rest-text-to-speech.md)használatával kapcsolatos beszédfelismerési szolgáltatások végponti információit tartalmazza.

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Az Azure China-ban a Speech Services REST API-végpontok formátuma a következő:

|  REST API típus/művelet | Végpont formátuma |
|--|--|
| Hozzáférési jogkivonat | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Beszéd – szöveg REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Beszéd – szöveg REST API rövid hanghoz](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Szövegfelolvasás REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Cserélje le az `<REGION_IDENTIFIER>` elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

|                     | Régió azonosítója |
|--|--|
| **Kelet-Kína 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Beszéd SDK

A szuverén felhőkben található Speech SDK esetén a "from host" parancsot kell használnia a `SpeechConfig` `--host` [Speech CLI](spx-overview.md)osztályának vagy lehetőségének létrehozásához. (Használhatja a "végpont" példányát is, és `--endpoint` Speech CLI-beállítás).

`SpeechConfig` az osztályt a következőhöz hasonló módon kell létrehozni:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
A Speech CLI-t ehhez hasonló módon kell használni (a beállítás megjegyzése `--host` ):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Cserélje le a szöveget `subscriptionKey` a beszédfelismerési erőforrás-kulcsára. Cserélje le a `azCnHost` kifejezést a szükséges szolgáltatási ajánlatnak megfelelő kifejezésre, és az előfizetés régióját ebből a táblából:

|  Régió/szolgáltatás ajánlat | Gazdagép kifejezése |
|--|--|
| **Kelet-Kína 2** | |
| Diktálás | `wss://chinaeast2.stt.speech.azure.cn` |
| Text-to-Speech | `https://chinaeast2.tts.speech.azure.cn` |