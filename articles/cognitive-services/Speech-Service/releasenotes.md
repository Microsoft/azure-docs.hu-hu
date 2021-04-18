---
title: Kibocsátási megjegyzések – Speech Service
titleSuffix: Azure Cognitive Services
description: A Speech Service-funkciók kiadásának, fejlesztésének, hibajavításának és ismert problémáinak futó naplója.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: e02fbe0f77a53b7d3f8f3ce420bf46189dc85a65
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600981"
---
# <a name="speech-service-release-notes"></a>A Speech Service kibocsátási megjegyzései

## <a name="text-to-speech-2021-april-release"></a>Szöveg beszédfelismerés – 2021. áprilisi kiadás

**A neurális TTS 21 régióban érhető el**

- **12** új régió hozzáadása – A neurális TTS már elérhető ezekben az új 12 régióban: `Japan East` , , , , , , , , `Japan West` , , `Korea Central` , `North Central US` `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . A [21](regions.md#text-to-speech) támogatott régió teljes listáját itt ellenőrizheti.

## <a name="text-to-speech-2021-march-release"></a>Szöveg beszédfelismerés – 2021. márciusi kiadás

**Új nyelvek és hangok hozzáadva a neurális TTS-hez**

- Hat új nyelv van bevezetve **–** 6 új nyelvben 12 új hang szerepel a neurális TTS nyelvi listában: Nia in `cy-GB` Welsh (Egyesült Királyság), Aled in `cy-GB` Welsh (Egyesült Királyság), Language in `en-PH` English (Magyarul) Charline in French `en-PH` `fr-BE` (Abban), Gerard in French `fr-BE` (Abban), Dena `nl-BE` in Holland (Abban), Aangud in `nl-BE` Holland (Rend), Polina `uk-UA` in Egyed (Angol), Ostap in `uk-UA` India (Rend), Uzma in `ur-PK` Urdu (Uzma in Urdu), Asad in `ur-PK` Urdu (Rend).

-  Öt nyelv az előzetes verziótól az GA-ig – 2020- novemberben bevezetett 5 területi nyelvben 10 hang érhető el: Ga ga: Kert in `et-EE` Fogn (Észt), Colm `ga-IE` inCinn (Írország), Nils in `lv-LV` Lettn (Felcsatul), Majdas in `lt-LT` Fogn (Kelleti), Joseph `mt-MT` maltese (].

- **Új, francia (Kanadai)** férfi hanggal bővült – Egy új hang érhető el a francia `fr-CA` (Kanada) nyelvhez.

- **Minőség javítása** – A kiejtési hibák arányának csökkentése magyar nyelven `hu-HU` – 48,17%, `nb-NO` norvég – 52,76%, holland `nl-NL` (Holland) – 22,11%.

Ebben a kiadásban összesen 142 neurális hangot támogatunk 60 nyelven/területi szinten. Emellett több mint 70 standard hang érhető el 49 nyelven/területi szinten. A [teljes listát a Nyelvi támogatásban](language-support.md#text-to-speech) keresse fel.

**Arcvonási események lekértése karakterek animizálása**

A [Viseme-esemény](how-to-speech-synthesis-viseme.md) bekerül a neurális TTS-be, amely lehetővé teszi a felhasználók számára, hogy a szintetizált beszédből kapják meg az arc testek sorozatát és időtartamát. A Viseme felhasználható a 2D- és 3D avatar-modellek mozgásának szabályozására, tökéletesen megfeleltetve a szájmozgást a szintetizált beszédnek. A viseme mostantól csak az en-US-AriaNeural hanghoz működik.

**Adja hozzá a könyvjelző elemet a Speech Synthesis Markup Language (SSML) nyelvhez**

A [könyvjelző elem lehetővé](speech-synthesis-markup.md#bookmark-element) teszi, hogy egyéni jelölőket szúrjon be az SSML-be a hangstream egyes jelölőinek eltolására. Ezzel hivatkozhat egy adott helyre a szöveg- vagy címkesorozatban.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: 2021. márciusi kiadás

> [!NOTE]
> A Windowson elérhető Speech SDK a 2015-ös, 2017-es és 2019-es verziókhoz Visual Studio Microsoft Visual C++ terjeszthető verziójától függ. Töltse le [ide.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

#### <a name="new-features"></a>Új funkciók

- **C++/C#/Java/Python:** Átkerült a GStreamer legújabb verziójára (1.18.3) a médiaformátumok Windows, Linux és Android rendszeren történő átírásának támogatásához. A dokumentációt itt [találhatja.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** A tömörített TTS/szintetizált hang sdk-ra való dekódolása már támogatott. Ha a kimeneti hangformátumot PCM-re beállította, és a GStreamer elérhető a rendszeren, az SDK automatikusan tömörített hangot kér a szolgáltatástól a sávszélesség megtakarítása és az ügyfélen található hang dekódolása érdekében. Beállíthatja a `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` `false` tulajdonságot a funkció letiltására. A [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [A C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet), a [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable)az [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid)és a [Python részletei.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python)
- **JavaScript:** Node.js felhasználók mostantól használhatjak az [ `AudioConfig.fromWavFileInput` API-t.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_) Ez a [GitHub-probléma megoldásához #252.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252)
- **C++/C#/Java/Objective-C/Python:** Új metódus hozzáadva a TTS-hez, amely `GetVoicesAsync()` visszaadja az összes rendelkezésre álló szintetézishangot. A [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [A C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [a Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods)az [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)és a [Python részletei.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods)
- **C++/C#/Java/JavaScript/Objective-C/Python:** TTS-/beszédszintézis-esemény hozzáadva szinkron `VisemeReceived` viseme animáció visszaadása érdekében. A dokumentációt itt [találhatja.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Új `BookmarkReached` esemény a TTS-hez. A bemeneti SSML-ben könyvjelzőket állíthat be, és lekértheti az egyes könyvjelzők hangeltolását. A dokumentációt itt [találhatja.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** A beszélőfelismerési API-k támogatása hozzáadva. Részletek [itt:](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable).
- **C++/C#/Java/JavaScript/Objective-C/Python:** Két új kimeneti hangformátum hozzáadva a TTS WebM-tárolóval (Webm16Khz16BitMonoOpus és Webm24Khz16BitMonoOpus). Ezek jobb formátumok a hangstreameléshez az Opus Kodekkel. A [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [A C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), a [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable)a [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)az [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat)és a [Python részletei.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)
- **C++/C#/Java:** Hangprofilok lehívásának támogatása a beszélőfelismerési forgatókönyvhöz. A [C++,](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer) [a C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)és a [Java részletei.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/Objective-C/Python:** Külön megosztott kódtár támogatása a hangmikonhoz és a beszélővezérléshez. Ez lehetővé teszi az SDK használatát olyan környezetekben, amelyek nem tartalmaznak szükséges hangkönyvtár-függőségeket.
- **Objective-C/Swift:** Támogatás hozzáadva a modul-keretrendszerhez az ének fejléccel. Ez lehetővé teszi a Speech SDK importálását modulként iOS/Mac Objective-C/Swift-alkalmazásokban. Ez a [GitHub-probléma #452.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)
- **Python:** A [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) támogatása és a Python 3.5 támogatása el lett vetve a Python életciklusának [3.5-ös időszakában.](https://devguide.python.org/devcycle/#end-of-life-branches)

#### <a name="improvements"></a>Fejlesztések

- A Speech SDK memóriahasználatának és lemezterületének csökkentésére irányuló több kiadási erőfeszítés részeként az Android bináris fájlok mostantól 3–5%-kal kisebbek.
- Továbbfejlesztett pontosság, olvashatóság és lásd a C# referenciadokumentáció itt található [szakaszát.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)

#### <a name="bug-fixes"></a>Hibajavítások

- **JavaScript:** A nagyméretű WAV fájlfejlécek helyesen vannak elemezve (a fejlécszeletet 512 bájtra növeli). Ez a [GitHub-probléma megoldásához #962.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)
- **JavaScript:** Javítva lett a mikrofon időzítési problémája, ha a mikrofonstream a felismerés befejeződés előtt véget ér, és a Firefoxban nem működik a Beszédfelismerés.
- **JavaScript:** Most már megfelelően kezeli az inicializálási ígéretet, amikor a böngésző kényszeríti a mikrofont, mielőtt a bekapcsolás befejeződik.
- **JavaScript:** Az URL-függőséget lecserélte az url-parse névre. Ez a [GitHub-probléma megoldásához #264.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)
- **Android:** Kijavítva a visszahívások nem működnek, ha `minifyEnabled` true (igaz) érték van beállítva.
- **C++/C#/Java/Objective-C/Python:** a késés csökkentése érdekében helyesen lesz beállítva a TTS mögöttes szoftvercsatornás `TCP_NODELAY` IO-jára.
- **C++/C#/Java/Python/Objective-C/Go:** Kijavítottunk egy alkalmankénti összeomlást, amikor a felismerőt a felismerés megkezdése után megsemmisítették.
- **C++/C#/Java:** Ki van javítva a speaker recognizer esetleges összeomlása.

#### <a name="samples"></a>Példák

- **JavaScript:** [A böngészőmintákhoz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) már nincs szükség külön JavaScript-kódtárfájl letöltésére.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Speech CLI (más néven SPX): 2021. márciusi kiadás

> [!NOTE]
> Az Azure Speech szolgáltatás parancssori felületének (CLI) első lépésekről itt [lesz szó.](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics) A CLI lehetővé teszi, hogy kód írása nélkül használja az Azure Speech szolgáltatást.

#### <a name="new-features"></a>Új funkciók

- Parancs `spx intent` hozzáadva a szándékfelismeréshez, a `spx recognize intent` helyett.
- A recognize és a intent mostantól képes az Azure Functions használatával kiszámítani a szóhibaarányt a `spx recognize --wer url <URL>` használatával.
- A Recognize mostantól VTT-fájlként tudja kihozni az eredményeket a `spx recognize --output vtt file <FILENAME>` használatával.
- A bizalmas kulcsadatok mostantól el vannak fedve a hibakeresési/részletes kimenetben.
- URL-ellenőrzés és hibaüzenet hozzáadva a kötegelt átírási létrehozás tartalommezőjéhez.

**COVID-19 rövidített tesztelés:**

Mivel a folyamatos világjárvány miatt a mérnökeinknek otthonról kell dolgozniuk, a világjárvány előtti manuális ellenőrzési szkriptek jelentősen lecsökkentek. Kevesebb konfigurációval rendelkező eszközön tesztelünk, és nőhet a környezetspecifikus hibák előfordulásának valószínűsége. Még mindig szigorú ellenőrzés alatt áll az automatizálás nagy készlete. Abban a nem valószínű esetben, ha kihagytunk valamit, tudajuk meg velünk a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!

## <a name="text-to-speech-2021-february-release"></a>Szöveg beszédfelismerés – 2021. februári kiadás

**egyedi neurális hang GA**

egyedi neurális hang a következő 13 nyelven érhető el februárban: kínai (mandarin, egyszerűsített), angol (Ausztrália), angol (India), angol (Egyesült Királyság), angol (Egyesült Királyság), angol (Egyesült Államok), francia (Kanada), francia (Franciaország), német (Németország), olasz (Olaszország), Japán (Japán), koreai (Korea), portugál (brazíliai), spanyol (Mexikó) és spanyol (Spanyolország). További információ a [egyedi neurális hang](custom-neural-voice.md) és a [használatukról.](concepts-guidelines-responsible-deployment-synthetic.md) egyedi neurális hang funkció regisztrációt igényel, és a Microsoft a Microsoft jogosultsági feltételei alapján korlátozhatja a hozzáférést. További információ a korlátozott [hozzáférésről.](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)  

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: 2021- januári kiadás

> [!NOTE]
> A Windowson elérhető Speech SDK a 2015-ös, 2017-es és 2019-es Visual Studio microsoftos Visual C++ terjeszthető verziójától függ. Töltse le [innen.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Kiemelések összegzése**
- A kisebb memória- és lemezterület-igény hatékonyabbá teszi az SDK-t.
- Magasabb minőségű kimeneti formátumok érhetők el az egyéni neurális hang privát előzetes kiadásához.
- Az Intent Recognizer mostantól a legfelső szándéknál többet tud visszaadni, így külön értékelést is lehet végezni az ügyfél szándéka alapján.
- A beszédfelismerési asszisztenst vagy robotot könnyebb beállítani, így nem kell azonnal figyelnie, és nagyobb mértékben szabályozhatja, hogyan reagáljon a hibákra.
- Javítva lett az eszköz teljesítménye a tömörítés opcionális készítésével.
- Használja a Speech SDK-t Windows ARM/ARM64 rendszeren.
- Továbbfejlesztett alacsony szintű hibakeresés.
- A kiejtési felmérési funkció már szélesebb körben elérhető.
- Számos hibajavítás a GitHubon ön, az értékes ügyfeleink által megjelölt problémák megoldásához. köszönöm! Tartsa meg a visszajelzést!

**Fejlesztések**
- A Speech SDK mostantól hatékonyabb és egyszerűsített. Több kiadást indítottunk, hogy csökkentsük a Speech SDK memóriahasználatát és lemezterület-igényét. Első lépésként jelentősen csökkentjük a fájlméretet a megosztott kódtárakban a legtöbb platformon. Az 1.14-es kiadáshoz képest:
  - A 64 bites UWP-kompatibilis Windows-kódtárak körülbelül 30%-kal kisebbek.
  - A 32 bites Windows-kódtárak még nem látnak méretbeli fejlesztéseket.
  - A Linux-kódtárak 20–25%-kal kisebbek.
  - Az Android-kódtárak 3–5%-kal kisebbek.

**Új funkciók**
- **Mind:** Új, 48 KHz-es kimeneti formátumok érhetők el az egyéni neurális hang privát előzeteséhez a TTS beszédszintézisi API-n keresztül: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **All**: Az egyéni hang is egyszerűbben használható. Egyéni hang beállításának támogatása a `EndpointId` következővel: ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java,](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_) [JavaScript,](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId) [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). A módosítás előtt az egyéni hangfelhasználóknak a metódussal kellett beállítaniuk a végpont `FromEndpoint` URL-címét. Az ügyfelek most már a nyilvános hangokhoz hasonló módon használhatja a metódust, majd meg is biztosítanak egy üzembe `FromSubscription` helyezési azonosítót a `EndpointId` beállításával. Ez leegyszerűsíti az egyéni hangok beállítását. 
- **C++/C#/Java/Objective-C/Python:** A felső szándéknál többet kap a `IntentRecognizer` következőtől: . Mostantól támogatja az összes szándékot tartalmazó JSON-eredmény konfigurálását, és nem csak a legjobb pontozási szándékot a metóduson keresztül az `LanguageUnderstandingModel FromEndpoint` `verbose=true` URI paraméter használatával. Ez a [GitHub-probléma #880.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880) A frissített dokumentációt itt [találhatja.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** Állítsa le a hangsegéd vagy robot azonnali figyelését. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) most már rendelkezik egy metódussal, amely a `StopListeningAsync()` (kódtárhoz) lesz `ListenOnceAsync()` használva. Ez azonnal leállítja a hangrögzítést, és elegánsan megvárja az eredményt, így tökéletes a "leállítás most" gombnyomási forgatókönyvekhez.
- **C++/C#/Java/JavaScript:** A hangsegéd vagy robot jobb reakciót a mögöttes rendszerhibákra. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) már rendelkezik egy új `TurnStatusReceived` eseménykezelővel. Ezek a választható események a robot minden megoldásának felelnek meg, és akkor jelentik a turn végrehajtási hibákat, amikor azok bekövetkeznek, például egy nem kezelt kivétel, időtúllépés vagy hálózatkimaradás következtében a Direct Line Speech és a [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) robot között. `TurnStatusReceived` A megkönnyíti a hibafeltételekre való válaszadást. Ha például egy robot túl sokáig tart egy háttéradatbázis-lekérdezésen (például egy termék kitekerését), lehetővé teszi az ügyfél számára, hogy a "sajnáljuk, nem egészen értem, próbálkozzon újra" vagy `TurnStatusReceived` hasonlóval.
- **C++/C#**: A Speech SDK használata több platformon. A [Speech SDK NuGet-csomag](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) mostantól támogatja a Windows ARM/ARM64 asztali natív bináris fájlokat (az UWP már támogatott), hogy a Speech SDK több géptípus esetében is hasznosabb legyen.
- **Java:** most már rendelkezik egy metódussal, amely véletlenül ki lett zárva [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) a korábban használt `setSpeechActivityTemplate()` nyelvből. Ez egyenértékű a tulajdonság beállításával, és kérni fogja, hogy a Direct Line Speech szolgáltatás által Bot Framework jövőbeli összes jövőbeli tevékenység egyesítse a megadott tartalmat a `Conversation_Speech_Activity_Template` JSON-adattartalmakban.
- **Java:** Továbbfejlesztett alacsony szintű hibakeresés. A osztálynak most már van egy eseménye, amely hasonló a többi [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) `MessageReceived` programnyelvhez (C++, C#). Ez az esemény alacsony szintű hozzáférést biztosít a szolgáltatásból bejövő adatokhoz, és hasznos lehet a diagnosztikához és a hibakereséshez.
- **JavaScript:** Egyszerűbb beállítás hangsegédek és robotok számára a parancs használatával, amely már rendelkezik olyan gyári metódusokkal, amelyek egyszerűbbé teszik az egyéni szolgáltatáshelyek használatát, és manuálisan beállítják a [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) `fromHost()` `fromEndpoint()` tulajdonságokat. Emellett szabványosítottunk egy opcionális specifikációt, amely nem alapértelmezett robotot `botId` használ a konfigurációs gyárakban.
- **JavaScript:** Javítva lett az eszköz teljesítménye a websocket tömörítéshez hozzáadott sztringvezérlési tulajdonság révén. Teljesítménybeli okokból alapértelmezés szerint letiltottuk a websocket-tömörítést. Ez kis sávszélességű forgatókönyvek esetén újra is használható. További [részleteket itt talál.](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid) Ez a [GitHub-probléma megoldásához #242.](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)
- **JavaScript:** A kiejtési értékelés támogatása hozzáadva a beszéd kiejtésének kiértékelése érdekében. A rövid útmutatót itt [láthatja.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Hibajavítások**
- **All** (a JavaScript kivételével): Ki lett javítva egy regresszió az 1.14-es verzióban, amelyben túl sok memóriát lefoglalt a recognizer.
- **C++**: Ki van javítva a szemétgyűjtési problémája a következővel: , a `DialogServiceConnector` [GitHub-probléma megoldása #794.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794)
- **C#**: Kijavítottuk a szálleállítással kapcsolatos hibát, amely miatt az objektumok körülbelül egy másodpercre blokkoltak a eldobáskor.
- **C++/C#/Java:** Kijavítottunk egy kivételt, amely megakadályozta, hogy egy alkalmazás a beszédengedélyezési jogkivonatot vagy tevékenységsablont egynél egynél többet ad meg egy `DialogServiceConnector` alkalmazásban.
- **C++/C#/Java:** Kijavítottunk egy recognizer-összeomlást egy verseny miatt a leszakadás során.
- **JavaScript:** korábban nem tiszteletben tette az üzemekben megadott opcionális [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) `botId` `BotFrameworkConfig` paramétert. Ezért a lekérdezési sztring paraméterét manuálisan kellett beállítani egy nem `botId` alapértelmezett robot használatára. A hibát kijavítottuk, és az üzemeknek megadott értékek figyelembe lesznek téve és fel lesznek használva, beleértve az új és a `botId` `BotFrameworkConfig` `fromHost()` `fromEndpoint()` kiegészítéseket is. Ez a paraméterre `applicationId` is `CustomCommandsConfig` vonatkozik.
- **JavaScript:** Javítva a [GitHub #881,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881)amely lehetővé teszi a recognizer objektum újrahasználatát.
- **JavaScript:** Kijavítottunk egy hibát, amely miatt az SKD többször is küldött egy `speech.config` TTS-munkamenetben, ami feleslegesen pazarolta a sávszélességet.
- **JavaScript:** Egyszerűsített hibakezelés a mikrofon engedélyezésénél, amely lehetővé teszi, hogy a leíróbb üzenetek felbuborodhatnak, ha a felhasználó nem engedélyezte a mikrofon bevitelét a böngészőben.
- **JavaScript:** Kijavítottuk a [GitHub-#249,](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) ahol a begépeli a hibákat, és fordítási hibát okozott `ConversationTranslator` a `ConversationTranscriber` TypeScript-felhasználók számára.
- **Objective-C:** Kijavítottunk egy hibát, amely miatt a GStreamer buildje sikertelen volt az iOS-en az Xcode 11.4-es verzión, és a [GitHub-probléma](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911)#911.
- **Python:** Javítva a [GitHub-#870,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)és eltávolítottuk az "ElalasztásWarning: az imp modul elavult az importlib használata érdekében".

**Példák**
- [A JavaScript-böngésző fájlból származó mintája](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) mostantól fájlokat használ a beszédfelismeréshez. Ez a [GitHub-probléma #884.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884)

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI (más néven SPX): 2021. januári kiadás

**Új funkciók**
- A Speech CLI most [már NuGet-csomagként](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) érhető el, és a .NET CLI-n keresztül telepíthető .NET globális eszközként, amely a rendszerhéjból/parancssorból hívható meg.
- A [Custom Speech DevOps-sablontár](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) frissítve lett, hogy a Speech CLI-t használja a Custom Speech-munkafolyamatokhoz.

**COVID-19** rövidített tesztelés: Mivel a folyamatos világjárvány továbbra is megköveteli, hogy a mérnökeink otthonról dolgoznak, a világjárvány előtti manuális ellenőrzési szkriptek jelentősen lecsökkentek. Kevesebb konfigurációval rendelkező eszközön tesztelünk, és nőhet a környezetspecifikus hibák előfordulásának valószínűsége. Még mindig szigorú ellenőrzés alatt áll az automatizálás nagy készlete. Abban a nem valószínű esetben, ha kihagytunk valamit, tudajuk meg velünk a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!

## <a name="text-to-speech-2020-december-release"></a>Szöveg beszédfelismerés – 2020. decemberi kiadás

**Új neurális hangok az ga és az előzetes verzióban**

51 új hang jelent meg összesen 129 neurális hanghoz 54 nyelven/területi szinten:

- **46** új hang az GA területi beállításaiban: Shakir in `ar-EG` Arab (Arab), Hamed `ar-SA` in Arab (Arab Emírségek), Boriemet in Arab (Arab Emírségek), Boriológus in Katalán `bg-BG` `ca-ES` (Spanyolország), Andin in Cseh `cs-CZ` (Cseh Köztársaság), Jeppe in `da-DK` Dániában (Norvég), Pedig `de-AT` Német (Japán), Jan `de-CH` németül (Svájc), Nestoras `el-GR` in Görög (Angol `en-CA` (Magyar), Angol (Kanada), Angol `en-IE` (Kanada), Madhur in `en-IN` Hindi (India), Mohan `en-IN` in Telugu (India), Prabhat angol `en-IN` (India), Valluvar `en-IN` in Tamil (India), Enric `es-ES` in Catalan (Spanyolország), Kert in `et-EE` Majdn (Illetve), Harri in Finn (Finn), Serri in Finn `fi-FI` `fi-FI` (Finn), Fabrice in `fr-CH` French (Svájc), Colm in `ga-IE` India (Írország), Avri in `he-IL` Héber (India), Srecko `hr-HR` in Fogalmi (Magyar), Tamas in Magyar (Magyar), Gadis in Indonéz `hu-HU` `id-ID` (Indonéz), Fogas `lt-LT` in Fogn (Magyar), Nils in Lett (Lett), `lv-LV` Osman in `ms-MY` Malay (Malay), Joseph in `mt-MT` Maltese (Rend) , Finn `nb-NO` norvég, Bokmål (Norvég), Pernille `nb-NO` norvég, Bokmål (Norvég), Fenna in `nl-NL` Holland (Holland), Maarten `nl-NL` in Holland (Holland), Agnieszka in Lengyel `pl-PL` (Lengyel), Marek in `pl-PL` Lengyel (Magyar), Duarte in `pt-BR` Portugál (Brazília), Raquel in Portugál `pt-PT` (Potugal), `ro-RO` Hamitry in Russian `ru-RU` (Oroszország), Svetlana in Orosz (Oroszország), Marek `ru-RU` in Szlovák (Brazil), Rok in Kínai `sk-SK` (svéd), Mattias in Svéd `sl-SI` `sv-SE` (Svéd), Sofie in `sv-SE` Svéd (Svéd), Niwat in `th-TH` Thai (Török), Ahmet in `tr-TR` Török (Török), NamMinh in `vi-VN` Vietnami (Vietnam), HsiaoChen in `zh-TW` Tajvanese Mandarin (Tajvan), YunJhe in `zh-TW` Tajvanese Mandarin (Tajvan), HiuMaan in chinese `zh-HK` cantonese (Hongkong), WanLung in `zh-HK` Kínai cantonese (Hongkong).

- **5** új hang az előzetes verzió területi technikáiban: Az előzetes verzió területi hangja: Az előzetes verzió területi hangja: Az ír Nils az előzetes verzióban: Az előzetes verzió területi hangja: Az előzetes verzió területi 5 új hangja: Az előzetes verzió területi 5 új hangja: Az előzetes verzió területi hangja: Az előzetes verzióban elérhető `et-EE` `ga-IE` nilshangok: `lv-LV` `lt-LT` Az előzetes verzió területi hangja: Az előzetes verzió területi hangja: Az előzetes verzió területi 5 új hangja: Az előzetes verzió területi hangja: Az előzetes verzió területi hangja: Az előzetes `mt-MT`

Ebben a kiadásban összesen 129 neurális hangot támogatunk 54 nyelven/területi szinten. Emellett több mint 70 standard hang érhető el 49 nyelven/területi szinten. A [teljes listát a Nyelvi támogatás](language-support.md#text-to-speech) listában keresse fel.

**A hanganyagkészítés**
- Továbbfejlesztett hangkiválasztási felhasználói felület hangkategóriák és részletes hangleírások segítségével. 
- Az intonáció finomhangolásának engedélyezése az összes neurális hanghoz a különböző nyelveken.
- Automatizálta a felhasználói felület honosítását a böngésző nyelve alapján.
- Vezérlők `StyleDegree` engedélyezve az összes `zh-CN` neurális hanghoz.
Az új [hanganyagkészítés az](https://speech.microsoft.com/audiocontentcreation) új funkciókért látogasson el a hanganyagkészítés eszközbe. 

**A zh-CN-hangok frissítései**
- Az összes `zh-CN` neurális hang frissítve lett az angol nyelvű beszéd támogatásához.
- Engedélyezte az összes `zh-CN` neurális hangot az intonációs módosítás támogatásához. Az SSML hanganyagkészítés eszköz használható a legjobb intonációhoz való beállításhoz.
- Az összes `zh-CN` több stílusú neurális hang frissítve a vezérlés `StyleDegree` támogatásához. Az érzelmek intenzitása (gyenge vagy erős) módosítható.
- `zh-CN-YunyeNeural`Frissítve, hogy több stílust is támogatjon, amelyek különböző érzelmeket hajthat végre.

## <a name="text-to-speech-2020-november-release"></a>Szöveg beszédfelismerés – 2020. novemberi kiadás

**Új területi és hangszínek előzetes verzióban**
- **A neurális** TTS-portfólióban öt új hang és nyelv van bevezetve. Ezek a következőek: Grace in maltese (Magyar), Ona in Fogn (Felcsút), Anu in Majdan (Magyar), Orla in ToEse (Írország) és Everita in Lettn (Lett).
- **Öt `zh-CN` új, több stílust** és szerepkört támogató hang: Yuhan, Yumo, Xiaorui, Xiaoxuan és Yunxi.

> Ezek a hangok három Azure-régióban érhetők el nyilvános előzetes verzióban: EastUS, SouthEastAsia és WestEurope.

**Neurális TTS-tároló GA**
- A neurális TTS-tárolóval a fejlesztők a természetesebb digitális hangokkal futtatják a beszédszintézist a saját környezetükben az adott biztonsági és adatirányítási követelményekhez. Tekintse [meg a Speech-tárolók telepítését.](speech-container-howto.md) 

**Új funkciók**
- **Custom Voice:** lehetővé tette a felhasználók számára, hogy hangmodellt másoljanak az egyik régióból a másikba; támogatott végpont felfüggesztése és a felfüggesztés. Itt a [portálra](https://speech.microsoft.com/customvoice) menek.
- [Az SSML csendes címke](speech-synthesis-markup.md#add-silence) támogatása. 
- Általános hangminőségi fejlesztések a TTS-ben: Továbbfejlesztett szószintű kiejtési pontosság az nb-NO-ban. Csökkentett 53%-os kiejtési hiba.

> További információt ebben [a tech blogban talál.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Szöveg beszédfelismerés – 2020. októberi kiadás

**Új funkciók**
- A Rendszer egy új stílust `newscast` támogat. Tekintse [meg, hogyan használhatja a beszédstílusokat az SSML-ben.](speech-synthesis-markup.md#adjust-speaking-styles)
- **A neurális hangok HiFiNet vocoderre frissítve,** magasabb hanghűséggel és gyorsabb szintézisi sebességgel. Ez azon ügyfelek számára előnyös, akiknek a forgatókönyve hi-fi hanganyagra vagy hosszú interakciókra támaszkodik, beleértve a videó-szinkronizálást, a hangoskönyveket vagy az online oktatási anyagokat. [A történetről további információt és a hangmintákat a technikai közösség blogján olvashat](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Custom Voice](https://speech.microsoft.com/customvoice)  &  [hanganyagkészítés Studio](https://speech.microsoft.com/audiocontentcreation) 17 területi stb.** A felhasználók a felhasználóbarátabb felhasználói élmény érdekében egyszerűen válthatnak helyi nyelvre.   
- **hanganyagkészítés:** Új stílusdifok-vezérlő hozzáadva a XiaonézoNeuralhoz; Finomította a testreszabott törés funkciót, hogy 50 ezredm-es növekményes töréseket tartalmaz. 

**Általános hangminőségi fejlesztések a TTS-hez**
- Továbbfejlesztett szószintű kiejtési pontosság a ben `pl-PL` (hibaarány csökkentése: 51%) és `fi-FI` (hibaarány csökkentése: 58%)
- Továbbfejlesztett `ja-JP` egyszavas olvasás a szótár forgatókönyvhöz. Csökkentett kiejtési hiba 80%-kal.
- `zh-CN-XiaoxiaoNeural`: Továbbfejlesztett hangulat/CustomerService/Newscast/Egyedek/Stílus hangminősége.
- `zh-CN`: Továbbfejlesztett erhua-kiejtés, világos hangvétel és finomított térproody, amely nagy mértékben javítja az érthetőséget. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: 2020- októberi kiadás

> [!NOTE]
> A Windowson elérhető Speech SDK a 2015-ös, 2017-es és 2019-es verziókhoz Visual Studio Microsoft Visual C++ terjeszthető verziójától függ. Töltse le [ide.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Új funkciók**
- **Linux:** Debian 10 és Ubuntu 20.04 LTS támogatása hozzáadva.
- **Python/Objective-C:** Az API támogatása `KeywordRecognizer` hozzáadva. A dokumentáció itt [található.](./custom-keyword-basics.md)
- **C++/Java/C#**: Támogatás hozzáadva bármilyen kulcs/érték beállításához a `HttpHeader` `ServicePropertyChannel::HttpHeader` használatával.
- **JavaScript:** Az API támogatása `ConversationTranscriber` hozzáadva. A dokumentációt itt [olvashatja el.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#**: Új metódus `AudioDataStream FromWavFileInput` hozzáadva (a olvasáshoz). WAV-fájlok) [itt (C++)](/cpp/cognitive-services/speech/audiodatastream) [és itt (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/C#/Java/Python/Objective-C/Swift:** Új metódus hozzáadva a `stopSpeakingAsync()` szöveg-beszéd szintézisének leállítására. Olvassa el a referenciadokumentációt itt [(C++),](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace) [itt (C#),](/dotnet/api/microsoft.cognitiveservices.speech) [itt (Java),](/java/api/com.microsoft.cognitiveservices.speech) [itt (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)és [itt (Objective-C/Swift)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java:** Hozzáadott egy függvényt a osztályhoz, amely a kapcsolati és leválasztási `FromDialogServiceConnector()` `Connection` eseményeinek figyelére `DialogServiceConnector` használható. Olvassa el a referenciadokumentációt [itt (C#),](/dotnet/api/microsoft.cognitiveservices.speech.connection) [itt (C++)](/cpp/cognitive-services/speech/connection)és [itt (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/C#/Java/Python/Objective-C/Swift:** Támogatás hozzáadva a kiejtési értékeléshez, amely kiértékeli a beszédkiejtést, és visszajelzést ad a beszélőknek a beszélt hang pontosságával és fluency-ével kapcsolatban. A dokumentációt itt [olvashatja.](how-to-pronunciation-assessment.md)

**A feltörést nem megváltoztató**
- **JavaScript:** A PullAudioOutputStream.read() visszatérési típus egy belső ígéretről egy natív JavaScript-ígéretre változott.

**Hibajavítások**
- **All**: Javítva lett az 1,13-as regresszió, amikor a rendszer figyelmen kívül hagyta `SetServiceProperty` a bizonyos speciális karakterekkel megadott értékeket.
- **C#**: A 2019-es Visual Studio windowsos konzolminták nem találják a natív DLL-eket.
- **C#**: Javítva az összeomlás memóriakezeléssel, ha a streamet használja `KeywordRecognizer` bemenetként.
- **ObjectiveC/Swift:** Javítva az összeomlás memóriakezeléssel, ha a stream recognizer bemenetként van használva.
- **Windows:** Kijavítva az UWP-n a BT HFP/A2DP közös megléte.
- **JavaScript:** Kijavítottuk a munkamenet-ip-ek leképezését a naplózás javítása és a belső hibakeresési/szolgáltatásbeli korrelációk segítése érdekében.
- **JavaScript:** Javítás hozzáadva a `DialogServiceConnector` hívások `ListenOnce` letiltására az első hívás után.
- **JavaScript:** Kijavítva a hiba, amely miatt az eredménykimenet csak "egyszerű" volt.
- **JavaScript:** Ki van javítva a folyamatos felismerési probléma macOS rendszeren a Safariban.
- **JavaScript:** CPU-terheléscsökkentés a magas kérésátviteli forgatókönyvhöz.
- **JavaScript:** Hozzáférés engedélyezése a hangprofil-regisztráció eredményének részleteihez.
- **JavaScript:** Javítás hozzáadva a folyamatos felismeréshez `IntentRecognizer` a-ban.
- **C++/C#/Java/Python/Swift/ObjectiveC:** Az australiaeast és a brazilsouth url-címe kijavítva `IntentRecognizer` a következőben: .
- **C++/C#**: `VoiceProfileType` Argumentumként hozzáadva objektum `VoiceProfile` létrehozásakor.
- **C++/C#/Java/Python/Swift/ObjectiveC:** Javítva a lehetőség, amikor egy adott pozícióból `SPX_INVALID_ARG` `AudioDataStream` próbál olvasni.
- **IOS:** Javítva az összeomlás beszédfelismeréssel a Unityben

**Példák**
- **ObjectiveC:** Kulcsszófelismerési minta hozzáadva [itt:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript:** Gyorsútmutató hozzáadva a beszédátíráshoz [itt (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) és [itt (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/C#/Java/Python/Swift/ObjectiveC:** Hozzáadott minta a kiejtési [felméréshez](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples) itt
- **Xamarin:** A rövid útmutató frissítve lett Visual Studio sablonra [itt.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin)

**Ismert probléma**
- A DigiCert globális szintű G2-tanúsítvány alapértelmezés szerint nem támogatott a HoloLens 2 és az Android 4.4 (KitKat) esetében, és a Speech SDK működőképessége érdekében hozzá kell adni a rendszerhez. A tanúsítvány a közeljövőben hozzá lesz adva a HoloLens 2 operációs rendszer rendszerképeihez. Az Android 4.4-ügyfeleknek hozzá kell adni a frissített tanúsítványt a rendszerhez.

**COVID-19 rövidített tesztelés:** Mivel az elmúlt néhány hétben távolról dolgoztunk, nem lehetett olyan sok manuális ellenőrzést tesztelni, mint általában. Nem végeztünk olyan módosításokat, amelyekről úgy gondoljuk, hogy bármit is hibásak lehetett volna, és az automatizált tesztjeink is teljesek. Abban a valószínűtlen esetben, ha kihagytunk valamit, lépjen velünk a [GitHubra.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI (más néven SPX): 2020- októberi kiadás
Az SPX egy parancssori felület, amely kódírás nélkül használja az Azure Speech szolgáltatást. Töltse le a legújabb verziót [itt:](./spx-basics.md). <br>

**Új funkciók**
- `spx csr dataset upload --kind audio|language|acoustic` – adatkészleteket hozhat létre helyi adatokból, nem csak URL-címekből.
- `spx csr evaluation create|status|list|update|delete` – összehasonlítja az új modelleket az alapkonfiguráció igazság-/egyéb modelljeivel.
- `spx * list` – támogatja a nem lapzó felhasználói élményt (ehhez nincs szükség --top X --skip X).
- `spx * --http header A=B` – egyéni fejlécek támogatása (az Office egyéni hitelesítéshez hozzáadva). 
- `spx help` – továbbfejlesztett szöveg és háttér-órajel szövegszín (kék).

## <a name="text-to-speech-2020-september-release"></a>Szöveg beszédfelismerés – 2020. szeptemberi kiadás

### <a name="new-features"></a>Új funkciók

* **Neurális TTS** 
    * **18 új nyelv/területi nyelv támogatása.** Ezek angol, cseh, német (Svájc), német (Svájc), görög, angol (Írország), francia (svájc), héber, magyar, indonéz, maláj, magyar, magyar, német, német, tamil, telugu és vietnami. 
    * **14 új hang jelent meg, amelyek a meglévő nyelvek változatosságát bővítik.** Lásd [a teljes nyelvet és hanglistát.](language-support.md#neural-voices)
    * **Új beszédstílusok a `en-US` és `zh-CN` a hangokhoz.** Az új hang az angol (US) nyelven, és támogatja a csevegőrobot, az ügyfélszolgálat és az asszisztens stílust. 10 új beszédstílus érhető el a zh-CN hanggal, a XiaoXio hanggal. Emellett a Neural Neural Voice támogatja a `StyleDegree` finomhangolást. Tekintse [meg, hogyan használhatja a beszédstílusokat az SSML-ben.](speech-synthesis-markup.md#adjust-speaking-styles)

* **Tárolók: A neurális TTS-tároló nyilvános előzetes verzióban jelent meg, 16 hanggal, 14 nyelven.** További információ a [Speech-tárolók neurális TTS-hez való üzembe helyezéséről](speech-container-howto.md)  

Olvassa el [az Ignite 2020-ra vonatkozó TTS-frissítések](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) teljes bejelentését 

## <a name="text-to-speech-2020-august-release"></a>Szöveg beszédfelismerés – 2020. augusztusi kiadás

### <a name="new-features"></a>Új funkciók

* **Neurális TTS: új beszédstílus a következő számára: `en-US` Aria voice**. Az AriaNeural úgy hangzik, mint egy híradó, amikor híreket olvas. A "newscast-formal" stílus sokkal komolyabbnak hangzik, míg a "newscast-alkalmi" stílus lazabb és kötetlenebb. Tekintse [meg, hogyan használhatja a beszédstílusokat az SSML-ben.](speech-synthesis-markup.md)

* Custom Voice: új funkció jelent meg, amely automatikusan **ellenőrzi a betanítás adatminőségét.** Az adatok feltöltésekor a rendszer megvizsgálja a hang- és átiratadatok különböző aspektusait, és automatikusan kijavítja vagy szűri a problémákat a hangmodell minőségének javítása érdekében. Ez magában foglalja a hang mennyiségét, a zajszintet, a beszéd kiejtési pontosságát, a beszéd normalizált szöveggel való igazítását, a hang csendes lejátszását, valamint a hang- és szkriptformátumot. 

* hanganyagkészítés: új funkciók, amelyek hatékonyabb hanghangolási és **hangkezelési képességeket biztosítanak.**

    * Kiejtés: a kiejtés finomhangolási funkciója a legújabb telefonszámkészletre frissül. A könyvtárból kiválaszthatja a megfelelő phoneme elemet, és finomíthatja a kiválasztott szavak kiejtését. 

    * Letöltés: A "Letöltés"/"Exportálás" funkció továbbfejlesztve támogatja a hang bekezdés szerinti generálásának támogatását. Ugyanabban a fájlban/SSML-ben több hangkimenet generálása közben is szerkesztheti a tartalmat. A "Letöltés" fájlstruktúrája is finomított. Mostantól egyetlen mappában is egyszerűen lekérte az összes hangfájlt. 

    * Feladat állapota: A több fájlból álló exportálási élmény javul. Ha több fájlt exportál a múltban, és az egyik fájl meghibásodott, a teljes feladat sikertelen lesz. Most azonban minden más fájl exportálása sikeres lesz. A feladatjelentés részletesebb és strukturáltabb információkkal bővült. A jelentéssel most már ellenőrizheti az összes hibás fájlt és mondatot a naplókban. 

    * SSML-dokumentáció: az SSML-dokumentumhoz csatolva ellenőrizheti az összes hangolási funkció használatára vonatkozó szabályokat.

* A Hanglista API frissül, és tartalmaz egy felhasználóbarát megjelenítendő nevet és a neurális **hangokhoz támogatott beszédstílusokat.**

### <a name="general-tts-voice-quality-improvements"></a>Általános hangminőségi fejlesztések a TTS-hez

* Csökkentett szószintű kiejtési hiba (%) a esetében `ru-RU` (56%-kal csökkentett hibák) és `sv-SE` (49%-kal csökkentett hibák)

* Továbbfejlesztett polinéz szóolvasás `en-US` neurális hangokon 40%-kal. Több szó lehet például az "olvasás", az "élő", a "tartalom", a "rekord", az "objektum" stb. 

* Javítva lett a kérdés hangnemének természetessége `fr-FR` a -ban. MOS (átlagos véleménypontszám) nyereség: +0,28

* Az alábbi hangokhoz frissítettük a vocodereket, a hűségbeli fejlesztésekkel és az általános teljesítmény 40%-kal gyorsításával.

    | Területi beállítás | Hang |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanoami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Hibajavítások

* Kijavítottunk néhány hibát a hanganyagkészítés eszközzel 
    * Ki van javítva az automatikus frissítéssel kapcsolatban. 
    * Kijavítottuk a hangstílusokkal kapcsolatos problémákat a zh-CN dél-Kelet-Ázsia régióban.
    * Ki van javítva a stabilitási probléma, beleértve a "break" címkével kapcsolatos exportálási hibát és az írásjelek hibáit.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Új beszédfelismerési területi lehetőségek: 2020. augusztusi kiadás
A beszédfelismerés augusztusban 26 új területi változatot adott ki: 2 európai nyelvet és 5 angol nyelvet és 19 spanyol nyelvet, amelyek a legtöbb dél-amerikai országot `cs-CZ` `hu-HU` lefedik. Az alábbi lista az új területi lehetőségeket tartalmazza. A teljes nyelvi listát itt [láthatja.](./language-support.md)

| Területi beállítás  | Nyelv                          |
|---------|-----------------------------------|
| `cs-CZ` | Cseh (Cseh Köztársaság)            | 
| `en-HK` | Angol (Hongkong)               | 
| `en-IE` | Angol (Írország)                 | 
| `en-PH` | Angol (Japánok)             | 
| `en-SG` | Angol (Szingapúr)               | 
| `en-ZA` | Angol (Dél-Afrika)            | 
| `es-AR` | Spanyol (Argentína)               | 
| `es-BO` | Spanyol (Spanyol)                 | 
| `es-CL` | Spanyol (Brazília)                   | 
| `es-CO` | Spanyol (Spanyolország)                | 
| `es-CR` | Spanyol (Angol)              | 
| `es-CU` | Spanyol (Spanyolország)                    | 
| `es-DO` | Spanyol (Dominikai Köztársaság)      | 
| `es-EC` | Spanyol (Spanyolország)                 | 
| `es-GT` | Spanyol (Éva)               | 
| `es-HN` | Spanyol (Éva)                | 
| `es-NI` | Spanyol (Nicaragua)               | 
| `es-PA` | Spanyol (Éva)                  | 
| `es-PE` | Spanyol (Spanyolország)                    | 
| `es-PR` | Spanyol (AmitEn)             | 
| `es-PY` | Spanyol (Éva)                | 
| `es-SV` | Spanyol (El Rendszer)             | 
| `es-US` | Spanyol (USA)                     | 
| `es-UY` | Spanyol (Spanyolország)                 | 
| `es-VE` | Spanyol (Spanyolország)               | 
| `hu-HU` | Magyar (Magyarország)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: 2020- júliusi kiadás

> [!NOTE]
> A Windowson elérhető Speech SDK a 2015-ös, 2017-es és 2019-es Visual Studio microsoftos Visual C++ terjeszthető verziójától függ. Töltse le és telepítse [innen:](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Új funkciók**
- **C#**: Aszinkron beszédátírás támogatása hozzáadva. A dokumentációt itt [találhatja.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** A Speaker Recognition és [a](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) [ böngészőhöz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)isnode.js.
- **JavaScript:** Támogatás hozzáadva az automatikus nyelvfelismeréshez/nyelvazonosítóhoz. A dokumentációt itt [találhatja.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** Többeszközes beszélgetés [és](./multi-device-conversation.md) beszédátírás [támogatása hozzáadva.](./conversation-transcription.md) 
- **Python:** Tömörített hangtámogatás hozzáadva a Pythonhoz Windows és Linux rendszeren. A dokumentációt itt [találhatja.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Hibajavítások**
- **All**: Kijavítottunk egy hibát, amely miatt a KeywordRecognizer nem haladt előre a streamek között a felismerés után.
- **All**: Kijavítottunk egy hibát, amely miatt a KeywordRecognitionResultból származó stream nem tartalmazta a kulcsszót.
- **All**: Kijavítottunk egy hibát, amely miatt a SendMessageAsync valójában nem küld üzenetet a vezetéken keresztül, miután a felhasználók befejezték a várakozást.
- **All**: Javítva lett egy összeomlás a Speaker Recognition API-kban, amikor a felhasználók többször is meg hívják a VoiceProfileClient::SpeakerRecEnrollProfileAsync metódust, és nem várták meg a hívások befejezését.
- **All**: Javítva a fájlnaplózás engedélyezése a VoiceProfileClient és a SpeakerRecognizer osztályban.
- **JavaScript:** Ki van [javítva](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) a szabályozással kapcsolatban a böngésző minimálisra csökkentésekor.
- **JavaScript:** Ki van [javítva](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) a streamek memóriavesztése.
- **JavaScript:** Gyorsítótárazás hozzáadva a NodeJS OCSP-válaszaihoz.
- **Java:** Kijavítottunk egy hibát, amely miatt a BigInteger-mezők mindig 0-t ad vissza.
- **iOS:** [Kijavítottuk](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) a Speech SDK-alapú alkalmazások iOS-App Store.

**Példák**
- **C++**: Mintakód hozzáadva a Speaker Recognition [itt.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)

**A COVID-19 rövidített tesztelése:** Mivel az elmúlt néhány hétben távolról dolgoztunk, nem lehetett olyan sok manuális ellenőrzést tesztelni, mint általában. Nem végeztünk olyan módosításokat, amelyekről úgy gondoljuk, hogy bármit is hibásak lehetett volna, és az automatizált tesztjeink is teljesek. Abban a valószínűtlen esetben, ha kihagytunk valamit, lépjen velünk a [GitHubra.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!

## <a name="text-to-speech-2020-july-release"></a>Szöveg beszédfelismerés – 2020. júliusi kiadás

### <a name="new-features"></a>Új funkciók

* Neurális **TTS, 15** új neurális hang: A neurális TTS-portfólióhoz hozzáadott új hangok: Salma in `ar-EG` Arab (Arab), Zarzarh in `ar-SA` Arab (Arab), Alba in `ca-ES` Katalán (Spanyolország), Ímeel in `da-DK` Dán (Dán), Neerja in `es-IN` English (India), Noora in `fi-FI` Finn (Finn) Finn), Swara in `hi-IN` Hindi (India), Colette in `nl-NL` Holland (Holland), Zorát lengyel `pl-PL` (Angol), Fernandát portugálul (Portugália), Dárdát `pt-PT` `ru-RU` oroszul (Oroszország), Hillevi `sv-SE` svédül (Brazília), Achara in `th-TH` Thai (Most), HiuGaai kínai `zh-HK` (kantoni, Hagyományos) és Kínai HsiaoYu `zh-TW` (tajvani mandarin). Ellenőrizze az [összes támogatott nyelvet.](./language-support.md#neural-voices)  

* **Custom Voice,** zökkenőmentes hangtesztelés a betanítási folyamatokkal a felhasználói élmény egyszerűsítése érdekében: Az új tesztelési funkcióval minden hang automatikusan tesztelve lesz az egyes nyelvekre optimalizált, előre meghatározott tesztkészletekkel, amelyek általános és hangsegédes forgatókönyveket fednek le. Ezeket a tesztelési készleteket gondosan kell kiválasztani és tesztelni, hogy tartalmazzák a tipikus használati eseteket és a nyelvben használt telefonszámokat. A felhasználók emellett saját tesztelési szkripteket is feltölthet a modell betanításkor.

* **hanganyagkészítés: megjelent egy új funkciókészlet, amely hatékonyabb hanghangolási és hangkezelési képességeket tesz lehetővé**

    * `Pitch`A , és továbbfejlesztve támogatja `rate` a finomhangolást egy előre meghatározott értékkel, például `volume` lassú, közepes és gyors. A felhasználók mostantól könnyen kiválaszthatnak egy "állandó" értéket a hangszerkesztéshez.

    ![Hanghangolás](media/release-notes/audio-tuning.png)

    * A felhasználók most már áttekintheti `Audio history` a munkahelyi fájljukat. Ezzel a funkcióval a felhasználók könnyedén nyomon követhetik a munkafájlhoz kapcsolódó összes létrehozott hangot. Ellenőrizhetik az előzményverziót, és összehasonlítják a minőséget, miközben egyszerre hangolnak. 

    ![Hangelőzmények](media/release-notes/audio-history.png)

    * A `Clear` funkció mostantól rugalmasabb. A felhasználók törölhetnek egy adott hangolási paramétert, miközben más paramétereket is elérhetővé tudnak tenni a kiválasztott tartalomhoz.  

    * A kezdőlapon egy oktatóvideó is [található,](https://speech.microsoft.com/audiocontentcreation) amely segít a felhasználóknak a TTS hanghangolás és hangkezelés gyors elkezdésében. 

### <a name="general-tts-voice-quality-improvements"></a>Általános hangminőségi fejlesztések a TTS-hez

* Továbbfejlesztett TTS vocoder a-ban a nagyobb hűség és kisebb késés érdekében.

    * Az Elsa új vocoderre frissült, amely +0,464 CMOS -t (összehasonlító átlagos véleménypontszám) ért el a hangminőségben, 40%-kal gyorsabban szintetizálva, és 30%-kal csökkent az első `it-IT` bájt késése. 
    * A Ahhoz, hogy a(z) `zh-CN` 20148 CMOS+0148 CMOS nyereséget ad az általános tartományhoz, a +0,348 a newscast stílushoz, a +0,195-öst a newscast stílushoz, a +0,195-öt pedig az általános tartományhoz. 

* Frissített `de-DE` és `ja-JP` hangmodellek, hogy természetesebb legyen a TTS-kimenet.
    
    * Katja frissítve lett a legújabb `de-DE` prosody modellezési módszerrel, a MOS (mean Opinion Score) nyeresége +0,13. 
    * FrissítettÜk a Nanoami-t egy új, prosody hangmagasságú modellel, a `ja-JP` MOS (átlagos véleménypontszám) nyeresége +0,19;  

* Továbbfejlesztett szószintű kiejtési pontosság öt nyelven.

    | Nyelv | Kiejtési hibák csökkentése |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Hibajavítások

* Pénznemek olvasása
    * Ki van javítva a pénznem és olvasásával `es-ES` kapcsolatban beolvasott hiba `es-MX`
     
    | Nyelv | Bevitel | Kiolvassa a fejlesztés után |
    |---|---|---|
    | `es-MX` | 1,58 USD | un peso cincuenta y ocho centavos |
    | `es-ES` | 1,58 USD | un dólar cincuenta y ocho centavos |

    * Negatív pénznem (például "-325 €" ) támogatása a következő területi beállításokban: `en-US` , , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Továbbfejlesztett címolvasás `pt-PT` a-ban.
* Kijavítottuk a Natasha ( ) és Libby ( ) kiejtési problémáit a `en-AU` `en-UK` "for" és a "four" szónál.  
* A hiba kijavítva hanganyagkészítés eszközben
    * A második bekezdés utáni további és váratlan szünet kijavítva.  
    * A "Nincs törés" funkció vissza lesz adva egy regressziós hiba miatt. 
    * A parancs véletlenszerű frissítési Speech Studio kijavítva.  

### <a name="samplessdk"></a>Minták/SDK

* JavaScript: Kijavítja a firefoxos és macOS-hez és iOS-hez való Safari lejátszással kapcsolatban problémákat. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: 2020- júniusi kiadás
**Speech CLI (más néven SPX)**
-   A CLI-hez elérhető súgókeresési funkciók hozzáadva:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Frissítve az újonnan üzembe helyezett 3.0-s Batch és Custom Speech API-khoz:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Új funkciók**
-   **C, \# C++**: Speaker Recognition előzetes verzió: Ez a funkció lehetővé teszi a beszélőazonosítást (ki beszél?) és a beszélő-ellenőrzést (a beszélő, akinek állítja magát?). Kezdjen egy [áttekintéssel,](./speaker-recognition-overview.md)olvassa el [a Speaker Recognition alapszintű](./get-started-speaker-recognition.md)dokumentációját vagy az [API-referencia dokumentációját.](/rest/api/speakerrecognition/)

**Hibajavítások**
-   **C, \# C++**: A mikrofon felvétele nem működött az 1.12-es kódban a beszélőfelismerésben.
-   **JavaScript:** A Text-To-Speech (Szöveg beszédfelismerés) és a Safari (macOS és iOS) hibajavítása.
-   Javítás a Windows-alkalmazás-ellenőrző hozzáférés-megsértési összeomlásához a beszélgetés átírása során nyolccsatornás stream használata esetén.
-   Javítás a Windows-alkalmazás-ellenőrző hozzáférés-megsértési összeomlásához többeszközes beszélgetési fordítás esetén.

**Példák**
-   **C#**: [Kódminta](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) beszélőfelismeréshez.
-   **C++**: [Kódminta](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) beszélőfelismeréshez.
-   **Java:** [Kódminta szándékfelismeréshez](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) Androidon. 

**COVID-19 rövidített tesztelés:** Mivel az elmúlt hetekben távolról dolgoztunk, nem lehetett olyan sok manuális ellenőrzést tesztelni, mint általában. Nem végeztünk olyan módosításokat, amelyekről úgy gondoljuk, hogy bármit is hibásak lehetett volna, és az automatizált tesztjeink mind leestek. Abban a nem valószínű esetben, ha kihagytunk valamit, tudajuk meg velünk a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: 2020. májusi kiadás
**Speech CLI (más mint SPX)**
- **Az SPX** egy új parancssori eszköz, amely lehetővé teszi a felismerés, a szintézis, a fordítás, a kötegelt átírás és az egyéni beszédkezelés parancssorból való elvégzését. Ezzel teszteli a Speech Service-t, vagy szkriptet futtathat a végrehajtania szükséges Speech Service-feladatokhoz. Töltse le az eszközt, és olvassa el a [dokumentációt itt.](./spx-overview.md)

**Új funkciók**
- **Go:** Új Go nyelvi támogatás a [beszédfelismeréshez és](./get-started-speech-to-text.md?pivots=programming-language-go) az [egyéni beszédfelismerési asszisztenshez.](./quickstarts/voice-assistants.md?pivots=programming-language-go) A fejlesztői környezetet itt [állíthatja be.](./quickstarts/setup-platform.md?pivots=programming-language-go) Mintakódért tekintse meg az alábbi Minták szakaszt. 
- **JavaScript:** Böngészőtámogatás hozzáadva a szövegfelismeréshez. A dokumentációt itt [találhatja.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** Új objektumok és API-k támogatottak `KeywordRecognizer` Windows, Android, Linux & iOS platformokon. A dokumentációt itt [olvashatja el.](./custom-keyword-overview.md) Mintakódért tekintse meg az alábbi Minták szakaszt. 
- **Java:** Többeszközes beszélgetés hozzáadása fordítási támogatással. A referencia-dokumentumot itt [láthatja.](/java/api/com.microsoft.cognitiveservices.speech.transcription)

**Az & fejlesztései**
- **JavaScript:** A böngésző mikrofon-implementációja javítja a beszédfelismerés pontosságát.
- **Java:** Újragyártott kötések közvetlen JNI-implementációval S IMPLEMENTáció nélkül. Ez a változás 10-ére csökkenti a Windows, Android, Linux és Mac rendszerhez használt összes Java-csomag kötésméretét, és megkönnyíti a Speech SDK Java-implementáció további fejlesztését.
- **Linux:** Frissítettük a támogatási [dokumentációt](./speech-sdk.md?tabs=linux) a legújabb RHEL 7-specifikus megjegyzésekkel.
- Továbbfejlesztett csatlakozási logika, amely többszöri kapcsolódást kísérel meg szolgáltatás- és hálózati hibák esetén.
- A [portal.azure.com](https://portal.azure.com) Speech gyorsútmutatója frissült, hogy a fejlesztők az Azure Speech folyamatának következő lépését választják.

**Hibajavítások**
- **C#, Java:** Kijavítottuk [az](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) SDK-kódtárak Linux ARM-en való betöltésével (32 bites és 64 bites) hiba is.
- **C#**: A TranslationRecognizer, az IntentRecognizer és a Connection objektumok natív leíróinak explicit megsemmisítése rögzített.
- **C#**: Rögzített hangbemenet élettartam-kezelése a ConversationTranscriber objektumhoz.
- Kijavítottunk egy hibát, amely miatt az eredmény oka nem lett megfelelően beállítva az egyszerű `IntentRecognizer` kifejezésekből származó szándékok felismerésekor.
- Kijavítottunk egy hibát, amely miatt `SpeechRecognitionEventArgs` az eredményeltolás helytelenül lett beállítva.
- Kijavítottunk egy verseny állapotot, amelyben az SDK hálózati üzenetet próbált küldeni a websocket-kapcsolat megnyitása előtt. Reprodukálható volt a számára `TranslationRecognizer` a résztvevők hozzáadása során.
- Javítva a memóriavesztés a recognizer kulcsszómotorban.

**Példák**
- **Go:** Gyorsútmutatók hozzáadva a [beszédfelismeréshez és](./get-started-speech-to-text.md?pivots=programming-language-go) az [egyéni hangfelismerési asszisztenshez.](./quickstarts/voice-assistants.md?pivots=programming-language-go) A mintakódot itt [találja.](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples) 
- **JavaScript:** Szöveg-beszéd, [](./get-started-text-to-speech.md?pivots=programming-language-javascript) [Translation](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)és [Szándékfelismerés.](./get-started-intent-recognition.md?pivots=programming-language-javascript)
- Kulcsszófelismerési minták [C-hez \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) és [Javához](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**COVID-19 rövidített tesztelés:** Mivel az elmúlt hetekben távolról dolgoztunk, nem lehetett olyan sok manuális ellenőrzést tesztelni, mint általában. Nem végeztünk olyan módosításokat, amelyekről úgy gondoljuk, hogy bármit is hibásak lehetett volna, és az automatizált tesztjeink mind leestek. Ha kihagyottunk valamit, tudd meg velünk a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Maradjon kifogástalan!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: 2020. márciusi kiadás
**Új funkciók**
- Linux: A Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 [](./how-to-configure-rhel-centos-7.md) támogatása hozzáadva a rendszer Speech SDK-hoz való konfigurálási utasításaival.
- Linux: A .NET Core C# támogatása hozzáadva Linux ARM32 és ARM64 rendszeren. További tudnivalók [itt](./speech-sdk.md?tabs=linux). 
- C#, C++: A-ben hozzáadva egy konzisztens azonosító az összes köztes és végső `UtteranceId` `ConversationTranscriptionResult` beszédfelismerési eredményhez. A [C# és a](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult) [C++ részletei.](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)
- Python: A támogatása `Language ID` hozzáadva. Lásd speech_sample.py fájlt [a GitHub-adattárban.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
- Windows: Tömörített hangbemeneti formátum támogatása a Windows platformon az összes win32-konzolalkalmazáshoz. Részletek [itt:](./how-to-use-codec-compressed-audio-input-streams.md). 
- JavaScript: A beszédszintézis (szöveg-beszéd) támogatása a NodeJS-ben. További információkat [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) talál. 
- JavaScript: Új API-k hozzáadása az összes küldési és fogadott üzenet vizsgálatához. További információkat [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) talál. 
        
**Hibajavítások**
- C#, C++: Kijavítottunk egy hibát, így a bináris üzenetet bináris `SendMessageAsync` típusként küldi el. A [C# és a](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) [C++ részletei.](/cpp/cognitive-services/speech/connection)
- C#, C++: Kijavítottunk egy hibát, amely miatt az esemény használata összeomlást okozhat, ha az objektum előtt `Connection MessageReceived` `Recognizer` el van `Connection` dobva. A [C# és a](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived) [C++ részletei.](/cpp/cognitive-services/speech/connection#messagereceived)
- Android: A mikrofon hangpufferének mérete 800 ezredmről 100 ezredmre csökkent a késés javítása érdekében.
- Android: Ki van [javítva](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) az x86 Android Emulator egyik problémája a Android Studio.
- JavaScript: Támogatás hozzáadva a kínai régiókhoz az `fromSubscription` API-val. Részletek [itt:](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-). 
- JavaScript: További hibainformációk hozzáadása a NodeJS csatlakozási hibákkal kapcsolatban.
        
**Példák**
- Unity: A szándékfelismerési nyilvános minta kijavítva, ahol a LUIS JSON-importálása sikertelen volt. Részletek [itt:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: Minta hozzáadva a következő `Language ID` hez: . Részletek [itt:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**A Covid19 rövidített tesztelése:** Mivel az elmúlt hetekben távolról dolgoztunk, nem lehetett olyan sok manuális eszközellenőrzési teszt, mint általában. Például nem sikerült tesztelni a mikrofon bemenetét és a beszélőkimenetet Linux, iOS és macOS rendszeren. Nem végeztünk olyan módosításokat, amelyekről úgy gondoljuk, hogy ezeken a platformokon bármit is hibásak lehetett volna, és az automatizált tesztjeink is teljesek. Abban a nem valószínű esetben, ha kihagytunk valamit, tudajuk meg velünk a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Köszönjük a folyamatos támogatását. Mint mindig, tegye fel kérdéseit vagy visszajelzéseit a [GitHubon,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) [vagy](https://stackoverflow.microsoft.com/questions/tagged/731)Stack Overflow.<br>
Maradjon kifogástalan!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: 2020. februári kiadás

**Új funkciók**

 - Python-csomagok hozzáadva a Python új 3.8-as kiadásának támogatásához.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 támogatás (C++, C#, Java, Python).
   > [!NOTE] 
   > Az ügyfeleknek a következő utasításoknak megfelelően kell konfigurálni az [OpenSSL-t:](./how-to-configure-openssl-linux.md).
 - Linux ARM32-támogatás Debian és Ubuntu rendszerekhez.
 - A DialogServiceConnector mostantól támogatja az opcionális "robotazonosító" paramétert a BotFrameworkConfig eszközben. Ez a paraméter lehetővé teszi több Direct Line Speech robot használatát egyetlen Azure Speech-erőforrással. A paraméter megadása nélkül a rendszer az alapértelmezett robotot használja (amelyet Direct Line Speech csatornakonfigurációs oldal határoz meg).
 - A DialogServiceConnector most már rendelkezik SpeechActivityTemplate tulajdonságtal. A JSON-sztring tartalmát a Direct Line Speech a Direct Line Speech-robotot el érő összes tevékenység számos támogatott mezőjének előzetes feltöltésére fogja használni, beleértve az olyan eseményekre adott válaszként létrehozott tevékenységeket is, mint a beszédfelismerés.
 - A TTS most előfizetői kulcsot használ a hitelesítéshez, így csökkentve az első szintetizátor létrehozása utáni első bájtos késést.
 - 19 területi elérhetőséggel frissített beszédfelismerési modell a szavak átlagos hibaarányának 18,6%-os csökkentéséhez (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). Az új modellek jelentős fejlesztéseket hoznak több tartományban, beleértve a diktálást, Call-Center átírási és videóindexelési forgatókönyveket.

**Hibajavítások**

 - Kijavítva a hiba, amely miatt a Conversation Transcriber nem várt megfelelően a JAVA API-kban 
 - Android x86-emulátorjavítás xamarin [GitHub-problémához](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Hiányzó hozzáadása (Get| Set)Tulajdonság metódusa: AudioConfig
 - Kijavítottunk egy TTS-hibát, amely miatt az audioDataStreamet nem lehetett leállítani, ha a kapcsolat meghiúsul
 - Egy régió nélküli végpont használata USP-hibákat okozna a beszélgetési fordítónál
 - Az univerzális Windows-alkalmazásokban az azonosítók létrehozása mostantól egy megfelelően egyedi GUID-algoritmust használ; korábban és akaratlanul egycsonkolt implementációt használt, amely gyakran ütközéseket is létrehozott a nagy interakciók során.
 
 **Példák**
 
 - Unity-minta a Speech SDK [Unity-mikrofonnal és leküldéses módú streameléssel való használatához](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**További változások**

 - [A Linuxhoz frissített OpenSSL konfigurációs dokumentáció](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: 2020- januári kiadás

**Új funkciók**

- Többeszközes beszélgetés: több eszközt csatlakoztathat ugyanhoz a beszédhez vagy szövegalapú beszélgetéshez, és igény szerint lefordíthatja a közöttük küldött üzeneteket. További információt ebben [a cikkben talál.](multi-device-conversation.md) 
- Kulcsszófelismerés támogatása androidos .aar-csomagokhoz, valamint az x86- és x64-változatok támogatása. 
- Objective-C: `SendMessage` és `SetMessageProperty` az objektumhoz adott `Connection` metódusok. A dokumentációt itt [találhatja.](/objectivec/cognitive-services/speech/spxconnection)
- A TTS C++ API mostantól támogatja a szintetézis szövegbevitelét, így nincs szükség sztringgé konvertálni a sztringet, mielőtt át kellene adni `std::wstring` az SDK-nak. A részleteket [itt láthatja.](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: Mostantól elérhető a [nyelvazonosító](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) és a [forrásnyelv](./how-to-specify-source-language.md?pivots=programming-language-csharp) konfigurációja.
- JavaScript: Új funkció hozzáadva az objektumhoz, amely a Speech Service egyéni üzeneteit adja át `Connection` `receivedServiceMessage` visszahívásként.
- JavaScript: A támogatása hozzáadva a-hoz, hogy megkönnyítse a használatot a saját tárolók és `FromHost API` szuverén felhők használata érdekében. A dokumentációt itt [találhatja.](speech-container-howto.md)
- JavaScript: Most az `NODE_TLS_REJECT_UNAUTHORIZED` [orgads](https://github.com/orgads)közreműködését köszönjük. A részleteket itt [láthatja.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Kompatibilitástörő változások**

- `OpenSSL` az 1.1.1b-es verzióra frissült, és statikusan kapcsolódik a Linuxhoz készült Speech SDK alapkönyvtárához. Ez törést okozhat, ha a beérkezett üzenetek még nem voltak telepítve a `OpenSSL` `/usr/lib/ssl` könyvtárba a rendszerben. A probléma [megoldásához](how-to-configure-openssl-linux.md) tekintse meg a Speech SDK dokumentációját.
- Módosítottuk a C# által visszaadott adattípust értékről típusra, hogy hozzáférést biztosítsunk, ha a beszédadatok 2 percnél `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` hosszabbak.
- `PushAudioInputStream` És most küldje el a wav fejléc adatait a Speech Service-nek a alapján, opcionálisan megadva `PullAudioInputStream` `AudioStreamFormat` a létrehozáskor. Az ügyfeleknek most a támogatott [hangbemeneti formátumot kell használniuk.](how-to-use-audio-input-streams.md) A többi formátum optimálisnál rosszabb felismerési eredményeket ad vissza, vagy más problémákat okozhat. 

**Hibajavítások**

- Tekintse meg `OpenSSL` a frissítést a fenti Breaking changes (Feltörés változások) alatt. Kijavítottunk egy időszakos összeomlást és egy teljesítménnyel kapcsolatos problémát (nagy terhelés esetén a zárolást) Linux és Java esetén. 
- Java: Továbbfejlesztve lett az objektumok lezárása magas egyidejűségi forgatókönyvekben.
- Újrastrukturáltuk a NuGet-csomagot. Eltávolítottuk a és a három példányát a lib mappákból, így a NuGet-csomag letöltése kisebb és gyorsabb volt, és hozzáadtunk néhány natív C++ alkalmazás fordítása érdekében szükséges `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` fejléceket.
- A gyorsindítási minták ki vannak [javítva itt.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Ezek a "mikrofon nem található" kivétel megjelenítése nélkül voltak kilépve Linux, macOS és Windows rendszeren.
- Kijavítottuk az SDK összeomlását a hosszú beszédfelismerési eredményekkel bizonyos kódútvonalakon, például [a mintán.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Kijavítottuk az SDK üzembe helyezési hibát az Azure Web App-környezetben az [ügyféllel kapcsolatos probléma megoldása érdekében.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Kijavítottunk egy TTS-hibát, amikor több címkét vagy címkét használtunk az `<voice>` `<audio>` [ügyféllel kapcsolatos probléma megoldásához.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Kijavítottunk egy TTS 401-es hibát, amikor az SDK-t helyreállították a felfüggesztett rendszerből.
- JavaScript: Kijavítottuk a hangadatok körkörös importálását az [euirim közreműködésének köszönhetően.](https://github.com/euirim) 
- JavaScript: a szolgáltatástulajdonságok beállításának támogatása az 1.7-es kódban megadottak szerint.
- JavaScript: Kijavítottunk egy hibát, amely miatt a kapcsolati hiba folyamatos, sikertelen újracsatlakozási websocket-kísérleteket eredményezhetett.

**Példák**

- Kulcsszófelismerési minta hozzáadva Androidhoz [itt:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- TTS-minta hozzáadva az itt található kiszolgálói [forgatókönyvhöz.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)
- Többeszközes beszélgetési gyorsútmutatók hozzáadva c# és C++ esetén [itt:](quickstarts/multi-device-conversation.md).

**További változások**

- Optimalizált SDK-alapkönyvtárméret Androidon.
- Az SDK az 1.9.0-s és újabb verziókban egyaránt támogatja a és típusokat a `int` `string` Conversation Transcriber hangaláírás-verzió mezőjében.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: 2019- novemberi kiadás

**Új funkciók**

- Hozzáadott egy API-t, amely megkönnyíti a használatot a saját `FromHost()` tárolók és szuverén felhők használata érdekében.
- Automatikus forrás-Nyelvfelismerés a beszédfelismeréshez (Java és C++)
- Objektum hozzáadva a Beszédfelismeréshez, amely a várt forrásnyelvek megadására használható (Java és `SourceLanguageConfig` C++)
- Támogatás `KeywordRecognizer` hozzáadva Windows (UWP), Android és iOS rendszeren a NuGet- és Unity-csomagokon keresztül
- A Remote Conversation Java API hozzáadva a beszélgetésátíráshoz aszinkron kötegben.

**Kompatibilitástörő változások**

- A beszélgetés átírási funkciói a névtérben helyezték `Microsoft.CognitiveServices.Speech.Transcription` át.
- A Conversation Transcriber metódusok egyes részei új osztályba vannak `Conversation` áthelyezve.
- A 32 bites (ARMv7 és x86) iOS támogatása eldobott

**Hibajavítások**

- Javítás az összeomláshoz, ha a local `KeywordRecognizer` elő van használva érvényes Speech service előfizetői kulcs nélkül

**Példák**

- Xamarin-minta a `KeywordRecognizer`
- Unity-minta a `KeywordRecognizer`
- C++ és Java-minták az automatikus forrás-Nyelvfelismerés.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: 2019. szeptemberi kiadás

**Új funkciók**

- Bétaverziós támogatás hozzáadva a Xamarinhoz Univerzális Windows-platform (UWP), Android és iOS rendszeren
- iOS-támogatás hozzáadva a Unityhez
- Bemeneti `Compressed` támogatás hozzáadva az A Mező, a Mu illetve a FLAC rendszerhez Android, iOS és Linux rendszeren
- Hozzá `SendMessageAsync` van `Connection` adva a osztályban az üzenetek szolgáltatásnak való elküldését.
- Hozzáadva `SetMessageProperty` a `Connection` osztályhoz egy üzenet tulajdonságának beállítására
- TTS hozzáadott kötések Java (JRE és Android), Python, Swift és Objective-C
- TTS-támogatás hozzáadva a macOS, iOS és Android rendszerekhez.
- "szóhatár" információ hozzáadva a TTS-hez.

**Hibajavítások**

- Ki van javítva az IL2CPP build problémája az Androidhoz készült Unity 2019-ben
- Ki lett javítva a hiba, amely miatt a helytelenül formázott fejlécek hibásan vannak feldolgozva a wav fájlbemenetben
- Kijavítva a hiba, amely miatt az UUID-azonosítók egyes kapcsolattulajdonságokban nem egyediek
- Ki van javítva néhány figyelmeztetés a Swift-kötések nullázhatósági kóddal kapcsolatos figyelmeztetéseiből (kis kódváltozásokat is szükségessé tehet)
- Kijavítottunk egy hibát, amely miatt a websocket-kapcsolatok nem sikeresen lezárultak hálózati terhelés alatt
- Kijavítottunk egy problémát Androidon, amely olykor a által használt duplikált megjelenési adatokhoz vezet `DialogServiceConnector`
- A többfordulatos interakciók kapcsolatainak stabilitásának fejlesztései és a hibák jelentése (eseményeken keresztül) a következő `Canceled` eseményekkel: `DialogServiceConnector`
- `DialogServiceConnector` A munkamenet-kezdetek mostantól megfelelően biztosítják az eseményeket, beleértve a hívását `ListenOnceAsync()` az aktív `StartKeywordRecognitionAsync()`
- A fogadott tevékenységekhez `DialogServiceConnector` kapcsolódó összeomlások elhárítva

**Példák**

- Rövid útmutató a Xamarinhoz
- Frissített CPP rövid útmutató Linux ARM64-információkkal
- Frissült a Unity rövid útmutatója iOS-információkkal

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019. júniusi kiadás

**Példák**

- Gyorsútmutató-minták a Text To Speechhez az UWP-n és a Unityn
- Gyorsútmutató-minta Swifthez iOS-hez
- Unity-minták a Speech & Szándékfelismerés és a Translation szolgáltatáshoz
- Frissített gyorsindítási minták a `DialogServiceConnector`

**Fejlesztések /módosítások**

- Párbeszédpanel névtere:
  - A `SpeechBotConnector` új nevet kapott: `DialogServiceConnector`
  - A `BotConfig` új nevet kapott: `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` A újra lett leképezve a `DialogServiceConfig::FromBotSecret()`
  - Az átnevezés Direct Line Speech az összes meglévő ügyfél támogatása továbbra is támogatott
- A TTS REST-adapter frissítése a proxy, az állandó kapcsolat támogatásához
- Hibaüzenet javítása érvénytelen régió esetén
- Swift/Objective-C:
  - Továbbfejlesztett hibajelentés: Az olyan módszerek, amelyek hibát eredményezhetnek, most két verzióban létezik: az egyik egy objektumot tesz elérhetővé hibakezelésre, a másik pedig `NSError` kivételt jelent. Az előbbit a Swift teszi elérhetővé. Ehhez a módosításhoz módosítani kell a meglévő Swift-kódot.
  - Továbbfejlesztett eseménykezelés

**Hibajavítások**

- Javítás a TTS-hez: ahol a jövő nem tért vissza várakozás nélkül, amíg a hang `SpeakTextAsync` renderelése be nem fejeződött
- Javítás a sztringek C#-ban való beállításához a teljes nyelvi támogatás engedélyezéséhez
- A .NET Core-alkalmazásokkal a core kódtár net461-es célkeretrendszerbe való betöltésének problémájának kijavítva a mintákban
- A natív kódtárak kimeneti mappában való üzembe helyezésével kapcsolatos problémák elhárítása mintákban
- Hibajavítás a webes szoftvercsatornák megbízható bezárásához
- Javítva a lehetséges összeomlások, amikor Linuxon nagy terhelés alatt nyit meg egy kapcsolatot
- A macOS-keretrendszer csomag hiányzó metaadatainak kijavítva
- A Windows `pip install --user` problémáinak elhárítása

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nincs hatással az SDK JavaScript-verziójára.

**Hibajavítások**

- A FromSubscription hibajavítása beszédátírással való használata során.
- Ki lett javítva a hangsegédek kulcsszófelismerési programhibája.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: 2019. májusi kiadás

**Új funkciók**

- A kulcsszavas kulcsszavas spotting (EGYS) mostantól Windows és Linux rendszeren is elérhető. A FOGJAS funkció bármilyen mikrofontípussal használható, a hivatalos PEDIG CSAK a Azure Kinect DK hardverben vagy a Speech Devices SDK-ban található mikrofontömbökre van korlátozva.
- A kifejezésmutató funkció az SDK-val érhető el. További információ: [.](./get-started-speech-to-text.md)
- A beszédátírási funkció az SDK-val érhető el. Lásd [itt:](./conversation-transcription.md).
- Hangsegédek támogatása a Direct Line Speech csatornával.

**Példák**

- Minták hozzáadva az SDK által támogatott új szolgáltatásokhoz vagy szolgáltatásokhoz.

**Fejlesztések /módosítások**

- Különböző recognizer tulajdonságok hozzáadva a szolgáltatás viselkedésének vagy a szolgáltatás eredményeinek módosításához (például profanitás maszkolása és egyebek).
- Most már konfigurálhatja a recognizert a szabványos konfigurációs tulajdonságokon keresztül, még akkor is, ha létrehozta a `FromEndpoint` recognizert.
- Objective-C: `OutputFormat` tulajdonság hozzáadva a következő hez: `SPXSpeechConfiguration` .
- Az SDK mostantól Linux-disztribúcióként támogatja a Debian 9-et.

**Hibajavítások**

- Kijavítottunk egy problémát, amely miatt a beszélő-erőforrás túl korán lett destrukturálva a szövegből a beszédbe.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Ez egy csak JavaScript-kiadás. Nem lett hozzáadva funkció. A következő javításokat hozzuk:

- Megakadályozza, hogy a webcsomag betöltsen https-proxy-agentet.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019. áprilisi kiadás

**Új funkciók**

- Az SDK mostantól bétaverzióként támogatja a text-to-speech szolgáltatást. Windows és Linux rendszerű asztali számítógépeken C++ és C# környezetben támogatott. További információért tekintse meg a [szöveg-beszéd áttekintését.](text-to-speech.md#get-started)
- Az SDK mostantól támogatja az MP3 és az Opus/OGG hangfájlokat streambemeneti fájlokként. Ez a funkció csak Linux rendszeren érhető el C++ és C# használatával, és jelenleg bétaverzióban érhető el (további [részletek itt).](how-to-use-codec-compressed-audio-input-streams.md)
- A Javához, .NET Core-hoz, C++-hoz és Objective-C-hez készült Speech SDK macOS-támogatást kapott. A macOS Objective-C támogatása jelenleg bétaverzióban érhető el.
- iOS: Az iOS-hez készült Speech SDK (Objective-C) mostantól CocoaPodként is közzé vantéve.
- JavaScript: Nem alapértelmezett mikrofon támogatása bemeneti eszközként.
- JavaScript: Proxytámogatás a Node.js.

**Példák**

- A Speech SDK C++ és Objective-C használatával macOS rendszeren való használatának mintái hozzáadva.
- A text-to-speech szolgáltatás használatát bemutató minták hozzáadva.

**Fejlesztések/módosítások**

- Python: A felismerési eredmények további tulajdonságai mostantól a tulajdonságon keresztül vannak `properties` elérhetővé téve.
- További fejlesztési és hibakeresési támogatásért átirányíthatja az SDK naplózási és diagnosztikai adatait egy naplófájlba (további részletek [itt találhatók).](how-to-use-logging.md)
- JavaScript: A hangfeldolgozási teljesítmény javítása.

**Hibajavítások**

- Mac/iOS: Ki lett javítva az a hiba, amely hosszú várakozáshoz vezetett, amikor nem lehetett kapcsolatot létesíteni a Speech szolgáltatással.
- Python: a Python-visszahívások argumentumai hibakezelésének javítása.
- JavaScript: Javítva lett a helytelen állapotjelentés a RequestSession által végződött beszédhez.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: 2019. februári frissítés

Ez egy hibajavítási kiadás, amely csak a natív/felügyelt SDK-t érinti. Ez nem befolyásolja az SDK JavaScript-verzióját.

**Hibajavítás**

- Ki van javítva a memóriavesztés mikrofonbemenet használata esetén. A streamalapú vagy fájlbemenet nincs hatással.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019. februári kiadás

**Új funkciók**

- A Speech SDK a osztályon keresztül támogatja a bemeneti mikrofon `AudioConfig` kiválasztását. Ez lehetővé teszi a hangadatok streamelését a Speech szolgáltatásba egy nem alapértelmezett mikrofonból. További információért tekintse meg a hangbemeneti eszközök [kiválasztását ismertető dokumentációt.](how-to-select-audio-input-devices.md) Ez a funkció még nem érhető el a JavaScriptből.
- A Speech SDK mostantól támogatja a Unityt egy bétaverzióban. Visszajelzést a GitHub mintaadattár probléma szakaszában [küldhet.](https://aka.ms/csspeech/samples) Ez a kiadás a Unityt támogatja Windows x86 és x64 (asztali vagy Univerzális Windows-platform-alkalmazások) és androidos (ARM32/64, x86) rendszereken. További információt a Unity rövid [útmutatója tartalmaz.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)
- A `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájlra (amely a korábbi kiadásokban lett kiállítva) már nincs szükség. A funkció mostantól integrálva van az alap SDK-ba.

