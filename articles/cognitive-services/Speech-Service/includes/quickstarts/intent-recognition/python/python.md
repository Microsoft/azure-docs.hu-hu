---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f8caa38fa77134ef156360af2fd3d7dc5d84bb3d
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445244"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Telepítse a fejlesztői környezethez készült SPEECH SDK-t, és hozzon létre egy üres minta projektet</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándék-felismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>A projekt megnyitása

1. Nyissa meg az előnyben részesített IDE-t.
2. Hozzon létre egy új projektet, és hozzon létre egy nevű fájlt `quickstart.py` , majd nyissa meg.

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az objektumok inicializálásához `IntentRecognizer` létre kell hoznia egy olyan konfigurációt, amely a Luis-előrejelzési erőforrás kulcsát és helyét használja.

Szúrja be ezt a kódot a alkalmazásba `quickstart.py` . Győződjön meg róla, hogy frissíti ezeket az értékeket:

* Cserélje le a- `"YourLanguageUnderstandingSubscriptionKey"` t a Luis-előrejelzési kulcsra.
* Cserélje le a helyére `"YourLanguageUnderstandingServiceRegion"` a Luis helyét. Régió **azonosítójának** használata [régióból](../../../../regions.md)

>[!TIP]
> Ha segítségre van szüksége ezeknek az értékeknek a megtalálásához, tekintse meg [a Luis-alkalmazás létrehozása a szándék felismeréséhez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Ez a példa `SpeechConfig` Luis-kulcs és-régió használatával hozza létre az objektumot. Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer` . Szúrja be a kódot közvetlenül a beszédfelismerési konfiguráció alá.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Hozzá kell rendelnie egy `LanguageUnderstandingModel` -t a szándék-felismerőhöz, és fel kell vennie a felismerni kívánt leképezéseket. Az előre elkészített tartományból szándékait fogjuk használni a Home Automation számára.

Szúrja be a kódot a alá `IntentRecognizer` . Győződjön meg arról, hogy a helyére `"YourLanguageUnderstandingAppId"` a Luis-alkalmazás azonosítóját írja. 

>[!TIP]
> Ha segítségre van szüksége az érték megkereséséhez, tekintse meg [a Luis-alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

Ez a példa a `add_intents()` függvényt használja a explicit módon definiált leképezések listájának hozzáadására. Ha egy modellből kívánja felvenni az összes leképezést, használja `add_all_intents(model)` és adja át a modellt.

> [!NOTE]
> A Speech SDK csak a LUIS v 2.0-végpontokat támogatja.
> A példa lekérdezési mezőben található v 3.0 végpont URL-címet manuálisan kell módosítani, hogy a v 2.0 URL-mintát használja.
> A LUIS v 2.0-végpontok mindig az alábbi két minta egyikét követik:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg fogja hívni a `recognize_once()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

Szúrja be ezt a kódot a modell alá.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

A hívása alatt `recognize_once()` adja hozzá ezt a kódot.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie.

> [!NOTE]
> Ehhez a verzióhoz hozzáadott néhány megjegyzést.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Futtassa a mintát a konzolról vagy az IDE-ben:

```
python quickstart.py
```

A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]