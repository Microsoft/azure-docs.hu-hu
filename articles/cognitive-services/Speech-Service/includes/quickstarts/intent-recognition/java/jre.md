---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8bcc3e41f87570822f48a111fe98d1fdaf72679f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470753"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Telepítse a fejlesztői környezethez készült SPEECH SDK-t, és hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> létre egy üres minta projektet</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándék-felismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>A projekt megnyitása

1. Nyissa meg az előnyben részesített IDE-t.
2. Töltse be a projektet, és nyissa meg `Main.java` .

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az objektumok inicializálásához `IntentRecognizer` létre kell hoznia egy olyan konfigurációt, amely a Luis-előrejelzési erőforrás kulcsát és helyét használja.  

Szúrja be ezt a kódot a try/catch blokkba `main()` . Győződjön meg róla, hogy frissíti ezeket az értékeket:

* Cserélje le a- `"YourLanguageUnderstandingSubscriptionKey"` t a Luis-előrejelzési kulcsra.
* Cserélje le a helyére `"YourLanguageUnderstandingServiceRegion"` a Luis helyét. Régió **azonosítójának** használata [régióból](https://aka.ms/speech/sdkregion)

>[!TIP]
> Ha segítségre van szüksége ezeknek az értékeknek a megtalálásához, tekintse meg [a Luis-alkalmazás létrehozása a szándék felismeréséhez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Ez a példa a metódust használja a létrehozásához `FromSubscription()` `SpeechConfig` . Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet&preserve-view=true).

A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer` . Szúrja be a kódot közvetlenül a beszédfelismerési konfiguráció alá.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Hozzá kell rendelnie egy- `LanguageUnderstandingModel` t a szándék-felismerőhöz, és fel kell vennie a felismerni kívánt leképezéseket. Az előre elkészített tartományból szándékait fogjuk használni a Home Automation számára.

Szúrja be a kódot a alá `IntentRecognizer` . Győződjön meg arról, hogy a helyére `"YourLanguageUnderstandingAppId"` a Luis-alkalmazás azonosítóját írja.

>[!TIP]
> Ha segítségre van szüksége az érték megkereséséhez, tekintse meg [a Luis-alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

Ez a példa a `addIntent()` függvényt használja egyéni leképezések hozzáadására. Ha egy modellből kívánja felvenni az összes leképezést, használja `addAllIntents(model)` és adja át a modellt.

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg fogja hívni a `recognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

Szúrja be a kódot a modell alá:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>Az elismerési eredmények (vagy hibák) megjelenítése

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményt a konzolon.

Szúrja be ezt a kódot a hívása alatt `recognizeOnceAsync()` .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Kiadási erőforrások

Fontos, hogy a használat során kiszabadítsa a beszédfelismerési erőforrásokat. Szúrja be ezt a kódot a try/catch blokk végén:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:

> [!NOTE]
> Ehhez a verzióhoz hozzáadott néhány megjegyzést.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Nyomja le az <kbd>F11</kbd>billentyűt, vagy válassza a **Run**  >  **hibakeresés**futtatása lehetőséget.
A mikrofonból érkező következő 15 másodpercnyi beszédet a rendszer felismeri, és a konzolablakban naplózza.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
