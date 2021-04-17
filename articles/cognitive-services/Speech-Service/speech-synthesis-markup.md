---
title: Beszédszintézis-jelölőnyelv (SSML) – Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Beszédszintézis-jelölőnyelv használata a kiejtés és a prosody szabályozása a szövegből a beszédbe.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1d21691af4d52892f507695a56331816b14bf517
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588377"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>A szintézis javítása beszédszintézis-jelölőnyelvvel (SSML)

A Beszédszintézis-jelölőnyelv (SSML) egy XML-alapú jelölőnyelv, amellyel a fejlesztők megadhatják, hogyan konvertálják a bemeneti szöveget szintetizált beszédgé a text-to-speech szolgáltatással. Az egyszerű szöveghez képest az SSML lehetővé teszi a fejlesztők számára, hogy finomhangolják a hangmagasságot, a kiejtést, a beszédarányt, a hangerőt és a szöveg-beszéd kimenetet. A rendszer automatikusan kezeli a normál írásjeleket, például a szüneteltetést egy pont után, vagy a helyes intonációt, amikor egy mondat kérdőjellel végződik.

Az SSML Beszédszolgáltatás-implementációja a World Wide Web Consortium Speech [Synthesis Markup Language 1.0-s](https://www.w3.org/TR/speech-synthesis)verzióján alapul.

> [!IMPORTANT]
> A kínai, japán és koreai karakterek két karakternek számítanak a számlázásban. További információ: [Díjszabás.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="neural-and-custom-voices"></a>Neurális és egyéni hangok

Használjon emberhez hasonló neurális hangot, vagy hozzon létre saját, a termékére vagy márkájára egyedi hangokat. A támogatott nyelvek, területi beállítások és hangok teljes listájáért tekintse meg a [nyelvtámogatást.](language-support.md) A neurális és az egyéni hangokkal kapcsolatos további információkért lásd: [Szöveg-beszéd – áttekintés.](text-to-speech.md)


> [!NOTE]
> A különböző stílusokban és dobómagasságban található hangok a példaszöveget olvasva hallják a [Text to Speech használatával.](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features)


## <a name="special-characters"></a>Speciális karakterek

Az SSML használata közben ne feledje, hogy a speciális karaktereket, például az idézőjeleket, az aposztrófokat és a szögletes zárójeleket escape-karakterrel kell megjelölni. További információ: [Extensible Markup Language (XML) 1.0: D függelék.](https://www.w3.org/TR/xml/#sec-entexpand)

## <a name="supported-ssml-elements"></a>Támogatott SSML-elemek

Minden SSML-dokumentum SSML-elemekkel (vagy címkékkel) jön létre. Ezek az elemek a hangmagasság, a prosody, a kötet stb. módosítására használhatók. Az alábbi szakaszok részletesen ismertetik az egyes elemek használhatók, és hogy mikor szükséges vagy mikor választható egy elem.

> [!IMPORTANT]
> Ne felejtsen el idézőjeleket használni az attribútumértékek körül. A megfelelően formázott, érvényes XML-szabványok megkövetelik, hogy az attribútumértékeket dupla idézőjelek közé kell tenni. A például `<prosody volume="90">` egy jól formázott, érvényes elem, de `<prosody volume=90>` nem az. Előfordulhat, hogy az SSML nem ismeri fel az idézőjelek között nem megadott attribútumértékeket.

## <a name="create-an-ssml-document"></a>SSML-dokumentum létrehozása

`speak` A a gyökérelem, és **minden** SSML-dokumentumhoz szükséges. A elem fontos információkat tartalmaz, például a verziót, a nyelvet és az `speak` jelölőszókészlet definícióját.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `version` | A dokumentum jelölőkódját értelmező SSML-specifikáció verzióját jelzi. Az aktuális verzió 1.0. | Kötelező |
| `xml:lang` | A gyökérdokumentum nyelvét adja meg. Az érték tartalmazhat kisbetűs, kétbetűs nyelvi kódot (például ), vagy a nyelvi kódot és a nagybetűs `en` országot/régiót `en-US` (például: ). | Kötelező |
| `xmlns` | Megadja az SSML-dokumentum jelölőszókészletét (elemtípusokat és attribútumneveket) meghatározó dokumentum URI-ját. Az aktuális URI a http://www.w3.org/2001/10/synthesis következő: . | Kötelező |

## <a name="choose-a-voice-for-text-to-speech"></a>Hang kiválasztása szövegfelismeréshez

A `voice` elem kötelező. Ezzel adható meg a szöveg beszédfelismeréshez használt hang.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/Nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szövegből a beszédbemenethez használt hangot azonosítja. A támogatott hangok teljes listájáért lásd: [Nyelvi támogatás.](language-support.md#text-to-speech) | Kötelező |

**Példa**

> [!NOTE]
> Ez a példa a hangot `en-US-JennyNeural` használja. A támogatott hangok teljes listájáért lásd: [Nyelvi támogatás.](language-support.md#text-to-speech)

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Több hang használata

A elemen belül több hang is megadható a `speak` szöveg-beszéd kimenethez. Ezek a hangok különböző nyelveken is lehetek. A szöveget minden hanghoz egy elembe kell `voice` csomagolni.

**Attribútumok**

| Attribútum | Leírás | Kötelező /Nem kötelező |
|-----------|-------------|---------------------|
| `name` | A szöveg-beszéd kimenethez használt hangot azonosítja. A támogatott hangok teljes listájáért lásd: [Nyelvi támogatás.](language-support.md#text-to-speech) | Kötelező |

> [!IMPORTANT]
> Több hang nem kompatibilis a szóhatár funkcióval. Több hang használatához le kell tiltani a szóhatár funkciót.

### <a name="disable-word-boundary"></a>Szóhatár letiltása

A Speech SDK nyelvétől függően a tulajdonságot a következőre kell beállítania az objektum `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` `false` egy `SpeechConfig` példányán: .

# <a name="c"></a>[C#](#tab/csharp)

További információ: <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

További információ: <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

További információ: <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

További információ: <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Beszédstílusok beállítása

Alapértelmezés szerint a text-to-speech szolgáltatás semleges beszédstílussal szintetizálja a szöveget a neurális hangokhoz. A beszédstílust módosíthatja úgy, hogy különböző érzelmeket fejez ki, például az érzelmeket, az empátiát és a tudatot, vagy optimalizálja a hangot különböző forgatókönyvekhez, például ügyfélszolgálathoz, híradáshoz vagy hangsegédhez a elem `mstts:express-as` használatával. Ez egy nem kötelező elem, amely csak a Speech szolgáltatásra igaz.

A beszédstílus-módosítások jelenleg a következő neurális hangokhoz támogatottak:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (Előzetes verzió)
* `zh-CN-XiaohanNeural` (Előzetes verzió)
* `zh-CN-XiaomoNeural` (Előzetes verzió)
* `zh-CN-XiaoxuanNeural` (Előzetes verzió)
* `zh-CN-XiaoruiNeural` (Előzetes verzió)

A beszédstílus intenzitása tovább módosítható, hogy jobban illeszkedjen az adott esethez. Erősebb vagy finomabb stílust is megadhat a segítségével, hogy kifejezőbb vagy visszatűrőbb legyen a `styledegree` beszéd. A beszédstílus-módosítások jelenleg kínai (mandarin, egyszerűsített) neurális hangokhoz támogatottak.

A beszédstílusok és a stílus fokának módosításán kívül a paramétert is beállíthatja úgy, hogy a hang más életkort és nemet `role` utánozza. Egy férfi például emelheti a hangmagasságot, és úgy módosíthatja az intonációt, hogy egy nő hangját utánozza, de a hangnév nem változik. A szerepkör-módosítások jelenleg a kínai (mandarin, egyszerűsített) neurális hangokhoz támogatottak:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

A fenti módosítások a mondat szintjén vannak alkalmazva, és a stílusok és a szerepjátékok hangonként változnak. Ha egy stílus vagy szerepkör nem támogatott, a szolgáltatás az alapértelmezett semleges beszéd módon fogja visszaadni a beszédet. A hanglista API-ján keresztül vagy [](rest-text-to-speech.md#get-a-list-of-voices) a kódmentes hangalapú platformon keresztül láthatja, hogy az egyes hangokhoz milyen stílusok [és szerepkörök hanganyagkészítés](https://aka.ms/audiocontentcreation) támogatottak.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> Jelenleg a csak `styledegree` a kínai (mandarin, egyszerűsített) neurális hangokat támogatja. `role` A csak a zh-CN-XiaomoNeural és a zh-CN-XiaoxuanNeural kódokat támogatja.

**Attribútumok**

| Attribútum | Leírás | Kötelező /Nem kötelező |
|-----------|-------------|---------------------|
| `style` | Megadja a beszédstílust. A beszédstílusok jelenleg hangspecifikusak. | A beszédstílus neurális hanghoz való módosításához szükséges. A használata `mstts:express-as` esetén meg kell adni a stílust. Ha érvénytelen értéket ad meg, a rendszer figyelmen kívül hagyja ezt az elemet. |
| `styledegree` | Megadja a beszédstílus intenzitását. **Elfogadott értékek:** 0,01 és 2 között. Az alapértelmezett érték 1, ami az előre meghatározott stílus intenzitását jelenti. A minimális egység 0,01, ami kissé a célstílushoz vezet. A 2 érték az alapértelmezett stílusintenzitás kétszeresét ad vissza.  | Választható (jelenleg csak `styledegree` a kínai (mandarin, egyszerűsített) neurális hangokat támogatja.)|
| `role` | Megadja a beszédszerepkedőt. A hang más életkorként és nemként fog viselkedni, de a hangnév nem változik.  | Nem kötelező (jelenleg csak `role` a zh-CN-XiaomoNeural és a zh-CN-XiaoxuanNeural.)|

Ezzel a táblázatgal meghatározhatja, hogy mely beszédstílusok támogatottak az egyes neurális hangokhoz.

| Hang                   | Stílus                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Formális, magabiztos és mérvadó hangvételt ad a hírek kézbesítésének |
|                         | `style="newscast-casual"` | Sokoldalú és alkalmi hangvételt kínál az általános hírek kézbesítéshez        |
|                         | `style="narration-professional"` | Professzionális, objektív hangvétel kifejezve a tartalomolvasáshoz        |
|                         | `style="customerservice"` | Kifejez egy barátságos és hasznos hangvételt az ügyfélszolgálat számára  |
|                         | `style="chat"`            | Alkalmi és lazán hangvételt fejez ki                         |
|                         | `style="cheerful"`        | Pozitív és elégedett hangvételt fejez ki                         |
|                         | `style="empathetic"`      | A gondoskodás és a megértés érzetét fejezi ki               |
| `en-US-JennyNeural`     | `style="customerservice"` | Kifejez egy barátságos és hasznos hangvételt az ügyfélszolgálat számára  |
|                         | `style="chat"`            | Alkalmi és lazán hangvételt fejez ki                         |
|                         | `style="assistant"`       | A digitális asszisztensek meleg és laza hangvételét fejezi ki    |
|                         | `style="newscast"`        | Sokoldalú és alkalmi hangvételt kínál az általános hírek kézbesítéshez   |
| `en-US-GuyNeural`       | `style="newscast"`        | Hivatalos és professzionális hangvételt ad a hírekhez |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Beszédkor egy menő, összegyűjtött és összeteszelt hozzáállást fejez ki. A hangnem, a hangmagasság és a prosody sokkal egységesebb a többi beszédtípushoz képest.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Hivatalos és professzionális hangvételt ad a hírekhez |
|                         | `style="customerservice"` | Kifejez egy barátságos és hasznos hangvételt az ügyfélszolgálat számára  |
|                         | `style="assistant"`       | A digitális asszisztensek meleg és laza hangvételét fejezi ki    |
|                         | `style="chat"`            | Hétköznapi és laza hangvételt ad a csevegéshez           |
|                         | `style="calm"`            | Beszédkor egy menő, összegyűjtött és összeteszelt hozzáállást fejez ki. A hangnem, a hangmagasság és a prosody sokkal egységesebb a többi beszédtípushoz képest.                                |
|                         | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangon                         |
|                         | `style="sad"`             | Egy tetszetebb hangvételt, magasabb hangmagasságot, kevésbé intenzitást és alacsonyabb hangmagasságot kifejez. Ennek az érzelemnek a gyakori mutatói a beszéd közbeni nápák vagy nápák.            |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate, displeased) és sértett állapotban van.       |
|                         | `style="fearful"`         | Magasabb hangmagasságot, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt fejez ki. A beszélő állapota nagy és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot fejez ki. A beszélő gyakran hangzú, és sokkal kevésbé lazán, erős ütemben.          |
|                         | `style="affectionate"`    | Meleg és barátságos hangvételt kifejez, magasabb hangmagassággal és hangmagassággal. A beszélő olyan állapotban van, hogy felkelti a figyelmét. A beszélő "személyisége" gyakran kiterjesszen.          |
|                         | `style="gentle"`          | Egy kis hangnemet, egy kis hangnemet, egy kis hangvételt és hangmagasságot kifejező,         |
|                         | `style="lyrical"`         | Hangulati és hangulati módon fejezi ki az érzelmeket         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Barátságos és hasznos hangvételt ad az ügyfélszolgálatnak  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Beszédkor egy menő, összegyűjtött és összetömött hozzáállást fejez ki. A hangvétel, a hangmagasság és a prosody sokkal egységesebb a többi beszédtípushoz képest.    |
|                         | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangos hangvételsel                         |
|                         | `style="sad"`             | Kifejezi a fuldoktó hangvételt, magasabb hangmagasságot, kevésbé intenzitású és alacsonyabb energiaszintet. Ennek az érzelemnek a gyakori mutatói a beszéd közbeni nápák vagy nápák.            |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate) vagy displeased (elégedetlen) és sértett állapotban van.       |
|                         | `style="fearful"`         | Kifejezi a hangos és hangos hangvételt, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt. A beszélő állapota nagy és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot ad. A beszélők gyakran hangznak, és sokkal kevésbé lazán, erős ütemben.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangon                         |
|                         | `style="sad"`             | Egy tetszetebb hangvételt, magasabb hangmagasságot, kevésbé intenzitást és alacsonyabb hangmagasságot kifejez. Ennek az érzelemnek a gyakori mutatói a beszéd közbeni nápák vagy érzelmek.            |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate) vagy displeased (elégedetlen) és sértett állapotban van.       |
|                         | `style="fearful"`         | A magasabb hangmagasságot, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt kifejezi. A beszélő állapota elégidős és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot ad. A beszélők gyakran hangznak, és sokkal kevésbé lazán, erős ütemben.    |
|                         | `style="depressed"`       | Melancholic és deponáns hangvételt fejez ki alacsonyabb hangmagassággal és energiaszintdel    |
|                         | `style="embarrassed"`     | Egy nem biztos és nem egyértelmű hangvételt fejez ki, amikor a beszélő kényelmetlennek érzi magát   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangos hangvételsel                         |
|                         | `style="sad"`             | Kifejezi a hangvételt, amely magasabb hangmagasságot, kevésbé intenzitású és alacsonyabb hangmagasságot ad. Ennek az érzelemnek a gyakori mutatói a beszéd közbeni nápák vagy nápák.            |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate, displeased) és sértett állapotban van.       |
|                         | `style="fearful"`         | Magasabb hangmagasságot, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt fejez ki. A beszélő állapota nagy és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot fejez ki. A beszélő gyakran hangzú, és sokkal kevésbé lazán, erős ütemben.    |
|                         | `style="embarrassed"`     | Egy nem biztos és nem egyértelmű hangvételt fejez ki, amikor a beszélő kényelmetlennek érzi magát   |
|                         | `style="affectionate"`    | Meleg és barátságos hangvételt fejez ki, magasabb hangmagassággal és hangmagassággal. A beszélő olyan állapotban van, hogy felkelti a figyelmét. A beszélő "személyisége" gyakran kiterjesszen.          |
|                         | `style="gentle"`          | Egy kis hangvételű, barátságos és hangvételű, alacsonyabb hangvételű és hangú hangvételt fejez ki         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangos hangvételsel                         |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate, displeased) és sértett állapotban van.       |
|                         | `style="fearful"`         | Kifejezi a hangos és hangos hangvételt, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt. A beszélő állapota elégidős és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot ad. A beszélők gyakran hangznak, és sokkal kevésbé lazán, erős ütemben.    |
|                         | `style="depressed"`       | Melancholic és deponáns hangvételt fejez ki alacsonyabb hangmagassággal és energiaszintdel    |
|                         | `style="gentle"`          | Egy barátságos, barátságos és hangnemet kifejez, alacsonyabb hangmagassággal és hangnemkel         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Lelkes és lelkes hangvételt kifejez, magasabb hangmagassággal és hangon                         |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő az irate , a displeased és a offend állapotban van.       |
|                         | `style="fearful"`         | A magasabb hangmagasságot, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt kifejezi. A beszélő állapota elégidős és nem megfelelő.                          |
|                         | `style="disgruntled"`     | Megvetésnek és panaszkodó hangvételnek ad ki. Ennek az érzelemnek a beszéde elégedetlenségről és megvetésről szól.              |
|                         | `style="serious"`         | Szigorú és parancshangot ad. A beszélők gyakran hangznak, és sokkal kevésbé lazán, erős ütemben.    |
|                         | `style="depressed"`       | Melancholic és deponáns hangvételt fejez ki alacsonyabb hangmagassággal és energiaszintdel    |
|                         | `style="gentle"`          | Egy finom, finom és hangnemet kifejező, alacsonyabb hangmagasságú és hangnemű         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Egy tetszetebb hangvételt, magasabb hangmagasságot, kevésbé intenzitást és alacsonyabb hangmagasságot kifejez. Ennek az érzelemnek a gyakori mutatói a beszéd közbeni nápák vagy nápák.            |
|                         | `style="angry"`           | Hangvételt és hangvételt kifejez, alacsonyabb hangmagassággal, magasabb intenzitással és magasabb hangi energiaszintel. A beszélő irate ( irate, displeased) és sértett állapotban van.       |
|                         | `style="fearful"`         | Magasabb hangmagasságot, magasabb hangmagasságot, magasabb hangmagasságot és gyorsabb hangvételt fejez ki. A beszélő állapota nagy és nem megfelelő.                          |

A táblázat segítségével ellenőrizheti a támogatott szerepköröket és azok definícióit.

|Szerepkör                     | Leírás                |
|-------------------------|----------------------------|
|`role="Girl"`            | A hang egy lánynak imitál. |
|`role="Boy"`             | A hang egy gyereknek imitál. |
|`role="YoungAdultFemale"`| A hang egy fiatal felnőtt nőre imitál.|
|`role="YoungAdultMale"`  | A hang egy fiatal felnőtt férfinak imitál.|
|`role="OlderAdultFemale"`| A hang egy régebbi, felnőtt nőre imitál.|
|`role="OlderAdultMale"`  | A hang egy régebbi felnőtt férfira imitál.|
|`role="SeniorFemale"`    | A hang egy szenior nő utánzása.|
|`role="SeniorMale"`      | A hang egy szenior férfinak imitál.|


**Példa**

Ez az SSML-kódrészlet azt mutatja be, hogyan használható a elem a `<mstts:express-as>` beszédstílusra való `cheerful` váltáshoz.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Ez az SSML-kódrészlet azt mutatja be, hogy a attribútummal hogyan változtatható meg a Beszédstílus intenzitása `styledegree` a XiaonézoNeural esetén.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Ez az SSML-kódrészlet azt mutatja be, hogyan változott meg a `role` XiaomoNeural szerepkör-lejátszása az attribútummal.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Szüneteltetés/szüneteltetés hozzáadása vagy eltávolítása

A elem használatával szüneteket (vagy töréseket) szúr be a szavak közé, vagy megakadályozhatja, hogy a szövegfelismerési szolgáltatás automatikusan szüneteket `break` ad hozzá.

> [!NOTE]
> Ezzel az elemmel felülbírálhatja egy szó vagy kifejezés szövegfelhangulat (TTS) alapértelmezett viselkedését, ha az ehhez a szóhoz vagy kifejezéshez használt szintetizált beszéd nem természetes. A `strength` `none` beállítással megakadályozhatja a prosodikus törést, amelyet a text-to-speech szolgáltatás automatikusan beszúr.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/Nem kötelező |
|-----------|-------------|---------------------|
| `strength` | A szüneteltetés relatív időtartamát adja meg az alábbi értékek egyikével:<ul><li>Nincs</li><li>x-gyenge</li><li>Gyenge</li><li>közepes (alapértelmezett)</li><li>Erős</li><li>x-strong</li></ul> | Választható |
| `time` | A szünet abszolút időtartamát határozza meg másodpercben vagy ezredmásodpercben,ezt az értéket 5000 ezredmásodpercnél kisebbre kell állítani. Példák az érvényes értékekre: `2s` és `500ms` | Választható |

| Erejét                      | Description |
|-------------------------------|-------------|
| Nincs, vagy ha nincs megtéve érték | 0 ms        |
| x-gyenge                        | 250 ms      |
| Gyenge                          | 500 ms      |
| közepes                        | 750 ms      |
| Erős                        | 1000 ms     |
| x-strong                      | 1250 ms     |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Csend hozzáadása

A elemmel szüneteket szúr be szöveg előtt vagy után, vagy a két szomszédos `mstts:silence` mondat között.

> [!NOTE]
>A és a közötti különbség az, hogy a szöveg bármely helyéhez hozzáadható, de a csend csak a bemeneti szöveg elején vagy végén, vagy 2 szomszédos mondat határán `mstts:silence` `break` `break` működik.


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező /Nem kötelező |
|-----------|-------------|---------------------|
| `type` | Megadja a hozzáadható csend helyét: <ul><li>`Leading` – a szöveg elején </li><li>`Tailing` – a szöveg végén </li><li>`Sentenceboundary` – szomszédos mondatok között </li></ul> | Kötelező |
| `Value` | A szünet abszolút időtartamát adja meg másodpercben vagy ezredmásodpercben, ezt az értéket 5000 ezredmásodpercnél kisebbre kell állítani. Példák az érvényes értékekre: `2s` és `500ms` | Kötelező |

**Példa** Ebben a `mtts:silence` példában a 200 ms csendet ad két mondat közé.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Bekezdések és mondatok megadása

`p` A és a elemek a bekezdések és mondatok `s` jegyzésére használhatók. Ezen elemek hiányában a text-to-speech szolgáltatás automatikusan meghatározza az SSML-dokumentum struktúráját.

A elem szöveget és a következő elemeket `p` tartalmazhatja: `audio` , , , , , , , és `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

A elem szöveget és a következő elemeket `s` tartalmazhatja: `audio` , , , , , , és `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Syntax**

```XML
<p></p>
<s></s>
```

**Példa**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>A kiejtés javítása a phonemes használatával

A `ph` elem az SSML-dokumentumokban a phonetic kiejtéshez használatos. Az `ph` elem csak szöveget tartalmazhat, más elemeket nem. Mindig biztosítson emberi olvasásra használható beszédet tartalékként.

A telefonos ábécék telefonokból állnak, amelyek esetenként betűkből, számokból vagy karakterekből állnak. Minden telefon egyedi beszédhangot ír le. Ez ellentétben áll a latin ábécével, ahol minden betű több beszélt hangot is képviselhet. Vegyük például a "c" betű különböző kiejtését a "candy" és a "szünni" szavakban, vagy a "th" betűkombináció különböző kiejtését a "dolog" és a "ezek" szavakban.

> [!NOTE]
> A Phonemes címke jelenleg nem támogatott ezen 5 hanghoz (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural és mt-MT-GarceNeural).

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező/Nem kötelező |
|-----------|-------------|---------------------|
| `alphabet` | Megadja a sztring kiejtésének az attribútumban való szinonimizálásakor használnia kell a phonetic `ph` ábécét. Az ábécét megszabadó sztringet kisbetűkben kell megadni. A következő lehetséges ábécék adhatók meg.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Nemzetközi telefonos ábécé</a></li><li>`sapi`&ndash; [Beszédszolgáltatás telefonos ábécé](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Univerzális telefonkészlet</a></li></ul><br>Az ábécé csak a elem `phoneme` elemére vonatkozik. | Választható |
| `ph` | Olyan telefonokat tartalmazó sztring, amelyek meghatározzák az elemben található szó `phoneme` kiejtését. Ha a megadott sztring ismeretlen telefonokat tartalmaz, a text-to-speech (TTS) szolgáltatás elutasítja a teljes SSML-dokumentumot, és nem állítja elő a dokumentumban megadott beszédkimenetet. | A phonemes használata esetén kötelező. |

**Példák**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Egyéni lexikon használata a kiejtés javításához

Előfordulhat, hogy a szövegfelismerési szolgáltatás nem képes pontosan kiírni egy szót. Ilyen lehet például egy vállalat neve vagy egy orvosi kifejezés. A fejlesztők a és címkék használatával határozhatják meg, hogy az egyes entitások hogyan olvashatók az `phoneme` SSML-ben. `sub` Ha azonban meg kell határoznia több entitás olvasási módszerét, létrehozhat egy egyéni lexikont a `lexicon` címkével.

> [!NOTE]
> Az egyéni lexikon jelenleg az UTF-8 kódolást támogatja.

> [!NOTE]
> Az egyéni lexikon jelenleg nem támogatott az 5 hanghoz (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural és mt-MT-GarceNeural).


**Syntax**

```XML
<lexicon uri="string"/>
```

**Attribútumok**

| Attribútum | Leírás                               | Kötelező / Nem kötelező |
|-----------|-------------------------------------------|---------------------|
| `uri`     | A külső PLS-dokumentum címe. | Kötelező.           |

**Használat**

Több entitás olvasásának meghatározásához létrehozhat egy egyéni lexikont, amely .xml- vagy .pls-fájlként van tárolva. Az alábbiakban egy minta .xml-fájl található.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

A `lexicon` elem legalább egy elemet `lexeme` tartalmaz. Minden elem legalább egy elemet és egy vagy több `lexeme` `grapheme` , és `grapheme` `alias` `phoneme` elemet tartalmaz. A `grapheme` elem az <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortography </a>szöveget tartalmazza. Az elemek egy betűszó vagy rövidített kifejezés kiejtését `alias` jelzik. A `phoneme` elem a kiejtésének `lexeme` szövegét írja le.

Fontos megjegyezni, hogy egy kifejezés kiejtése nem állíthatja be közvetlenül az egyéni lexikon használatával. Ha be kell állítania a kiejtést egy betűszóhoz vagy rövidített kifejezéshez, először adjon meg egy kifejezést, majd társítsa a következővel: `alias` `phoneme` `alias` . Például:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

A rövidítéshez vagy rövidítéshez közvetlenül is megírhatja a `alias` várt kifejezést. Például:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> Az `phoneme` elem nem tartalmazhat szóközöket az IPA használata esetén.

További információ az egyéni lexikonfájlról: [Kiejtési lexikon specifikáció (PLS) 1.0-s verziója.](https://www.w3.org/TR/pronunciation-lexicon/)

Ezután tegye közzé az egyéni lexikonfájlt. Bár a fájl tárolására nem vonatkoznak korlátozások, javasoljuk, hogy használja a [Azure Blob Storage.](../../storage/blobs/storage-quickstart-blobs-portal.md)

Az egyéni lexikon közzététele után hivatkozhat rá az SSML-ben.

> [!NOTE]
> A `lexicon` elemnek a elemen belül kell `voice` lennie.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Az egyéni lexikon használata esetén a "BTW" a következőként lesz beolvasva: "By the way". A "Benigni" a megadott IPA "b< és nji" adatokat fogja olvasni.

**Korlátozások**
- Fájlméret: az egyéni lexikonfájl maximális mérete 100 KB, ha ez a méret meghaladja, a szintéziskérés sikertelen lesz.
- Lexikon-gyorsítótár frissítése: az egyéni lexikon az első betöltésekor URI-val lesz gyorsítótárazva kulcsként a TTS-szolgáltatásban. Az azonos URI-val használt lexikon nem töltődik be újra 15 percen belül, így az egyéni lexikonváltozásnak a effektus effektusig legalább 15 percet várnia kell.

**Beszédszolgáltatás-telefonkészletek**

A fenti mintában az IPA-telefonkészlet néven is ismert Nemzetközi telefonos ábécét használjuk. Javasoljuk, hogy a fejlesztők az IPA-t használják, mert ez a nemzetközi szabvány. Egyes IPA-karaktereknél a Unicode-karakterekkel való ábrázoláskor a "precomposed" és a "decomposed" (felosztott) verziót kapják. Az egyéni lexikon csak a felosztott unicode-okat támogatja.

Figyelembe véve, hogy az IPA-t nem könnyű megjegyezni, a Speech service egy phonetic készletet határoz meg hét nyelvhez ( `en-US` , , , , , , és `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` ).

Az attribútum értékeként használhatja az egyéni `sapi` `alphabet` lexikonokat az alább látható módon:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

További információ a Speech service phonetic alphabet (Beszédszolgáltatás telefonos ábécé) részletes betűrendjével kapcsolatban: [Speech service phonetic sets (A Speech szolgáltatás telefonos készlete).](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Prosody beállítása

A elem a hangmagasság, a hossz, a tartomány, a sebesség, az időtartam és a szöveg-beszéd kimenetének `prosody` változásait határozza meg. A elem szöveget és a következő elemeket `prosody` tartalmazhatja: `audio` , , , , , , , és `break` `p` `phoneme` `prosody` `say-as` `sub` `s` .

Mivel a prosodikus attribútumértékek széles tartományon belül változhatnak, a beszédfelismerő a hozzárendelt értékeket a kiválasztott hang tényleges prosodikus értékeinek javaslatként értelmezi. A text-to-speech szolgáltatás korlátozza vagy helyettesíti a nem támogatott értékeket. Nem támogatott értékek például az 1 MHz-es vagy a 120-as kötet.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `pitch` | A szöveg alapértékét jelzi. A hangmagasságot a következő ként fejezheti ki:<ul><li>Abszolút érték, számmal és "Hz" (Hertz) kifejezéssel kifejezve. Például: `<prosody pitch="600Hz">some text</prosody>`.</li><li>Egy relatív érték, amelyet a "+" vagy a "-" karakterrel, majd a "Hz" vagy az "st" karakterrel megadott számmal fejeznek ki, és amely meghatározza a dobás értékét. Például: `<prosody pitch="+80Hz">some text</prosody>` vagy `<prosody pitch="-2st">some text</prosody>` . Az "st" azt jelzi, hogy a változási egység szemiton, ami fél hangvételű (féllépéses) a szabványos diatonikus skálán.</li><li>Állandó érték:<ul><li>x-low</li><li>Alacsony</li><li>közepes</li><li>magas</li><li>x-high</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| `contour` |A Now mostantól a neurális és a standard hangokat is támogatja. A levezetés az pitch változásait jelöli. Ezeket a módosításokat a beszéd kimenetében megadott időpozíciókban található célok tömbjeként ábrázolja a rendszer. Az egyes célokat paraméterpárok készletei határozzák meg. Például: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Az egyes paraméterkészletek első értéke a szöveg időtartamának százalékában adja meg a pitch változás helyét. A második érték határozza meg a dobás emelésének vagy csökkentésének mennyiségét relatív érték vagy a pitch enumerálásának értéke használatával `pitch` (lásd: ). | Választható |
| `range` | Egy érték, amely a szöveg dobástartományát jelöli. A kifejezés kifejezése a leírására használt abszolút értékekkel, relatív értékekkel vagy `range` enumerálásértékekkel is `pitch` lehetséges. | Választható |
| `rate` | A szöveg beszédarányát jelzi. A kifejezés a következő `rate` lehet:<ul><li>Egy relatív érték számként kifejezve, amely az alapértelmezett érték szorzójaként működik. Az *1* érték például nem módosítja az arányt. A *0,5* érték az arány felére való felére való feléresztezést ad vissza. A *3 érték* megbotlott arányt ad vissza.</li><li>Állandó érték:<ul><li>x-lassú</li><li>Lassú</li><li>közepes</li><li>Gyors</li><li>x-fast</li><li>alapértelmezett</li></ul></li></ul> | Választható |
| `duration` | Az az időtartam, amely eltelt, amíg a beszédszintézis (TTS) szolgáltatás beolvassa a szöveget másodpercben vagy ezredmásodpercben. Például *2 vagy* *1800ms.* Az Időtartam csak a standard hangokat támogatja.| Választható |
| `volume` | A beszélő hangkötetének szintjét jelzi. A kötetet a következő ként fejezheti ki:<ul><li>Abszolút érték 0,0 és 100,0 között megadott számként,  a legcsendesebbtől a leghangosabb *érték ig.* Például: 75. Az alapértelmezett érték 100.0.</li><li>Egy relatív érték, amelyet a "+" vagy a "-" karakterrel megadott számmal fejeznek ki, és amely meghatározza a kötetet módosító mennyiséget. Például: +10 vagy -5.5.</li><li>Állandó érték:<ul><li>Csendes</li><li>x-soft</li><li>Puha</li><li>közepes</li><li>Hangos</li><li>x-hang</li><li>alapértelmezett</li></ul></li></ul> | Választható |

### <a name="change-speaking-rate"></a>Beszédszám módosítása

A beszédarány neurális hangokra és standard hangokra is alkalmazható szó- vagy mondatszinten.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Kötet módosítása

A kötetváltozások alkalmazhatók a standard hangokra a szavak vagy mondatok szintjén. Míg a kötetváltozások csak mondatszinten alkalmazhatók neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>A pitch módosítása

A beszédhangok a szabványos hangokra is alkalmazhatók a szavak vagy mondatok szintjén. Míg a dobás változásai csak mondatszinten alkalmazhatók neurális hangokra.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Az pitch (dobás) módosítása

> [!IMPORTANT]
> A hangmagasságok mostantól támogatják a dobás módosításait.

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as elem

`say-as` A nem kötelező elem, amely az elem szövegének tartalomtípusát (például számát vagy dátumát) jelzi. Ez útmutatást nyújt a beszédszintézis-motornak arról, hogyan lehet leindoklani a szöveget.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező / Nem kötelező |
|-----------|-------------|---------------------|
| `interpret-as` | Az elem szövegének tartalomtípusát jelzi. A típusok listáját az alábbi táblázatban láthatja. | Kötelező |
| `format` | További információt nyújt az elem szövegének pontos formázásával kapcsolatban olyan tartalomtípusok esetében, amelyek nem egyértelmű formátumokkal is rendelkezik. Az SSML definiálja a használatukhoz használt tartalomtípusok formátumát (lásd az alábbi táblázatot). | Választható |
| `detail` | A beszélt adatok szintjét jelzi. Ez az attribútum például kérheti, hogy a beszédszintézis-motor lekisértsen írásjeleket. A nem határoz meg standard értékeket `detail` a számára. | Választható |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

A és attribútumok támogatott tartalomtípusai a `interpret-as` `format` következők. Csak akkor `format` foglalja bele az `interpret-as` attribútumot, ha az dátumra és időpontra van beállítva.

| interpret-as | Formátum | Értelmezés |
|--------------|--------|----------------|
| `address` | | A szöveg egy címként van eléneklve. A beszédszintézis motorja a következő:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />A következőként: "I'm at 150th court north east redmond washington." |
| `cardinal`, `number` | | A szöveg számos számként van elénekve. A beszédszintézis motorja a következő:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />A következőként: "Három alternatíva van.". |
| `characters`, `spell-out` | | A szöveg külön betűként van kitűzve (ki van tűzve). A beszédszintézis motorja a következő:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T" ként. |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | A szöveg dátumként van kiszengelve. A `format` attribútum határozza meg a dátum formátumát (*d=day, m=month és y=year*). A beszédszintézis-motor a következőt növeli:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />A "Today is October is T nineteenth two thousand thousand." (Ma október 19., 200 000 bájt) |
| `digits`, `number_digit` | | A szöveget a rendszer különálló számjegyek sorozataként beszélte el. A beszédszintézis-motor a következőt növeli:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9." |
| `fraction` | | A szöveg törtszámként van kiszengelve. A beszédszintézis-motor a következőt növeli:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Mint "három hüvelyk nyolcas" |
| `ordinal` | | A szöveg sorszámként van elénekálva. A beszédszintézis-motor a következőt növeli:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"Válassza ki a harmadik lehetőséget". |
| `telephone` | | A szöveg telefonszámként van elbeszélve. Az `format` attribútum tartalmazhat számjegyeket, amelyek országkódot képviselnek. Például az "1" az "Egyesült Államok" vagy "39" Olaszország esetében. A beszédszintézis-motor ezzel az információval irányíthatja a telefonszám kiejtését. A telefonszám az országkódot is tartalmazhatja, és ha igen, elsőbbséget élvez az országkódjával. `format` A beszédszintézis-motor a következőt növeli:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />A "My number is area code eight88 five five one two one two" (Az én számom a területkód nyolc nyolc nyolc, öt öt, egy kettő és kettő). |
| `time` | hms12, hms24 | A szöveg el van éneklve. A attribútum meghatározza, hogy az idő `format` 12 órás (hs12) vagy 24 órás (hs24) óra használatával van-e megadva. Kettősponttal válassza el az órákat, perceket és másodperceket képviselő számokat. Az alábbi érvényes idő példák: 12:35, 1:14:32, 08:15 és 02:50:45. A beszédszintézis motorja a következő:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"A betanítás négy órakor indul." |

**Használat**

A `say-as` elem csak szöveget tartalmazhat.

**Példa**

A beszédszintézis-motor a következő példát mondja: "Az első kérés egy teremben volt október 9-én, 20-án, korai érkezéssel 12:5-kor."

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Felvett hang hozzáadása

`audio` A nem kötelező elem, amely lehetővé teszi MP3-hang beszúrása egy SSML-dokumentumba. A hangelem törzse tartalmazhat egyszerű szöveget vagy SSML-jelölőt, amely akkor van elhangzva, ha a hangfájl nem érhető el vagy nem lejátszható. Emellett a elem szöveget és a következő elemeket is `audio` tartalmazhatja: , , , , , , `audio` és `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Az SSML-dokumentumban szereplő összes hanganyagnak meg kell felelnie az alábbi követelményeknek:

* Az MP3-nak egy internetről elérhető HTTPS-végponton kell üzemelnie. HTTPS szükséges, és az MP3-fájlt üzemeltető tartománynak érvényes, megbízható TLS-/SSL-tanúsítványt kell bemutatnia.
* Az MP3-nak érvényes MP3-fájlnak (MPEG v2) kell lennie.
* A bitszámnak 48 kbps-nek kell lennie.
* A minta sebességének 16 000 Hz-nek kell lennie.
* Az összes szöveges és hangfájl összesített teljes ideje egyetlen válaszban nem haladhatja meg a kilencven (90) másodpercet.
* Az MP3 nem tartalmazhat semmilyen ügyfélspecifikus vagy egyéb bizalmas információt.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribútumok**

| Attribútum | Leírás                                   | Kötelező / Nem kötelező                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Megadja a hangfájl helyét/URL-címét. | Kötelező, ha az SSML-dokumentum hangelemét használja. |

**Példa**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Háttérhang hozzáadása

A elem lehetővé teszi, hogy háttérhangot adjon az SSML-dokumentumokhoz (vagy egy hangfájlt `mstts:backgroundaudio` szöveg-beszéddel keverjen). A háttérben egy hangfájlt behűthet, elhalványulhat a szövegtől a beszédig, és elhalványulhat a szövegből `mstts:backgroundaudio` a beszédig.

Ha a megadott háttérhang rövidebb, mint a szöveg-beszéd vagy az elhalványulás, hurkot fog látni. Ha hosszabb a szöveg-beszédnél, akkor a kihalványulás befejezésekor leáll.

SSML-dokumentumonként csak egy háttér-hangfájl engedélyezett. A elemen belül azonban több elem címkéi között is el lehet ásni, hogy további hanganyagokat adjon `audio` `voice` hozzá az SSML-dokumentumhoz.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribútumok**

| Attribútum | Leírás | Kötelező /Nem kötelező |
|-----------|-------------|---------------------|
| `src` | A háttér-hangfájl helyét/URL-címét adja meg. | Szükséges, ha háttérhangot használ az SSML-dokumentumban. |
| `volume` | A háttér-hangfájl kötetét adja meg. **Elfogadott értékek:** a `0` befogadó `100` értékekkel. Az alapértelmezett érték `1`. | Választható |
| `fadein` | A háttérhang "behalványításának" időtartamát adja meg ezredmásodpercben. Az alapértelmezett érték , amely egyenértékű `0` azzal, ha nincs elhalványulás. **Elfogadott értékek:** a `0` befogadó `10000` értékekkel.  | Választható |
| `fadeout` | Megadja a háttérhang elhalványításának időtartamát ezredmásodpercben. Az alapértelmezett érték a , ami azzal `0` egyenértékű, hogy nincs elhalványulás. **Elfogadott értékek:** a `0` befogadó `10000` értékekhez.  | Választható |

**Példa**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Könyvjelző elem

A könyvjelző elem lehetővé teszi, hogy egyéni jelölőket szúrjon be az SSML-be a hangstream egyes jelölőinek eltolására.
A könyvjelzőelemeket nem fogjuk kiolvasni.
A könyvjelző elemmel hivatkozhat egy adott helyre a szöveg- vagy címkesorozatban.

> [!NOTE]
> `bookmark` A elem jelenleg csak a `en-US-AriaNeural` hanggal működik.

**Syntax**

```xml
<bookmark mark="string"/>
```

**Attribútumok**

| Attribútum | Leírás                                   | Kötelező/nem kötelező                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Megadja a elem hivatkozási `bookmark` szövegét. | Kötelező. |

**Példa**

Érdemes lehet például tudni az egyes virágszavak időeltolását az alábbiak szerint

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Könyvjelző lekérte a Speech SDK-val

A könyvjelző-eltolások lekért `BookmarkReached` értékéhez feliratkozhat a Speech SDK eseményére.

> [!NOTE]
> `BookmarkReached` az esemény csak a Speech SDK 1.16.0-s verziója óta érhető el.

`BookmarkReached` A események akkor történnek, amikor a kimeneti hangadatok elérhetővé válnak, ami gyorsabb lesz, mint a kimeneti eszközre való visszajátszás.

* `AudioOffset` A a kimeneti hang a szintézis kezdete és a könyvjelző elem közötti eltelt idejét jelenti. Ezt száz nanoszekundumos egységekben (HNS) mérjük, 10 000 HNS-sel egyenértékű 1 ezredmásodpercnek.
* `Text` A a könyvjelző elem referenciaszövege, amely a attribútumban beállított `mark` sztring.

# <a name="c"></a>[C#](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

A fenti SSML-példában az esemény kétszer lesz aktiválva, és a konzol kimenete a következő `BookmarkReached` lesz:
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

A fenti SSML-példában az esemény kétszer lesz aktiválva, a konzol `BookmarkReached` kimenete pedig
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

A fenti SSML-példában az esemény kétszer aktiválódik, a konzol `BookmarkReached` kimenete pedig
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

A fenti SSML-példában az esemény kétszer lesz aktiválva, a konzol `bookmark_reached` kimenete pedig
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

További információ: <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

A fenti SSML-példában az esemény kétszer lesz aktiválva, a konzol `bookmarkReached` kimenete pedig
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

További információ: <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

A fenti SSML-példában az esemény kétszer lesz aktiválva, a konzol `BookmarkReached` kimenete pedig
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

További információ: <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>.

---

## <a name="next-steps"></a>Következő lépések

* [Nyelvi támogatás: hangok, területi beállítások, nyelvek](language-support.md)
