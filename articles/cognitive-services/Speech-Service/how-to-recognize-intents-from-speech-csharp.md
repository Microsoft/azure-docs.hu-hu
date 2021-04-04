---
title: 'A beszédfelismerés céljainak felismerése a Speech SDK C használatával #'
titleSuffix: Azure Cognitive Services
description: Ebből az útmutatóból megtudhatja, hogyan ismerheti fel a beszédfelismerés célját a C# Speech SDK használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 93a3adf00203e317be912e3e72de7a3f7ca666c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001096"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>A beszédfelismerés céljainak felismerése a C Speech SDK használatával #

A Cognitive Services [SPEECH SDK](speech-sdk.md) a [Language Understanding szolgáltatással (Luis)](https://www.luis.ai/home) integrálódik a **szándék-felismerés** biztosításához. A szándék az, amit a felhasználó tenni szeretne: például repülőutat foglalni, megnézni az időjárást vagy telefonhívást indítani. A felhasználó bármilyen kifejezést használhat, amely számára természetes. A Machine learning használatával a LUIS leképezi a felhasználói kéréseket a definiált szándékokra.

> [!NOTE]
> A LUIS alkalmazás meghatározza a felismerni kívánt szándékokat és entitásokat. Az alkalmazás elkülönül a Speech Service szolgáltatást használó C#-alkalmazástól. Ebben a cikkben az „app” program kifejezés a LUIS-alkalmazásra, az „alkalmazás” kifejezés pedig a C#-kódra utal.

Ebben az útmutatóban a Speech SDK használatával fejlesztünk egy C# konzolos alkalmazást, amely az eszköz mikrofonján keresztül a felhasználói hosszúságú kimondott szöveg származó leképezéseket származtat. A következőket fogja megtanulni:

> [!div class="checklist"]
>
> - A Speech SDK NuGet-csomagra vonatkozó Visual Studio-projekt létrehozása
> - Beszédfelismerési konfiguráció létrehozása és a szándék felismerésének beolvasása
> - A LUIS-app modelljének lekérése és a kívánt szándékok hozzáadása
> - A beszédfelismerés nyelvének megadása
> - Beszédfelismerés végrehajtása fájlból
> - Aszinkron, eseményvezérelt folyamatos felismerés használata

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Egy LUIS-fiók. Létrehozhat egy ingyenes fiókot a [LUIS portal](https://www.luis.ai/home) segítségével.
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (bármely kiadás).

## <a name="luis-and-speech"></a>A LUIS és a beszéd

A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.

A LUIS háromféle kulcsot használ:

| Kulcs típusa  | Cél                                               |
| --------- | ----------------------------------------------------- |
| Tartalomkészítés | Lehetővé teszi a LUIS-alkalmazások programozott módon történő létrehozását és módosítását |
| Kezdő   | Lehetővé teszi a LUIS-alkalmazás tesztelését csak szöveg használatával   |
| Végpont  | Engedélyezi a hozzáférést egy adott LUIS-alkalmazáshoz            |

Ebben az útmutatóban a végponti kulcs típusát kell megadnia. Ez az útmutató a példaként szolgáló Home Automation LUIS alkalmazást használja, amelyet az [előre elkészített Home Automation-alkalmazás használatának](../luis/luis-get-started-create-app.md) első lépésein hozhat létre. Ha saját LUIS-alkalmazást hozott létre, azt használhatja helyette.

LUIS-alkalmazás létrehozásakor a LUIS automatikusan létrehoz egy alapszintű kulcsot, amellyel szöveges lekérdezések használatával tesztelheti az alkalmazást. Ez a kulcs nem engedélyezi a beszédfelismerési szolgáltatás integrációját, és nem fog működni ezzel az útmutatóval. Hozzon létre egy LUIS-erőforrást az Azure irányítópulton, és rendelje hozzá a LUIS alkalmazáshoz. Ehhez az útmutatóhoz használhatja az ingyenes előfizetési szintet.

Miután létrehozta a LUIS-erőforrást az Azure-irányítópulton, jelentkezzen be a [Luis portálra](https://www.luis.ai/home), válassza ki az alkalmazást a **saját alkalmazások** lapon, majd váltson az alkalmazás **kezelése** lapra. Végül válassza a **kulcsok és végpontok** lehetőséget az oldalsávon.

![A LUIS portál kulcs- és a végpontbeállításai](media/sdk/luis-keys-endpoints-page.png)

A **kulcsok és végpont beállításai** lapon:

1. Görgessen le az **erőforrások és kulcsok** szakaszhoz, majd válassza az **erőforrás kiosztása** elemet.
1. A **kulcs kiosztása az alkalmazáshoz** párbeszédpanelen végezze el a következő módosításokat:

   - A **bérlő** területen válassza a **Microsoft** lehetőséget.
   - Az **előfizetés neve** területen válassza ki azt az Azure-előfizetést, amely a használni kívánt Luis-erőforrást tartalmazza.
   - A **kulcs** alatt válassza ki az alkalmazással használni kívánt Luis-erőforrást.

   Az új előfizetés hamarosan megjelenik a lap alján található táblázatban.

1. Kattintson a kulcs melletti ikonra a vágólapra másoláshoz. (Bármelyik kulcsot használhatja.)

![A LUIS-app előfizetési kulcsai](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Beszédprojekt létrehozása Visual Studióban

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>A kód hozzáadása

Ezután adja hozzá a projekthez egy kódot.

1. A **megoldáskezelő** nyissa meg a **program. cs** fájlt.

1. Cserélje le az utasítások blokkját a `using` fájl elejére a következő deklarációkkal:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Cserélje le a megadott `Main()` metódust a következő aszinkron egyenértékű értékkel:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Hozzon létre egy üres aszinkron metódust az `RecognizeIntentAsync()` itt látható módon:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Az új metódus törzsében adja hozzá a következő kódot:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. A metódusban szereplő helyőrzőket cserélje le a LUIS előfizetési kulcsára, a régióra és az app azonosítójára az alább látható módon.

   | Helyőrző | Csere erre |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | A LUIS végpontkulcsa. Ezt az elemet újra be kell szereznie az Azure-irányítópultról, nem pedig "kezdő kulcs". A [Luis portálon](https://www.luis.ai/home)megtalálhatja az alkalmazás **kulcsai és végpontok** lapján (a **kezelés** alatt). |
   | `YourLanguageUnderstandingServiceRegion` | Annak a régiónak a rövid azonosítója, amelyben a LUIS-előfizetése van, például az USA nyugati régiója esetén `westus`. Lásd: [Régiók](regions.md). |
   | `YourLanguageUnderstandingAppId` | A LUIS-app azonosítója. A [Luis portálon](https://www.luis.ai/home)megtalálhatja az alkalmazás **Beállítások** lapján. |

Ezekkel a módosításokkal létrehozhatja az alkalmazást (**vezérlő + SHIFT + B**) és futtathatja (**F5**) az alkalmazást. Ha a rendszer kéri, próbálja meg "a fények kikapcsolása" kifejezést a számítógép mikrofonjában. Az alkalmazás megjeleníti az eredményt a konzol ablakban.

A kód ismertetése a következő szakaszokban szerepel.

## <a name="create-an-intent-recognizer"></a>Szándékfelismerő létrehozása

Először létre kell hoznia egy beszédfelismerési konfigurációt a LUIS Endpoint kulcsból és régióból. A beszédfelismerési konfigurációk segítségével felismerőket hozhat létre a Speech SDK különböző képességeihez. A beszédfelismerési konfiguráció több módon is megadhatja a használni kívánt előfizetést. Itt használjuk `FromSubscription` , amely az előfizetési kulcsot és régiót veszi igénybe.

> [!NOTE]
> Használja a LUIS-előfizetés kulcsát és régióját, nem pedig a Speech Service-előfizetést.

A következő lépés egy szándékfelismerő létrehozása a `new IntentRecognizer(config)` metódus használatával. Mivel a konfiguráció már tudja, hogy melyik előfizetést szeretné használni, nem kell újra megadnia az előfizetési kulcsot és a végpontot a felismerő létrehozásakor.

## <a name="import-a-luis-model-and-add-intents"></a>LUIS-modell importálása és szándékok hozzáadása

Most importálja a modellt a LUIS-appból a `LanguageUnderstandingModel.FromAppId()` használatával, majd adja hozzá azokat a LUIS-szándékokat, amelyeket a felismerő `AddIntent()` metódusával fel szeretne ismerni. Ezzel a két lépéssel növelheti a beszédfelismerés pontosságát, ha megadja azokat a szavakat, amelyeket a felhasználó a kérésekben nagy valószínűséggel használni fog. Nem kell hozzáadnia az összes alkalmazást, ha az alkalmazásban nem kell felismernie őket.

A leképezések hozzáadásához három argumentumot kell megadnia: a LUIS modellt (amelyet létrehoztak és elneveztek `model` ), a szándék nevét és a szándék azonosítóját. Az azonosító és a név közötti különbség a következő.

| `AddIntent()`&nbsp;argumentum | Cél |
| --------------------------- | ------- |
| `intentName` | A szándék LUIS-appban meghatározott neve. Ennek az értéknek pontosan egyeznie kell a LUIS-cél nevével. |
| `intentID` | A Speech SDK által felismert szándékhoz rendelt azonosító. Ez az érték lehet bármilyen hasonló; nem kell megegyeznie a cél nevével a LUIS alkalmazásban meghatározottak szerint. Ha például ugyanaz a kód több szándékot is kezel, használhatja hozzájuk ugyanazt az azonosítót. |

A Home Automation LUIS alkalmazásnak két célja van: egyet az eszköz bekapcsolásához, egy másikat pedig egy eszköz kikapcsolásához. A felismerő az alábbi sorokkal adható hozzá a felismerőhöz. Cserélje le a `RecognizeIntentAsync()` metódus három `AddIntent` sorát erre a kódra.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

Az egyéni leképezések hozzáadása helyett a metódus használatával is `AddAllIntents` hozzáadhatja a modellben található összes leképezést a felismerőhöz.

## <a name="start-recognition"></a>Felismerés indítása

A felismerő létrehozása és a szándékok hozzáadása után elkezdődhet a felismerés. A Speech SDK az egyszeri és folyamatos felismerést is támogatja.

| Felismerési mód | Meghívandó metódusok | Eredmény |
| ---------------- | --------------- | ------ |
| Egyszeri | `RecognizeOnceAsync()` | Egyszer kimondott szöveg alapján visszaadja a felismert szándékot (ha van). |
| Folyamatos | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Több hosszúságú kimondott szöveg felismerése; eseményeket bocsát ki (például `IntermediateResultReceived` ), ha az eredmények elérhetők. |

Az alkalmazás egylemezes módot használ, ezért a rendszer `RecognizeOnceAsync()` megkezdi az elismerés megkezdését. Az eredmény egy `IntentRecognitionResult` objektum, amely a felismert szándékra vonatkozó információkat tartalmaz. A LUIS JSON-választ kinyerheti a következő kifejezés használatával:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Az alkalmazás nem elemzi a JSON-eredményt. Csak a JSON-szöveget jeleníti meg a konzol ablakban.

![Egyetlen LUIS-felismerés eredményei](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Adja meg a felismerés nyelvét

A LUIS alapértelmezés szerint amerikai angol (`en-us`) nyelven végzi a szándékfelismerést. A területibeállítás-kódnak a beszédkonfiguráció `SpeechRecognitionLanguage` tulajdonságához való hozzárendelésével más nyelveken is végezhet szándékfelismerést. Például vegyen fel az `config.SpeechRecognitionLanguage = "de-de";` alkalmazásba, mielőtt létrehozza a felismerést, hogy felismerje a leképezéseket német nyelven. További információért lásd a [Luis nyelvi támogatását](../LUIS/luis-language-support.md#languages-supported)ismertető témakört.

## <a name="continuous-recognition-from-a-file"></a>Folyamatos felismerés fájlból

Az alábbi kód szemlélteti a Speech SDK-val való szándékfelismerés két további képességét. Az első a korábban említett folyamatos felismerés, amelynek esetén a felismerő eseményeket bocsát ki, ha eredmények érhetők el. Ezeket az események aztán feldolgozhatók az Ön által megadott eseménykezelőkkel. A folyamatos felismeréssel a felismerő metódusának meghívásával `StartContinuousRecognitionAsync()` elindíthatja az elismerést a helyett `RecognizeOnceAsync()` .

A másik képesség a feldolgozandó beszédet tartalmazó hangfelvétel leolvasása egy WAV-fájlból. A megvalósítás magában foglalja egy hangkonfiguráció létrehozását, amelyet a rendszer a szándék-felismerő létrehozásakor használhat. A fájlnak egycsatornásnak (mono) kell lennie, 16 kHz-es mintavételi aránnyal.

A funkciók kipróbálásához törölje vagy véleményezze a metódus törzsét `RecognizeIntentAsync()` , és adja hozzá a következő kódot a helyére.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Módosítsa a kódot úgy, hogy benne legyen a LUIS végpontkulcsa, a régió és az app azonosítója, illetve hogy az előzőekhez hasonlóan hozzáadhassa az otthonautomatizálási szándékokat. Váltson `whatstheweatherlike.wav` a rögzített hangfájl nevére. Ezután hozza létre, másolja a hangfájlt a Build könyvtárba, és futtassa az alkalmazást.

Ha például a "fények kikapcsolása", a pause, majd a "fények bekapcsolása" lehetőséget választja a rögzített hangfájlban, a konzol kimenete az alábbihoz hasonló lehet:

![A LUIS hangfájl-felismerési eredményei](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Keresse meg a kódot ebből a cikkből a **Samples/csharp/sharedcontent/Console** mappában.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: beszéd felismerése mikrofonból](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)