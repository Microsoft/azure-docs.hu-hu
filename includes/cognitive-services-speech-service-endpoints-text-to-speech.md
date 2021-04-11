---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 832cc1d4f9ec3cec4ada6e964e3ab2f6f023dd41
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554869"
---
### <a name="neural-and-standard-voices"></a>Neurális és standard hangok

A táblázat segítségével meghatározhatja az **neurális és a szabványos hangok rendelkezésre állását** régiónként/végpont szerint:

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Az USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Nyugat-Japán | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Dél-Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA nyugati középső régiója | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> Az előzetes verzióban megjelenő [hangok](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) csak a következő 3 régióban érhetők el: USA keleti régiója, Nyugat-Európa és Délkelet-Ázsia.

### <a name="custom-voices"></a>Egyéni hangok

Ha egyéni hangbetűkészletet hozott létre, használja a létrehozott végpontot. Az alább felsorolt végpontokat is használhatja, és lecserélheti a `{deploymentId}` hangmodell üzembe helyezési azonosítóját.

| Régió | Végpont |
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

| Régió | Végpont |
|--------|----------|
| USA keleti régiója | `https://eastus.customvoice.api.speech.microsoft.com` |
| Közép-India | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Délkelet-Ázsia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Az Egyesült Királyság déli régiója | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Nyugat-Európa | `https://westeurope.customvoice.api.speech.microsoft.com` |
