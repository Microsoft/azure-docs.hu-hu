---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2dbf9e269474b3e71a665957f1765f726718e6cc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444421"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Telepítse a fejlesztői környezethez készült SPEECH SDK-t, és hozzon létre egy üres minta projektet</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>LUIS-alkalmazás létrehozása a szándék-felismeréshez

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>A projekt megnyitása a Visual Studióban

Ezután nyissa meg a projektet a Visual Studióban.

1. Indítsa el a Visual Studio 2019-es kiadását.
2. Töltse be a projektet, és nyissa meg `Program.cs` .

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez. Jegyezze fel, hogy létrehozta a nevű aszinkron metódust `RecognizeIntentAsync()` .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

Az objektumok inicializálásához `IntentRecognizer` létre kell hoznia egy olyan konfigurációt, amely a Luis-előrejelzési erőforrás kulcsát és helyét használja.

> [!IMPORTANT]
> Az alapszintű kulcs és a szerzői kulcsok nem fognak működni. A korábban létrehozott előrejelzési kulcsot és helyet kell használnia. További információ: LUIS- [alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition).

Szúrja be ezt a kódot a `RecognizeIntentAsync()` metódusba. Győződjön meg róla, hogy frissíti ezeket az értékeket:

* Cserélje le a- `"YourLanguageUnderstandingSubscriptionKey"` t a Luis-előrejelzési kulcsra.
* Cserélje le a helyére `"YourLanguageUnderstandingServiceRegion"` a Luis helyét. Régió **azonosítójának** használata [.](../../../../regions.md)

>[!TIP]
> Ha segítségre van szüksége ezeknek az értékeknek a megtalálásához, tekintse meg [a Luis-alkalmazás létrehozása a szándék felismeréséhez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Ez a példa a metódust használja a létrehozásához `FromSubscription()` `SpeechConfig` . Az elérhető módszerek teljes listáját lásd: [SpeechConfig osztály](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig).

A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer inicializálása

Most hozzon létre egy `IntentRecognizer` . Ez az objektum egy using utasításon belül jön létre a nem felügyelt erőforrások megfelelő kiadásának biztosítása érdekében. Szúrja be ezt a kódot a `RecognizeIntentAsync()` metódusba közvetlenül a beszédfelismerési konfiguráció alatt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel és szándékok hozzáadása

Hozzá kell rendelnie egy- `LanguageUnderstandingModel` t a szándék-felismerőhöz, és fel kell vennie a felismerni kívánt leképezéseket. Az előre elkészített tartományból szándékait fogjuk használni a Home Automation számára. Szúrja be ezt a kódot az előző szakasz using utasításában. Győződjön meg arról, hogy a helyére `"YourLanguageUnderstandingAppId"` a Luis-alkalmazás azonosítóját írja.

>[!TIP]
> Ha segítségre van szüksége az érték megkereséséhez, tekintse meg [a Luis-alkalmazás létrehozása a szándék-felismeréshez](#create-a-luis-app-for-intent-recognition)című témakört.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

Ez a példa a `AddIntent()` függvényt használja egyéni leképezések hozzáadására. Ha egy modellből kívánja felvenni az összes leképezést, használja `AddAllIntents(model)` és adja át a modellt. 

> [!NOTE]
> A Speech SDK csak a LUIS v 2.0-végpontokat támogatja.
> A példa lekérdezési mezőben található v 3.0 végpont URL-címet manuálisan kell módosítani, hogy a v 2.0 URL-mintát használja.
> A LUIS v 2.0-végpontok mindig az alábbi két minta egyikét követik:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Szándék felismerése

Az `IntentRecognizer` objektumból meg fogja hívni a `RecognizeOnceAsync()` metódust. Ez a módszer lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld az észleléshez, és ha a kifejezést azonosította a beszédfelismerés felismerésének leállításához.

A using utasításon belül adja hozzá ezt a kódot a modellhez.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Megjelenítés felismerési eredményei (vagy hibák)

Ha a beszédfelismerési szolgáltatás visszaadja a felismerés eredményét, érdemes megtennie a dolgot. Megtartjuk az egyszerűséget, és kinyomtathatjuk az eredményeket a konzolon.

Az alábbi using utasításban `RecognizeOnceAsync()` adja hozzá a következő kódot:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>A kód megkeresése

Ezen a ponton a kódnak így kell kinéznie:

> [!NOTE]
> Ehhez a verzióhoz hozzáadott néhány megjegyzést.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

1. **Fordítsa le a kódot** – a Visual Studio menüsávján válassza a **Build**  >  **megoldás** létrehozása lehetőséget.
2. **Indítsa** el az alkalmazást – a menüsávban **válassza a hibakeresés**  >  **indítása** vagy az <kbd>F5</kbd>billentyűt.
3. **Felismerés elindítása** – felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
