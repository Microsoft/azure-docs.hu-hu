---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514909"
---
### <a name="standard-and-neural-voices"></a>Standard és neurális hangok

A táblázat segítségével meghatározhatja a standard és a neurális hangok rendelkezésre állását régiónként/végpont szerint:

| Region | Végpont | Neurális hangok | Standard hangok |
|--------|----------|-----------------|---------------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Az USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Nyugat-Japán | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Dél-Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Nem | Igen |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Yes |

> [!TIP]
> Az előzetes verzióban megjelenő [hangok](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) csak a következő 3 régióban érhetők el: USA keleti régiója, Nyugat-Európa és Délkelet-Ázsia.

### <a name="custom-voices"></a>Egyéni hangok

Ha egyéni hangbetűkészletet hozott létre, használja a létrehozott végpontot. Az alább felsorolt végpontokat is használhatja, és lecserélheti a `{deploymentId}` hangmodell üzembe helyezési azonosítóját.

| Region | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Dél-Brazília | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Az USA középső régiója | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Ázsia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA keleti régiója | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. keleti régiója | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Franciaország | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-India | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Japán | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nyugat-Japán | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Dél-Korea középső régiója | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA északi középső régiója | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Észak-Európa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA déli középső régiója | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Délkelet-Ázsia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Az Egyesült Királyság déli régiója | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nyugat-Európa | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA nyugati régiója | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. nyugati régiója | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Egyéni neurális hang

Az alábbi táblázat az egyéni neurális hangfunkciók regionális támogatását ismerteti.

| Szolgáltatás | Támogatott régiók |
|---|---|
| Hangmodell-üzemeltetés | USA keleti régiója, USA 2. nyugati régiója, USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, Kelet-Ausztrália |
| Valós idejű karakterek | USA keleti régiója, USA 2. nyugati régiója, USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, Kelet-Ausztrália |
| Hosszú hangjelek | USA keleti régiója, Nyugat-Európa, Egyesült Királyság déli régiója, Délkelet-Ázsia, Közép-India |
| Egyéni neurális képzés | USA keleti régiója, Egyesült Királyság déli régiója |

### <a name="long-audio-api"></a>Hosszú hang API

A hosszú hang API több régióban is elérhető egyedi végpontokkal.

| Region | Végpont |
|--------|----------|
| USA keleti régiója | `https://eastus.customvoice.api.speech.microsoft.com` |
| Közép-India | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Délkelet-Ázsia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Az Egyesült Királyság déli régiója | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Nyugat-Európa | `https://westeurope.customvoice.api.speech.microsoft.com` |