**Példák**

A mintaadattárban a következő új tartalom [érhető el:](https://aka.ms/csspeech/samples)

- További példák a `AudioConfig.FromMicrophoneInput` szolgáltatáshoz.
- További Python-minták a szándékfelismeréshez és -fordításhoz.
- További minták az objektum `Connection` iOS-hez való használatával.
- További Java-minták fordításhoz hangkimenettel.
- Új minta a Kötegelt [átírási REST API.](batch-transcription.md)

**Fejlesztések/módosítások**

- Python
  - Továbbfejlesztett paraméter-ellenőrzés és hibaüzenetek `SpeechConfig` a-ban.
  - Az objektum támogatása `Connection` hozzáadva.
  - 32 bites Python (x86) támogatása Windows rendszeren.
  - A Pythonhoz készült Speech SDK a bétaverzióból készült.
- iOS
  - Az SDK mostantól az iOS SDK 12.1-es verziójára épül.
  - Az SDK mostantól támogatja az iOS 9.2-es és újabb verzióit.
  - A referenciadokumentáció fejlesztése és számos tulajdonságnév javítása.
- JavaScript
  - Az objektum támogatása `Connection` hozzáadva.
  - Típusdefiníciós fájlok hozzáadása a kötegelt JavaScripthez
  - Kifejezésmutatók kezdeti támogatása és implementációja.
  - Tulajdonsággyűjtemény visszaadva a szolgáltatás JSON-fájlját a felismeréshez
- A Windows-beli DLL-ek már tartalmaznak verzióerőforrást.
- Ha létrehoz egy recognizert, közvetlenül a végpont URL-címéhez `FromEndpoint` adhat hozzá paramétereket. A használatával a recognizer nem `FromEndpoint` konfigurálható a szabványos konfigurációs tulajdonságokkal.

**Hibajavítások**

- Az üres proxynevet és proxyjelszót nem megfelelően kezelték. Ha ebben a kiadásban a proxy felhasználónevét és proxyjelszóját egy üres sztringre adja meg, a proxyhoz való csatlakozáskor ezek nem lesznek elküldve.
- Az SDK által létrehozott SessionId egyes nyelvekhez/környezetekhez nem mindig volt &nbsp; igazán véletlenszerű. A probléma megoldásához véletlenszerű generátori inicializálást adtunk hozzá.
- Az engedélyezési jogkivonat kezelésének javítása. Ha engedélyezési jogkivonatot szeretne használni, adja meg a értéket a értékben, és hagyja üresen az `SpeechConfig` előfizetői azonosítót. Ezután hozza létre a recognizert a szokásos módon.
- Bizonyos esetekben az `Connection` objektum nem lett megfelelően kiadva. Ez a probléma megoldva.
- A JavaScript-minta úgy lett kijavítva, hogy a Safariban is támogassa a fordításszintézis hangkimenetét.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Ez egy csak JavaScript-kiadás. Nem lett hozzáadva funkció. A következő javításokat hozzuk:

- A stream tűzvége a turn.end, nem pedig a speech.end.
- Ki lett javítva a hangküldetőben lévő hiba, amely nem ütemezi a következő küldést, ha az aktuális küldés sikertelen volt.
- A folyamatos felismerés kijavítva hitelesítési jogkivonattal.
- Hibajavítás különböző recognizer/végpontok esetén.
- A dokumentáció fejlesztései.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018. decemberi kiadás

**Új funkciók**

- Python
  - A Python-támogatás bétaverziója (3.5-ös és újabb verziók) ebben a kiadásban érhető el. További információ: itt](quickstart-python.md).
- JavaScript
  - A JavaScripthez készült Speech SDK nyílt forráskódú. A forráskód elérhető a [GitHubon.](https://github.com/Microsoft/cognitive-services-speech-sdk-js)
  - Most már támogatjuk a Node.js, további információt itt [talál.](./get-started-speech-to-text.md)
  - A hang-munkamenetek hosszkorlátja el lett távolítva, az újracsatlakozás automatikusan megtörténik a lefedés alatt.
- `Connection` Objektum
  - A `Recognizer` -ból hozzáférhet egy `Connection` objektumhoz. Ezzel az objektummal explicit módon kezdeményezheti a szolgáltatáskapcsolatot, és feliratkozhat az események csatlakoztatására és leválasztására.
    (Ez a funkció még nem érhető el a JavaScriptből és a Pythonból.)
- Az Ubuntu 18.04 támogatása.
- Android
  - ProGuard-támogatás engedélyezése az APK-generáció során.

**Fejlesztések**

- A belső szálhasználat fejlesztései, a szálak, a zárolások és a mutexek számának csökkentése.
- Továbbfejlesztett hibajelentés / információ. Számos esetben a hibaüzenetek nem propagálva vannak a kiútig.
- Frissített fejlesztési függőségek a JavaScriptben, hogy naprakész modulokat használjanak.

**Hibajavítások**

- Ki van javítva a memóriavesztés, mert a típusa nem `RecognizeAsync` egyező.
- Bizonyos esetekben kivételek szivárogtak ki.
- A memóriavesztés kijavítva a fordítási esemény argumentumai között.
- Kijavítottunk egy zárolási hibát a hosszú ideig futó munkamenetek újracsatlakozása során.
- Kijavítottunk egy hibát, amely a sikertelen fordítások végeredményének hiányához vezethetett.
- C#: Ha a főszálban nem várt műveletet, lehetséges volt, hogy a felismerőt az aszinkron feladat befejezése előtt el lehetett `async` volna dobni.
- Java: Kijavítottunk egy problémát, amely a Java virtuális gép összeomlását eredményezi.
- Objective-C: Rögzített felsorolásleképezés; A recognizedIntent érték lett visszaadva a `RecognizingIntent` helyett.
- JavaScript: Állítsa az alapértelmezett kimeneti formátumot "simple" formátumra `SpeechConfig` a fájlban.
- JavaScript: Inkonzisztencia eltávolítása a konfigurációs objektum tulajdonságai között a JavaScriptben és más nyelveken.

**Példák**

- Több minta frissítése és kijavítása (például a fordítás kimeneti hangja stb.).
- Új Node.js a [mintaadattárban.](https://aka.ms/csspeech/samples)

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Új funkciók**

- Android x86/x64 támogatása.
- Proxytámogatás: A objektumban most már hívhat meg egy függvényt `SpeechConfig` a proxyinformációk (állomásnév, port, felhasználónév és jelszó) beállításához. Ez a funkció még nem érhető el iOS-hez.
- Továbbfejlesztett hibakód és üzenetek. Ha egy felismerés hibát adott vissza, ez már be lett állítva (megszakítva esemény esetén) vagy (a felismerés `Reason` eredményében) `CancellationDetails` a következőre: `Error` . A lemondott esemény most már két további tagot tartalmaz: `ErrorCode` és `ErrorDetails` . Ha a kiszolgáló további hibainformációt adott vissza a jelentett hibával együtt, az mostantól elérhető lesz az új tagokban.

**Fejlesztések**

- További ellenőrzés hozzáadva a recognizer konfigurációjában, és további hibaüzenet is hozzáadva.
- Továbbfejlesztett hosszú távú csend kezelése egy hangfájl közepén.
- NuGet-csomag: a .NET-keretrendszer esetén megakadályozza az AnyCPU-konfigurációval való építést.

**Hibajavítások**

- Kijavítottunk néhány kivételt, amelyek a recognizersben találhatók. Emellett a kivételek is el vannak fogva, és eseménygé `Canceled` alakulnak.
- Memóriavesztés kijavítva a tulajdonságkezelésben.
- Kijavítva a hiba, amely miatt egy hangbemeneti fájl összeomlott a recognizerben.
- Kijavítottunk egy hibát, amely miatt a munkamenet-leállítási esemény után eseményeket lehetett megkapni.
- Kijavítottunk néhány versenykörülményt a szálolásban.
- Kijavítottunk egy kompatibilitási problémát, amely összeomláshoz vezethet.
- Stabilitással kapcsolatos fejlesztések az Android-mikrofonok támogatásához.
- Kijavítottunk egy hibát, amely miatt a JavaScript felismerője figyelmen kívül hagyja a felismerési nyelvet.
- Kijavítottunk egy hibát, amely megakadályozta a `EndpointId` beállítását a JavaScriptben (bizonyos esetekben).
- Módosította a paramétersorrendet a JavaScript AddIntent paraméterében, és hozzáadta a `AddIntent` hiányzó JavaScript-aláírást.

**Példák**

- C++ és C#-minták hozzáadva leküldéses és leküldéses streamhasználathoz a [mintaadattárban.](https://aka.ms/csspeech/samples)

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Megbízhatósági fejlesztések és hibajavítások:

- Ki van javítva a lehetséges végzetes hiba, amely a recognizer kiesése miatt verseny miatt lépett fel
- Ki van javítva a lehetséges végzetes hiba, ha a tulajdonságok nem vannak beszűkülve.
- További hiba- és paraméter-ellenőrzés hozzáadva.
- Objective-C: Kijavítva az NSStringben a név felülírása által okozott lehetséges végzetes hiba.
- Objective-C: Az API módosított láthatósága
- JavaScript: Kijavítva az eseményekre és azok hasznos adatokra vonatkozóan.
- A dokumentáció fejlesztései.

A [mintaadattárban](https://aka.ms/csspeech/samples)egy új JavaScript-minta lett hozzáadva.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018- szeptemberi kiadás

**Új funkciók**

- Az Objective-C támogatása iOS-ben. Tekintse meg az [Objective-C rövid útmutatót iOS-hez.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)
- JavaScript-támogatás böngészőben. Tekintse meg a [JavaScript rövid útmutatóját.](./get-started-speech-to-text.md)

**Kompatibilitástörő változások**

- Ebben a kiadásban számos jelentős változást vezetünk be.
  A [részletekért tekintse meg](https://aka.ms/csspeech/breakingchanges_1_0_0) ezt az oldalt.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018. augusztusi kiadás

**Új funkciók**

- A Speech SDK-val készült UWP-alkalmazások mostantól Windows alkalmazásminősítő készlet (WACK) is.
  Tekintse meg az [UWP rövid útmutatóját.](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)
- A .NET Standard 2.0 támogatása Linux rendszeren (Ubuntu 16.04 x64).
- Kísérleti: A Java 8 támogatása Windows (64 bites) és Linux (Ubuntu 16.04 x64) rendszeren.
  Tekintse meg a [Java Runtime Environment útmutatót.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre)

**Funkcionális változás**

- További hibainformációk elérhetővé tevése a kapcsolati hibákkal kapcsolatban.

**Kompatibilitástörő változások**

- Java (Android) rendszeren a `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` függvénynek már nincs szüksége elérésiút-paraméterre. A rendszer mostantól automatikusan észleli az elérési utat az összes támogatott platformon.
- A Java és A C# tulajdonság get-accessor `EndpointUrl` tulajdonsága el lett távolítva.

**Hibajavítások**

- A Java-ban a translation recognizer hangszintézisének eredménye most már meg van valósítva.
- Kijavítottunk egy hibát, amely inaktív szálakat és több nyitott és nem használt szoftvercsatornát okozhatott.
- Kijavítottunk egy problémát, amely miatt a hosszan futó felismerés leállhat az átvitel közben.
- Ki van javítva egy verseny feltétel a recognizer leállításakor.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018- júliusi kiadás

**Új funkciók**

- Android-platform támogatása (API 23: Android 6.0 Marshmallow vagy újabb). Tekintse meg az [Android rövid útmutatóját.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)
- A .NET Standard 2.0 támogatása Windows rendszeren. Tekintse meg a [.NET Core rövid útmutatóját.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)
- Kísérleti: Az UWP támogatása Windows rendszeren (1709-es vagy újabb verzió).
  - Tekintse meg az [UWP rövid útmutatóját.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)
  - Vegye figyelembe, hogy a Speech SDK-val készült UWP-alkalmazások még nem Windows alkalmazásminősítő készlet (WACK).
- Hosszú ideig futó felismerés támogatása automatikus újracsatlakozással.

**Funkcionális változások**

- `StartContinuousRecognitionAsync()` A támogatja a hosszú ideig futó felismerést.
- A felismerés eredménye több mezőt tartalmaz. Eltolást jelentenek a felismert szöveg hanganyagának elejétől és időtartamától (órajelben), valamint a felismerés állapotát felismerő további értékektől, például és `InitialSilenceTimeout` `InitialBabbleTimeout` értéktől.
- Az AuthorizationToken támogatása a gyári példányok létrehozásához.

**Kompatibilitástörő változások**

- Felismerési események: `NoMatch` az eseménytípus egyesítése az `Error` eseménybe.
- A C#-ban a SpeechOutputFormat új nevet ad, hogy `OutputFormat` a C++-hoz igazodva maradjon.
- A felület néhány metódusának visszatérési `AudioInputStream` típusa némileg megváltozott:
  - Javában a `read` metódus a értéket adja vissza a `long` `int` helyett.
  - A C#-ban a `Read` metódus a értéket adja vissza a `uint` `int` helyett.
  - A C++-ban a és a `Read` `GetFormat` metódusok mostantól az helyett `size_t` térnek `int` vissza.
- C++: A hangbemeneti streamek példányai mostantól csak típusúként `shared_ptr` továbbíthatóak.

**Hibajavítások**

- Az eredmény helytelen visszaadott értékei ki vannak javítva `RecognizeAsync()` időkorreklúként.
- A Windows media Foundation-kódtárainak függősége el lett távolítva. Az SDK mostantól Core Audio API-kat használ.
- Dokumentációjavítás: A támogatott [régiókat leíró régióoldal](regions.md) hozzáadva.

**Ismert probléma**

- Az Androidhoz készült Speech SDK nem jelenti a fordítás beszédszintézisének eredményeit. Ez a probléma a következő kiadásban ki lesz javítva.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018. júniusi kiadás

**Funkcionális változások**

- AudioInputStream

  A recognizer mostantól használhatja a streamet hangforrásként. További információért tekintse meg a kapcsolódó [útmutatót.](how-to-use-audio-input-streams.md)

- Részletes kimeneti formátum

  A létrehozásakor `SpeechRecognizer` lekért vagy kimeneti `Detailed` `Simple` formátumot kérhet. A tartalmaz egy megbízhatósági pontszámot, felismert szöveget, nyers lexikális formát, normalizált formát és normalizált formát `DetailedSpeechRecognitionResult` maszkolt profanitással.

**A feltörést nem megváltoztató**

- A `SpeechRecognitionResult.Text` C#-ban a `SpeechRecognitionResult.RecognizedText` következőre változott: .

**Hibajavítások**

- Kijavítottunk egy lehetséges visszahívási hibát az USP-rétegben a leállítás során.
- Ha egy felismerő felhasznált egy hangbemeneti fájlt, akkor a szükségesnél tovább tartott a fájlkezelőben.
- Több holtpont el lett távolítva az üzenetklip és a recognizer között.
- Az eredmény `NoMatch` akkor lesz kioltva, ha a szolgáltatás válasza időkorrekulált.
- A Windows média alapkönyvtárai késve töltődnek be. Ez a kódtár csak mikrofonbemenethez szükséges.
- A hangadatok feltöltési sebessége az eredeti hangsebesség körülbelül kétszeresére van korlátozva.
- Windows rendszeren a C# .NET-szerelvények neve már erős.
- Dokumentációjavítás: `Region` a recognizer létrehozásához szükséges információ.

További mintákat adtunk hozzá, és folyamatosan frissülnek. A legújabb mintákért tekintse meg a [Speech SDK-minták GitHub-adattárát.](https://aka.ms/csspeech/samples)

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018. májusi kiadás

Ez a kiadás a Cognitive Services Speech SDK első nyilvános előzetes kiadása.
